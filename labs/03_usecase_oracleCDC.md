## Initial Steps

In your ssh terminal **ssh ec2-user@<PublicIP>**

Go to and start the docker environment
```bash
cd /home/ec2-user/ksqldbWorkshop/docker
docker-compose up -d
```

After a couple of minutes check the connector plugins installed in the Kafka Connect Cluster
```bash
curl -s -X GET -H 'Content-Type: application/json' http://localhost:8083/connector-plugins | jq '.'
```
Search for "io.confluent.connect.oracle.cdc.OracleCdcSourceConnector"

Lets analyze the Oracle Database tables 
```bash
docker exec -i oracle sqlplus sys/Admin123@//localhost:1521/ORCLCDB as sysdba 
```
And check the tables CUSTOMER and TRANSACTIONS (execute the count command a couple of times to check if the number of transactions is growing)
```bash
select * from "C##MYUSER".CUSTOMERS;
select count(*) from "C##MYUSER".TRANSACTIONS;
```

Now we are going to create the connector

```bash
curl -s -X PUT -H 'Content-Type: application/json'  http://localhost:8083/connectors/OracleCDC/config -d '{      
     			"connector.class": "io.confluent.connect.oracle.cdc.OracleCdcSourceConnector",
     			"name": "OracleCDC",
          "tasks.max":2,
          "key.converter": "io.confluent.connect.avro.AvroConverter",
          "key.converter.schema.registry.url": "http://schema-registry:8081",
          "value.converter": "io.confluent.connect.avro.AvroConverter",
          "value.converter.schema.registry.url": "http://schema-registry:8081",
          "confluent.license": "",
          "confluent.topic.bootstrap.servers": "kafka:29092",
          "confluent.topic.replication.factor": "1",
          "oracle.server": "oracle",
          "oracle.port": 1521,
          "oracle.sid": "ORCLCDB",
          "oracle.username": "C##MYUSER",
          "oracle.password": "mypassword",
          "start.from":"snapshot",
          "redo.log.topic.name": "redo-log-topic",
          "redo.log.consumer.bootstrap.servers":"kafka:29092",
          "table.inclusion.regex": "ORCLCDB[.]C##MYUSER[.].*",
          "table.topic.name.template": "${databaseName}.${schemaName}.${tableName}",
          "numeric.mapping": "best_fit",
          "connection.pool.max.size": 20,
          "redo.log.row.fetch.size":1,
          "oracle.dictionary.mode": "auto",
          "topic.creation.redo.include": "redo-log-topic",
          "topic.creation.redo.replication.factor": 1,
          "topic.creation.redo.partitions": 1,
          "topic.creation.redo.cleanup.policy": "delete",
          "topic.creation.redo.retention.ms": 1209600000,
          "topic.creation.default.replication.factor": 1,
          "topic.creation.default.partitions": 1,
          "topic.creation.default.cleanup.policy": "delete"
  }'
```

Lets verify that the connector is running
```bash
curl -s -X GET -H 'Content-Type: application/json' http://localhost:8083/connectors/OracleCDC/status | jq
```
Also check control center if the connector is running. : **http://yourpublicip:9021**

You should have 3 topics created related to the CDC, do you know which ones?

Now lets start creating the Data Pipelines.

First, lets specify a Table for the customers

```bash
docker exec -it workshop-ksqldb-cli ksql http://ksqldb-server:8088
ksql> set 'auto.offset.reset'='earliest';
```
Lets use schema inference te get the structure of the stream  
```bash
create stream customers_cdc
with ( 
        kafka_topic='ORCLCDB.C__MYUSER.CUSTOMERS', 
        value_format='avro');
```
And a STREAM for the transactions. 
    
```bash
create stream trx_cdc
with ( 
        kafka_topic='ORCLCDB.C__MYUSER.TRANSACTIONS', 
        value_format='avro');
```

Describe both streams to understand the structure:
```bash
ksql>  describe stream trx_cdc;
ksql>  describe stream customers_cdc;
```
We are going to create a table from stream customers_cdc
```bash
create table customers_cdc_table as select
 id,
 LATEST_BY_OFFSET(first_name) as first_name, 
 LATEST_BY_OFFSET(last_name) as last_name, 
 LATEST_BY_OFFSET(email) as email, 
 LATEST_BY_OFFSET(club_status) as club_status
 from customers_cdc
 group by id;

```


Lets Join Customers and Transactions tu enrich the result

```bash
create stream enriched_trx as select 
  tr.id as id_trx,
  tr.amount as amount,
  tr.account_type_origin as account_type_origin_trx, 
  tr.id_origin as id_origin_trx, 
  tr.account_type_destination as account_type_destination_trx,
  tr.id_destination as id_destination_trx,
  c.first_name, 
  c.last_name, 
  c.email, 
  c.club_status 
  from trx_cdc tr 
  left join customers_cdc_table c on tr.id_origin = c.id; 
```
Lets check the result:
```bash
select * from enriched_trx emit changes limit 3;
```

As we are generating random data in the transaction we can have the same origin and destination.
We can filter this by day (we send a complete analysis daily)

```bash
create stream filtered_enriched_trx as select 
  ID_ORIGIN_TRX, 
 ID_TRX, 
 AMOUNT, 
 ACCOUNT_TYPE_ORIGIN_TRX, 
 ACCOUNT_TYPE_DESTINATION_TRX, 
 ID_DESTINATION_TRX,
 FIRST_NAME, 
 LAST_NAME, 
 EMAIL, 
 CLUB_STATUS
  from enriched_trx 
  where id_origin_trx != id_destination_trx
; 

```
Now lets check the total amount sent per customer per day

```bash
create table total_amount_customer as select
ID_ORIGIN_TRX,
LATEST_BY_OFFSET(FIRST_NAME) FIRST_NAME, 
LATEST_BY_OFFSET(LAST_NAME) as LAST_NAME,
SUM(AMOUNT) as Total_Amount
from filtered_enriched_trx
WINDOW TUMBLING (SIZE 1 DAYS)
group by ID_ORIGIN_TRX
EMIT CHANGES;
```
check if we have values :
```bash
select * from total_amount_customer emit changes limit 4;
```
Or with a PULL query
```bash
select * from total_amount_customer where ID_ORIGIN_TRX=1;
```

Check control center to see the final Pipeline, Connector and Topics

End

[go back to Agenda](https://github.com/jr-marquez/Workshop_Confluent/blob/main/README.md#confluent-hands-on-workshop)