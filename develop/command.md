# 命令行工具

## 下载 Apache Kafka 二进制包

1. 下载 [kafka_2.13-2.6.1.tgz 二进制包](https://archive.apache.org/dist/kafka/2.6.1/kafka_2.13-2.6.1.tgz)。

2. 解压

    ```shell
    tar -xzvf kafka_2.13-2.6.1.tgz
    ```

3. 进入命令行目录

    ```shell
    cd kafka_2.13-2.6.1/bin
    ```

## 基本操作

> 1. 请先在控制台上创建Topic：`test-topic`，参考：[Topic创建](/ukafka/guide/topic/create)
> 2. 请在实际执行中，将以下地址替换成您UKafka集群的接入地址，参考：[查看接入点](/ukafka/guide/cluster/address)。

- Topic列表

```shell
./kafka-topics.sh --bootstrap-server 10.9.188.159:9092 --list
```

![img](/images/develop/command_topic_list.png)

- 查看Topic详情

```shell
./kafka-topics.sh --bootstrap-server 10.9.188.159:9092 --describe --topic test-topic
```

![img](/images/develop/command_topic_detail.png)

- 发送消息

```shell
./kafka-console-producer.sh --bootstrap-server 10.9.188.159:9092 --topic test-topic
```

![img](/images/develop/command_console_producer.png)

- 接收消息

```shell
./kafka-console-consumer.sh --bootstrap-server 10.9.188.159:9092 --topic test-topic --group test-group --from-beginning
```

![img](/images/develop/command_console_consumer.png)

- 消费组列表

> 接收消息命令不退出，另开终端执行下述命令

```shell
./kafka-consumer-groups.sh --bootstrap-server 10.9.188.159:9092 --list
```

![img](/images/develop/command_group_list.png)

- 消费组详情

```shell
./kafka-consumer-groups.sh --bootstrap-server 10.9.188.159:9092 --describe --group test-group
```

![img](/images/develop/command_group_detail.png)

- 删除消费组

```shell
./kafka-consumer-groups.sh --bootstrap-server 10.9.188.159:9092 --delete --group test-group
```

![img](/images/develop/command_group_delete.png)
