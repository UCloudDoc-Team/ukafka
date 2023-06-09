# Topic参数配置

在Topic管理界面，点击【更新配置】。

![img](/images/guide/topic/config_button_one.png)

或者选择多个Topic，批量更新配置。

![img](/images/guide/topic/config_button_multi.png)

> Topic参数修改完立即生效，无需重启集群。

## Topic参数说明

| 参数名称     | 参数类型 | 参数范围       | 释义        |
| ----------- | -------- | -------------- | ----------- |
| cleanup.policy       | string  | delete/compact    | 日志清理策略    |
| max.message.bytes    | int  | 1-2147483647    | 单条消息最大大小   |
| min.insync.replicas  | int  | 1-100    | producer设置request.required.acks=-1时有效，最少同步副本数。 |
| retention.bytes     | long  | -1-2^64-1 | 日志保留的最大大小   |
| retention.ms        | long | -1-2^64-1 | 日志保留时长（毫秒） |
