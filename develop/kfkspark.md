{{indexmenu_n>6}}

### 使用Spark消费UKafka消息

#### 1 Java代码示例

\- JavaKafkaWordCount.java

``` java
package org.apache.spark.examples.streaming;
import java.util.Map;
import java.util.HashMap;
import java.util.regex.Pattern;
import scala.Tuple2;
import com.google.common.collect.Lists;
import org.apache.spark.SparkConf;
import org.apache.spark.api.java.function.FlatMapFunction;
import org.apache.spark.api.java.function.Function;
import org.apache.spark.api.java.function.Function2;
import org.apache.spark.api.java.function.PairFunction;
import org.apache.spark.streaming.Duration;
import org.apache.spark.streaming.api.java.JavaDStream;
import org.apache.spark.streaming.api.java.JavaPairDStream;
import org.apache.spark.streaming.api.java.JavaPairReceiverInputDStream;
import org.apache.spark.streaming.api.java.JavaStreamingContext;
import org.apache.spark.streaming.kafka.KafkaUtils;

public final class JavaKafkaWordCount {
    private static final Pattern SPACE = Pattern.compile(" ");

    private JavaKafkaWordCount() { }

    public static void main(String[] args) {
        if (args.length < 4) {
            System.err.println("Usage: JavaKafkaWordCount <zkQuorum> <grou p> <topics> <numThreads>");
            System.exit(1);
        }

        SparkConf sparkConf = new SparkConf().setAppName("JavaKafkaWord Count");
        // Create the context with a 1 second batch size
        JavaStreamingContext jssc = new JavaStreamingContext(sparkConf, new Duration(2000));
        int numThreads = Integer.parseInt(args[3]);
        Map<String, Integer> topicMap = new HashMap<String, Integer>();
        String[] topics = args[2].split(",");
        for (String topic: topics) {
            topicMap.put(topic, numThreads);
        }

        JavaPairReceiverInputDStream<String, String> messages = KafkaUtils.createStream(jssc, args[0], args[1], topicMap);

        JavaDStream<String> lines = messages.map(new Function<Tuple2<Strin g, String>, String>() {
            @Override
            public String call(Tuple2<String, String> tuple2) {
                return tuple2._2();
            }
        });

        JavaDStream<String> words = lines.flatMap(new FlatMapFunction<Strin g, String>() {
            @Override
            public Iterable<String> call(String x) {
                return Lists.newArrayList(SPACE.split(x)); }
        });

        JavaPairDStream<String, Integer> wordCounts = words.mapToPair(
            new PairFunction<String, String, Integer>() {
                @Override
                public Tuple2<String, Integer> call(String s) {
                    return new Tuple2<String, Integer>(s, 1);
                }
            }).reduceByKey(new Function2<Integer, Integer, Integer>() {
                @Override
                public Integer call(Integer i1, Integer i2) {
                    return i1 + i2;
                }
            });

        wordCounts.print();

        //wordCounts.saveAsHadoopFiles("jwc", "sufix"); jssc.start();
        jssc.awaitTermination();
    }
}
```

\- 下载依赖包

