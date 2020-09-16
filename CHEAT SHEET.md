#check any running processes

#for zookeeper
ps -aux|grep zookeeper

#for kafka
ps -aux|grep kafka

#start zookeeper
aab@aab-ubuntu:/opt/kafka$ sudo systemctl start zookeeper

#zookeeper status
aab@aab-ubuntu:/opt/kafka$ sudo systemctl status zookeeper
● zookeeper.service - Apache Zookeeper service
     Loaded: loaded (/etc/systemd/system/zookeeper.service; disabled; vendor preset: enabled)
     Active: active (running) since Wed 2020-09-16 11:31:39 CDT; 6s ago
       Docs: http://zookeeper.apache.org
   Main PID: 142846 (java)
      Tasks: 42 (limit: 18804)
     Memory: 58.6M
     CGroup: /system.slice/zookeeper.service
             └─142846 /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java -Xmx512M -Xms512M -server -XX:+UseG1GC -XX:MaxGCPauseMillis=20 -XX:InitiatingHeapOccupancyPercent=35 -XX:+ExplicitGCInvokesConcurrent>

#start kafka
aab@aab-ubuntu:/opt/kafka$ sudo systemctl start kafka

#kafka status
aab@aab-ubuntu:/opt/kafka$ sudo systemctl status kafka
● kafka.service - Apache Kafka Service
     Loaded: loaded (/etc/systemd/system/kafka.service; disabled; vendor preset: enabled)
     Active: active (running) since Wed 2020-09-16 11:32:04 CDT; 5s ago
       Docs: http://kafka.apache.org/documentation.html
   Main PID: 143266 (java)
      Tasks: 81 (limit: 18804)
     Memory: 368.5M
     CGroup: /system.slice/kafka.service
             └─143266 /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java -Xmx1G -Xms1G -server -XX:+UseG1GC -XX:MaxGCPauseMillis=20 -XX:InitiatingHeapOccupancyPercent=35 -XX:+ExplicitGCInvokesConcurrent -Dj>

#CREATE TOPIC 
$ sudo bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic first_topic

#DISPLAY TOPIC INFORMATION
aab@aab-ubuntu:/opt/kafka$ ./bin/kafka-topics.sh --describe --zookeeper localhost:2181 --topic first_topic
Topic: first_topic	PartitionCount: 1	ReplicationFactor: 1	Configs: 
	Topic: first_topic	Partition: 0	Leader: 0	Replicas: 0	Isr: 0

$ kafka-topics.sh --describe --zookeeper localhost:2181 --topic <topic name>
Topic:beacon	PartitionCount:6	ReplicationFactor:1	Configs:
	Topic: beacon	Partition: 0	Leader: 1	Replicas: 1	Isr: 1
	Topic: beacon	Partition: 1	Leader: 1	Replicas: 1	Isr: 1

#Add Partitions to a Topic
aab@aab-ubuntu:/opt/kafka$ ./bin/kafka-topics.sh --zookeeper localhost:2181 --describe --topic first_topic
Topic: first_topic	PartitionCount: 1	ReplicationFactor: 1	Configs: 
	Topic: first_topic	Partition: 0	Leader: 0	Replicas: 0	Isr: 0
aab@aab-ubuntu:/opt/kafka$ ./bin/kafka-topics.sh --zookeeper localhost:2181 --alter --topic first_topic --partitions 3
WARNING: If partitions are increased for a topic that has a key, the partition logic or ordering of the messages will be affected
Adding partitions succeeded!
aab@aab-ubuntu:/opt/kafka$ ./bin/kafka-topics.sh --zookeeper localhost:2181 --describe --topic first_topic
Topic: first_topic	PartitionCount: 3	ReplicationFactor: 1	Configs: 
	Topic: first_topic	Partition: 0	Leader: 0	Replicas: 0	Isr: 0
	Topic: first_topic	Partition: 1	Leader: 0	Replicas: 0	Isr: 0
	Topic: first_topic	Partition: 2	Leader: 0	Replicas: 0	Isr: 0

#Change topic retention i.e set SLA
aab@aab-ubuntu:/opt/kafka$ ./bin/kafka-topics.sh --zookeeper localhost:2181 --alter --topic first_topic --config retention.ms=28800000
WARNING: Altering topic configuration from this script has been deprecated and may be removed in future releases.
         Going forward, please use kafka-configs.sh for this functionality
Updated config for topic first_topic.
aab@aab-ubuntu:/opt/kafka$ ./bin/kafka-topics.sh --zookeeper localhost:2181 --describe --topic first_topic
Topic: first_topic	PartitionCount: 3	ReplicationFactor: 1	Configs: retention.ms=28800000
	Topic: first_topic	Partition: 0	Leader: 0	Replicas: 0	Isr: 0
	Topic: first_topic	Partition: 1	Leader: 0	Replicas: 0	Isr: 0
	Topic: first_topic	Partition: 2	Leader: 0	Replicas: 0	Isr: 0

