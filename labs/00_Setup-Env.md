# Set up the environment for KSQLDB Hands-on Workshop

Check if there are containers running **ONLY IF YOU DID PREVIOUSLY THE MONITORING WORKSHOP**
```bash
docker ps
```
If yes , please stop them with the following command:
```bash
docker stop $(docker ps -a -q)
```

We will switch to the ksqldb folder:

```bash
cd /home/ec2-user/ksqldbWorkshop/docker
```
and run :
```bash
docker-compose up -d
```


# Check Confluent Control Center
Open URL in Browser
* from your local machine connecting to cloud http://publicip:9021/

You should see in Control Center
* a running cluster
* three connectors are running
* 1 KSQL APP is running
* three topics AML_Status, Funds_Status, Payment_Instruction are created and some internal topics.

[go back to Agenda](https://github.com/jr-marquez/Workshop_Confluent/blob/main/README.md#confluent-hands-on-workshop)
