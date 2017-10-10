---
title: Kafka And Zookeeper
comments: true
date: 2017-01-12 13:58:15
updated: 2017-01-12 13:58:56
categories: Software
tags:
- Kafka
---

**说明：**Kafka And Zookeeper。
<!-- more -->


---
## Kafka And Zookeeper 简介
* kafka是分布式消息队列Message Queue产品，依赖于Zookeeper进行集群的消息控制。
* zookeeper(简写为zk)是分布式服务器管理，为高层应用提供服务。
* 分布式集群的优点是可横向扩展，人多力量大，局部故障仍可运行。缺点是需要协调管理，考虑调度优化等问题。zk为分布式管理提供服务，很多分布式产品都可以直接使用zk提供协调服务，比如solr、kafka等。

---
## Zookeeper的配置和使用
参考文章：[Zookeeper的配置](http://www.cnblogs.com/yuyijq/p/3438829.html)

### Zookeeper的安装配置
* linux版本下安装zk只需要官网搜索，下载，解压即可。
* 配置一个zk server主要是修改其conf文件夹中的文件。比如zoo.cfg设置为如下。

```
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/home/zju/software/zookeeper/data/2181/data
dataLogDir=/home/zju/software/zookeeper/data/2181/logs
clientPort=2181
server.1=10.15.82.65:3888:4888
server.2=10.15.82.65:3889:4889
server.3=10.15.82.66:3888:4888
```

* 然后将该zk再拷贝2份，只要保证一台服务器上clientPort不冲突，server列表保持一致。
* 配置中，每个server配置中有两个TCP port，前一个是Leader和Follower或Observer交换数据使用的，后面一个是用于Zookeeper选举用的。
* server.后面的数字，这个数字就是myid，在dataDir里会放置一个myid文件，里面就一个数字，用来唯一标识这个服务。这个id是很重要的，一定要保证整个集群中唯一。zookeeper会根据这个id来取出server.x上的配置。比如当前id为1，则对应着zoo.cfg里的server.1的配置，myid还有其他作用，比如用于leader选举等。

### zookeeper的启动关闭脚本
* 启动脚本可以直接写 `./zookeeper-3.4.10-2181/bin/zkServer.sh start`
* shutdown脚本直接写 `./zookeeper-3.4.10-2181/bin/zkServer.sh stop`
* 重启脚本就是先用关闭脚本，再用启动脚本。

## Kafka的配置和使用
### Kafka的安装配置
* linux安装kafka就是下载，解压即可。
* 配置kafka主要是其conf文件夹下的文件。

* 示例配置：使用1，2，3个broker，修改3个kafka server(称为broker)的server.properties，配置各自的id、端口号、zk的集群等。选取2个示例broker的部分配置如下。实际中，1和2在一个服务器，3在另一个服务器。

```
broker.id=1
delete.topic.enable=true
listeners=PLAINTEXT://10.15.82.65:9092
log.dirs=/home/zju/software/kafka/data/9092/kafka-logs
num.partitions=3
zookeeper.connect=10.15.82.65:2181,10.15.82.65:2182,10.15.82.66:2181
zookeeper.connection.timeout.ms=6000
```

```
broker.id=3
delete.topic.enable=true
listeners=PLAINTEXT://10.15.82.66:9092
log.dirs=/home/zju/software/kafka/data/9092/kafka-logs
num.partitions=3
zookeeper.connect=10.15.82.65:2181,10.15.82.65:2182,10.15.82.66:2181
zookeeper.connection.timeout.ms=6000
```

### Kafka的使用
#### 使用Kafka终端进行单机连通性测试
* 在Kafka的bin目录下有producer和consumer的脚本，可以直接用来进行通讯。
* 运行producer
    * `./bin/kafka-console-prucer.sh --broker-list 10.15.82.65:9092 --topic test`
    * 参数是指定broker列表，可以使用逗号列出多个boker列表，topic表示MQ的主题，类似于不同的信息有不同的管道。
    * 输入消息，以换行作为一个结束标志。输入多行运行consumer。如果出现超时，send message出错key为null的错误，可能是Kafka集群有问题，重启集群试试。
* 运行consumer
    *  `./bin/kafka-console-consumer.sh --bootstrap-server 10.15.82.65:9092,10.15.82.65:9093,10.15.82.66:9092 --topic test --from-beginning`
    * 参数是bootstrap-server表示ZK集群，多个时用逗号连接，topic表示消息主题，然后从头读取所有消息。
* 如果producer和consumer都正常，说明producer正常发送到broker集群，consumer正确取出消息，ZK集群为broker集群提供消息控制。

#### 使用Java API测试Kafka
* producer测试代码

```
package com.sr.util.kafka;
import org.apache.kafka.clients.producer.*;
import java.util.Properties;
public class TestProducer {
    public static final String topic = "testTopic"; // kafka不存在的主体会自动创建，且使用默认分区、备份等配置

    public static void main(String[] args) throws Exception {
        Properties props = new Properties();
        props.put("bootstrap.servers", "10.15.82.65:9092,10.15.82.65:9093,10.15.82.66:9092");

        props.put("group.id", "test");
        props.put("producer.type", "sync"); // 频繁写用异步async以进行batch发送，不频繁写可以用同步写sync
        props.put("enable.auto.commit", "true");
        props.put("auto.commit.interval.ms", "1000");

        //props.put("acks", "all"); // 提交进行阻塞,这是最慢但最持久的设置
        props.put("retries", 0);
        props.put("batch.size", 16384);
        props.put("linger.ms", 0); //设置为大于0的请求数可以等待以减少请求次数，但是即使为0一般也会成批发送
        props.put("buffer.memory", 33554432);
        props.put("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
        props.put("value.serializer","org.apache.kafka.common.serialization.StringSerializer");


        Producer<String, String> producer = new KafkaProducer<>(props);
        for(int i = 0; i < 1000; i++){
            ProducerRecord record = new ProducerRecord<>(topic, Integer.toString(i), Integer.toString(i));
            producer.send(record);
            producer.flush(); // 强制刷新，但是上一个如果发送失败可能引起错误，但是上一个仍是异步阻塞发送啊...
            System.out.println("send: " + ++i);
            ProducerRecord record2 = new ProducerRecord<>(topic, Integer.toString(i), Integer.toString(i));
            producer.send(record2,
                    new Callback() {
                        public void onCompletion(RecordMetadata metadata, Exception e) {
                            if(e != null)
                                e.printStackTrace();
                            System.out.println("The offset of the record we just sent is: " + metadata.offset());
                        }
                    });
            Thread.sleep(1000);
        }
        producer.close();

    }
}
```

* consumer测试代码

```
package com.sr.util.kafka;
import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.KafkaConsumer;

import java.util.*;
public class TestConsumer {
	public static final String topic = "testTopic";

	public static void main(String[] args) throws Exception {
		Properties props = new Properties();
		props.put("bootstrap.servers", "10.15.82.65:9092,10.15.82.65:9093,10.15.82.66:9092");
		props.put("group.id", "test");
		props.put("enable.auto.commit", "true");
		props.put("auto.commit.interval.ms", "1000");
		props.put("session.timeout.ms", "30000");
		props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
		props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
		KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);
		consumer.subscribe(Arrays.asList(topic, "bar"));
		while (true) {
			ConsumerRecords<String, String> records = consumer.poll(1);
			for (ConsumerRecord<String, String> record : records)
				System.out.printf("offset = %d, key = %s, value = %s\n", record.offset(), record.key(), record.value());
		}
	}
}
```



