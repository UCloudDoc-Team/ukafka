{{indexmenu_n>5}}

## Consumer Group管理

Consumer Group管理中可以看到当前各消费者组订阅Topic情况，也可以看到在每个Topic、每个分区上的消息堆积条数。

### 名词说明

| 名词              | 解释                      |
| --------------- | ----------------------- |
| Topic::消息堆积数    | 消费者组在该Topic上尚未消费的所有消息条数 |
| Topic最新更新时间     | Topic中消息写入磁盘的最新时间       |
| 分区::消息总数        | 该分区中消息总条数               |
| Consumer offset | 消费者在当前分区上的消费偏移量         |
| 分区::消息堆积数       | 消费者在该分区上尚未消费的所有消息条数     |

### Consumer Group管理概览

可点击Consumer group名称，打开Consumer group详情页。
![](/images/common/consumer_group_list.png)
