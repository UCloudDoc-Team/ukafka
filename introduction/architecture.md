# 产品架构

UKafka服务主中主要包含以下组件：

> 1. 消息主题 (Topic)
> 2. 消息生产者 (Producer)
> 3. 消息消费者 (Consumer)
> 4. 消息代理 (Broker)

其结构如图所示：

![image](/images/introduction/arch.png)

| 名词      | 含义       | 说明                                                                                     |
|-----------|------------|------------------------------------------------------------------------------------------|
| Topic     | 消息主题   | 即特定的消息流或类消息队列。其中，消息即为有效字节，而主题即为消息的属类或所标注消息来源 |
| Producer  | 消息生产者 | 即能够发布任何消息的任何服务或系统                                                       |
| Consumer  | 消息消费者 | 对单个或多个消息进行订阅，并从Broker中获取已发布消息数据的消息接收者或处理者             |
| Broker    | 消息节点   | 能够保存消息的一组服务器，即为UKafka集群中的服务节点                                     |
| Partition | 消息分区   | 即为Topic分区，能够将一个消息主题分布在多个Broker中，以实现服务的分布式与高可用          |