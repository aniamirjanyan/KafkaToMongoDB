# KafkaToMongoDB
Please follow the links we provide for more details and not to miss the latest versions. 

**1. Install MongoDB**

```
$ sudo apt-get update
$ sudo apt install mongodb
$ sudo systemctl start mongodb
```
To enter the mongodb environment type `mongo` in terminal.

**2. Install Confluent** (https://docs.confluent.io/current/installation/installing_cp/zip-tar.html)

Download tar.gz file using `curl` and then extract it  
```
curl -O http://packages.confluent.io/archive/5.3/confluent-5.3.1-2.12.tar.gz
tar xzf confluent-5.3.1-2.12.tar.gz
```

**3. Configurations**

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

- Control Center

Navigate to `/etc/confluent-control-center/control-center-production.properties` and modify it as shown
```
confluent.controlcenter.data.dir=/var/lib/confluent/control-center
zookeeper.connect=zookeeper:2181
bootstrap.servers=kafka:9092
confluent.controlcenter.schema.registry.url=http://schema-registry:8081

# Uncomment the lines below
confluent.controlcenter.connect.cluster=http://connect:8083
confluent.controlcenter.schema.registry.url=http://schema-registry:8081
```

- Schema Registry

Navigate to `/etc/schema-registry/schema-registry.properties` and modify it as shown
```
listeners=http://0.0.0.0:8081
# Uncomment the lines below
kafkastore.bootstrap.servers=PLAINTEXT://localhost:9092
```

**4. Install MongoDB Sink Connector** (https://github.com/mongodb/mongo-kafka/blob/master/docs/install.md)

Go to https://www.confluent.io/hub/mongodb/kafka-connect-mongodb and download the connector. 




