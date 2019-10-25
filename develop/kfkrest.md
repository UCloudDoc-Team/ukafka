

## kafka rest proxy 安装

### 1. kafka rest proxy用途

\- 通过http接口访问kafka

\- 可以在有外网的Uhost安装，kafka rest proxy外网访问内网Ukafka

### 2. 安装条件

\- 基于Centos6

\- kafka 0.10.X版本

\- confluent 版本3.1（kafka rest proxy是confluent平台的一部分）

\- 有外网 备注：可以根据kafka选择对应的confluent 版本

### 3. 安装步骤

\- 安装jdk1.8 以上，并配置环境变量

\- 配置yum源

在/etc/yum.repos.d目录下创建confluent.repo文件，并添加如下内容

```
[Confluent.dist]
name=Confluent repository (dist)
baseurl=http://packages.confluent.io/rpm/3.1/6
gpgcheck=1
gpgkey=http://packages.confluent.io/rpm/3.1/archive.key
enabled=1
[Confluent]
name=Confluent repository
baseurl=http://packages.confluent.io/rpm/3.1
gpgcheck=1
gpgkey=http://packages.confluent.io/rpm/3.1/archive.key
enabled=1
```

\- yum安装 confluent-kafka-rest和 confluent-schema-registry

```
yum install confluent-kafka-rest confluent-schema-registry -y
```

\- 修改confluent-schema-registry 配置

```
vim /etc/schema-registry/schema-registry.properties

kafkastore.connection.url=ip:2181 
```

备注：上述ip:2181换成kafka对应的zookeeper地址。

\- 启动schema-registry-start（默认端口号8081）

```
schema-registry-start /etc/schema-registry/schema-registry.properties
```

\- 修改confluent-kafka-rest配置

```
vim /etc/kafka-rest/kafka-rest.properties

zookeeper.connect=ip:2181  
```

备注：zookeeper地址修改为kafka zookeeper地址

\- 启动 kafka-rest-start（默认端口号8082）

```
kafka-rest-start /etc/kafka-rest/kafka-rest.properties
```

### 4. kafka rest proxy 测试

备注：下面localhost请修改为kafka-rest地址 - list所有topics

```
curl "http://localhost:8082/topics"
```

\- 获取一个topic信息

```
curl "http://localhost:8082/topics/test"
```

\- 写数据到topic test

```
curl -X POST -H "Content-Type: application/vnd.kafka.json.v1+json" -H "Accept: application/vnd.kafka.v1+json" --data '{"records":[{"value":{"foo":"bar"}}]}' "http://localhost:8082/topics/test"
```

### 5. 参考资料

\- 更多操作请查看confluent官网：

<https://docs.confluent.io/3.0.0/kafka-rest/docs/index.html>

<https://github.com/confluentinc/kafka-rest/tree/3.1.x>

\- api文档：

<https://github.com/confluentinc/kafka-rest/blob/3.1.x/docs/api.rst>
