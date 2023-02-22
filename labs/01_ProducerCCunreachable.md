## Confluent Cloud Unreachable

This first scenario will introduce a blocker from the producer to Confluent Cloud

==What metrics are used?==
- **Producer Metrics** (MBean kafka.producer:type=producer-metrics,client-id=producer-1)
- **Metrics API**

Lets introduce the failure:

```bash
docker-compose exec producer iptables -A OUTPUT -p tcp --dport 9092 -j DROP
```

Go to :
```bash
cd /home/ec2-user/Workshop_Monitoring_CC-main/ccloud-observability
```
```bash
./start.sh
```
yes to all

After some time you'll see:
```bash
Status: Downloaded newer image for lightbend/kafka-lag-exporter:0.5.5
Creating kafka-lag-exporter              ... done
Creating node-exporter                   ... done
Creating ccloud-observability_producer_1 ... done
Creating grafana                         ... done
Creating ccloud-observability_consumer_1 ... done
Creating prometheus                      ... done

====== Login to Grafana at http://localhost:3000/ un:admin pw:password

====== Query metrics in Prometheus at http://localhost:9090 (verify targets are being scraped at http://localhost:9090/targets/, may take a few minutes to start up)

```
If not please execute the following command:
```bash
./docker-compose up -d
```

continue with slides...