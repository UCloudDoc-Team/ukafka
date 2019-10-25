

### 同步UKafka数据到UHadoop

（以下文件目录来历可参考上一小节）

\- 准备jar包

由于UHadoop集群使用hadoop-2.6.0-cdh5.4.9版本。所以，需要将hadoop-hdfs-2.6.0-cdh5.4.9.jar拷贝到上一步中下载的apache/flume/1.6.0-bin/lib目录下。

jar包可以从UHadoop集群的/home/hadoop/lib/lib/hadoop-hdfs-2.6.0-cdh5.4.9.jar拷贝，或者[点此下载](http://uhadoop-new.ufile.ucloud.com.cn/hadoop/hadoop-hdfs-2.6.0-cdh5.4.9.jar)。

> 注解：如果是自建集群，请拷贝相应版本jar包到相同目录。

\- 配置文件

在cong/flume-conf.properties中添加以下配置：

```
agent.sources = seqGenSrc
agent.channels = memoryChannel
agent.sinks = hdfsSink

# source的来源通过kafka获取
# 请参考https://flume.apache.org/FlumeUserGuide.html#kafka-source
agent.sources.seqGenSrc.type = org.apache.flume.source.kafka.KafkaSource

#kafka zookeeper 地址
agent.sources.seqGenSrc.zookeeperConnect = ip1:2181,ip2:2181,ip3:2181
agent.sources.seqGenSrc.topic = flume_kafka_sink
agent.sources.seqGenSrc.groupId = flume
agent.sources.seqGenSrc.interceptors = i1
agent.sources.seqGenSrc.interceptors.i1.type = timestamp
agent.sources.seqGenSrc.kafka.consumer.timeout.ms = 100

# 为soure绑定channel
agent.sources.seqGenSrc.channels = memoryChannel

# sink到hdfs
agent.sinks.hdfsSink.type = hdfs

# sink到hdfs的地址
agent.sinks.hdfsSink.hdfs.path = hdfs://uhadoop-YYYYYY-master1:8020/kafka/%{topi c}/%y-%m-%d
agent.sinks.hdfsSink.hdfs.rollInterval = 0
agent.sinks.hdfsSink.hdfs.rollSize = 134217728
agent.sinks.hdfsSink.hdfs.rollCount = 0
agent.sinks.hdfsSink.hdfs.rollInterval = 0
agent.sinks.hdfsSink.hdfs.minBlockReplicas = 1 agent.sinks.hdfsSink.hdfs.writeFormat = Text
agent.sinks.hdfsSink.hdfs.fileType = DataStream
agent.sinks.hdfsSink.hdfs.batchSize = 1000
agent.sinks.hdfsSink.hdfs.threadsPoolSize = 100

# 指定从哪个channel sink数据
agent.sinks.hdfsSink.channel = memoryChannel

# channel的配置，将Source接收到数据的一个缓冲到内存中。
# 详细说明请参考https://flume.apache.org/FlumeUserGuide.html#memory-channel
agent.channels.memoryChannel.type = memory
agent.channels.memoryChannel.capacity = 10000
agent.channels.memoryChannel.transactionCapacity = 1500
```

>
> 1.需要将ip1、ip2、ip3换成自己kafka集群节点ip，YYYYYY修改为具体的uhadoop集群id;
>
> 2.需要为UKafka集群的节点添加UHadoop集群的host

\- 启动命令

```
./bin/flume-ng agent -n agent -c conf -f conf/flume-conf.properties
```

\- 执行结果

可以在hdfs上看到上传的文件

```
[hadoop@uhadoop-YYYYYY-master1 root]$ hdfs dfs -ls -R /kafka
drwxrwxrwt  -   root    supergroup  0   2016-03-12 18:48    /kafka/flume_kafka_sink
drwxrwxrwt  -   root    supergroup  0   2016-03-12 18:48    /kafka/flume_kafka_sink/16-06-12
-rw-r--r--  3   root    supergroup  6   2016-03-12 18:48    /kafka/flume_kafka_sink/16-06-12/FlumeData.1457779695244.tmp
```
