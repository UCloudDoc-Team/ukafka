# 集群磁盘扩容

- 在集群列表或详情界面，点击【编辑节点】，进入编辑节点信息页面

![img](/images/guide/cluster/modify_node_button.png)

![img](/images/guide/cluster/modify_node_button_detail.png)

编辑节点页面展示当前节点信息、编辑节点以及付费信息。

- 执行集群磁盘扩容

![img](/images/guide/cluster/modify_node_resize_disk.png)

编辑节点模块修改类型选择【修改磁盘大小】、磁盘大小按需选择，点击【确定】完成集群磁盘扩容。

> 1. 集群磁盘扩容期间对集群内所有节点逐个进行操作，磁盘为在线扩容，不影响业务。
> 2. 磁盘大小为单节点数据磁盘量，以三副本为例，如您购买 300G 磁盘，实际存储业务的磁盘大小为 100G，其余 200G 为备份容量。

## 当前节点信息模块

![img](/images/guide/cluster/modify_node_nodeinfo.png)

当前节点信息模块包括集群名称、资源ID、节点个数、磁盘信息。

## 编辑节点模块

![img](/images/guide/cluster/modify_node_select.png)

编辑节点模块包括包括修改类型、节点机型。

## 付费信息

![img](/images/guide/cluster/modify_node_charge.png)

付费信息模块包括付费方式、到期时间、应补差价。
