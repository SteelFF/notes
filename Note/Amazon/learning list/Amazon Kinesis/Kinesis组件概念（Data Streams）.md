介绍组件概念、与Kafka的对比以及个人理解

###### 主题

- [[#数据记录 (Record)]]
- [[#数据流 (Stream)]]
- [[#数据分片 (Shard)]]
- [[#生产者 (Producer)]]
- [[#消费者 (Consumer)]]
- [[#消费者组 (Consumer Group)]]

## 数据记录 (Record)
Kinesis Data Streams 中的最小数据单元，通常是一个 <font color="red"> JSON 对象</font> ，包含一条记录的所有数据。
（类似于Kafka中的消息Message，不同的是记录通常为JSON对象，而Kafka中的消息Message通常为键值对和一些元数据）
 
## 数据流 (Stream)
一个持续不断的、时间序列的数据记录流。数据流由多个数据分片组成，可以包含数百万个数据记录。（类似于Kafka中的主题Topic）
 
## 数据分片 (Shard)
一个数据流的分片，是一个独立的、有序的数据记录序列。每个数据分片都有一个唯一的标识符，并且可以独立处理和存储数据记录。

数据分片 (Shard) 是 Kinesis Data Streams 中的一个重要概念，用于将数据流 <font color="red">水平分割</font> 成多个独立的、有序的数据记录序列。每个数据分片都有一个唯一的标识符，并且可以独立处理和存储数据记录。数据分片的数量和大小可以根据数据流量和业务需求进行动态调整，以满足不同的数据处理和分析需求。

类似于kafka当中的分区Partition概念

## 生产者 (Producer)
一个向数据流中写入数据记录的应用程序或服务。

## 消费者 (Consumer)
一个从数据流中读取数据记录的应用程序或服务。

## 消费者组 (Consumer Group)
一个由多个消费者组成的逻辑组，每个消费者组都可以独立地读取数据流中的数据记录。