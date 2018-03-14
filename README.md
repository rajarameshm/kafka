# kafka
kafka spark

Source 		---	Tool	---	Storage	

MYSQL 		-->	Sqoop 	--> 	Hadoop
LOGS		--> 	Flume	--> 	Hadoop
SENSOR		--> 	FuseESB	--> 	Hadoop
CLICKSTREAM	--> 	KAFKA	--> 	Hadoop

Sqoop, Flume, FuseESB, KAFKA all the tools to load the data to Hadoop.

Source 		-->	Channel	--> 	Sink
HYD		-->	DUBAI	--> 	NY

For example Agent is Emirates Airlines

Kafka Concepts:

Sender 		--> Message 	--> 	multple receivers


Kafka Manager by Yahoo can be used to do admin part of Kafka.

https://www.github.com/yahoo/kafka-manager

Producer will create a topic along with partition and replication.

data is sent to the topic in sequentially

one consumer from a consumer-group can read from a partition

Producer record will have 4 values:
1. Topic Name
2. Partition	--optional
3. Key		--optional
4. Value

If the key is specified it is Hash code partitioning else it is round robin partitioning.


Producer Record --> Topic --> send() --> key serializer, value serializer --> paritioner

if the send() is 'sucess', it will return record-metadata-object with information: topic, partition and offset; the same information will be send to zoo-keeper.

Producer Configuration:

three mandatory
1. bootstrap.servers
2. host
3. port


serializers
1. byte
2. string
3. integer

methods to send
1. simple send
2. synchronous send
3. asynchronous send

significant impact on the performance
1. acks
2. buffer.memory
3. retries
4. batch.size


Concept of in-sync replica (ISR)

acks settings

	0		1		all or -1
high throuput	
low latency


Repartition strategy will be done by master of the kafka group coordinator
1. range strategy
2. round robin

the first consumer who sending the join group request will be the leader


Kafka creates topic called -Consumer_offset which maintains data like: Group, Topic, Partition, offset

parition rebalance
1. when a new consumer added
2. when an existing consumer dropped


for each of the partition a unique commit log is generated

important configuration :
log.retention.ms
log.segments.bytes (default to 1gb)
log.segments.ms
message.max.bytes (default 1 mb)


capacity planning:

=100 X num-of-brokers X replication-factor


FLUME:
~~~~~

Kafka Broker 	-->	Flafka (Consumer)	--> HDFS/No-SQL/JMS
			(data extraction)	    (data loading)

Flume can act as a both a consumer and producer for Kafka.

SPARK:
~~~~~

Arrays 	--> RDD Resilient Distributed Dataset
Tables 	--> Data Frames
	--> Transformations and Actions



SPARK-STREAMING:
~~~~~~~~~~~~~~~

1. receiver based approach.
write-ahead-log

2. direct based approach.
there wont be any write-ahead-log in this approach as it will rely on kafka-broker to update the committed offset.





Explore:
picPICK - design tool
Abinitio/Pentaho/Talend/Informatica
NiFi
E [Sqoop/Flume/Kafka/NiFi/RestAPI/Put]
T [Spark/MapR/]
L [Hive/NoSQL]
Data Stax --> Spark



Commands:
~~~~~~~~
bin/zkServer.sh start

bin/kafka-topics.sh --list --zookeeper localhost:2181
bin/kafka-topics --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic weblogs
bin/kafka-topics.sh --describe --zookeeper localhost:2181 --topic weblogs

bin/kafka-topics.sh --alter --zookeeper localhost:2181 --partitions 11 --topic hello-topic

kafka-console-producer --broker-list localhost:9092 --topic weblogs
kafka-console-consumer --zookeeper localhost:2181 --topic weblogs --from-beginning

bin/kafka-topics.sh --zookeeper localhost:2181 --alter --topic hello-topic --config max.message.bytes=128000
bin/kafka-topics.sh --zookeeper localhost:2181 --alter --topic hello-topic --delete-config max.message.bytes
bin/kafka-topics.sh --delete --zookeeper localhost:2181 --topic hello-topic

bin/kafka-topics --create --zookeeper localhost:2181 --replication-factor 2 --partitions 3 --topic HelloKafkaTopic
bin/kafka-topics.sh --describe --zookeeper localhost:2181 --topic HelloKafkaTopic
bin/kafka-topics.sh --delete --zookeeper localhost:2181 --topic HelloKafkaTopic

bin/kafka-topics --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic UserMessageTopic10





hdfs dfs -mkdir -p /loudacre/weblogs_flume

in case of exception: Cannot create directory in hdfs NameNode is in safe mode

sudo -u hdfs hdfs dfsadmin -safemode leave

sudo mkdir -p /flume/weblogs_spooldir

sudo chmod a+w -R /flume

cd /home/cloudera/training/devsh/exercises/flume

flume-ng agent --conf /etc/flume-ng/conf --conf-file spooldir.conf --name agent1 -Dflume.root.logger=INFO,console

/home/cloudera/training/devsh/exercises/flume/copy-move-weblogs.sh /flume/weblogs_spooldir

hdfs dfs -ls /loudacre/weblogs_flume | head -20







cd /home/cloudera/training/devsh/exercises/flafka
flume-ng agent --conf /etc/flume-ng/conf --conf-file spooldir_kafka.conf --name agent1 -Dflume.root.logger=INFO,console
kafka-console-consumer --zookeeper localhost:2181 --topic weblogs
cd /home/cloudera/training/devsh/exercises/flafka
./copy-move-weblogs.sh /flume/weblogs_spooldir





cd /home/cloudera/training/devsh/exercises/spark-streaming
python streamtest.py localhost 1234 20 /home/cloudera/training/data/weblogs/*


cd /home/cloudera/training/devsh/exercises/spark-streaming
cd /home/cloudera/training/devsh/exercises/spark-streaming/streaminglogs_project
mvn package
spark-submit --master 'local[2]' --class solution.StreamingLogs target/streamlog-1.0.jar localhost 1234



cd /home/cloudera/training/confluent-oss-4.0.0-2.11.tar/confluent-4.0.0
bin/kafka-topics --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic my-connect-test


bin/kafka-console-consumer --bootstrap-server localhost:9092 --topic my-connect-test --from-beginning

References:
~~~~~~~~~~
http://blog.cloudera.com/blog/2014/11/flafka-apache-flume-meets-apache-kafka-for-event-processing/

Splunk Add-on for Kafka
http://docs.splunk.com/Documentation/AddOns/latest/Kafka/About


cloudera-quickstart-vm-5.12.0-0-vmware
VMware-player-12.1.1-3770994
https://www.cloudera.com/downloads/quickstart_vms.html
