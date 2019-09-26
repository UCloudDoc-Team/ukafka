{{indexmenu_n>2}}

## Logstash连接UKafka

Logstash可以很容易的将服务器日志收集并推送到UKafka。Logstash中可通过”bin/plugin install
logstash-output-kafka”和”bin/plugin install
logstash-input-kafka”安装连接UKafka所需插件。

### 1. logstash-output-kafka

配置logstash-output-kafka.conf文件

<file json logstash-output-kafka.conf>
input {
    generator { count => 30000000 }
}

output {
    kafka {
        bootstrap_servers => "ip1:9092,ip2:9092,ip3:9092"
        topic_id => "logstash"
    }
}
</file>

执行"./bin/logstash agent -f logstash-output-kafka.conf"往UKafka发送消息

==== 2. logstash-input-kafka ====

配置logstash-input-kafka.conf文件

<file json logstash-input-kafka.conf>
input {
    kafka {
        zk_connect => "ip1:2181,ip2:2181,ip3:2181"
        group_id => "logstash"
        topic_id => "logstash"
        auto_offset_reset => "smallest"
        reset_beginning => true
    }
}
output {
    stdout {}
}
</file>

执行"./bin/logstash agent -f logstash-input-kafka.conf"读取UKafka消息
