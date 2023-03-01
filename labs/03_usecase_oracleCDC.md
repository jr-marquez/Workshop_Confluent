## Initial Steps

In your ssh terminal .

Check the connector plugins installed in Connect
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
curl -s -X PUT -H 'Content-Type: application/json'  http://localhost:8083/connectors/SimpleOracleCDC/config -d '{      
    "connector.class": "io.confluent.connect.oracle.cdc.OracleCdcSourceConnector",
    "name": "SimpleOracleCDC",
    "tasks.max":3,
    "key.converter": "io.confluent.connect.avro.AvroConverter",
    "key.converter.schema.registry.url": "http://schema-registry:8081",
    "value.converter": "io.confluent.connect.avro.AvroConverter",
    "value.converter.schema.registry.url": "http://schema-registry:8081",
    "confluent.topic.bootstrap.servers":"kafka:29092",
    "confluent.license": "",
    "confluent.topic.replication.factor": "3",
    "oracle.server": "oracle",
    "oracle.port": 1521,
    "oracle.sid": "ORCLCDB",
    "oracle.username": "C##MYUSER",
    "oracle.password": "mypassword",
    "start.from":"snapshot",
    "redo.log.topic.name": "redo-log-topic",
    "table.inclusion.regex": ".*CUSTOMERS.*",
    "table.topic.name.template": "${databaseName}.${schemaName}.${tableName}",
    "numeric.mapping": "best_fit",
    "oracle.dictionary.mode": "auto",
    "topic.creation.redo.include": "redo-log-topic",
    "topic.creation.redo.replication.factor": 3,
    "topic.creation.redo.partitions": 1,
    "topic.creation.redo.cleanup.policy": "delete",
    "topic.creation.redo.retention.ms": 1209600000,
    "topic.creation.default.replication.factor": 3,
    "topic.creation.default.partitions": 1,
    "topic.creation.default.cleanup.policy": "delete",
    "poll.linger.ms":0
  }'

```

Lets verify that the connector is running
```bash
curl -s -X GET -H 'Content-Type: application/json' http://localhost:8083/connectors/SimpleOracleCDC/status | jq
```
Also check control center if the connector is running
Go to control center and select the message option in topic ORCLCDB.C__MYUSER.EMP

```bash
create stream customers( 
    id INTEGER KEY,
    first_name VARCHAR,
    last_name VARCHAR,
    email VARCHAR,
    gender VARCHAR,
    club_status VARCHAR,
    comments VARCHAR,
    create_ts VARCHAR,
    update_ts VARCHAR
    ) with ( 
        kafka_topic='Payment_Instruction', 
        value_format='avro');
```

```bash
create stream 
( 
    id INTEGER KEY,
    first_name VARCHAR,
    last_name VARCHAR,
    email VARCHAR,
    gender VARCHAR,
    club_status VARCHAR,
    comments VARCHAR,
    create_ts VARCHAR,
    update_ts VARCHAR
    ) with ( 
        kafka_topic='Payment_Instruction', 
        value_format='avro');


Now we are going to insert some data in oracle to check cdc.
Open
```bash
docker-compose exec oracle /scripts/go_sqlplus.sh
```
And then: 
```bash
SQL> insert into C##MYUSER.emp (name) values ('Dale');
SQL> insert into C##MYUSER.emp (name) values ('Emma');
SQL> commit;
```
Check if messages appear.
Now lets update and delete.
```bash
SQL> update C##MYUSER.emp set name = 'Robert' where name = 'Bob';
SQL> delete C##MYUSER.emp where name = 'Jane';
SQL> commit;
SQL> exit
```
Check what happend to the topic in control Center

Now we are going to validate **schema mutation**
```bash
docker-compose exec oracle /scripts/go_sqlplus.sh
```
```bash
SQL> ALTER TABLE C##MYUSER.EMP ADD (SURNAME VARCHAR2(100));

SQL> insert into C##MYUSER.emp (name, surname) values ('Mickey', 'Mouse');
SQL>commit;
```
Check the new row added to the topic and the schema mutation (versions)


[go back to Agenda](https://github.com/jr-marquez/Workshop_Confluent/blob/main/README.md#confluent-hands-on-workshop)