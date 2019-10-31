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
Extract the connector folder. In there you can find `lib` directory that contains a `.jar` file. Copy the file in your `plugin.path`. The `.jar` file must be also copied in confluent plugin path. 
```
sudo cp mongodb-kafka-connect-mongodb-0.2/lib/* /usr/share/java
sudo cp mongodb-kafka-connect-mongodb-0.2/lib/* <path-to-confluent>/share/java

```

You can find the path in confluent directory by navigating to `/etc/kafka/connect-standalone.properties`.
You can have more than one paths. Example: 
```
plugin.path=/usr/share/java,/usr/share/java/mongo-kafka-0.2-all.jar
```

Copy the MongoSinkConnector.properties file (located in mongodb-kafka-connect folder) to path-to-confluent/etc/kafka.
```
sudo cp <path-to-mongodb-kafka-connect-mongodb-0.2>/etc/MongoSinkConnector.properties <path-to-confluent-5.3.1>/etc/kafka/
```

Now, modify the MongoSinkConnector.properties 

```ini    
name=mongo-sink
topics=avrotest
connector.class=com.mongodb.kafka.connect.MongoSinkConnector
tasks.max=1

# Message types
key.converter=io.confluent.connect.avro.AvroConverter
key.converter.schema.registry.url=http://localhost:8081
value.converter=io.confluent.connect.avro.AvroConverter
value.converter.schema.registry.url=http://localhost:8081

# Specific global MongoDB Sink Connector configuration
#connection.uri=mongodb://mongo1:27017,mongo2:27017,mongo3:27017
database=admin
collection=avro_data
max.num.retries=3
retries.defer.timeout=5000

## Document manipulation settings
key.projection.type=none
key.projection.list=
value.projection.type=none
value.projection.list=

field.renamer.mapping=[]
field.renamer.regex=[]

document.id.strategy=com.mongodb.kafka.connect.sink.processor.id.strategy.BsonOidStrategy
post.processor.chain=com.mongodb.kafka.connect.sink.processor.DocumentIdAdder

# Write configuration
delete.on.null.values=false
writemodel.strategy=com.mongodb.kafka.connect.sink.writemodel.strategy.ReplaceOneDefaultStrategy

max.batch.size = 0
rate.limiting.timeout=0
rate.limiting.every.n=0

# Change Data Capture handling
change.data.capture.handler=

# Topic override examples for the sourceB topic
topic.override.sourceB.collection=sourceB
topic.override.sourceB.document.id.strategy=com.mongodb.kafka.connect.sink.processor.id.strategy.ProvidedInValueStrategy
```
