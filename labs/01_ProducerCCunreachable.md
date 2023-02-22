## Confluent Cloud Unreachable

#### Description
This first scenario will introduce a blocker from the producer to Confluent Cloud

*What metrics are used?*
- Producer Metrics (MBean kafka.producer:type=producer-metrics,client-id=producer-1)
- Metrics API

Lets introduce the failure:

```bash
docker-compose exec producer iptables -A OUTPUT -p tcp --dport 9092 -j DROP
```
*What do you expect?*
- Recall producer internals
- Which parts are affected?
- Which metrics provide relevant insights?

How would you isolate the problem?

Lets go to the slides..

To fix the error: 
```bash
docker-compose exec producer iptables -D OUTPUT -p tcp --dport 9092 -j DROP

```