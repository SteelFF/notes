
[微软文档](https://learn.microsoft.com/en-us/azure/hdinsight/hdinsight-overview)
[世纪互联文档](https://docs.azure.cn/zh-cn/hdinsight/)

## 概述

借助 HDInsight，可以在 Azure 环境中使用开源框架，例如 Apache Spark、Apache Hive、LLAP、Apache Kafka、Hadoop 等

## 安装群集
* [门户创建集群](https://docs.azure.cn/zh-cn/hdinsight/hdinsight-hadoop-create-linux-clusters-portal)
* [创建 Apache Hadoop 群集](https://docs.azure.cn/zh-cn/hdinsight/hadoop/apache-hadoop-linux-create-cluster-get-started-portal)
* [创建 Apache Spark 群集](https://docs.azure.cn/zh-cn/hdinsight/spark/apache-spark-jupyter-spark-sql-use-portal)
* [创建 Apache HBase 群集](https://docs.azure.cn/zh-cn/hdinsight/hbase/quickstart-resource-manager-template)
* [创建交互式查询群集](https://docs.azure.cn/zh-cn/hdinsight/interactive-query/quickstart-resource-manager-template)
* [创建 Apache Kafka 群集](https://docs.azure.cn/zh-cn/hdinsight/kafka/apache-kafka-get-started)

## 使用Spark集群
#### 群集体系结构
![[Pasted image 20230327091913.png|275]]
SparkContext 连接到 Spark 主节点，负责将应用程序转换为各个任务的有向图 (DAG)。 在工作器节点上的执行程序进程内执行的任务。 每个应用程序都有其自己的执行程序进程。 这些进程在整个应用程序期间保持运行，并在多个线程中运行任务。

#### 使用集群访问 Blob中的文件
[官网](https://docs.azure.cn/zh-cn/hdinsight/hdinsight-hadoop-create-linux-clusters-portal)
```shell
wasb://<containername>@<accountname>.blob.core.chinacloudapi.cn/<file.path>/ wasbs://<containername>@<accountname>.blob.core.chinacloudapi.cn/<file.path>/

wasb:///<file.path>/  # 群集根的路径
wasbs:///<file.path>/

/<file.path>/   # 文件的相对路径
```

举例
```sh
# 本地存储上创建一个文件
touch testFile.txt  
# 群集存储上创建目录
hdfs dfs -mkdir wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.chinacloudapi.cn/sampledata1/
hdfs dfs -mkdir wasbs:///sampledata2/
hdfs dfs -mkdir /sampledata3/
# 数据从本地存储复制到群集存储
hdfs dfs -copyFromLocal testFile.txt  wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.chinacloudapi.cn/sampledata1/
hdfs dfs -copyFromLocal testFile.txt  wasbs:///sampledata2/
hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
# 列出群集存储上的目录内容
hdfs dfs -ls wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.chinacloudapi.cn/sampledata1/
hdfs dfs -ls wasbs:///sampledata2/
hdfs dfs -ls /sampledata3/
```
```SQL
-- 创建 Hive 表
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.chinacloudapi.cn/example/data/';
LOCATION 'wasbs:///example/data/';   -- 任意一种 loaction 都可以
LOCATION '/example/data/';


```
https://docs.azure.cn/zh-cn/hdinsight/hadoop/apache-hadoop-use-hive-beeline
 
[https://docs.microsoft.com/en-us/azure/hdinsight/hadoop/apache-hadoop-use-hive-beeline](https://docs.microsoft.com/en-us/azure/hdinsight/hadoop/apache-hadoop-use-hive-beeline)

[https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-un](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)


[https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)

[https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-administer-use-portal-linux#change-passwords](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-administer-use-portal-linux#change-passwords)

## Spark数据流式处理
 Spark 流式处理是一个长时间运行的作业，接收各种来源（包括 Azure 事件中心）的输入数据。
 ![[Pasted image 20230327092214.png|375]]
#### 使用举例
[Spark流式处理指南](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
```python
# env
import org.apache.spark._
import org.apache.spark.streaming._
val ssc = new StreamingContext(sc, Seconds(1))
# create dstream
val lines = ssc.textFileStream("/uploads/Test/")
# dstream transformations
val words = lines.flatMap(_.split(" "))
val pairs = words.map(word => (word, 1))
val wordCounts = pairs.reduceByKey(_ + _)
# dstream sink
wordCounts.print()
# app start
ssc.start()
ssc.awaitTermination()
```

```scala
// 自定义数据源
class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

    /** Start the thread that simulates receiving data  模拟接收数据 */
    def onStart() {
        new Thread("Dummy Source") { override def run() { receive() } }.start()
    }

    def onStop() {  }

    /** Periodically generate a random number from 0 to 9, and the timestamp */
    private def receive() {
        var counter = 0  
        while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)  // 5s生成一次数据
        }
    }
}

// A batch is created every 30 seconds  批处理间隔为 30 秒
val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

// Set the active SQLContext so that we can access it statically within the foreachRDD
org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

// 键流
val stream = ssc.receiverStream(new DummySource())

// 每次处理一批次的数据 30s的数据
stream.foreachRDD { rdd =>
    // Access the SQLContext and create a table called demo_numbers we can query
    val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
    _sqlContext.createDataFrame(rdd).toDF("value", "time")
        .registerTempTable("demo_numbers")
}

// Start the stream processing
ssc.start()
```
```SQL
-- 定期查询 DataFrame 来查看批处理中存在的当前值集
%%sql
SELECT * FROM demo_numbers
```

#### 滑动窗口

[官网](https://docs.azure.cn/zh-cn/hdinsight/spark/apache-spark-streaming-overview#sliding-windows)
https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#window-operations
```scala
// 窗口案例
class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

    /** Start the thread that simulates receiving data */
    def onStart() {
        new Thread("Dummy Source") { override def run() { receive() } }.start()
    }

    def onStop() {  }

    /** Periodically generate a random number from 0 to 9, and the timestamp */
    private def receive() {
        var counter = 0  
        while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
        }
    }
}

// A batch is created every 30 seconds
val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

// Set the active SQLContext so that we can access it statically within the foreachRDD
org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

// Create the stream
val stream = ssc.receiverStream(new DummySource())

// Process batches in 1 minute windows
stream.window(org.apache.spark.streaming.Minutes(1)).foreachRDD { rdd =>

    // Access the SQLContext and create a table called demo_numbers we can query
    val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
    _sqlContext.createDataFrame(rdd).toDF("value", "time")
    .registerTempTable("demo_numbers")
}

// Start the stream processing
ssc.start()
```

#### 检查点
https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#checkpointing

#### Spark 结构化流
 每当生成新数据时，可以刷新结果表中的数据，使之包含自开始执行流查询以来生成的所有输出数据（完整模式），或者只包含自上次处理查询以来生成的新数据（追加模式）。
```json
// 存储在 HDInsight 群集容器下的 `temps` 子文件夹
{"time":1469501107,"temp":"95"}
{"time":1469501147,"temp":"95"}
{"time":1469501202,"temp":"95"}
{"time":1469501219,"temp":"95"}
{"time":1469501225,"temp":"95"}
```
```scala
import org.apache.spark.sql.types._
import org.apache.spark.sql.functions._

// 路径确定
val inputPath = "/temps/" 
//定义 JSON Schema
val jsonSchema = new StructType().add("time", TimestampType).add("temp", StringType)

// 调用 readStream 读取对应位置 并设置 schema配置 创建输入流
val streamingInputDF = spark.readStream.schema(jsonSchema).json(inputPath)
// 流式查询
val streamingAggDF = streamingInputDF.groupBy(window($"time", "1 hour")).agg(min($"temp"), avg($"temp"), max($"temp"))
// 启动查询
val query = streamingOutDF.start()
```
```SQL
-- 同一个 SparkSession ，可以针对存储查询结果的 `temps` 表运行 SparkSQL 查询。
select * from temps
```

#### exactly-once
[官网说明](https://docs.azure.cn/zh-cn/hdinsight/spark/apache-spark-streaming-exactly-once)
##### 使用预写日志
```shell
`spark.streaming.receiver.writeAheadLog.enable` = true
```
###### 检查点
```scala
val ssc = new StreamingContext(spark, Seconds(1))
ssc.checkpoint("/path/to/checkpoints") //指定检查点保存位置

val lines = ssc.socketTextStream("hostname", 9999)
lines.checkpoint(30)  //指定检查点间隔
ssc.start()
ssc.awaitTermination()
```
##### repalyable sources
```shell
# 系统发生故障，则源必须再次提供同一消息。
#    Azure Event Hubs
#    HDInsight 上的 Apache Kafka
```
##### reliable-receivers
```shell
# 接收器发生故障并随后重启，它可以从上次中断的位置继续拾取数据
# Event Hubs和 Kafka 等源具有可靠的接收器
```