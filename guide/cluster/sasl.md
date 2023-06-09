# 集群认证配置

UKafka集群认证配置使用Kafka SASL协议实现，认证配置修改后必须重启集群才能生效，您可按实际情况选择【立即重启】或【稍后重启】。

> 【立即重启】立即执行集群重启操作，会逐次重启所有节点，重启过程中服务不会中断，建议在业务低峰期操作，重启后认证配置立即生效。  
> 【稍后重启】认证配置将在下次重启节点服务后生效。  

- 在集群列表界面，点击【认证配置】

![img](/images/guide/cluster/sasl_button.png)

- 进入认证配置信息页面，包括当前认证信息、认证状态

![img](/images/guide/cluster/sasl_view.png)

- 点击任务状态【OFF】开启集群认证配置，填写用户名称、设置密码

![img](/images/guide/cluster/sasl_setting.png)

- 【确定】后，会提示【立即重启】或【稍后重启】，可按需选择

![img](/images/guide/cluster/sasl_policy.png)

开启认证配置后，如您可以使用SASL协议接入集群，SASL协议接入点参考：[SASL_PLAINTEXT协议地址](/ukafka/guide/node/address#SASL_PLAINTEXT协议地址)

> 配置需要等待重启完成生效，SASL_PLAINTEXT协议的接入点可能会有延迟
