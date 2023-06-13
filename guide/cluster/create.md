# 集群创建

首先登录并前往[控制台 UKafka 页面](https://console.ucloud.cn/ukafka/ukafka)，点击【创建集群】，进入UKafka集群配置信息页面。

![img](/images/guide/cluster/create_button.png)

## 选择可用区

![img](/images/guide/cluster/create_button.png)

## 基础配置

选择版本、消息最长保存时间、磁盘管理方式及磁盘使用率阈值

![img](/images/guide/cluster/create_baseconfig.png)

> 1. 版本号与开源版本号对应，如无特殊要求，建议您选择最新版本。
> 2. 消息最长保存时间为 Kafka server 配置 `log.retention.hours`，集群创建后您可在 [参数配置](/ukafka/guide/cluster/config) 修改。
> 3. 磁盘管理方式参考：[磁盘限制配置](/ukafka/guide/cluster/diskmanager)。

## 选择机型

选择磁盘类型、节点机型、磁盘大小、节点数量

![img](/images/guide/cluster/create_type.png)

> 1. 选择节点机型，不同机型 CPU、内存不一致，您可根据业务需求量按需选择。机型列表参考：[机型列表](/ukafka/price/charge)
> 2. 磁盘大小为单节点数据磁盘量。
> 3. 集群最小规模至少3个节点，后续您可根据业务增长情况在控制台扩容节点，参考：[节点扩容](/ukafka/guide/node/expand)。

## 配置网络

选择 VPC、子网

![img](/images/guide/cluster/create_network.png)

## 配置集群名称、业务组

指定集群名称、业务组

![img](/images/guide/cluster/create_moreconfig.png)

## 选择付费方式并支付

系统默认付费方式为时付，时间默认为购买一小时。系统同时还支持月付、年付、以及按时支付三种方式。
以上三种均为预付费，系统会提前扣取下一阶段的费用。点击【立即购买】，完成支付，返回至控制台页面，即可在列表中看到新购买的UKafka集群信息。

![img](/images/guide/cluster/create_bill.png)
