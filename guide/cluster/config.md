# 集群参数配置

集群参数配置修改后必须重启集群才能生效，您可按实际情况选择【立即重启】或【稍后重启】。

> 【立即重启】立即执行集群重启操作，会逐次重启所有节点，重启过程中服务不会中断，建议在业务低峰期操作，重启后新参数立即生效。  
> 【稍后重启】新参数将在下次重启节点服务后生效；如需某些参数立即生效且不重启服务，可在Topic管理页面针对单个Topic修改相关配置。  

- 在UKafka集群列表或详情界面，点击【集群配置】，进入集群参数配置信息页面

![img](/images/guide/cluster/config_button.png)

![img](/images/guide/cluster/config_button_detail.png)

- 按需修改集群参数配置

![img](/images/guide/cluster/config_view.png)

集群可配置参数见下表：

| 参数名称                        | 参数类型 | 参数范围       | 释义                     |
| ------------------------------- | -------- | -------------- | ------------------------ |
| auto.create.topics.enable       | bool     | true/false     | 是否允许自动创建Topic    |
| auto.leader.rebalance.enable    | bool     | true/false     | 是否开启节点间数据均衡   |
| default.replication.factor      | int      | 1-10           | 默认复制因子数量（Topic分区副本数）|
| delete.topic.enable             | bool     | true/false     | 是否允许删除topic信息    |
| log.cleanup.policy              | string   | delete/compact | 日志清理策略，删除/压缩  |
| log.retention.hours             | int      | 1-10000        | 日志最小保留时间         |
| message.max.bytes               | int      | 1-2147483646   | 消息体的最大大小         |
| num.io.threads                  | int      | 1-32           | 处理磁盘IO的线程数       |
| num.network.threads             | int      | 1-32           | 处理网络请求的线程数     |
| num.partitions                  | int      | 1-1000         | 分区数量                 |
| replica.fetch.max.bytes         | int      | 1-2147483646   | 副本同步时消息的最大尺寸 |
<!--
| log.cleaner.delete.retention.ms | int      | 1-2147483646   | 压缩日志最长保留时间     |
| log.cleaner.enable              | bool     | true/false     | 是否开启压缩日志清理     |
| log.segment.bytes               | int      | 1-2147483646   | 每个segment文件的大小    |
| zookeeper.session.timeout.ms    | int      | 1-2147483646   | zk session超时时间       |
| zookeeper.connection.timeout.ms | int      | 1-2147483646   | 客户端连接zk超时时间     |
| zookeeper.sync.time.ms          | int      | 1-2147483646   | zk信息同步间隔           |
-->

> 注意事项：  
> auto.create.topics.enable 生产业务建议关闭，以免影响线上环境稳定运行。  
> default.replication.factor 至少为2，否则无法保证服务高可用性。  
> replica.fetch.max.bytes 须大于等于 message.max.bytes。  
> num.io.threads 与 num.network.threads 可控制节点负载与集群性能均衡。

- 修改完参数后，点击【确定】，会进入重启策略页面。按需选择【立即重启】或【稍后重启】

![img](/images/guide/cluster/config_policy.png)
