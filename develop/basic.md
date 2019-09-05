{{indexmenu_n>1}}

## 快速开始

### 1. 集群访问

申请集群后，系统为每个 UKafka 节点自动分配一个内网 IP。kafka 服务监听 9092 端口，zookeeper 服务监听 2181 端口。

以下快速开始测试来自 Apache Kafka 官方[Quickstart](https://kafka.apache.org/quickstart) 。

### 2. 下载 Apache Kafka 代码

1. 登陆可以 ping 通您 UKafka 集群节点的服务器。

2. 下载 [Apache Kafka 项目代码](https://www.apache.org/dyn/closer.cgi?path=/kafka/2.1.0/kafka_2.11-2.1.0.tgz)。本文选择的是 kafka\_2.11-2.1.0.tgz。

3. 解压文件夹：

        $ tar -xzvf kafka_2.11-2.1.0.tgz

4. 进入文件夹：

        $ cd kafka_2.11-2.1.0/bin

### 3. 基本操作

请在实际测试中，将以下 \[broker1 IP\] 替换成您 UKafka 集群中 Broker ID 为 1 的 IP。

- 创建Topic

        $ kafka-topics.sh --zookeeper [broker1 IP]:2181 --create --topic test_ucloud --partitions 3 --replication-factor 3
        Created topic "test_ucloud".

- 查看Topic

        $ kafka-topics.sh --zookeeper [broker1 IP]:2181 --describe --topic test_ucloud
        Topic:test_ucloud   PartitionCout:3     ReplicationFactor:3    Configs:
            Topic: test_ucloud  Partition: 0    Leader: 5   Replicas: 5,1,2 Isr: 5,1,2
            Topic: test_ucloud  Partition: 1    Leader: 1   Replicas: 1,2,3 Isr: 1,2,3
            Topic: test_ucloud  Partition: 2    Leader: 2   Replicas: 2,3,4 Isr: 2,3,4

- 发送消息

        $ kafka-console-producer.sh --broker-list [broker1 IP]:9092 --topic test_ucloud
        [2016-08-11 13:42:31,788] WARN Property topic is not valid(kafka.utils.VerifiableProperties)
        hello ukafka
        hello ukafka

- 接收消息

        $ kafka-console-consumer.sh --bootstrap-server [broker1 IP]:9092 --topic test_ucloud --from-beginning
        hello ukafka
        hello ukafka
        ^CConsumed 2 messages

> 注解：from-beginning参数表示从所存储message的第一行开始消费。

- 删除Topic

        $ kafka-topics.sh --zookeeper [broker1 ip]:2181 --delete --topic test_ucloud
        Topic test_ucloud is marked for deletion
        Note: This will have no impact if delete.topic.enable is not set to true.

> 注解：此命令会标记Topic为删除状态，只有当delete.topic.enable设置为true时才会真正删除数据，默认为true。
