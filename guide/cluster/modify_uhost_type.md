# 集群规格升级

UKafka集群支持在线纵向升级集群规格，包括节点CPU、内存，为集群提供一键升配能力，友好的支持客户业务增长场景。

> 1. 集群规格升级期间将逐次重启所有节点，升级过程中服务不会中断，建议在业务低峰期操作。
> 2. 集群规格暂不支持降级。

- 在集群列表或详情界面，点击【编辑节点】，进入编辑节点信息页面

![img](/images/guide/cluster/modify_node_button.png)

![img](/images/guide/cluster/modify_node_button_detail.png)

编辑节点页面展示当前节点信息、编辑节点以及付费信息。

- 执行集群规格升级

![img](/images/guide/cluster/modify_node_execute.png)

编辑节点模块修改类型选择【升级规格】、节点机型按需选择，点击【确定】完成集群规格升级。

## 当前节点信息模块

![img](/images/guide/cluster/modify_node_nodeinfo.png)

当前节点信息模块包括集群名称、资源ID、节点个数、磁盘信息。

## 编辑节点模块

![img](/images/guide/cluster/modify_node_select.png)

编辑节点模块包括包括修改类型、节点机型。

## 付费信息

![img](/images/guide/cluster/modify_node_charge.png)

付费信息模块包括付费方式、到期时间、应补差价。
