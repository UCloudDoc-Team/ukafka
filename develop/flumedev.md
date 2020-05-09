## 使用Flume+UKafka

### 1. Flume简介

Flume是一个高可用的，高可靠的，分布式的海量日志采集、聚合和传输的系统，Flume支持在日志系统中定制各类数据发送方，用于收集数据；同时，Flume提供对数据进行简单处理，并写到各种数据接受方（可定制）的能力。

![flume-01.png](/images/flume-01.png)

\- Flume event

Flume event是Flume自定义的数据传输格式。

\- Agent

启动的一个Flume进程。

\- Source

Flume Agent的Source用于接收外部数据源发送过来的数据（例如上例中的Web
Server）。需要注意的是，外部数据源发送的数据必须满足Agent
Source定义的数据源格式。比如，对于Avro Source，那么这个Source仅接收Avro格式的数据。外部数据源可以通过Avro
Client的方式给Agent的Source发送数据；Avro Source也可以接收其它Agent的Avro Sink发送的Avro
Event数据。

\- Channel

Agent Channel是Agent
Source接收到数据的一个缓冲。数据在被消费前(写入到Sink)，对读取到的数据进行缓冲。一个Source可以写到多个Channel。

\- Sink

从Challe的缓冲中取出数据，存储到最终流向的地方。

### 2. 下载安装

以flume-1.6.0版本为例：

\- 下载并解压文件

```
wget http://apache.fayea.com/flume/1.6.0/apache-flume-1.6.0-bin.tar.gz
tar xf apache-flume-1.6.0-bin.tar.gz
```

\- 修改配置文件

在apache-flume-1.6.0-bin/conf/flume-env.sh中添加JAVA\_OPTS

```
export JAVA_OPTS="-Xms100m -Xmx1024m -Dcom.sun.management.jmxremote"
```

### 3. 抓取本地日志到UKafka

\- 修改配置文件

在cong/flume-conf.properties.sink.kafka中添加以下配置：

```
# 自定义一个source channel和sink
agent.sources = seqGenSrc
agent.channels = memoryChannel
agent.sinks = kafkaSink

# source的来源通过unix命令方式获取
# 参考 https://flume.apache.org/FlumeUserGuide.html#exec-source
agent.sources.seqGenSrc.type = exec
agent.sources.seqGenSrc.command = tail -f /tmp/access.log

# 为source绑定一个channel
agent.sources.seqGenSrc.channels = memoryChannel

# sink source的内容到kafka
# 详细内容请参考 https://flume.apache.org/FlumeUserGuide.html#kafka-sink
agent.sinks.kafkaSink.type = org.apache.flume.sink.kafka.KafkaSink
agent.sinks.kafkaSink.topic = flume_kafka_sink

# kafka brokers地址
agent.sinks.kafkaSink.brokerList = ip1:9092,ip2:9092,ip3:9092
agent.sinks.kafkaSink.batchSize = 20
agent.sinks.kafkaSink.partition.key=region agent.sinks.kafkaSink.partitioner.class=org.apache.flume.plugins.SinglePartition

# 为sink绑定一个 channel.
agent.sinks.kafkaSink.channel = memoryChannel

# channel的配置，将Source接收到数据的一个缓冲到内存中
# 详细说明请参考 https://flume.apache.org/FlumeUserGuide.html#memory-channel
agent.channels.memoryChannel.type = memory
agent.channels.memoryChannel.capacity = 10000
agent.channels.memoryChannel.transactionCapacity = 1500
```

> 注解：需要将ip换成自己kafka集群ip

\- 启动命令

```
./bin/flume-ng agent -n agent -c conf -f conf/flume-conf.properties.sink.kafka
```

\- 查看结果

在kafka集群上执行：

```
kafka-console-consumer.sh --zookeeper ip:2181 --topic flume_kafka_sink
```

> 注解：需要将ip换成自己kafka集群节点ip

可以看到上传的日志内容。