[com.google.common\_1.0.0.201004262004.jar](http://uhadoop-new.ufile.ucloud.com.cn/kafka/com.google.common_1.0.0.201004262004.jar)

[spark-streaming-kafka-assembly\_2.10-1.5.2.jar](http://uhadoop-new.ufile.ucloud.com.cn/kafka/spark-streaming-kafka-assembly_2.10-1.5.2.jar)

[spark-assembly-1.5.2-hadoop2.6.0.jar](http://uhadoop-new.ufile.ucloud.com.cn/spark/spark-assembly-1.5.2-hadoop2.6.0.jar)

\- 创建工程

在eclipse中创建一个新的工程，并将上面的代码添加到工程中。然后，导入上面的3个jar包：

![spark-01.bmp](/images/spark-01.png)

添加jar包到build目录

![spark-02.jpg](/images/spark-02.jpg)
![spark-03.bmp](/images/spark-03.png)

最后，导出jar包

![spark-04.bmp](/images/spark-04.png)
![spark-05.bmp](/images/spark-05.png)

\- 启动命令

将上一步导出的jar包拷贝到spark集群，并执行以下命令：

```
spark-submit --master yarn --jars spark-streaming-kafka-assembly_2.10-1.5.2.jar --class org.apache.spark.examples.streaming.JavaKafkaWordCount kjwc.jar ip:2181 test-consumer-group test_topic 1 2
```

> 注解：需要将ip换成自己kafka集群节点ip，。

启动程序后，使用上面介绍的发送消息方法向test\_topic这个topic发送消息，可以在输出终端看到类似这样的字段：

``` 
 -------------------------------------------
 Time: 1457593316000 ms
 -------------------------------------------
 (one,1)
 (onee,1)
```

#### 2 Scala代码示例

\- KafkaWordCountProducer.scala

``` java
package org.apache.spark.examples.streaming

import java.util.HashMap
import org.apache.kafka.clients.producer.{ProducerConfig, KafkaProducer, Produce rRecord}
import org.apache.spark.streaming._
import org.apache.spark.streaming.kafka._
import org.apache.spark.SparkConf

object KafkaWordCount {
    def main(args: Array[String]) {
        if (args.length < 4) {
            System.err.println("Usage: KafkaWordCount <zkQuorum> <group> <topic s> <numThreads>")
            System.exit(1)
        }
        StreamingExamples.setStreamingLogLevels()

        val Array(zkQuorum, group, topics, numThreads) = args
        val sparkConf = new SparkConf().setAppName("KafkaWordCount")
        val ssc = new StreamingContext(sparkConf, Seconds(2))
        ssc.checkpoint("checkpoint")

        val topicMap = topics.split(",").map((_, numThreads.toInt)).toMap
        val lines = KafkaUtils.createStream(ssc, zkQuorum, group, topicMap).map (_._2)
        val words = lines.flatMap(_.split(" "))
        val wordCounts = words.map(x => (x, 1L)).reduceByKeyAndWindow(_ + _, _ - _, Minutes(10), Seconds(2), 2)
        wordCounts.print()
        ssc.start()
        ssc.awaitTermination()
    }
}

// Produces some random words between 1 and 100.
object KafkaWordCountProducer {
    def main(args: Array[String]) {
        if (args.length < 4) {
            System.err.println("Usage: KafkaWordCountProducer <metadataBrokerList> <topic> <messagesPerSec> <wordsPerMessage>")
            System.exit(1)
        }

        val Array(brokers, topic, messagesPerSec, wordsPerMessage) = args

        // Zookeeper connection properties
        val props = new HashMap[String, Object]()
        props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, brokers)         props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG,
            "org.apache.kafka.common.serialization.StringSerializer")
        props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG,
            "org.apache.kafka.common.serialization.StringSerializer")

        val producer = new KafkaProducer[String, String](props)

        // Send some messages
        while(true) {
            (1 to messagesPerSec.toInt).foreach { messageNum =>
                val str = (1 to wordsPerMessage.toInt).map(x => scala.util.Random.nextInt(10).toString).mkString(" ")

                val message = new ProducerRecord[String, String](topic, null, str)
                producer.send(message)
            }
            Thread.sleep(1000)
        }
    }
}
```

\- 发送消息

```
spark-submit --master yarn --deploy-mode client --class org.apache.spark.example.streaming.KafkaWordCountProducer /home/hadoop/spark/lib/spark-examples-1.5.2-hadoop2.6.0-cdh5.4.4.jar ip:9092 test_tpoic 1 2
```

> 注解：需要将ip修改为具体的ukafka集群节点ip。

ip:9092表示producer的ip地址和端口；test\_topic表示topic；1表示每秒发1条消息；2表示每天消息中有2个单词。

在test\_topic的消息接收端，可以看到有持续的记录输出。

\- 消费消息

```
spark-submit --master yarn --deploy-mode client --class org.apache.spark.examples.streaming.KafkaWordCount /home/hadoop/spark/lib/spark-examples-1.5.2-hadoop2.6.0-cdh5.4.4.jar ip:2181 test-consumer-group test_topic 1
```

> 注解：需要将ip修改为ukafka集群节点ip。

ip:2181表示zookeeper的监听地址；test-consumer-group表示当前消费的程序的一个编号；test\_topic表示topic；1表示线程数。

会在输出的终端中显示类似下面的内容：

``` 
 -------------------------------------------
 Time: 1457593574000 ms
 -------------------------------------------
 (one,2)
 (three,1)
 (to,1)
 (fo,1)
```

#### 3 Python代码示例

\- wordcount.py

``` python
#/usr/bin/python
import sys

from pyspark import SparkContext
from pyspark.streaming import StreamingContext
from pyspark.streaming.kafka import KafkaUtils

if __name__ == "__main__":
    if len(sys.argv) != 4:
        print("%d",len(sys.argv))
        ## print("Usage: kafka_wordcount.py <zk> <topic>", file=sys.stder
        exit(-1)

    sc = SparkContext(appName="PythonStreamingKafkaWordCount")
    ssc = StreamingContext(sc, 10)

    zkQuorum, topic, file = sys.argv[1:]
    kvs = KafkaUtils.createStream(ssc, zkQuorum, "spark-streaming-consum er", {topic: 1})
    lines = kvs.map(lambda x: x[1])
    counts = lines.flatMap(lambda line: line.split(" ")) \
        .map(lambda word: (word, 1)) \
        .reduceByKey(lambda a, b: a+b)
    counts.pprint()
    lines.pprint()
    lines.saveAsTextFiles(file)
    ssc.start()
    ssc.awaitTermination()
```

\- 执行代码

```
spark-submit --packages org.apache.spark:spark-streaming-kafka_2.10:1.5.2 --master yarn --deploy-mode client wordcount.py ip:2181 test_topic wc
```

> 注解：需要将ip修改为具体的ukafka集群节点ip。

需要配置外网ip，才能自动下载依赖包。如果没有外网ip，可以在手动下载[依赖包](http://uhadoop-new.ufile.ucloud.com.cn/kafka/spark-streaming-kafka-assembly_2.10-1.5.2.jar)后，指定jar包：

```
spark-submit --jars spark-streaming-kafka-assembly_2.10-1.5.2.jar --master yarn --deploy-mode client wordcount.py ip:2181 test_topic wc
```

在另一个终端向指定的topic发送消息

``` 
 -------------------------------------------
 Time: 2016-03-10 15:07:53
 -------------------------------------------
 (u'', 4)
 (u'helo', 1)
 (u'eon', 1)
 (u'three', 2)
 (u'one', 7)
 (u'to', 4)
 (u'\tthree', 1)
 (u'threee', 1)
 (u'two', 1)
 (u'fo', 2)
```
