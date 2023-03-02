## Initial Steps

In your ssh terminal .

Lets verify that the connector is running
```bash
curl -s -X GET -H 'Content-Type: application/json' http://localhost:8083/connectors/OracleCDC/status | jq
```

Now lets stop the connector

```bash
curl -s -X DELETE -H 'Content-Type: application/json' http://localhost:8083/connectors/OracleCDC/
```

Check again if it is running and also go to Control Center --> Connect (you must have 3)
Delete the topics redo-log-topic, ORCLCDB.C__MYUSER.CUSTOMERS and ORCLCDB.C__MYUSER.TRANSACTIONS

Lets mask the customer information using transforms:

```bash
"transforms": "EMAIL, CLUB_STATUS",

"transforms.EMAIL.type": "org.apache.kafka.connect.transforms.MaskField$Value",
"transforms.EMAIL.fields": "EMAIL",
"transforms.EMAIL.replacement": "***@***.com",

"transforms.CLUB_STATUS.type": "org.apache.kafka.connect.transforms.MaskField$Value",
"transforms.CLUB_STATUS.fields": "CLUB_STATUS",
"transforms.CLUB_STATUS.replacement": "XXXXX"
```

Lets include this parameters in the connector:

```bash
curl -s -X PUT -H 'Content-Type: application/json'  http://localhost:8083/connectors/OracleCDCMask/config -d '{      
     			"connector.class": "io.confluent.connect.oracle.cdc.OracleCdcSourceConnector",
     			"name": "OracleCDCMask",
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
          "topic.creation.default.cleanup.policy": "delete",
          "transforms": "EMAIL",
          "transforms.EMAIL.type": "org.apache.kafka.connect.transforms.MaskField$Value",
          "transforms.EMAIL.fields": "EMAIL",
          "transforms.EMAIL.replacement": "******"
  }'
```
got to ksqldb:
```bash
docker exec -it workshop-ksqldb-cli ksql http://ksqldb-server:8088
```
and check if the masking is applied to the column:
```bash
print 'ORCLCDB.C__MYUSER.CUSTOMERS' from beginning limit 1;
```

and also check the table created (the joins will need to be recreated.)

```bash
ksql> set 'auto.offset.reset'='earliest';
select * from CUSTOMERS_CDC_TABLE emit changes;
```

End

[go back to Agenda](https://github.com/jr-marquez/Workshop_Confluent/blob/main/README.md#confluent-hands-on-workshop)