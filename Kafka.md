# Kafka

```
视频学习：
【【尚硅谷】Kafka3.x教程（从入门到调优，深入全面）】https://www.bilibili.com/video/BV1vr4y1677k?vd_source=f4fa55dcba9415699434901e9c870565

进度：
20250106  [6,9]是linux下集群安装，这里没做笔记，简单看了一遍，感觉实用性很低，看完就忘。
```



## 0 环境安装

### 1 Docker环境下模拟Kafka

Docker中模拟环境创建 Zookeeper + Kafka 环境

```

环境准备，从docker安装ZooKeeper和Kafka：
https://blog.csdn.net/m0_64210833/article/details/134199061?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522406ed1e58003fc31b644c435e4faa7f1%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=406ed1e58003fc31b644c435e4faa7f1&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-134199061-null-null.142^v101^pc_search_result_base3&utm_term=docker%E4%B8%AD%E9%83%A8%E7%BD%B2kafka&spm=1018.2226.3001.4187
```



```
docker启动：
环境准备：
1.确认安装docker，在cmd窗口通过：>docker info
2.如果没有安装ubuntu，请在cmd窗口安装>docker pull ubuntu
3.如果没有安装zookeeper，请在cmd窗口安装>docker pull wurstmeister/zookeeper
4.如果没有安装kafka，请在cmd窗口安装>docker pull wurstmeister/kafka

启动：
请在docker desktop启动ubuntu zookeeper kafka
zookeeper端口是2181 kafka端口是9092

cmd下>docker ps -a可以查看运行状态

验证：
1.进入cmd命令，进入kafka容器>docker exec -it kafka /bin/bash
2.创建测试主题：>kafka-topics.sh --create --topic test --partitions 1 --replication-factor 1 --zookeeper zookeeper:2181
3.创建生产者命令>kafka-console-producer.sh --broker-list localhost:9092 --topic test
4.输入一些命令作为消息
5.另一个cmd窗口先执行1进入kafka容器，然后创建一个消费者来读取测试主题的消息>kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
```



## 1 基础入门



### 1 Kafka概述

#### 1.1 定义

前端埋点记录用户行为数据（浏览、点赞、收藏等等事件）。或者在《阿里巴巴大数据实践》中日志采集体系主要是两部分：Web端的日志采集技术+UserTrack等APP端的日志采集方案。

传统的flume处理较慢，kafka可以负责缓冲。示例图如下：

![](image\Kafka_image\k_1_1.png)



#### 1.2 消息队列

常见的消息队列：Kafka 、ActiveMQ 、RabbitMQ 、RocketMQ

大数据场景下主要使用Kafka。JavaEE中主要使用ActiveMQ 、RabbitMQ 、RocketMQ

传统的消息队列的应用场景主要有：**缓存/消峰、解耦、异步通信**：

- 缓存/消峰：有助于控制和优化数据流经过系统的速度，解决生产消息和消费消息的处理速度不一致的情况。
- 解耦：数据源不一致，producer各种数据源写入消息队列，Consumer读取从消息队列读取。
- 异步通信：允许用户把消息放入队列，但是并不立即处理，然后在需要的时候再去处理他们。

消息队列的两种模式：

- 点对点模式：消费者主动拉取数据，信息收到后清除释放。
- 发布/订阅模式：可以有多个Topic。消费者消费数据后不删除，每个消费者相互独立，都可以消费到数据。



#### 1.3 基础架构

注意几个概念：

- 生产者producer
- broker：
- 消费者Customer

![](image\Kafka_image\k_1_2.png)



### 2 Kafka入门

```
Topic相关命令：
查看当前集群都有哪些topic：
>kafka-topics.sh --list --bootstrap-server localhost:9092
__consumer_offsets
test

```





### 3 Kafka生产者



#### 3.1 生产者消息发送流程

sender线程发送的条件：到达batch.size或者linger.ms

![](image\Kafka_image\k_1_3.png)



以下是**同步/异步**的处理流程

![](D:\study\image\Kafka_image\k_1_4.png)





#### 3.2 异步发送API

异步是指：外部消息发送到队列的过程。

以下为示例代码，普通的异步发送：

```java
package kafka.chapter01;


import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerConfig;
import org.apache.kafka.clients.producer.ProducerRecord;
import org.apache.kafka.clients.producer.RecordMetadata;
import org.apache.kafka.common.serialization.StringSerializer;

import java.util.Properties;
import java.util.concurrent.Future;

public class CustomProducer {
    public static void main(String[] args) {

        // 配置
        Properties properties = new Properties();
        properties.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG,"localhost:9092");
        // 指定KV的序列化类型
        properties.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        // 创建生产者对象
        KafkaProducer<String, String> kafkaProducer = new KafkaProducer<String, String>(properties);
        // 发送数据
        Future<RecordMetadata> send = kafkaProducer.send(new ProducerRecord<String, String>("test", "Ryze from java"));
        // 关闭资源
        System.out.println(send);
        kafkaProducer.close();
    }
}

```

