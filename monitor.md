{{indexmenu_n>3}}

# 监控项说明

UKafka 控制台的 \<监控视图\> 提供了集群和节点的多维度监控，以下分模块对监控项做说明。

<table>
<thead>
<tr class="header">
<th>监控指标类别</th>
<th>监控项</th>
<th>说明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>节点指标</td>
<td>CPU使用率（%）</td>
<td>节点的CPU使用率</td>
</tr>
<tr class="even">
<td>:::</td>
<td>内存使用率（%）</td>
<td>节点的内存使用率</td>
</tr>
<tr class="odd">
<td>:::</td>
<td>数据盘使用率（%）</td>
<td>节点数据盘使用率</td>
</tr>
<tr class="even">
<td>:::</td>
<td>系统盘使用率（%）</td>
<td>节点系统盘使用率</td>
</tr>
<tr class="odd">
<td>:::</td>
<td>磁盘读/写吞吐（Kb/s）</td>
<td>磁盘读写吞吐量</td>
</tr>
<tr class="even">
<td>:::</td>
<td>磁盘读/写次数（次/s）</td>
<td>磁盘读写次数</td>
</tr>
<tr class="odd">
<td>:::</td>
<td>网卡出/入带宽（Kb/s）</td>
<td>网卡出入带宽值</td>
</tr>
<tr class="even">
<td>:::</td>
<td>网卡出/入包量（个/s）</td>
<td>网卡出入包量</td>
</tr>
<tr class="odd">
<td>zookeeper指标</td>
<td>watcher数量（个）</td>
<td>watch机制用于数据变更时zookeeper的主动通知。<br />
watch可以被附加到每一个节点上，那么如果一个应用有10W个节点，那zookeeper中就可能有10W个watch（甚至更多）</td>
</tr>
<tr class="even">
<td>:::</td>
<td>znode数量（个）</td>
<td>znode是zookeeper的节点，类似文件系统的目录或者文件</td>
</tr>
<tr class="odd">
<td>kafka指标</td>
<td>每秒流入消息个数（个/s）</td>
<td>所有的topic的消息速率（个/s），取一分钟的平均值</td>
</tr>
<tr class="even">
<td>:::</td>
<td>每秒流入数据（B/s）</td>
<td>所有的topic的流入数据速率（B/s），取一分钟的平均值</td>
</tr>
<tr class="odd">
<td>:::</td>
<td>每秒流出数据（B/s）</td>
<td>所有的topic的流出数据速率（B/s），取一分钟的平均值</td>
</tr>
<tr class="even">
<td>:::</td>
<td>Follower落后Leader最大消息量（个）</td>
<td>follower落后leader replica的最大的消息数量</td>
</tr>
<tr class="odd">
<td>:::</td>
<td>分布在该节点上的分区总数（个）</td>
<td>该节点上分区总数</td>
</tr>
<tr class="even">
<td>:::</td>
<td>分布在该节点上的leader分区总数（个）</td>
<td>该节点上leader分区总数</td>
</tr>
<tr class="odd">
<td>:::</td>
<td>未复制的分区总数（个）</td>
<td>待做复制的分区的数量，正常值为0</td>
</tr>
<tr class="even">
<td>:::</td>
<td>ISR收缩速率（个/s）</td>
<td>ISR的收缩(shrink)速率。<br />
如果一个broker挂掉了，一些partition的ISR会收缩。<br />
当那个broker重新起来时，一旦它的replica完全跟上，ISR会扩大(expand)。<br />
除此之外，正常情况下，此值和下面的扩大速率都是0。</td>
</tr>
<tr class="odd">
<td>:::</td>
<td>ISR扩大速率（个/s）</td>
<td>ISR的扩大(expansion)速率，参见ISR的收缩(shrink)速率</td>
</tr>
<tr class="even">
<td>:::</td>
<td>管理节点个数（个）</td>
<td>当前的broker是否为controller。<br />
在集群中只有一个broker的这个值为1，其他值为0，如果都为0，集群有问题。</td>
</tr>
<tr class="odd">
<td>:::</td>
<td>离线分区总数（个）</td>
<td>离线的partition个数</td>
</tr>
<tr class="even">
<td>:::</td>
<td>消费者失败请求（次/s）</td>
<td>消费者失败的请求个数，取一分钟的平均值</td>
</tr>
<tr class="odd">
<td>:::</td>
<td>生产者失败请求（次/s）</td>
<td>生成者失败请求的个数，取一分钟的平均值</td>
</tr>
<tr class="even">
<td>:::</td>
<td>Broker拒绝的消息（B/s）</td>
<td>Broker拒绝的消息量，取一分钟的平均值</td>
</tr>
<tr class="odd">
<td>:::</td>
<td>生产者请求响应时间（ms）</td>
<td>生产者平均响应时间</td>
</tr>
<tr class="even">
<td>:::</td>
<td>消费者请求响应时间（ms）</td>
<td>消费者平均响应时间</td>
</tr>
<tr class="odd">
<td>:::</td>
<td>生产者QPS（次/s）</td>
<td>生产者QPS，取一分钟的平均值</td>
</tr>
<tr class="even">
<td>:::</td>
<td>消费者QPS（次/s）</td>
<td>消费者QPS，取一分钟的平均值</td>
</tr>
</tbody>
</table>
