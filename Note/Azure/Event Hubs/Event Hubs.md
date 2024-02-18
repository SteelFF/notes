[微软官方文档](https://learn.microsoft.com/zh-cn/azure/event-hubs/event-hubs-about)
[世纪互联官方文档](https://docs.azure.cn/zh-cn/event-hubs/)


## 概述

* 完全托管
* 支持实时处理和批处理
	* 可以与 Functions 无缝集成
* 捕获事件数据
	* 导入Event hubs 中的数据可以被 Blob 捕获，好备份
* 可缩放
	* 自动根据数据流，调整吞吐量
* 高级层和专用层
	* 保证苛刻的数据处理需求

## Event Hubs 相关概念

#### 体系结构组件

-   **事件生成者**：向事件中心发送数据的所有实体。 可用 **HTTPS、AMQP 1.0 或 Apache Kafka** 发布事件。
-   **分区**：每个使用者只读取消息流的特定子集或分区。
-   **使用者组**： 类似消费者组
-   [吞吐量单位（标准层）](https://docs.azure.cn/zh-cn/event-hubs/event-hubs-scalability#throughput-units)或[处理单位（高级层）](https://docs.azure.cn/zh-cn/event-hubs/event-hubs-scalability#processing-units)或[容量单位（专用）](https://docs.azure.cn/zh-cn/event-hubs/event-hubs-dedicated-overview)：预先购买的容量单位，用于控制事件中心的吞吐量容量。
-   **事件接收者**：从事件中心读取事件数据的所有实体。
  ![[Pasted image 20230323162112.png|575]]

#### Event Hubs 与 Kafka
| Kafka概念      | Event Hubs概念 |
| -------------- | -------------- |
| 集群           | namespace      |
| topic          | 事件中心       |
| 分区           | 分区           |
| consumer group | consumer group |
| offset         | offset         |


## 创建Event Hubs

* [Azure 门户创建可视化创建](https://docs.azure.cn/zh-cn/event-hubs/event-hubs-create)
* [Azure CLI 创建](https://docs.azure.cn/zh-cn/event-hubs/event-hubs-quickstart-cli#set-the-subscription-context)
* [Azure PowerShell 创建](https://docs.azure.cn/zh-cn/event-hubs/event-hubs-quickstart-powershell)
* [Bicep 创建](https://docs.azure.cn/zh-cn/event-hubs/event-hubs-bicep-namespace-event-hub?tabs=CLI)

## 使用Python发送和接收事件

#### 事前准备

**==1 前提==**
* 世纪互联 Azure 订阅
* Python 3.7 或更高版本，pip且已经更新
* IDE （推荐Visual Studio Code）
* 创建事件中心命名空间和事件中心

**2 安装发送事件需要的包**
```shell
# 推荐的无密码模式
pip install azure-eventhub
pip install azure-identity
pip install aiohttp
```

[**3 向 Azure 验证应用**](https://docs.azure.cn/zh-cn/event-hubs/event-hubs-python-get-started-send?tabs=passwordless%2Croles-azure-portal#authenticate-the-app-to-azure)

#### 发送事件
```python
# 无密码的发送方式
# `EVENT_HUB_FULLY_QUALIFIED_NAMESPACE`  命名空间 集群
# `EVENT_HUB_NAME`                       世界中心 主题

import asyncio
from azure.eventhub import EventData
from azure.eventhub.aio import EventHubProducerClient
from azure.identity import DefaultAzureCredential

EVENT_HUB_FULLY_QUALIFIED_NAMESPACE = "EVENT_HUB_FULLY_QUALIFIED_NAMESPACE"
EVENT_HUB_NAME = "EVENT_HUB_NAME"
credential = DefaultAzureCredential()

async def run():
    producer = EventHubProducerClient(
        fully_qualified_namespace=EVENT_HUB_FULLY_QUALIFIED_NAMESPACE,
        eventhub_name=EVENT_HUB_NAME,
        credential=credential,
    )
    # 异步发送
    async with producer:
        # 创建一个 batch
        event_data_batch = await producer.create_batch()

        # 向 batch 中添加 event 数据
        event_data_batch.add(EventData("First event "))
        event_data_batch.add(EventData("Second event"))
        event_data_batch.add(EventData("Third event"))

        # 异步向 EVENT_HUB_NAME 主题中发送数据
        await producer.send_batch(event_data_batch)

        # 超时关闭认证
        await credential.close()

asyncio.run(run())
```

[使用连接字符串以异步方式将发送事件 ](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py)。

#### 接收事件

使用 Azure Blob 存储作为检查点存储，方便故障恢复

[**1 创建 Azure 存储帐户和 Blob 容器**](https://docs.azure.cn/zh-cn/event-hubs/event-hubs-python-get-started-send?tabs=passwordless%2Croles-azure-portal#create-an-azure-storage-account-and-a-blob-container)

**2 安装收事件的包**
```shell
pip install azure-eventhub-checkpointstoreblob-aio 
pip install azure-identity
```

**3 编写py脚本**
```python
# `BLOB_STORAGE_ACCOUNT_URL`               Blob account url
# `BLOB_CONTAINER_NAME`                    Blob 容器名
# `EVENT_HUB_FULLY_QUALIFIED_NAMESPACE`    namespace
# `EVENT_HUB_NAME`                         event hub
import asyncio
from azure.eventhub.aio import EventHubConsumerClient
from azure.eventhub.extensions.checkpointstoreblobaio import (
    BlobCheckpointStore,
)
from azure.identity.aio import DefaultAzureCredential

BLOB_STORAGE_ACCOUNT_URL = "BLOB_STORAGE_ACCOUNT_URL"
BLOB_CONTAINER_NAME = "BLOB_CONTAINER_NAME"
EVENT_HUB_FULLY_QUALIFIED_NAMESPACE = "EVENT_HUB_FULLY_QUALIFIED_NAMESPACE"
EVENT_HUB_NAME = "EVENT_HUB_NAME"

credential = DefaultAzureCredential()

async def on_event(partition_context, event):
    # 输出打印数据
    print(
        'Received the event: "{}" from the partition with ID: "{}"'.format(
            event.body_as_str(encoding="UTF-8"), partition_context.partition_id
        )
    )
    # 更新检查点，防止下次重复读 event
    await partition_context.update_checkpoint(event)

async def main():
    # 创建 blob 检查点存储
    checkpoint_store = BlobCheckpointStore(
        blob_account_url=BLOB_STORAGE_ACCOUNT_URL,
        container_name=BLOB_CONTAINER_NAME,
        credential=credential,
    )

    # 创建消费者客户端
    client = EventHubConsumerClient(
        fully_qualified_namespace=EVENT_HUB_FULLY_QUALIFIED_NAMESPACE,
        eventhub_name=EVENT_HUB_NAME,
        consumer_group="$Default",
        checkpoint_store=checkpoint_store,
        credential=credential,
    )
    async with client:
        # 调用接收event 方法. 并从分区头开始接收 : (starting_position: "-1")
        await client.receive(on_event=on_event, starting_position="-1")

    # 超时关闭身份认证
    await credential.close()

if __name__ == "__main__":   # 运行方法
    asyncio.run(main())
```

### 运行脚本

```shell
# 先运行消费者
python recv.py

# 再运行生产者
python send.py

# 可以在消费者端看到数据信息
```

### python 官方案例大全

https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/eventhub/azure-eventhub/samples/sync_samples

## 捕获 Event Hubs 的事件

按指定的**时间间隔**或**大小间隔**将 Event Hubs 中的流数据自动捕获到 [Azure Blob 存储](https://azure.microsoft.com/services/storage/)中。 Azure Data Lake Store也可以。
* 捕获数据以 Apache Arvo 格式写入

#### 创建Event Hubs时启用捕获

1. 在命名空间的“概述”页面上，选择命令栏上的“+ 事件中心”。
   ![[Pasted image 20230323174228.png|500]]
   
2. 在“创建事件中心”页上，键入事件中心的名称，然后选择页面底部的“下一步: 捕获”。
   ![[Pasted image 20230323174306.png|500]]
   
3. 在“捕获”选项卡上，针对“捕获”选择“启用”。
4. 拖动滑块以设置“时间范围”（以分钟为单位）。 默认时间窗口为 5 分钟。 最小值为 1，最大值为 15。
5. 拖动滑块以设置“大小范围”(MB)。 默认值为 300 MB。 最小值为 10 MB，最大值为 500 MB。
6. 指定是否希望事件中心在捕获时间段内未发生任何事件时，发出空文件。
7. [将数据捕获到 Azure 存储](https://docs.azure.cn/zh-cn/event-hubs/event-hubs-capture-enable-through-portal#capture-data-to-azure-storage)




## Spark连接到Event Hubs

参考资料
* [Structured Streaming + Event Hubs Integration Guide for PySpark](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/PySpark/structured-streaming-pyspark.md#table-of-contents)
* [将 Apache Spark 应用程序与 Azure 事件中心连接](https://docs.azure.cn/zh-cn/event-hubs/event-hubs-kafka-spark-tutorial)
* [获取事件中心连接字符串](https://docs.azure.cn/zh-cn/event-hubs/event-hubs-get-connection-string)
* [从策略生成签名（令牌）](https://learn.microsoft.com/zh-cn/azure/event-hubs/authenticate-shared-access-signature#generating-a-signaturetoken-from-a-policy)

#### Maven 入库
```
  groupId = com.microsoft.azure
  artifactId = azure-eventhubs-spark_2.11
  version = 2.3.22
or
  groupId = com.microsoft.azure
  artifactId = azure-eventhubs-spark_2.12
  version = 2.3.22
```

#### 连接字符串准备
```shell
# Namespace 的连接字符串
Endpoint=sb://<NamespaceName>.servicebus.chinacloudapi.cn/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>

# EventHub 的连接字符串
Endpoint=sb://<NamespaceName>.servicebus.chinacloudapi.cn/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>;EntityPath=<EventHubName>

# 连接字符串
Endpoint=sb://{NamespaceName}.servicebus.windows.net/{EventHubName};EntityPath={EventHubName};SharedAccessKeyName={KeyName};SharedAccessKey={KeyValue}


# <NamespaceName>  <EventHubName>  名字
# KeyName  KeyValue   共享访问密钥的名称 和 值
```

### Event Hubs配置

[其他可选配置](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/PySpark/structured-streaming-pyspark.md#event-hubs-configuration)
```python
connectionString = "连接字符串"

# 使用连接字符串
# For 2.3.15 version and above
ehConf['eventhubs.connectionString'] = sc._jvm.org.apache.spark.eventhubs.EventHubsUtils.encrypt(connectionString)

# Before azure-eventhubs-spark_2.12 2.3.15
# ehConf['eventhubs.connectionString'] = connectionString   

# 设置消费者组
ehConf['eventhubs.consumerGroup'] = "消费者组"
```
##### 消费位置设置
[官方说明](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/PySpark/structured-streaming-pyspark.md#event-position)
![[Pasted image 20230324175209.png|500]]
```python
from datetime import datetime as dt
import json

# Start from beginning of stream
startOffset = "-1"

# End at the current time. This datetime formatting creates the correct string format from a python datetime object
endTime = dt.now().strftime("%Y-%m-%dT%H:%M:%S.%fZ")


# Create the positions
startingEventPosition = {
  "offset": startOffset,  
  "seqNo": -1,            #not in use
  "enqueuedTime": None,   #not in use
  "isInclusive": True
}

endingEventPosition = {
  "offset": None,           #not in use
  "seqNo": -1,              #not in use
  "enqueuedTime": endTime,
  "isInclusive": True
}


# Put the positions into the Event Hub config dictionary
ehConf["eventhubs.startingPosition"] = json.dumps(startingEventPosition)
ehConf["eventhubs.endingPosition"] = json.dumps(endingEventPosition)
```

##### 接收超时和操作超时
```python
# Setting the receiver timeout to 200 seconds
receiverTimeoutDuration = datetime.time(0,3,20).strftime("PT%HH%MM%SS") #200 seconds
ehConf["eventhubs.receiverTimeout"] = receiverTimeoutDuration

# Setting the receiver timeout to 60 seconds
operationTimeoutDuration = datetime.time(0,1,0).strftime("PT%HH%MM%SS") #60 seconds
ehConf["eventhubs.operationTimeout"] = operationTimeoutDuration
```

### 从Event Hubs 读取数据
```python
# 读流数据
connectionString = "YOUR.CONNECTION.STRING"
ehConf = {
  'eventhubs.connectionString' : connectionString
}

df = spark \
  .readStream \
  .format("eventhubs") \
  .options(**ehConf) \
  .load()


# 读批数据
connectionString = "YOUR.CONNECTION.STRING"
ehConf = {
  'eventhubs.connectionString' : connectionString
}

df = spark \
  .read \
  .format("eventhubs") \
  .options(**ehConf) \
  .load()
df = df.withColumn("body", df["body"].cast("string"))
```
### 向 Event Hubs 写数据

```python
writeConnectionString = "YOUR.EVENTHUB.NAME"
ehWriteConf = {
  'eventhubs.connectionString' : writeConnectionString
}

# 写入 Event Hubs. Events are distributed across partitions using round-robin model.
ds = df \
  .select("body") \
  .writeStream \                   # write 就是批写入， writeStream 就是写流
  .format("eventhubs") \
  .options(**ehWriteConf) \
  .option("checkpointLocation", "///output.txt") \
  .start()

# 指定分区键，写入Event Hubs
ds = df \
  .selectExpr("partitionKey", "body") \
  .writeStream \
  .format("eventhubs") \
  .options(**ehWriteConf) \
  .option("checkpointLocation", "///output.txt") \
  .start()
```


https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/PySpark/structured-streaming-pyspark.md#recovering-from-failures-with-checkpointing