**带回调的异步发送**

```
// 这是普通的异步发送数据
Future<RecordMetadata> send = kafkaProducer.send(new ProducerRecord<String, String>("test", "Ryze from java"));

// 带回调的实际上就是send多带一个callback参数

```

**带回调的异步**发送代码如下：

```java
package kafka.chapter01;

import org.apache.kafka.clients.producer.*;
import org.apache.kafka.common.serialization.StringSerializer;

import java.util.Properties;
import java.util.concurrent.Future;

public class CustomProducerCallBack {

    public static void main(String[] args) {
        // 配置
        Properties properties = new Properties();
        properties.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG,"localhost:9092");
        // 指定KV的序列化类型
        properties.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        // 创建生产者对象
        KafkaProducer<String, String> kafkaProducer = new KafkaProducer<String, String>(properties);
        // 发送数据
        Future<RecordMetadata> send = kafkaProducer.send(new ProducerRecord<String, String>("test", "Ryze from java with callback"), new Callback() {
            public void onCompletion(RecordMetadata recordMetadata, Exception e) {
                if (e == null){
                    System.out.println("topic: " + recordMetadata.topic() + " , 分区：" + recordMetadata.partition());
                }
            }
        });
        // 关闭资源
        System.out.println(send);
        kafkaProducer.close();
    }
}

```



#### 3.3 同步发送API

示例图如下，同步发送是指，外部数据传输到RecordAccumulator上，等处理完再传输。

![](image\Kafka_image\k_1_5.png)

示例代码如下：【和异步差不多，只是在send后边加上了get方法】

```java
package kafka.chapter01;

import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerConfig;
import org.apache.kafka.clients.producer.ProducerRecord;
import org.apache.kafka.clients.producer.RecordMetadata;
import org.apache.kafka.common.serialization.StringSerializer;

import java.util.Properties;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

public class CustomProducerSync {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        // 配置
        Properties properties = new Properties();
        properties.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG,"localhost:9092");
        // 指定KV的序列化类型
        properties.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        // 创建生产者对象
        KafkaProducer<String, String> kafkaProducer = new KafkaProducer<String, String>(properties);
        // 发送数据
        kafkaProducer.send(new ProducerRecord<String, String>("test", "Ryze from java")).get();
        // 关闭资源
        kafkaProducer.close();
    }
}

```



#### 3.4 生产者分区

##### 3.4.1 分区的意义

主要是针对下边红色框内数据：

- send是上节内容中同步异步
- 拦截器在生产中主要通过flume实现
- 序列化器，由于生产中消息通常为String

![](D:\study\image\Kafka_image\k_1_6.png)

分区器的**好处**：

- 便于合理使用存储资源。每个partition在一个Broker上存储，可以把海量的数据按照分区切割成分块数据存储在多台Broker上。合理控制分区的任务，可以实现负载均衡的效果。
- 提高并行度。生产者以分区为单位发送数据，消费者可以以分区为单位进行消费数据。

##### 3.4.2 分区策略

默认的分区器DefaultPartitioner，[指定分区主要是在kafkaProducer.send方法中]

- 指明分区
- 未指明分区+有key
- 未指明分区+无key，则采用黏性分区器

![](image\Kafka_image\k_1_7.png)



示例代码如下：

```java
package kafka.chapter01;

import org.apache.kafka.clients.producer.*;
import org.apache.kafka.common.serialization.StringSerializer;

import java.util.Properties;
import java.util.concurrent.Future;

public class CustomProducerCallBackPartitions {

    public static void main(String[] args) {
        // 配置
        Properties properties = new Properties();
        properties.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG,"localhost:9092");
        // 指定KV的序列化类型
        properties.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        // 创建生产者对象
        KafkaProducer<String, String> kafkaProducer = new KafkaProducer<String, String>(properties);
        // 发送数据
        Future<RecordMetadata> send = kafkaProducer.send(new ProducerRecord<String, String>("topic_0107",0, "", "Ryze from java to Partition0"), new Callback() {
            public void onCompletion(RecordMetadata recordMetadata, Exception e) {
                if (e == null){
                    System.out.println("topic: " + recordMetadata.topic() + " , 分区：" + recordMetadata.partition());
                }
            }
        });
        // 关闭资源
        System.out.println(send);
        kafkaProducer.close();
    }
}

```



