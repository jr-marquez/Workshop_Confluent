## Confluent Cloud Producer

#### Description
We will create a Confluent Producer using the Confluent console for Confluent Cloud (the same **confluent** cli is universal for both onpremise and cloud) and produce messages, then we are going to use the consumer to consume messages through the cli.

#### Steps to create a Producer (for the first time)

Lets list the available environments

```bash
confluent environment list
```
then we are going to specify the newly created environment (copy the id of the env)

```bash
confluent environment use <id>
```
Lets list the available kafka clusters for this environment
```bash
confluent kafka cluster list
```
Specify the newly create kafka cluster (copy the id of the cluster)

```bash
confluent kafka cluster use <id>
```
List again the cluster and check if it includes a *

Now lets create a topic **test**

```bash
confluent kafka topic create test
```
We will need an API Key to be able to send messages
```bash
confluent api-key create --resource <cluster ID>
```
Now lets Produce some messages. Write something and press *enter* (as many times as you wish) then press ctrl+c to finish

```bash
confluent kafka topic produce test --api-key <API key> --api-secret <API secret>
```

Now lets Read the messages created. Then press ctrl+c to stop

```bash
confluent kafka topic consume -b test --api-key <API key> --api-secret <API secret>
```

End of lab

[go back to Agenda](https://github.com/jr-marquez/Workshop_Confluent/blob/main/README.md#confluent-hands-on-workshop)