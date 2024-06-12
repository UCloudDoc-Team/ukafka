# 外网访问配置

## 开启集群认证配置

[集群认证配置](/ukafka/guide/cluster/sasl)

## 查看 SASL 接入点

[SASL_PLAINTEXT协议地址](/ukafka/guide/node/address#SASL_PLAINTEXT协议地址)

## 配置外网转发

### 1. 创建与UKafka集群处于同一子网下的云主机

### 2. 在云主机上安装 nginx

```shell
yum install nginx
```

### 3. 配置 nginx 代理

- 编辑 `/etc/nginx/nginx.conf`，增加 stream 配置

```conf
stream {
    log_format proxy '$remote_addr [$time_local] '
                 '$protocol $status $bytes_sent $bytes_received '
                 '$session_time "$upstream_addr" '
                 '"$upstream_bytes_sent" "$upstream_bytes_received" "$upstream_connect_time"';

    access_log /var/log/nginx/tcp-access.log proxy;
    open_log_file_cache off;

    # 统一放置，方便管理
    include /etc/nginx/tcpConf.d/*.conf;
}
```

- 创建 `/etc/nginx/tcpConf.d/` 目录

```shell
mkdir -p /etc/nginx/tcpConf.d/
```

- 编辑 `/etc/nginx/tcpConf.d/kafka.conf` 配置文件

```conf
upstream tcp9093 {
    server 10.23.26.105:9093;
}
upstream tcp9094 {
    server 10.23.180.35:9094;
}
upstream tcp9095 {
    server 10.23.202.164:9095;
}

server {
    listen 9093;
    proxy_connect_timeout 8s;
    proxy_timeout 24h;
    proxy_pass tcp9093;
}
server {
    listen 9094;
    proxy_connect_timeout 8s;
    proxy_timeout 24h;
    proxy_pass tcp9094;
}
server {
    listen 9095;
    proxy_connect_timeout 8s;
    proxy_timeout 24h;
    proxy_pass tcp9095;
}
```

- 应用配置

```shell
nginx -s reload
```

### 4. 主机防火墙配置

主机防火墙需要接收转发请求的 9093, 9094, 9095 端口。

## 外网访问

### 1. 配置本地 hosts

修改本地 `/etc/hosts` 文件，将 UKafka SASL_PLAINTEXT 协议监听的地址，全都指向转发请求的云主机的公网 IP：

```conf
113.31.114.125 ukafka-q0j01x5g-kafka1
113.31.114.125 ukafka-q0j01x5g-kafka2
113.31.114.125 ukafka-q0j01x5g-kafka3
```

### 2. Kafka 命令行工具访问

#### 下载并配置 Kafka 命令行工具

以 Kafka 2.6.1 命令行工具为例：

> 可按实例版本下载命令行工具

```shell
# 下载命令行工具
wget https://archive.apache.org/dist/kafka/2.6.1/kafka_2.13-2.6.1.tgz

# 解压
tar -zxvf kafka_2.13-2.6.1.tgz

# 进入命令行工具根目录
cd kafka_2.13-2.6.1
```

配置 `config/kafka_client_jaas.conf` 文件如下，`username` 和 `password` 填写控制台开启集群认证配置的用户名和密码：

```shell
cat > config/kafka_client_jaas.conf << EOF
KafkaClient {
  org.apache.kafka.common.security.plain.PlainLoginModule required
  username="admin"
  password="admin_pass";
};
EOF
```

#### 发送消息

- 修改 `bin/kafka-console-producer.sh` 增加 `java.security.auth.login.config` 参数

```sh
if [ "x$KAFKA_HEAP_OPTS" = "x" ]; then
    export KAFKA_HEAP_OPTS="-Xmx512M"
fi
exec $(dirname $0)/kafka-run-class.sh -Djava.security.auth.login.config=./config/kafka_client_jaas.conf kafka.tools.ConsoleProducer "$@"
```

- 修改 `config/producer.properties`，指定 `security.protocol`, `sasl.mechanism`

```sh
security.protocol=SASL_PLAINTEXT
sasl.mechanism=PLAIN
```

- 运行 `bin/kafka-console-producer.sh` 发送消息：

```shell
bin/kafka-console-producer.sh  --producer.config ./config/producer.properties --topic foo --broker-list ukafka-q0j01x5g-kafka1:9093,ukafka-q0j01x5g-kafka2:9094,ukafka-q0j01x5g-kafka3:9095
>hello
>world
>foo
>bar
>
```

#### 接收消息

- 修改 `bin/kafka-console-consumer.sh` 增加 `java.security.auth.login.config` 参数

``` sh
if [ "x$KAFKA_HEAP_OPTS" = "x" ]; then
    export KAFKA_HEAP_OPTS="-Xmx512M"
fi
exec $(dirname $0)/kafka-run-class.sh -Djava.security.auth.login.config=./config/kafka_client_jaas.conf kafka.tools.ConsoleConsumer "$@"
```

- 修改 `config/consumer.properties`，指定 `security.protocol`, `sasl.mechanism`

``` sh
group.id=test-consumer-group

security.protocol=SASL_PLAINTEXT
sasl.mechanism=PLAIN
```

- 运行 `bin/kafka-console-consumer.sh` 接收消息

```shell
bin/kafka-console-consumer.sh --consumer.config ./config/consumer.properties --topic foo --bootstrap-server ukafka-q0j01x5g-kafka1:9093,ukafka-q0j01x5g-kafka2:9094,ukafka-q0j01x5g-kafka3:9095 --from-beginning
world
hello
foo
bar
```

### 3. Java SDK 访问

Java SDK 访问有多种方式，如配置 `kafka_client_jaas.conf` 文件，或直接设置属性 `sasl.jaas.config`，详情可参考下述代码示例中的三种方式。

如使用 `kafka_client_jaas.conf` 配置文件，`username` 和 `password` 填写控制台开启集群认证配置的用户名和密码：

```shell
KafkaClient {
  org.apache.kafka.common.security.plain.PlainLoginModule required
  username="admin"
  password="admin_pass";
};
```

#### 生产者示例

``` java
package cn.ucloud.ukafka.Example;

import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerRecord;
import org.apache.kafka.clients.producer.RecordMetadata;

import java.util.Properties;
import java.util.concurrent.Future;

public class KafkaProducerExample {

    public static void main(String[] args) throws Exception {
        // 方式1：添加环境变量，需要指定配置文件的路径
        System.setProperty("java.security.auth.login.config", "./config/kafka_client_jaas.conf");

        // 方式2：添加启动 JVM 参数 `-Djava.security.auth.login.config=./config/kafka_client_jaas.conf`

        Properties props = new Properties();
        props.put("bootstrap.servers", "ukafka-q0j01x5g-kafka1:9093,ukafka-q0j01x5g-kafka2:9094,ukafka-q0j01x5g-kafka3:9095");

        // 需要指定 security.protocol 与 sasl.mechanism
        props.put("security.protocol", "SASL_PLAINTEXT");
        props.put("sasl.mechanism", "PLAIN");

        // 方式3：Properties 设置 jaas 配置内容
        // props.put("sasl.jaas.config", "org.apache.kafka.common.security.plain.PlainLoginModule required username=\"admin\" password=\"admin_pass\";");

        props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");

        KafkaProducer<String, String> producer = new KafkaProducer<String, String>(props);

        String topic = "foo";
        String value = "this is the message's value";

        ProducerRecord<String, String>  kafkaMessage =  new ProducerRecord<String, String>(topic, value);
        try {
            Future<RecordMetadata> metadataFuture = producer.send(kafkaMessage);
            RecordMetadata recordMetadata = metadataFuture.get();
            System.out.println(String.format("Produce ok: topic:%s partition:%d offset:%d value:%s",
                    recordMetadata.topic(), recordMetadata.partition(), recordMetadata.offset(), value));
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

#### 消费者示例

``` java
package cn.ucloud.ukafka.Example;

import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.KafkaConsumer;

import java.util.ArrayList;
import java.util.List;
import java.util.Properties;

public class KafkaConsumerExample {

    public static void main(String[] args) throws Exception {
        // 方式1：添加环境变量，需要指定配置文件的路径
        System.setProperty("java.security.auth.login.config", "./config/kafka_client_jaas.conf");

        // 方式2：添加启动 JVM 参数 `-Djava.security.auth.login.config=./config/kafka_client_jaas.conf`

        Properties props = new Properties();
        props.put("bootstrap.servers", "ukafka-q0j01x5g-kafka1:9093,ukafka-q0j01x5g-kafka2:9094,ukafka-q0j01x5g-kafka3:9095");
        props.put("group.id", "test_group");
        props.put("auto.offset.reset", "earliest");

        // 需要指定 security.protocol 与 sasl.mechanism
        props.put("security.protocol", "SASL_PLAINTEXT");
        props.put("sasl.mechanism", "PLAIN");

        // 方式3：Properties 设置 jaas 配置内容
        // props.put("sasl.jaas.config", "org.apache.kafka.common.security.plain.PlainLoginModule required username=\"admin\" password=\"admin_pass\";");

        props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");

        KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);
        List<String> subscribedTopics = new ArrayList<String>();
        subscribedTopics.add("foo");
        consumer.subscribe(subscribedTopics);

        while (true){
            try {
                ConsumerRecords<String, String> records = consumer.poll(1000);
                for (ConsumerRecord<String, String> record : records) {
                    System.out.println(String.format("Consume topic:%s partition:%d offset:%d value:%s",
                            record.topic(), record.partition(), record.offset(), record.value()));
                }
            }
            catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
}
```

> 不同语言的 Kafka SDK 对认证的支持与设置存在区别，具体设置需要查看相关 SDK 的文档
