

### 增量实时表 DLT
Delta Live Tables

#### 相关概念

* 增量实时表DLT ：一个数据管道 pipeline
	* 管道包含在 Python 或 SQL 中声明的 物化视图和流表
* DLT数据集：
	* Streaming table 流表: 对流式或增量数据处理的额外支持，每个数据处理一次
	* Materialized views 物化视图 :  live table ，支持任何查询结果的变化，任何管道更新，适合更新、删除或聚合。
	* views 视图 ：只缓存在管道里，可以用作中间表

For details on using Python and SQL to write source code for pipelines, see [Delta Live Tables SQL language reference](https://learn.microsoft.com/zh-cn/azure/databricks/delta-live-tables/sql-ref) and [Delta Live Tables Python language reference](https://learn.microsoft.com/zh-cn/azure/databricks/delta-live-tables/python-ref).

For more on pipeline settings and configurations, see [Configure pipeline settings for Delta Live Tables](https://learn.microsoft.com/zh-cn/azure/databricks/delta-live-tables/settings).

#### Python创建DLT

##### 定义代码
```python
# dlt包 定义了 DLT 相关函数
import dlt

# 创建 Materialized views，定义对数据源静态读取
# 创建 Streaming table，定义对数据源流失读取
# 这是两者的相同的语法规范
import dlt
@dlt.table(
  name="<name>",              # 可选名字，建议不写，默认为function-name
  comment="<comment>",        # 可选表注释  
  spark_conf={"<key>" : "<value", "<key" : "<value>"},         # 可选执行时的spark配置
  table_properties={"<key>" : "<value>", "<key>" : "<value>"}, # 可选 DLT表配置，官网说明
  path="<storage-location-path>",      # 可选表数据存储位置，默认存管道
  partition_cols=["<partition-column>", "<partition-column>"], # 可选分区列集合
  schema="schema-definition",          # 表schema，str或 Python StructType
  temporary=False)           # 临时表？ 默认false，保留元数据
@dlt.expect                  # 一个约束：出错时不处理，包含错误数据
@dlt.expect_or_fail          # 数据出错时，关闭execution
@dlt.expect_or_drop          # 出错删除错误数据
@dlt.expect_all              # 多个约束：
@dlt.expect_all_or_drop
@dlt.expect_all_or_fail
def <function-name>():       # 定义 dlt的函数
    return (<query>)         # 用 SparkSQL 返回一个 Dataset

# views 定义语法
import dlt

@dlt.view(
  name="<name>",
  comment="<comment>")
@dlt.expect
@dlt.expect_or_fail
@dlt.expect_or_drop
@dlt.expect_all
@dlt.expect_all_or_drop
@dlt.expect_all_or_fail
def <function-name>():
    return (<query>)
```

##### 配置项解析
[table_properties官网说明](https://learn.microsoft.com/zh-cn/azure/databricks/delta-live-tables/properties#--delta-live-tables-pipeline-configurations)
[@dlt.expect官网说明](https://learn.microsoft.com/zh-cn/azure/databricks/delta-live-tables/python-ref#--python-delta-live-tables-properties)

##### schema举例
```python
# 定义 schema
schema = StructType(
  [
    StructField("artist_id", StringType(), True),
    StructField("artist_lat", DoubleType(), True),
    StructField("artist_long", DoubleType(), True),
    StructField("artist_location", StringType(), True),
    StructField("artist_name", StringType(), True),
    StructField("duration", DoubleType(), True),
    StructField("end_of_fade_in", DoubleType(), True),
    StructField("key", IntegerType(), True),
    StructField("key_confidence", DoubleType(), True),
    StructField("loudness", DoubleType(), True),
    StructField("release", StringType(), True),
    StructField("song_hotnes", DoubleType(), True),
    StructField("song_id", StringType(), True),
    StructField("start_of_fade_out", DoubleType(), True),
    StructField("tempo", DoubleType(), True),
    StructField("time_signature", DoubleType(), True),
    StructField("time_signature_confidence", DoubleType(), True),
    StructField("title", StringType(), True),
    StructField("year", IntegerType(), True),
    StructField("partial_sequence", IntegerType(), True)
  ]
)


```

##### from DLT 举例

```python
# 导入必要包
import dlt
from pyspark.sql.functions import *
from pyspark.sql.types import *

# 抽取原始的点击的日志json数据
json_path = "/databricks-datasets/wikipedia-datasets/data-001/clickstream/raw-uncompressed-json/2015_2_clickstream.json"
@dlt.create_table(
  comment="The raw wikipedia clickstream dataset, ingested from /databricks-datasets."
)
def clickstream_raw():          
  return (
    spark.read.json(json_path)    # 读取的数据，dataset名为 clickstream_raw
  )

# 读取DLT表，并生成一个新DLT表
@dlt.table(
  comment="Wikipedia clickstream data cleaned and prepared for analysis."
)
@dlt.expect("valid_current_page_title", "current_page_title IS NOT NULL")
@dlt.expect_or_fail("valid_count", "click_count > 0")
def clickstream_prepared():
  return (
    dlt.read("clickstream_raw")
      .withColumn("click_count", expr("CAST(n AS INT)"))
      .withColumnRenamed("curr_title", "current_page_title")
      .withColumnRenamed("prev_title", "previous_page_title")
      .select("current_page_title", "click_count", "previous_page_title")
  )


# 执行分析
@dlt.table(
  comment="A table containing the top pages linking to the Apache Spark page."
)
def top_spark_referrers():
  return (
    dlt.read("clickstream_prepared")
      .filter(expr("current_page_title == 'Apache_Spark'"))
      .withColumnRenamed("previous_page_title", "referrer")
      .sort(desc("click_count"))
      .select("referrer", "click_count")
      .limit(10)

  )
```

##### from cloudFiles
```python
@dlt.table
def customers():
  return (
    spark.readStream.format("cloudFiles")
      .option("cloudFiles.format", "csv")
      .load("/databricks-datasets/retail-org/customers/")
  )

@dlt.table
def sales_orders_raw():
  return (
    spark.readStream.format("cloudFiles")
      .option("cloudFiles.format", "json")
      .load("/databricks-datasets/retail-org/sales_orders/")
  )
```

##### from 消息队列

message bus

```python
import dlt
@dlt.table
def kafka_raw():
  return (
    spark.readStream
      .format("kafka")
      .option("kafka.bootstrap.servers", "<server:ip>")
      .option("subscribe", "topic1")
      .option("startingOffsets", "latest")
      .load()
  )
```

##### from 外部系统
See [Interact with external data on Azure Databricks](https://learn.microsoft.com/zh-cn/azure/databricks/external-data/).
```python
import dlt
@dlt.table
def postgres_raw():
  return (
    spark.read
      .format("postgresql")
      .option("dbtable", table_name)
      .option("host", database_host_url)
      .option("port", 5432)
      .option("database", database_name)
      .option("user", username)
      .option("password", password)
      .load()
  )
```

##### from  静态小文件
```python
@dlt.table
def clickstream_raw():
  return (spark.read.format("json").load("/databricks-datasets/wikipedia-datasets/data-001/clickstream/raw-uncompressed-json/2015_2_clickstream.json"))
```

##### 使用账号安全读取数据

https://learn.microsoft.com/zh-cn/azure/databricks/delta-live-tables/load#--securely-access-storage-credentials-with-secrets-in-a-pipeline

##### 建DLT不存元数据
```python
@dlt.table(
  temporary=True)   # 设置为临时表
def temp_table():
  return ("...")
```

#### 从管道读取数据

```python
# 从管道读取数据集，两个方法
dlt.read("LIVE.customers")    # 使用  LIVE.name, name就是name或function_name
spark.table("sales.customers")    # 使用 database.name

# 流式读取
dlt.read_stream()
```


#### 管道触发更新 trigger update




### Auto loader 自动加载程序

* 以增量方式高效处理数据
* **数据源**：AWS S3 (`s3://`)、Azure Data Lake Storage Gen2 (ADLS Gen2, `abfss://`)、Google Cloud Storage (GCS, `gs://`)、Azure Blob 存储 (`wasbs://`)、ADLS Gen1 (`adl://`) 和 Databricks File System (DBFS, `dbfs:/`)
* **文件格式**支持  `JSON`、`CSV`、`PARQUET`、`AVRO`、`ORC`、`TEXT` 和 `BINARYFILE` 
* **使用 `cloudFiles` 的结构化流式处理源**
	* 给定云文件存储上的**输入目录路径**
	* `cloudFiles` 源将在新文件到达时自动处理
	* **检查点**中断的位置恢复，检查点可缩放键值存储 (RocksDB) 中
* 建议在**增量实时表**中使用，DLT同时支持 Python 和 SQL

##### 普通表使用 Auto loader

[auto-loader使用案例一](https://learn.microsoft.com/zh-cn/azure/databricks/getting-started/etl-quick-start#auto-loader)

```python
# import
from pyspark.sql.functions import input_file_name, current_timestamp
from pyspark.sql.types import DoubleType, IntegerType, StringType, StructType, StructField

# 定义需要的变量  
file_path = "/databricks-datasets/structured-streaming/events"     # 输入目录路径
username = spark.sql("SELECT regexp_replace(current_user(), '[^a-zA-Z0-9]', '_')").first()[0]
table_name = f"{username}_etl_quickstart"
checkpoint_path = f"/tmp/{username}/_checkpoint/etl_quickstart"    # 检查点

# 使用前清空数据，防止出错
spark.sql(f"DROP TABLE IF EXISTS {table_name}")
dbutils.fs.rm(checkpoint_path, True)

# 定义 schema
schema = StructType(
  [
    StructField("artist_id", StringType(), True),
    StructField("artist_lat", DoubleType(), True),
    StructField("artist_long", DoubleType(), True),
    StructField("artist_location", StringType(), True),
    StructField("artist_name", StringType(), True),
    StructField("duration", DoubleType(), True),
    StructField("end_of_fade_in", DoubleType(), True),
    StructField("key", IntegerType(), True),
    StructField("key_confidence", DoubleType(), True),
    StructField("loudness", DoubleType(), True),
    StructField("release", StringType(), True),
    StructField("song_hotnes", DoubleType(), True),
    StructField("song_id", StringType(), True),
    StructField("start_of_fade_out", DoubleType(), True),
    StructField("tempo", DoubleType(), True),
    StructField("time_signature", DoubleType(), True),
    StructField("time_signature_confidence", DoubleType(), True),
    StructField("title", StringType(), True),
    StructField("year", IntegerType(), True),
    StructField("partial_sequence", IntegerType(), True)
  ]
)


# 配置 Auto Loader 用来增量导入 JSON 数据到 Delta table
(spark.readStream                         # 读流
  .format("cloudFiles")                   # 使用cloudFiles结构化流式处理源
  .schema(schema)                         # schema
  .option("cloudFiles.format", "json")    # 指定文件格式
  .option("cloudFiles.schemaLocation", checkpoint_path)  # 指定检查点
  .load(file_path)                        # 指定输入目录路径
  .select("*", input_file_name().alias("source_file"), current_timestamp().alias("processing_time"))
  .writeStream                                           # 写流
  .option("checkpointLocation", checkpoint_path)         # 指定检查点
  .trigger(availableNow=True)                            # trigger
  .toTable(table_name))                                  # 目标表
```

若要将笔记本计划为任务，请执行以下操作：
1.  单击标题栏右侧的“计划”。
2.  为“作业名”输入唯一的名称。
3.  单击“手动”。
4.  在“群集”下拉列表中，选择在步骤 1 中创建的群集。
5.  单击“创建”。
6.  在出现的窗口中单击“立即运行”。
7.  若要查看作业运行结果，请单击“上次运行”时间戳旁边的![外部链接](https://learn.microsoft.com/zh-cn/azure/databricks/_static/images/icons/external-link.png)图标。

```python
df = spark.read.table(table_name)    # 读取刚刚写入的表
display(df)                          # 显示
```

#### DLT 使用 Auto loader

##### 文件格式选项
一些格式需要设置选项，如csv设不设置header,分割符等
* [`JSON` 选项](https://learn.microsoft.com/zh-cn/azure/databricks/ingestion/auto-loader/options#json-options)
* [ `CSV` 选项](https://learn.microsoft.com/zh-cn/azure/databricks/ingestion/auto-loader/options#csv-options)
* [ `PARQUET` 选项](https://learn.microsoft.com/zh-cn/azure/databricks/ingestion/auto-loader/options#parquet-options)
* [ `AVRO` 选项](https://learn.microsoft.com/zh-cn/azure/databricks/ingestion/auto-loader/options#avro-options)
* [ `TEXT` 选项](https://learn.microsoft.com/zh-cn/azure/databricks/ingestion/auto-loader/options#text-options)
* [ `ORC` 选项](https://learn.microsoft.com/zh-cn/azure/databricks/ingestion/auto-loader/options#orc-options)
* [ 特定于 Azure 的选项](https://learn.microsoft.com/zh-cn/azure/databricks/ingestion/auto-loader/options#azure-specific-options)

```python
# 使用文件格式选项
@dlt.table
def wiki_raw():
  return (
    spark.readStream.format("cloudFiles")
      .schema("title STRING, id INT, revisionId INT, revisionTimestamp TIMESTAMP, revisionUsername STRING, revisionUsernameId INT, text STRING")
      .option("cloudFiles.format", "parquet")
      .load("/databricks-datasets/wikipedia-datasets/data-001/en_wikipedia/articles-only-parquet")
  )
```
```sql
CREATE OR REFRESH STREAMING LIVE TABLE wiki_raw
AS SELECT *
  FROM cloud_files(
    "/databricks-datasets/wikipedia-datasets/data-001/en_wikipedia/articles-only-parquet",
    "parquet",
    map("schema", "title STRING, id INT, revisionId INT, revisionTimestamp TIMESTAMP, revisionUsername STRING, revisionUsernameId INT, text STRING")
  )
```


```python
# 全量导入，不设置选项
@dlt.table
def customers():
  return (
    spark.readStream.format("cloudFiles")
      .option("cloudFiles.format", "csv")
      .load("/databricks-datasets/retail-org/customers/")
  )

@dlt.table
def sales_orders_raw():
  return (
    spark.readStream.format("cloudFiles")
      .option("cloudFiles.format", "json")
      .load("/databricks-datasets/retail-org/sales_orders/")
  )
```
```SQL
CREATE OR REFRESH STREAMING LIVE TABLE customers
AS SELECT * FROM cloud_files("/databricks-datasets/retail-org/customers/", "csv")

CREATE OR REFRESH STREAMING LIVE TABLE sales_orders_raw
AS SELECT * FROM cloud_files("/databricks-datasets/retail-org/sales_orders/", "json")
```



### 根据源数据变化更新表 CDC

[官网说明](https://learn.microsoft.com/zh-cn/azure/databricks/delta-live-tables/cdc)

```python
dlt.apply_changes(
  target = "<target-table>",
  source = "<data-source>",
  keys = ["key1", "key2", "keyN"],
  sequence_by = "<sequence-column>",
  ignore_null_updates = False,
  apply_as_deletes = None,
  apply_as_truncates = None,
  column_list = None,
  except_column_list = None,
  stored_as_scd_type = <type>,
  track_history_column_list = None,
  track_history_except_column_list = None
)
```