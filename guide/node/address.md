# 节点地址

> 节点地址为UKafka服务接入地址，为保证高可用，建议客户端至少使用1、2、3主节点地址接入。

## PLAINTEXT协议地址

在节点管理界面，点击【节点地址】，进入节点地址信息页面。

![img](/images/guide/node/address_button.png)

![img](/images/guide/node/address_view.png)

节点地址信息页面展示节点名称、PLAINTEXT协议（默认）。PLAINTEXT协议的接入地址可以在集群同一内网环境下使用。

## SASL_PLAINTEXT协议地址

若您开启了[集群认证配置](/ukafka/guide/cluster/sasl)，【节点地址】信息页面会增加SASL_PLAINTEXT协议字段展示，用于客户端使用SASL协议接入集群，适合转发到外网环境使用。

![img](/images/guide/node/address_sasl.png)
