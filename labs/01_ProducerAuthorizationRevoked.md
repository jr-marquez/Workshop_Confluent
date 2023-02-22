## Confluent Cloud Unreachable

#### Description
In this case we will revoke an active producer’s authorization to write to topic 

*What metrics are used?*
- Producer Metrics (MBean kafka.producer:type=producer-metrics,client-id=producer-1)
- Metrics API

Get the service account id from Confluent Cloud: Home --> Accounts & Access : Accounts / Service accounts

Lets revoke the privilage :

first select the environment

```bash
confluent environment list
       ID      |                    Name                      
---------------+----------------------------------------------
  * env-d9g12y | default                                      
    env-3rn7vj | ccloud-stack-sa-j9vnd2-ccloud-observability  
```
now lets use the observability environment

```bash
confluent environment use env-3rn7vj
Now using "env-3rn7vj" as the default (active) environment.
```
check you have a * near your only cluster created for that environment, that means that cluster will be used

```bash
confluent kafka cluster list
       Id      |             Name             | Type  | Provider |  Region   | Availability | Status  
---------------+------------------------------+-------+----------+-----------+--------------+---------
  * lkc-r56dmk | demo-kafka-cluster-sa-j9vnd2 | BASIC | aws      | us-west-2 | single-zone  | UP     
```
Now , lets revoke the privileges

```bash
confluent kafka acl create --service-account sa-123456 --operation write  --topic '*' --deny
```

*What do you expect?*
- Recall producer internals
- Which parts are affected?
- Which metrics provide relevant insights?

How would you isolate the problem?

Lets go to the slides..

To fix the error: 
```bash
confluent kafka acl delete --service-account sa-j9vnd2 --operation write  --topic '*' --deny
```