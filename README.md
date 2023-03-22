# Confluent Hands-on Workshop
This github project describes a Hands-on Workshop around Confluent and Confluent Cloud. The structure of the Hands-on is as followed
  * Explaining and Introduce 
  * Labs: Get to know the environment and exercise

### Initial Setup Cloud

1. [Setup the environment](labs/000_Setup-Env.md)
2. We will start with some console checks:
    * [Producer/Consumer client](labs/000_ProdConsClient.md)

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
    * [Masking](labs/03_usecase_oracleSMT.md)


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
3. Lab eCommerce and Retail
    * [ETL for realtime inventory](labs/04_usecase_realtime_inventory_ETL.md)
4. Stream Designer 
    * [Quick Start](labs/05_QuickStartSD.md)
    * [Joins](labs/05_JoinSD.md)
    * [Aggregations](labs/05_AggSD.md)

Thanks a lot for attending
Confluent Team