##### 3.4.3 自定义分区器

实现步骤：定义类实现Partitioner接口，重写partition()方法

示例代码如下：

```java
// 这里构建MyPartitioner
public class MyPartitioner implements Partitioner {
    public int partition(String topic, Object key, byte[] keybytes, Object value, byte[] valuebytes, Cluster cluster) {

        // 获取数据
        String myValues = value.toString();
        int partition;
        if (myValues.contains("auserwn")){
            partition = 0 ;
        } else{
            partition = 1;
        }
        return partition;
    }

    public void close() {

    }

    public void configure(Map<String, ?> map) {

    }
}

=========================  ========================	 ========================

package kafka.chapter01;

import org.apache.kafka.clients.producer.*;
import org.apache.kafka.common.serialization.StringSerializer;

import java.util.Properties;
import java.util.concurrent.Future;

public class CustomProducerCallBackPartitions {

    public static void main(String[] args) {
        // 配置
        Properties properties = new Properties();
        properties.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG,"localhost:9092");
        // 指定KV的序列化类型
        properties.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

        // 关联绑定自定义分区器
properties.put(ProducerConfig.PARTITIONER_CLASS_CONFIG,"kafka.chapter01.MyPartitioner");
        
        // 创建生产者对象
        KafkaProducer<String, String> kafkaProducer = new KafkaProducer<String, String>(properties);
        // 发送数据
        Future<RecordMetadata> send = kafkaProducer.send(new ProducerRecord<String, String>("topic_0107","zzz, from user:auser"), new Callback() {
            public void onCompletion(RecordMetadata recordMetadata, Exception e) {
                if (e == null){
                    System.out.println("topic: " + recordMetadata.topic() + " , 分区：" + recordMetadata.partition());
                }
            }
        });
        // 关闭资源
        System.out.println(send);
        kafkaProducer.close();
    }
}

```



#### 3.5 生产者如何提升吞吐量

相关参数：

- batch.size：批次大小，默认16k 可以到32k
- linger.ms：等待时间，通常修改为5-100ms[如果这里修改的很大，会导致数据到Kafka集群延迟较大]
- compression.type：压缩snappy
- RecordAccumulator：缓冲区大小，修改为64Mb

参数测试代码如下：

```java
package kafka.chapter01;

import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerConfig;
import org.apache.kafka.clients.producer.ProducerRecord;
import org.apache.kafka.clients.producer.RecordMetadata;
import org.apache.kafka.common.serialization.StringSerializer;

import java.util.Properties;
import java.util.concurrent.Future;

public class CustomProducerParameters {

    public static void main(String[] args) throws InterruptedException {

        // 配置
        Properties properties = new Properties();
        properties.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG,"localhost:9092");
        // 指定KV的序列化类型
        properties.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

        //设置相关参数
        properties.put(ProducerConfig.BUFFER_MEMORY_CONFIG, 33554432);
        properties.put(ProducerConfig.BATCH_SIZE_CONFIG, 16384);
        properties.put(ProducerConfig.LINGER_MS_CONFIG, 1000*10);
        properties.put(ProducerConfig.COMPRESSION_TYPE_CONFIG, "snappy");


        // 创建生产者对象
        KafkaProducer<String, String> kafkaProducer = new KafkaProducer<String, String>(properties);
        // 发送数据
        for (int i = 0; i < 20; i++) {
            Future<RecordMetadata> send = kafkaProducer.send(new ProducerRecord<String, String>("f1", "newnewnew bacch in :"+i));
            System.out.println(i);
            Thread.sleep(2000);
        }
        // 关闭资源
        kafkaProducer.close();

    }
}

```

比如上述代码：[参数解读： properties.put(ProducerConfig.LINGER_MS_CONFIG, 1000*10);意味着10s发送一次，然后设置了Thread.sleep(2000); 两秒生成一个数据，刷新kafka集群会发现，数据10s刷一次，每次进入5条，其他参数可以进行测试。]



#### 3.6 数据可靠性 ACK应答级别

发送数据过程请看3.1发送原理：外部数据到Producer生产者，创建main线程，调用send方法，进行拦截器、序列化器、分区器，然后数据到缓存队列(一个分区一个队列)，sender线程会把缓存队列数据进行发送，然后到kafka集群，kafka集群进行应答acks(三种模式)：

- 0：生产者发送过来数据，不需要等数据落盘应答。
- 1：生产者发送过来数据，Leader收到数据后应答。
- -1/all：生产者发送过来数据，Leader和isr队列里面的所有节点收齐数据后应答。-1和all等价。