#List all topics
aab@aab-ubuntu:/opt/kafka$ ./bin/kafka-topics.sh --list --zookeeper localhost:2181
DevOps
__consumer_offsets
first_topic
second_topic

#Delete Topic:
aab@aab-ubuntu:/opt/kafka$ ./bin/kafka-topics.sh --list --zookeeper localhost:2181
DevOps
__consumer_offsets
first_topic
second_topic
aab@aab-ubuntu:/opt/kafka$ ./bin/kafka-topics.sh --zookeeper localhost:2181 --delete --topic second_topic
Topic second_topic is marked for deletion.
Note: This will have no impact if delete.topic.enable is not set to true.
aab@aab-ubuntu:/opt/kafka$ ./bin/kafka-topics.sh --list --zookeeper localhost:2181
DevOps
__consumer_offsets
first_topic

#Purge a topic
#Kafka by default keeps the messages for 168 hrs which is 7 days. 
#If you wanted to force kafka to purge the topic, you can do this by multiple ways. Let’s see each in detail.
#If you want to view the configurations for all topic Either you can view these properties log.retention.hours or log.retention.ms in server.properties in kafka config directory.

aab@aab-ubuntu:/opt/kafka$ ./bin/kafka-topics.sh --zookeeper localhost:2181 --create --replication-factor 1 --partitions 1 --topic second_topic
WARNING: Due to limitations in metric names, topics with a period ('.') or underscore ('_') could collide. To avoid issues it is best to use either, but not both.
Created topic second_topic.

aab@aab-ubuntu:/opt/kafka$ ./bin/kafka-topics.sh --zookeeper localhost:2181 --describe --topic second_topic
Topic: second_topic	PartitionCount: 1	ReplicationFactor: 1	Configs: 
	Topic: second_topic	Partition: 0	Leader: 0	Replicas: 0	Isr: 0
  
aab@aab-ubuntu:/opt/kafka$ ./bin/kafka-topics.sh --zookeeper localhost:2181 --alter --topic second_topic --config retention.ms=2000
WARNING: Altering topic configuration from this script has been deprecated and may be removed in future releases.
         Going forward, please use kafka-configs.sh for this functionality
Updated config for topic second_topic.

aab@aab-ubuntu:/opt/kafka$ ./bin/kafka-topics.sh --zookeeper localhost:2181 --describe --topic second_topic
Topic: second_topic	PartitionCount: 1	ReplicationFactor: 1	Configs: retention.ms=2000
	Topic: second_topic	Partition: 0	Leader: 0	Replicas: 0	Isr: 0

aab@aab-ubuntu:/opt/kafka$ ./bin/kafka-topics.sh --zookeeper localhost:2181 --alter --topic second_topic --delete-config retention.ms
WARNING: Altering topic configuration from this script has been deprecated and may be removed in future releases.
         Going forward, please use kafka-configs.sh for this functionality
Updated config for topic second_topic.

aab@aab-ubuntu:/opt/kafka$ ./bin/kafka-topics.sh --zookeeper localhost:2181 --describe --topic second_topic
Topic: second_topic	PartitionCount: 1	ReplicationFactor: 1	Configs: 
	Topic: second_topic	Partition: 0	Leader: 0	Replicas: 0	Isr: 0
  
#Detailed explanation
https://stackoverflow.com/questions/54384014/how-to-purge-or-delete-a-topic-in-kafka-2-1-0-version

#Kafka Consumer Groups
aab@aab-ubuntu:/opt/kafka$ ./bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic second_topic --consumer-property group.id=second-group

^CProcessed a total of 0 messages
aab@aab-ubuntu:/opt/kafka$ ./bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --describe --group second-group

Consumer group 'second-group' has no active members.

GROUP           TOPIC           PARTITION  CURRENT-OFFSET  LOG-END-OFFSET  LAG             CONSUMER-ID     HOST            CLIENT-ID
second-group    second_topic    0          0               0               0               -               -               -


XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX

Get the earliest offset still in a topic
bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list localhost:9092 --topic mytopic --time -2

Get the latest offset still in a topic
bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list localhost:9092 --topic mytopic --time -1


Consume messages with the console consumer
bin/kafka-console-consumer.sh --new-consumer --bootstrap-server localhost:9092 --topic mytopic --from-beginning

Get the consumer offsets for a topic
bin/kafka-consumer-offset-checker.sh --zookeeper=localhost:2181 --topic=mytopic --group=my_consumer_group

Read from __consumer_offsets
Add the following property to config/consumer.properties: exclude.internal.topics=false

bin/kafka-console-consumer.sh --consumer.config config/consumer.properties --from-beginning --topic __consumer_offsets --zookeeper localhost:2181 --formatter "kafka.coordinator.GroupMetadataManager\$OffsetsMessageFormatter"

View the details of a consumer group
bin/kafka-consumer-groups.sh --zookeeper localhost:2181 --describe --group <group name>

kafkacat
Getting the last five message of a topic
kafkacat -C -b localhost:9092 -t mytopic -p 0 -o -5 -e

