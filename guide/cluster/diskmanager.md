# 磁盘限制配置

磁盘管理方式包括自动清理日志和无操作，清理日志即为当磁盘到达设定的阈值时开始清理日志文件，避免由于磁盘写满导致UKafka服务不可用。

## 清理日志

当集群中某个节点的磁盘使用率达到您设定的阈值时，UKafka会开始自动清理日志。

**清理策略**：根据当前节点上各 topic 实际占用比例与磁盘总容量，按比例计算要清理到磁盘阈值之下时各 topic 的占用，以这个值设置 topic 保留大小。当磁盘使用率降低到阈值之下后，会恢复 topic 的保留大小设置。

每个 topic partition 在磁盘上会对应多个 segment 数据文件，segment 文件是可操作的最小单位，因此清理的最小粒度也是 1G。

当集群磁盘使用达到阈值时，如果 topic partition 的实际占用小于节点磁盘的 2%，为了防止 topic 限制过小导致不可用，清理服务会跳过这类 topic。

当 topic 占用增长过快时，磁盘清理可能会被频繁触发，磁盘清理策略可以预防磁盘写满导致 Kafka 服务不可用，但制定的清理策略只能被动的根据 topic 实际占比来决定，并不是最符合实际业务需求的方案，因此当出现这种情况时，建议您根据自身业务对 topic 保留策略进行调整。

> 如果集群中所有 topic partition 都没有达到清理限制的大小，但是因为 topic partition 总数太多导致磁盘占用率达到阈值，这时日志清理服务将不会进行工作，这种情况必须由用户自己调整 topic 或者扩容集群

## 开启清理日志

集群创建时和集群运行中都可以开启该功能。

- 集群创建时

磁盘管理方式默认为“清理日志”。不建议您选择“无操作”，这样会导致磁盘写满之后kafka服务不可用。

![img](/images/guide/cluster/diskmanager_create_way.png)

磁盘使用率阈值默认为80%，可选范围为70%\~90%。

![img](/images/guide/cluster/diskmanager_create_threshold.png)

- 集群运行中

点击【磁盘限制】，然后可以修改磁盘管理方式和磁盘使用率阈值，调整后立即生效。

![img](/images/guide/cluster/diskmanager_button_list.png)

![img](/images/guide/cluster/diskmanager_button_detail.png)

## 无操作

若选择不由UKafka管理磁盘，十分建议您在监控中开启磁盘使用率告警，在磁盘使用率达到80%时，可在控制台清理各topic的旧日志。

清理方法为，修改 [Topic参数配置](/ukafka/guide/topic/config)，适当调小“日志保存时间”。
