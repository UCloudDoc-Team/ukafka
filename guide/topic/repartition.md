# Topic重新分区

对Topic进行重新分区，能够在集群节点变化时对Topic与节点的关联关系进行重新定义，并对分区数量进行重新设置。

在Topic管理界面，点击【重新分区】。

![img](/images/guide/topic/repartition_button_one.png)

或者选择多个Topic，批量重新分区。

![img](/images/guide/topic/repartition_button_multi.png)

> 1. Topic重新分区是异步任务，同时只能有一个任务执行。
> 2. Topic重新分区会同步数据，增加节点负载，建议在业务低峰期操作。
