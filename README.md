# KafkaToMongoDB
Please follow the links we provide for more details and not to miss the latest versions. 

**1. Install Confluent** (https://docs.confluent.io/current/installation/installing_cp/zip-tar.html)

Download tar.gz file using `curl` and then extract it  
```
curl -O http://packages.confluent.io/archive/5.3/confluent-5.3.1-2.12.tar.gz
tar xzf confluent-5.3.1-2.12.tar.gz
```

**2. Configurations**

- Zookeper  
Inside conflunet navigate to `/etc/kafka/zookeeper.properties` and modify it as shown
```
tickTime=2000
dataDir=/var/lib/zookeeper/
clientPort=2181
```
Note: This configurations are for standalone mode.

- Kafka  
Navigate to `/etc/kafka/server.properties` and modify it as shown
```
zookeeper.connect=zookeeper:2181
listeners=PLAINTEXT://:9092
# Uncomment the lines below
metric.reporters=io.confluent.metrics.reporter.ConfluentMetricsReporter
confluent.metrics.reporter.bootstrap.servers=localhost:9092
confluent.metrics.reporter.topic.replicas=1
```
