## Confluent Cloud Producer

#### Description
We will create a Confluent Producer using the Confluent console for Confluent Cloud (the same **confluent** cli is universal for both onpremise and cloud)

*What metrics are used?*
- Consumer metrics
- kafka-lag-exporter 
- Metrics API


Lets introduce the failure:

```bash
docker-compose up -d --scale producer=5
```
*What do you expect?*

How would you isolate the problem?
- Where to find relevant symptoms?

Lets go to the slides..

To fix the error: 
```bash
docker-compose up -d --scale producer=1
```

**Tier down the docker machines:**
```bash
docker-compose down -v
```

[go back to Agenda](https://github.com/jr-marquez/Workshop_Confluent/blob/main/README.md#confluent-hands-on-workshop)