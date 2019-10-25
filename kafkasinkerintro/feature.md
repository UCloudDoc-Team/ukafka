

## 产品功能

### 名词说明

| 名词         | 说明                                           |
|--------------|------------------------------------------------|
| 上游         | 连接器的上游数据源                             |
| Topic        | UKafka的Topic                                  |
| 下游         | 连接器的下游数据目标地                         |
| 下游标识符   | 下游UHadoop或UES集群的名词                     |
| 文件切分方式 | 数据在HDFS中的切分方式，支持日期和小时         |
| 插件         | 支持自定义插件，以对数据流做转换               |
| HDFS路径     | 下游HDFS的目标文件路径，连接器将传输数据到此处 |
| Index        | ES的Index                                      |
| Index Type   | ES的Index Type                                 |
| 处理单元     | 作为数据流的单位传输机，目前固定为1核2G        |
| 连接器       | kafka连接器的简称                              |

### 上下游支持

目前上下游必须是UCloud的大数据产品，详情如下：

| 上游     |  下游      |
| ---------- | ---------- |
| UKafka      | UHadoop、UES |

### 配置连接器

控制台页面点击【创建连接器】即可开始配置一个新的Kafka连接器。

选择地域以及可用区的时候请注意，Kafka 连接器必须和上下游产品在同一可用区。

上下游信息填写完成后，您可以根据自身需求选择处理单元的数量，处理单元的处理能力可以参考性能测试章节。

需要注意的是，处理单元的上限是上游kafka topic的分区数量，当处理单元数量大于该数值时，额外的处理单元会处于空跑状态。

#### 添加上游

配置上游时，您可以通过下拉菜单选择已有的UKafka集群，选定UKafka集群后，通过Topic下拉菜单选择需要导出的具体数据源。

目前连接器支持Kafka自带的ByteArrayDeserializer（org.apache.kafka.common.serialization.ByteArrayDeserializer）格式，后续会支持其它Kafka自带格式以及自定义格式。

UKafka集群的创建请参考文档：https://doc.ucloud.cn/analysis/ukafka/common/cluster

#### 添加下游

 1. 添加HDFS下游

首先选择下游类型为HDFS，并在下游标识符中选择您的UHadoop集群。

为了方便您的数据管理，Kafka连接器允许动态配置数据的导出目录，按照机器系统时钟（东八区时间）对目录进行分割。

最后，您需要指定数据的最终导出路径，该路径为UHadoop文件系统的绝对路径，不需要包含UHadoop的HDFS文件系统名称。
如果启用了文件分割，Kafka连接器会在改路径下产生对应的时间目录。

当前文件的压缩方式统一为gzip，后续将支持更多压缩方式。

 2. 添加Elasticsearch下游

如果选择下游类型为Elasticsearch，则需要在下游标识符中选择您的UES集群，并填写目标索引的名称和类型。

### 插件管理（灰度） 

自定义插件功能目前已灰度发布，如有需求请联系技术支持开通。

为了更好的支持业务，我们允许您通过编写自定义Java代码解析自定义数据格式，并动态指定UHadoop的存储路径。

插件的接口定义如下：

<file java ThirdpartPlugin.java>
package cn.ucloud.sinker.thirdpart;

import java.io.IOException;

public interface ThirdpartPlugin {

  /**
   * @param body 原始消息
   * @return 转换后的消息
   */
  public byte[] transform(byte[] body) throws IOException;

  /**
   * @param body 原始消息
   * @return 该消息的 hdfs 目录前缀
   */
  public String extractDirName(byte[] body) throws IOException;
}
</file>

请确保该接口的实现类有默认的构造函数，以便我们构造该实现类的实例。

在通过Kafka连接器的上游接收到数据时，我们会对每条从UKafka中接收到的消息调用transform方法对消息进行转换，同时对每条接收到的消息调用extractDirName方法确定数据的存储路径前缀。
如果您不需要自定义存储路径，可以通过返回null来忽略。

在编写完自定义插件逻辑，并打包为jar包后，需要将该jar包上传到您对应地区的ufile存储中。

表单中的插件ufile地址为该插件的ufile下载地址，可以在ufile文件管理界面点击下载按钮获得，入口类为插件接口实现类的引用路径，为full-package-name.ClassName格式。

### 监控说明

通过监控视图标签页，可以看到我们提供的5个监控项。

关于每个监控项的详细描述，如下表所示：

| 监控项     | 描述                                                         |
|------------|--------------------------------------------------------------|
| 读取消息数 | 自上次节点启动以来，由上游读取的消息总个数，每分钟更新一次。 |
| 写出消息数 | 自上次节点启动以来，写入下游目标的消息个数，每分钟更新一次。 |
| 每秒读消息 | 每秒由上游读取的消息个数，每分钟更新一次。                   |
| 每秒写消息 | 每秒写入下游目标的消息个数，每分钟更新一次。                 |
| 消息堆积数 | Kafka中堆积的未导出数据量，每分钟更新一次。                  |
