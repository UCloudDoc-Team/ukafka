# 快速开始

本例以UKafka数据同步到UHadoop来做说明。

## 创建连接器

### 1. 点击【创建连接器】按钮，打开配置页面

![img](/images/kafkasinkerintro/create_button.png)

### 2. 配置连接器

基本信息配置按需填写。

【上游】意为Kafka连接器上游的Kafka集群，【下游】意为Kafka连接器将数据传输到哪个目的地。

若【下游】为HDFS，则需配置路径，若为Elasticsearch，则需填写Index信息。

本例中上游为UKafka集群，下游为UHadoop集群，目标HDFS路径为/test/kafkaSinker。

![img](/images/kafkasinkerintro/create_view.png)

连接器创建完成后，可在列表中查看属性概览，也可在详情页中做更多管理。

![img](/images/kafkasinkerintro/连接器列表.png)

![img](/images/kafkasinkerintro/详情页.png)

### 3. 数据验证

- 在UKafka集群创建Topic："for-sinker-test"，参考：[Topic创建](/ukafka/guide/topic/create)

- 写入消息到Topic，参考：[命令行工具](/ukafka/develop/command)

- 登陆UHadoop的master1节点，查看 HDFS 的文件

```sh
hadoop fs -ls /test/kafkaSinker/2019-07-21/
```

![img](/images/kafkasinkerintro/hdfs-fs.png)

- 查看文本内容是否与UKafka中输入的相同：

```sh
hadoop fs -text /test/kafkaSinker/2019-07-21/for-sinker-test-1.1563728241834.gz.tmp
```

![img](/images/kafkasinkerintro/hdfs-text.png)
