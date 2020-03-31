

## 集群管理

### 创建集群

1\. 点击控制台左上角“全部产品”选择“Kafka消息队列 UKafka”,也可锁定到左侧菜单栏。

2\. 进入UKafka操作界面，点击“创建集群”进行UKafka集群创建。

3\. 选择相应Kafka版本和业务需求量，以及节点机型与数量，集群最小规模至少3个节点。选择完毕后，进入集群设置页面，可对集群参数进行配置。

集群可选机型可参考：[机型列表](ukafka/price)

集群可配置参数见下表（集群已有默认配置，以下配置请您在确认实际需求后再更改）：

| 参数名称                            | 参数类型   | 参数范围           | 释义             |
| ------------------------------- | ------ | -------------- | -------------- |
| auto.create.topics.enable       | bool   | true/false     | 是否允许自动创建Topic  |
| auto.leader.rebalance.enable    | bool   | true/false     | 是否开启节点间数据均衡    |
| default.replication.factor      | int    | 1-10           | 默认复制因子数量       |
| delete.topic.enable             | bool   | true/false     | 是否允许删除topic信息  |
| log.cleaner.delete.retention.ms | int    | 1-2147483646   | 压缩日志最长保留时间     |
| log.cleaner.enable              | bool   | true/false     | 是否开启压缩日志清理     |
| log.cleanup.policy              | string | delete/compact | 日志清理策略，删除/压缩   |
| log.retention.hours             | int    | 1-10000        | 日志最小保留时间       |
| log.segment.bytes               | int    | 1-2147483646   | 单个日志文件最大容量     |
| message.max.bytes               | int    | 1-2147483646   | 单个日志文件最大容量     |
| num.io.threads                  | int    | 1-32           | 处理磁盘IO的线程数     |
| num.network.threads             | int    | 1-32           | 处理网络请求的线程数     |
| num.partitions                  | int    | 1-1000         | 分区数量           |
| replica.fetch.max.bytes         | int    | 1-2147483646   | 副本同步时消息的最大尺寸   |
| zookeeper.session.timeout.ms    | int    | 1-2147483646   | zk session超时时间 |
| zookeeper.connection.timeout.ms | int    | 1-2147483646   | 客户端连接zk超时时间    |
| zookeeper.sync.time.ms          | int    | 1-2147483646   | zk信息同步间隔       |

>
> replica.fetch.max.bytes须大于等于message.max.bytes；
>
> num.io.threads与num.network.threads可控制节点负载与集群性能均衡。

### 调整集群参数 

同创建集群，创建完毕后，也可调整Kafka服务的配置参数。参数内容详见上表。

![](/images/ukafka-02-01.jpg)
