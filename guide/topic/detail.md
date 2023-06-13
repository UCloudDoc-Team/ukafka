# Topic详情

在Topic管理界面，点击【详情】，将进入Topic详情页面。

![img](/images/guide/topic/detail_button.png)

节点详情页面展示Topic属性、Topic消息动态、分区情况。

## Topic属性模块

![img](/images/guide/topic/detail_baseconfig.png)

Topic属性模块包括Topic数据量、副本数、分区数、分区偏移量总和、落后副本占比(%)、优先副本占比(%)、Broker倾斜率(%)、Broker使用率(%)等信息。

## Topic消息动态模块

![img](/images/guide/topic/detail_message.png)

Topic消息动态模块包括topic最新落盘时间、流入消息量均速(byte/s)、流出消息量均速(byte/s)等信息。

> 1. Topic最新落盘时间：该Topic最新写入磁盘的时间
> 2. 流入消息量均速(byte/s)：最近1分钟内流入该Topic的消息速率
> 3. 流出消息量均速(byte/s)：最近1分钟内流出该Topic的消息速率

## 分区情况模块

![img](/images/guide/topic/detail_partition.png)

分区情况模块包括分区序号、分区数据量、副本分布、落后副本、Leader等信息。

> 1. 副本分布：副本在broker上分布情况，（3,1,2）代表三副本分布在broker3、broker1、broker2上
> 2. 落后副本：没能同步到leader副本信息的follower副本
> 3. Leader：leader副本所在的broker ID
