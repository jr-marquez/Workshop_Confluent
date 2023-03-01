# Confluent Hands-on Workshop
This github project describes a Hands-on Workshop around Confluent and Confluent Cloud. The structure of the Hands-on is as followed
  * Explaining and Introduce 
  * Labs: Get to know the environment and exercise 

### Monitoring/Observability Hands-On:

1. We will start first with an environment check:
    * [Setup the environment](labs/01_Setup-Env.md)
2. Scenarios Related to the **Producer**
    * [Confluent Cloud unreachable](labs/01_ProducerCCunreachable.md)
    * [Authorization Revoked](labs/01_ProducerAuthorizationRevoked.md)
3. Scenarios Related to the **Consumer**
    * [Increasing consumer lag](labs/01_ConsumerLag.md)

### Kafka Connect Hands-On:

1. Kafka Connect Plugins:
    * [Oracle - Read Data using CDC](labs/03_usecase_oracleCDC.md)
2. Simple Message Transform (SMT):
    * [Masking](labs/01_Setup-Env.md)


### Stream Governance:

1. Initial Setup of your Confluent Cloud environment:
    * [Setup the environment](labs/02_Setup-Env.md)
2. Use of Schema Registry to have **trusted** data:
    * [Schema Registry lab](labs/02_SR_lab.md)
3. Use of Stream Catalog to have **discoverable** data:
    * [Stream Catalog lab](labs/02_SC_lab.md)
4. Use of Stream Lineage to have **observable** data:
    * [Stream Lineage lab](labs/02_SL_lab.md)
5. (Optional) Use Advanced Governance:
    * [Advanced Governance lab](labs/02_AG_lab.md)


### ksqlDB Hands-On:

1. We will start first with an environment check:
    * [Setup the environment](labs/00_Setup-Env.md)
2. Labs Financial service
     * [Payment Status Check](labs/02_usecase_finserv_1.md)
    * [Stock price calculation with User defined functions](labs/02_usecase_finserv_2.md)
    * [Create personalized banking promotions](labs/02_usecase_finserv_3.md)


Thanks a lot for attending
Confluent Team