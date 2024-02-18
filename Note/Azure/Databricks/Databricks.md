
1.  如何在本地连接到databricks，及本地上传文件命令([文档](https://willvelida.medium.com/installing-configuring-and-using-the-azure-databricks-cli-5d0381e662a1)

## Databricks 支持读写的文件格式

[官网文档](https://learn.microsoft.com/zh-cn/azure/databricks/external-data/#what-data-formats-can-you-use-in-azure-databricks)

-   [DLT 增量实时表 Delta Lake](obsidian://open?vault=note&file=BigData%2FCloud%20services%2FAzure%2FDatabricks%2FAuto%20loader%20%26%20DLT)

-   [Parquet](https://learn.microsoft.com/zh-cn/azure/databricks/external-data/parquet)
```python
df=spark.read.parquet('python/test_support/sql/parquet_partitioned')
df.dtypes

df.write.parquet(os.path.join(tempfile.mkdtemp(), 'data'))
```
-   [ORC](https://learn.microsoft.com/zh-cn/azure/databricks/external-data/orc)
```python
df = spark.read.orc('python/test_support/sql/orc_partitioned')
df.dtypes

orc_df = spark.read.orc('python/test_support/sql/orc_partitioned')
orc_df.write.orc(os.path.join(tempfile.mkdtemp(), 'data'))
```
-   [JSON](https://learn.microsoft.com/zh-cn/azure/databricks/external-data/json)
```python
df1 = spark.read.json('python/test_support/sql/people.json')
df1.dtypes
rdd = sc.textFile('python/test_support/sql/people.json')
df2 = spark.read.json(rdd)
df2.dtypes

df.write.json(os.path.join(tempfile.mkdtemp(), 'data'))
```
-   [CSV](https://learn.microsoft.com/zh-cn/azure/databricks/external-data/csv)
```python
df = spark.read.csv('python/test_support/sql/ages.csv')
df.dtypes
rdd = sc.textFile('python/test_support/sql/ages.csv')
df2 = spark.read.csv(rdd)
df2.dtypes

df.write.csv(os.path.join(tempfile.mkdtemp(), 'data'))
```
-   [Avro](https://learn.microsoft.com/zh-cn/azure/databricks/external-data/avro)

-   [文本](https://learn.microsoft.com/zh-cn/azure/databricks/external-data/text)
```python
df = spark.read.text('python/test_support/sql/text-test.txt')
df.collect()
df = spark.read.text('python/test_support/sql/text-test.txt', wholetext=True)
df.collect()

DataFrameWriter.text(
		_path: str_, 
		_compression: Optional[str] = None_, 
		_lineSep: Optional[str] = None_
	) → None[[source]]
```
-   [二进制](https://learn.microsoft.com/zh-cn/azure/databricks/external-data/binary)
```python
df = spark.read.format("binaryFile").load("<path-to-image-dir>")
display(df)    
```

##  连接 Event Hubs

[官方文档](https://docs.azure.cn/zh-cn/event-hubs/event-hubs-kafka-spark-tutorial)  ：将 Spark 应用程序连接到事件中心进行实时流式处理。

#### 前提准备

开始本教程前，请确保具备：
-   Azure 订阅。 如果没有，请[创建一个试用版订阅](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。
-   [Apache Spark v2.4](https://spark.apache.org/downloads.html)
-   [Apache Kafka v2.0](https://kafka.apache.org/20/documentation.html)
-   [Git](https://www.git-scm.com/downloads)

#### 创建事件中心命名空间

#### 克隆示例项目
[git案例](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark/)
```shell
# 克隆 Azure 事件中心存储库并导航到 `tutorials/spark` 子文件夹
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/spark
```

#### 从 Event Hubs 中读写事件
```scala
// 从 Event Hub 读流
val df = spark.readStream
    .format("kafka")
    .option("subscribe", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("kafka.request.timeout.ms", "60000")
    .option("kafka.session.timeout.ms", "30000")
    .option("kafka.group.id", GROUP_ID)
    .option("failOnDataLoss", "true")
    .load()

// 写流
val df_write = df.writeStream
    .outputMode("append")
    .format("console")
    .start()

// 向 Event Hubs 中写流
df = /**Dataframe**/

//Write to your Event Hub!
df.writeStream
    .format("kafka")
    .option("topic", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("checkpointLocation", "./checkpoint")
    .start()
    
```


## 连接 Blob 存储

Blob 需要升级 Azure Data Lake Storage Gen2
[databricks访问data lake storage gen2](https://learn.microsoft.com/zh-cn/azure/databricks/storage/azure-storage)



## 连接 Cosmos DB

Azure Databricks 从 Azure Cosmos DB 读取数据或将数据写入 Azure Cosmos DB。
对于 Cosmos DB for MongoDB API，使用 [MongoDB Spark 连接器](https://docs.mongodb.com/spark-connector/master/)

[Databricks连接Cosmos](obsidian://open?vault=note&file=BigData%2FCloud%20services%2FAzure%2FCosmosDB%2FNoSQL%E5%BC%80%E5%8F%91)