Zookeeper
Starting the Zookeeper Shell
bin/zookeeper-shell.sh localhost:2181

##Different options :
Option                                   Description                            
------                                   -----------                            
--alter                                  Alter the number of partitions,        
                                           replica assignment, and/or           
                                           configuration for the topic.         
--at-min-isr-partitions                  if set when describing topics, only    
                                           show partitions whose isr count is   
                                           equal to the configured minimum. Not 
                                           supported with the --zookeeper       
                                           option.                              
--bootstrap-server <String: server to    REQUIRED: The Kafka server to connect  
  connect to>                              to. In case of providing this, a     
                                           direct Zookeeper connection won't be 
                                           required.                            
--command-config <String: command        Property file containing configs to be 
  config property file>                    passed to Admin Client. This is used 
                                           only with --bootstrap-server option  
                                           for describing and altering broker   
                                           configs.                             
--config <String: name=value>            A topic configuration override for the 
                                           topic being created or altered.The   
                                           following is a list of valid         
                                           configurations:                      
                                         	cleanup.policy                        
                                         	compression.type                      
                                         	delete.retention.ms                   
                                         	file.delete.delay.ms                  
                                         	flush.messages                        
                                         	flush.ms                              
                                         	follower.replication.throttled.       
                                           replicas                             
                                         	index.interval.bytes                  
                                         	leader.replication.throttled.replicas 
                                         	max.compaction.lag.ms                 
                                         	max.message.bytes                     
                                         	message.downconversion.enable         
                                         	message.format.version                
                                         	message.timestamp.difference.max.ms   
                                         	message.timestamp.type                
                                         	min.cleanable.dirty.ratio             
                                         	min.compaction.lag.ms                 
                                         	min.insync.replicas                   
                                         	preallocate                           
                                         	retention.bytes                       
                                         	retention.ms                          
                                         	segment.bytes                         
                                         	segment.index.bytes                   
                                         	segment.jitter.ms                     
                                         	segment.ms                            
                                         	unclean.leader.election.enable        
                                         See the Kafka documentation for full   
                                           details on the topic configs.It is   
                                           supported only in combination with --
                                           create if --bootstrap-server option  
                                           is used.                             
--create                                 Create a new topic.                    
--delete                                 Delete a topic                         
--delete-config <String: name>           A topic configuration override to be   
                                           removed for an existing topic (see   
                                           the list of configurations under the 
                                           --config option). Not supported with 
                                           the --bootstrap-server option.       
--describe                               List details for the given topics.     
--disable-rack-aware                     Disable rack aware replica assignment  
--exclude-internal                       exclude internal topics when running   
                                           list or describe command. The        
                                           internal topics will be listed by    
                                           default                              
--force                                  Suppress console prompts               
--help                                   Print usage information.               
--if-exists                              if set when altering or deleting or    
                                           describing topics, the action will   
                                           only execute if the topic exists.    
                                           Not supported with the --bootstrap-  
                                           server option.                       
--if-not-exists                          if set when creating topics, the       
                                           action will only execute if the      
                                           topic does not already exist. Not    
                                           supported with the --bootstrap-      
                                           server option.                       
--list                                   List all available topics.             
--partitions <Integer: # of partitions>  The number of partitions for the topic 
                                           being created or altered (WARNING:   
                                           If partitions are increased for a    
                                           topic that has a key, the partition  
                                           logic or ordering of the messages    
                                           will be affected). If not supplied   
                                           for create, defaults to the cluster  
                                           default.                             
--replica-assignment <String:            A list of manual partition-to-broker   
  broker_id_for_part1_replica1 :           assignments for the topic being      
  broker_id_for_part1_replica2 ,           created or altered.                  
  broker_id_for_part2_replica1 :                                                
  broker_id_for_part2_replica2 , ...>                                           
--replication-factor <Integer:           The replication factor for each        
  replication factor>                      partition in the topic being         
                                           created. If not supplied, defaults   
                                           to the cluster default.              
--topic <String: topic>                  The topic to create, alter, describe   
                                           or delete. It also accepts a regular 
                                           expression, except for --create      
                                           option. Put topic name in double     
                                           quotes and use the '\' prefix to     
                                           escape regular expression symbols; e.
                                           g. "test\.topic".                    
--topics-with-overrides                  if set when describing topics, only    
                                           show topics that have overridden     
                                           configs                              
--unavailable-partitions                 if set when describing topics, only    
                                           show partitions whose leader is not  
                                           available                            
--under-min-isr-partitions               if set when describing topics, only    
                                           show partitions whose isr count is   
                                           less than the configured minimum.    
                                           Not supported with the --zookeeper   
                                           option.                              
--under-replicated-partitions            if set when describing topics, only    
                                           show under replicated partitions     
--version                                Display Kafka version.                 
--zookeeper <String: hosts>              DEPRECATED, The connection string for  
                                           the zookeeper connection in the form 
                                           host:port. Multiple hosts can be     
                                           given to allow fail-over. 
