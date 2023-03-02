## Confluent Cloud Unreachable

#### Description
Consumer lag is a tremendous performance indicator. It tells you the offset difference between the producer’s last produced message and the consumer group’s last commit.

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