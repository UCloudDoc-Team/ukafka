## FAQs

### 创建 Topic 时，我应该怎么设置分区 Partitions 数量与副本 replication-factor 数量？

副本数量一般建议为 3。Partitions 数量可根据集群所有节点磁盘块总数(如普通实例每个节点只挂载一块磁盘，而均衡I/O型根据配置不同挂载2-8块磁盘不等)和客户端需要的并发量设置，一般建议不要设置过大，影响集群性能。

### 一个UKafka集群可以创建多少个Topic？

集群可以支持任意数量的Topic，建议使用较大规模Topic代替大量较小规模的Topic，例如：需要将消息按天区分，应该在每条消息上加上时间戳，而不是每天自动创建一个新的Topic。大量的Topic和分区会严重影响集群性能。

### 如何增加Topic的副本数量(ReplicationFactor)？

参考[官方文档](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor)

> 在增加topic的副本时，由于会复制数据，建议节点数据盘保留有足够的空间。

### 节点kafka-logs目录下consumer_offset相关目录占用大量磁盘空间不释放，怎么处理 ？

`__consumer_offset` 是 Kafka 用来存放客户端消费的 offset 信息的 Topic，默认采用压缩策略。

修改 `log.cleaner.enable` 参数为 `ture`，然后按顺序重启每个节点kafka服务。

### 收到离线分区总数\>=10.0个告警，离线分区总数是什么，怎么处理，怎样避免影响服务？

分区是Kafka中Topic的一个物理概念上的消息分块，以实现服务的分布式与高可用。（Kafka介绍可参考[intro](/analysis/ukafka/intro)）

收到离线分区总数异常告警一般是某个节点宕机或者服务异常导致。可通过UKafka
console“节点管理”页面，依次查看每个节点“关联的Topic”信息，若为空，说明此节点异常，进而

可在“节点管理”中查看server.log，观察是否有异常日志。若kafka服务卡住，可在评估后在控制台重启该节点kafka服务。

排查过程中，每个Topic的复制因子replication-factor尽量大于等于3，以避免单机故障带来的业务不可用

### 怎样消费超过单条1MB的消息？

集群默认配置message.max.bytes为1MB，若需支持更大的消息，可通过集群参数配置管理修改message.max.bytes，replica.fetch.max.bytes，consumer端则需修改fetch.message.max.bytes。

### Kafka Manager怎么访问？

每个集群的第一个节点默认已开启Kafka
Manager服务，可通过IP:9000访问,由于未提供外网IP绑定，需通过有外网访问权限的UHost转发或者建立隧道，并注意此UHost所绑定外网防火墙需开放9000端口。

### 外网怎么访问UKafka集群？

暂不提供外网访问集群功能。

### 集群单个节点配置不够，需要怎么升级？

默认不支持单个节点纵向升级，如果需要扩充资源，可以横向添加节点;如果遇到内存或其它单个节点的资源瓶颈，可以联系我们提供后台升级。

### 怎么查看UKafka集群的监控数据？

集群监控视图页面提供集群流入、流出数据量、消息个数监控，Kafka生产者、消费者监控数据，以及Zookeeper相关监控数据，并提供每个Broker上的CPU、MEM、磁盘、网卡，Kafka服务，以及Zookeeper监控数据；
可通过Kafka Manager页面查看每个Topic、Broker的实时流量。

### 发现zookeeper最大延时很高，是否有问题？

zookeeper最大延时（zk\_max\_latency）是表示集群创建以来出现过的请求延时最大值，无法代表当前状态。若想了解当前zookeeper请求延时情况，建议关注平均请求延时监控项。

### 获取消费者详情错误

目前控制台消费者信息是根据消费者类型分别通过访问 zookeeper 或者调用 kafka api 得到的，但是 kafka 客户端 sdk 可以灵活的决定对消费者信息的存储方式，所以在使用没有以标准方式存储信息的 sdk 时，消费者信息可能会获取错误。对于这些消费者，我们目前没有去单独适配，已知会出现问题的 sdk 有：

1. [pykafka](https://github.com/Parsely/pykafka)
    * https://github.com/Parsely/pykafka/issues/888
    * https://github.com/Parsely/pykafka/issues/567
2. jstorm：不会按标准的方式存储消费者组信息，是由自己管理消费实例与 topic partition 的对应关系以及对应的 offset，部分信息存储在 zookeeper 的 `/jstorm` 路径下
3. flink 0.9 版本的 kafka 消费信息由自己管理，不会在 kafka 这边注册生成 group 信息

在遇到获取信息错误时，可以先使用 `kafka-consumer-groups.sh --bootstrap-server $(hostname):9092  --describe --group $group` 命令确认消费者是否有信息缺失或者错误。

### 重新分区

扩容节点后，在原来机器上的 topic partition 不会自动均衡到新的机器，需要使用分区重新分配工具来均衡

[重分区功能官方描述](http://kafka.apache.org/documentation/#basic_ops_cluster_expansion)

控制台提供的功能是[Automatically migrating data to new machines](http://kafka.apache.org/documentation/#basic_ops_automigrate)的部分

