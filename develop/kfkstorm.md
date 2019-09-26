{{indexmenu_n>7}}

### Storm消费kafka消息

- 安装Strom依赖库

下载并解压[Storm发行版本](http://uhadoop-new.ufile.ucloud.com.cn/storm/apache-storm-0.9.2-incubating.tar.gz)

这个版本带有kafka相关操作的完整依赖包。

- 修改storm.yaml配置文件

Storm发行版本的解压目录下有一个conf/storm.yaml文件，用于配置Storm。默认配置可以在[github](https://github.com/nathanmarz/storm/blob/master/conf/defaults.yaml)查看。conf/storm.yaml中的配置选项将覆盖defaults.yaml中的默认配置。以下配置选项是必须在conf/storm.yaml中进行配置的：

**1.storm.zookeeper.servers**

Storm集群使用的Zookeeper集群地址，其格式如下：

storm.zookeeper.servers:

"111.222.333.444"

"555.666.777.888"

如果Zookeeper集群使用的不是默认端口，那么还需要配置storm.zookeeper.port

**2.storm.local.dir**

Nimbus和Supervisor进程用于存储少量状态，如jars、confs等的本地磁盘目录，需要提前创建该目录并给以足够的访问权限。然后在storm.yaml中配置该目录，如：

storm.local.dir: "/data/storm"

**3.nimbus.host**

Storm集群的Nimbus机器地址。各个Supervisor工作节点需要知道哪个机器是Nimbus，以便下载Topologies的jars、confs等文件，如：

nimbus.host: "111.222.333.444"

**4.supervisor.slots.ports**

对于每个Supervisor工作节点，需要配置该工作节点可以运行的worker数量和每个worker使用的端口。默认情况下，每个节点上可运行4个workers，分别在6700、6701、6702、6703端口，如：

supervisor.slots.ports:

6700

6701

6702

6703

- 启动Storm各个后台进程

最后一步，启动Storm的所有后台进程。和Zookeeper一样，Storm也是快速失败(fail-fast)的系统。这样Storm才能在任意时刻被停止，并且当进程重启后被正确地恢复执行。这也是为什么Storm不在进程没保存状态的原因。即使Nimbus或Supervisors被重启，运行中的Topologies不会受到影响。

以下是启动Storm各个后台进程的方式：

**1.Nimbus**

在Storm主控节点上执行

```
bin/storm nimbus > /dev/null 2>&1 &"
```

**2.Supervisor**

在Storm各个工作节点上执行

```
bin/storm supervisor > /dev/null 2>&1 &"
```

**3.UI**

在Storm主控节点上执行

```
bin/storm ui > /dev/null 2>&1 &"
```

启动后，可通过http:\\/\\/{nimbus
host}:8080观察集群的worker资源使用情况、Topologies的运行状态等信息。

> 注解：
>
> 1.Storm后台进程被启动后，将在Storm、安装部署目录下的logs/子目录生成各个进程的日志文件；
>
> 2.Storm UI必须和Storm Nimbus部署在同一台机器上。否则，Storm UI无法正常工作。

至此，Storm集群已经部署、配置完毕。

Storm UI展示界面：

![storm-01.bmp](/images/storm-01.png)

- Java代码

**1.topology.java**

<file java topology.java>
import java.util.HashMap;
import java.util.Map;
import backtype.storm.Config;
import backtype.storm.LocalCluster;
import backtype.storm.StormSubmitter;
import backtype.storm.spout.SchemeAsMultiScheme;
import backtype.storm.topology.TopologyBuilder;
import storm.kafka.BrokerHosts;
import storm.kafka.KafkaSpout;
import storm.kafka.SpoutConfig;
import storm.kafka.ZkHosts;
import storm.kafka.bolt.KafkaBolt;

public class topology {
    public static void main(String [] args) throws Exception{
        //配置 zookeeper 主机:端口号
        BrokerHosts brokerHosts = new ZkHosts("110.64.76.130:2181,110.64.76.131:2181, 110.64.76.132:2181");

        //接收消息队列的主题
        String topic="test";

        //zookeeper 设置文件中的配置，如果 zookeeper 配置文件中设置为主机 名:端口号 ，该项为空
        String zkRoot="";

        //任意
        String spoutId="test_consumer_group";
        SpoutConfig spoutConfig = new SpoutConfig(brokerHosts, topic, zkRoot, spoutId);

        //设置如何处理 kafka 消息队列输入流
        spoutConfig.scheme=new SchemeAsMultiScheme(new MessageScheme());

        //从 offset 最小值开始读取数据，否则从启动后开始读
        spoutConfig.forceFromStart=true;

        Config conf=new Config();
        //不输出调试信息
        conf.setDebug(false);
        //设置一个spout task中处于pending状态的最大的tuples数量
        conf.put(Config.TOPOLOGY_MAX_SPOUT_PENDING, 1);

        Map<String, String> map=new HashMap<String,String>();
        //配置 Kafka broker 地址
        map.put("metadata.broker.list", "master:9092,slave1:9092,slave2:9092");
        // serializer.class为消息的序列化类
        map.put("serializer.class", "kafka.serializer.StringEncoder");
        conf.put("kafka.broker.properties", map);

        // 配置 KafkaBolt 生成的 topic
        conf.put("topic", "receiver");

        TopologyBuilder builder = new TopologyBuilder();
        builder.setSpout("spout", new KafkaSpout(spoutConfig),1);
        builder.setBolt("bolt1", new QueryBolt(),1).setNumTasks(1).shuffleGrouping("spout");
        builder.setBolt("bolt2", new KafkaBolt<String, String>(),1).setNumTasks(1).shuffleGrouping("bolt1");

        String name= topology.class.getSimpleName();
        if (args != null && args.length > 0) {
            // Nimbus host name passed from command line
            conf.put(Config.NIMBUS_HOST, args[0]);
            conf.setNumWorkers(3);
            StormSubmitter.submitTopologyWithProgressBar(name, conf, builder.createTopology());
        } else {
            conf.setMaxTaskParallelism(3);
            LocalCluster cluster = new LocalCluster();
            cluster.submitTopology(name, conf, builder.createTopology());
            Thread.sleep(60000);
            cluster.shutdown();
        }
    }
}
</file>

**2.MessageScheme.java**

<file java MessageScheme.java>
import java.io.UnsupportedEncodingException;
import java.util.List;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import backtype.storm.spout.Scheme;
import backtype.storm.tuple.Fields;
import backtype.storm.tuple.Values;

public class MessageScheme implements Scheme {
    private static final Logger LOGGER = LoggerFactory.getLogger(MessageScheme.class);

    public List<Object> deserialize(byte[] ser) {
        try {
            //从 kafka 中读取的值直接序列化为 UTF-8 的 str
            String mString=new String(ser, "UTF-8");
            return new Values(mString);
        } catch (UnsupportedEncodingException e) {
            // TODO Auto-generated catch block
            LOGGER.error("Cannot parse the provided message");
        }
        return null;
    }

    public Fields getOutputFields() {
        // TODO Auto-generated method stub
        return new Fields("msg");
    }
}
</file>

**3.QueryBolt.java**

<file java QueryBolt.java>
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.PrintStream;
import java.util.ArrayList;
import java.util.List;
import java.util.Map;

import backtype.storm.task.OutputCollector;
import backtype.storm.task.TopologyContext;
import backtype.storm.topology.IRichBolt;
import backtype.storm.topology.OutputFieldsDeclarer;
import backtype.storm.tuple.Fields;
import backtype.storm.tuple.Tuple;
import backtype.storm.tuple.Values;

public class QueryBolt implements IRichBolt {
    List<String> list;
    OutputCollector collector;

    public void prepare(Map stormConf, TopologyContext context, OutputCollector collector) {
        list=new ArrayList<String>();
        this.collector=collector;
    }

    public void execute(Tuple input) {
        // TODO Auto-generated method stub
        String str=(String) input.getValue(0);
        //将 str 加入到 list
        list.add(str);
        //发送 ack
        collector.ack(input);
        //发送该 str
        collector.emit(new Values(str));
    }

    public void cleanup() {//topology被killed时调用
        //将 list 的值写入到文件
        try {
            FileOutputStream outputStream = new FileOutputStream("/data/" + this + ".txt");
            PrintStream p = new PrintStream(outputStream);
            p.println("begin!");
            p.println(list.size());
            for(String tmp:list){
                p.println(tmp);
            }
            p.println("end!");
            try {
                p.close();
                outputStream.close();
            } catch (IOException e) {
                // TODO Auto-generated catch block
                e.printStackTrace();
            }
        } catch (FileNotFoundException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }

    public void declareOutputFields(OutputFieldsDeclarer declarer) {
        declarer.declare(new Fields("message"));
    }

    public Map<String, Object> getComponentConfiguration() {
        // TODO Auto-generated method stub
        return null;
    }
}
</file>

- 编译

从上面下载的storm包中找到下面3个包，导入到工程中：

![storm-02.bmp](/images/storm-02.png)

编译后导出jar包。

- 拷贝集群运行依赖包到默认路径

```
cp /usr/local/kafka/libs/* /storm-kafka/storm-kafka-0.9.2-incubating.jar apache-storm-0.9.2-incubating/lib/
```

- 启动Storm Topology

```
bin/storm jar /data/sjwc.jar topology
```

其中，sjwc.jar是包含Topology实现代码的jar包，topology的main方法是Topology的入口。

运行结果可以在/data/下看到：

```
[root@uhadoop-XXXXXX-master1 data]# more QueryBolt@78ae427c.txt
begin!
94
jjjlk;a lkkj ivl
;okv
2 7
8 9
6 3
```

94是单词个数，下面是topic中所有内容。

- 停止Storm Topology

```
storm kill {topname}
```

> 注解：{topname}为Topology提交到Storm集群时指定的Topology任务名称。
