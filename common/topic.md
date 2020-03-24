## Topic管理

Topic即为特定的消息流或类消息队列。UKafka为用户提供直接在控制台中对Topic进行创建、配置、重新分区、删除等操作。

### 名词说明

| 名词                   | 解释                                                                                                |
|------------------------|-----------------------------------------------------------------------------------------------------|
| 占用broker数           | Topic各分区占用broker总数                                                                           |
| 落后副本               | 没能同步到leader副本信息的follower副本                                                              |
| 落后副本占比           | 落后副本占该Topic所有副本的比例                                                                     |
| Topic最新落盘时间      | 该Topic最新写入磁盘的时间                                                                           |
| 流入消息量均速(byte/s) | 最近1分钟内流入该Topic的消息速率                                                                    |
| 流出消息量均速(byte/s) | 最近1分钟内流出该Topic的消息速率                                                                    |
| 副本分布               | 副本在broker上分布情况，（3,1,2）代表三副本分布在broker3、broker1、broker2上，leader副本在broker3上 |

### 创建Topic

在集群列表中点击“Topic管理”功能，或在集群管理中，点击“Topic管理”标签。

点击“创建Topic”按钮，并分别录入“Topic名称”，分区数量，副本（数量可参考[faq\#创建Topic时,我应该怎么设置分区Partitions数量与副本replication-factor数量](ukafka/faq#创建Topic时,我应该怎么设置分区Partitions数量与副本replication-factor数量)）进行Topic创建。

![](/images/common/create_topic_2.png)

> 创建时使用默认参数.如需对Topic进行配置，可在创建时点击“参数配置”；或在创建完毕后，更新Topic配置。Topic各项参数无默认，默认采用集群属性。


### 删除Topic

从集群中删除Topic，删除会清除此Topic所有数据，删除前请确认。

![](/images/common/delete_topic_2.png)

### 重新分区

对Topic进行重新分区，能够在集群节点变化时对Topic与节点的关联关系进行重新定义，并对分区数量进行重新设置。

![](/images/common/re_part_2.png)

### 修改配置

修改Topic参数配置，默认采用集群配置，修改完立即生效。

![](/images/common/modify_topic_2.png)

### Topic详情展示

Topic详情页展示该Topic的属性以及消息流入流出动态情况。
![](/images/common/detail_topic_2.png)