以下是ack=-1的可靠性分析：

![](image\Kafka_image\k_1_8.png)

可靠性总结：

- acks=0 生产者发送过来数据就不管了，可靠性差，效率高。
- acks=1 生产者发送完leader应答，可靠性中等，效率中等。
- acks=-1 生产者发送完，leader和ISR队列中的所有Follower应答，可靠性高，效率低。
- 生产环境中，【acks=0很少使用】，【acks=1一般用于传输普通日志，允许个别丢失，比如用户行为日志】，【acks=-1一般传输账务等对可靠性要求较高的场景】



数据重复分析：[场景：acks=-1，leader收到后并且ISR队列同步完成，但是应答时leader挂了，认为失败，选取新的leader后继续发送，导致数据重复。]



配置代码：

```java
// acks 默认是all
properties.put(ProducerConfig.ACKS_CONFIG, "1");
// 重试次数 默认是int的最大值
properties.put(ProducerConfig.RETRIES_CONFIG, 1000);
```



#### 3.7 数据去重

##### 3.7.1 数据传递语义

语义：

- **至少一次(At Least Once)** =:  [ACK=-1] and [分区副本>=2] + [ISR里应答的最小副本数量>=2]
- **最多一次(At MostOnce)** =:  [ACK=0]

至少一次可以保证数据不丢失，但是不能保证数据重复。

最多一次可以保证数据不重复，但是不能保证数据丢失。

**精确一次(Exactly Once)**，对于一些非常重要的信息，要求数据**既不能重复也不能丢失**。

kafka 0.11版本后，引入了重大特性：**幂等性和事务**。



##### 3.7.2 幂等性

> 幂等性是指：Producer不论向Broker发送多少次重复数据，Broker端都只会持久化一次，保证了不重复。

重复判断标准：<PID,Partition,SeqNumber> PID是Kafka每次重启都会分配一个新的，Partition表示分区号，SeqNumber是单调递增的。

幂等性只能保证的是单分区单会话内不重复。

使用：开启参数 `enable.idempotence=true` ,fasle为关闭，默认为true。



##### 3.7.3 生产者事务

> 针对上边，集群重启会产生新的PID，只能通过事务解决。开始事务，必须开启幂等性。事务底层依赖幂等性。

Producer使用事务前，必须自定义一个唯一的transactional.id，有了它，即使客户端挂掉了，重启后也能继续处理未完成的事务。



创建事务代码如下：

```java
package kafka.chapter01;


import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerConfig;
import org.apache.kafka.clients.producer.ProducerRecord;
import org.apache.kafka.clients.producer.RecordMetadata;
import org.apache.kafka.common.serialization.StringSerializer;

import java.util.Properties;
import java.util.concurrent.Future;

public class CustomProducerTranactions {
    public static void main(String[] args) {

        // 配置
        Properties properties = new Properties();
        properties.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG,"localhost:9092");
        // 指定KV的序列化类型
        properties.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        // 创建生产者对象
        KafkaProducer<String, String> kafkaProducer = new KafkaProducer<String, String>(properties);

        // 指定事务id
        properties.put(ProducerConfig.TRANSACTIONAL_ID_CONFIG, "0107_tranaction_1");
        // 事务
        kafkaProducer.initTransactions();
        kafkaProducer.beginTransaction();

        try{
            // 发送数据
            Future<RecordMetadata> send = kafkaProducer.send(new ProducerRecord<String, String>("test", "0107_02_zzzz"));
            // 提交事务
            //            int i = 1/0;
            kafkaProducer.commitTransaction();
        } catch (Exception e){
            System.out.println("Exception as :" + e);
            kafkaProducer.abortTransaction();
        } finally {
            kafkaProducer.close();
        }

    }
}

```



#### 3.8 数据有序



#### 3.9 数据乱序

kafka在1.x版本之前保证数据在单分区内有序，条件为：

- `max.in.flight.requests.per.connection=1` (不需要考虑是否开启幂等性)

kafka在1.x版本之后，条件如下：

- 未开启幂等性 `max.in.flight.requests.per.connection=1` 
- 开启幂等性 `max.in.flight.requests.per.connection` 需要设置为小于等于5。原因说明：因为在kafka1.x版本之后，启用幂等之后，kafka客户端会缓存producer发来的最近的5个request的元数据，无论如何都可以保证5个request的数据都是有序的。



### 4 Kafka Broker



#### 4.1 Kafka Broker工作流程

总体工作流程如下：



#### 4.2 节点退役和服役

新增 or 删除节点



## 2 外部系统集成



## 3 生产调优手册



## 4 源码解析







