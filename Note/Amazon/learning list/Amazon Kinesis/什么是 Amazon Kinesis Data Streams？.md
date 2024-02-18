您可以使用 Amazon Kinesis Data Streams 收集和处理大数据流。[流](http://www.amazonaws.cn/streaming-data/)实时的数据记录。可以创建称为 “数据处理应用程序”（称_Kinesis Data Streams 应用程序_. 典型的 Kinesis Data Streams 应用程序从_数据流_作为数据记录。这些应用程序可使用 Kinesis 客户端库，并且可在 Amazon EC2 实例上运行。您可以将处理后的记录发送到控制面板，使用这些记录生成警报、动态更改定价和广告战略或将数据发送给多种其他控制面板。Amazon服务。有关 Kinesis Data Streams 功能和定价的信息，请参阅[Amazon Kinesis Data Streams](http://www.amazonaws.cn/kinesis/streams/).

与 Kinesis Data Streams 和 Kinesis Data Streams 都是 Kinesis 流数据平台的一部分。[Kinesis Data Firehose](https://docs.amazonaws.cn/firehose/latest/dev/)、[Kinesis Video Streams](https://docs.amazonaws.cn/kinesisvideostreams/latest/dg/), 和[Kinesis Data Analytics](https://docs.amazonaws.cn/kinesisanalytics/latest/dev/).

有关 的更多信息Amazon大数据解决方案，请参阅[开启大数据Amazon](http://www.amazonaws.cn/big-data/). 有关 的更多信息Amazon流数据解决方案，请参阅[什么是流数据？](http://www.amazonaws.cn/streaming-data/).

###### 主题

- [[#可以使用 Kinesis Data Streams 做什么？]]
- [[#使用 Kinesis Data Streams 的好处]]
- [[#相关服务]]

## 可以使用 Kinesis Data Streams 做什么？

您可以使用 Kinesis Data Streams 进行快速而持续的数据引入和聚合。使用的数据类型可以包括 IT 基础设施日志数据、应用程序日志、社交媒体、市场数据源和 Web 点击流数据。由于数据引入和处理的响应时间是实时的，因此处理通常是轻量级的。

以下是使用 Kinesis Data Streams 的典型场景：

加速的日志和数据源引入和处理

您可以让创建者直接将数据推入流。例如，推送系统和应用程序日志，它们可在几秒内就绪，以用于处理。这可以防止因前端或应用程序服务器失败而造成日志数据丢失。因为您在提交数据以备引入之前，未在服务器上批处理数据，因此 Kinesis Data Streams 提供加速的数据源引入。

- 实时指标和报告

您可以将数据收集到 Kinesis Data Streams 中的数据用于实时的简单数据分析和报告。例如，您的数据处理应用程序可以处理系统和应用程序日志的指标和报告，因为数据将流入而不是等待接收批量数据。

- 实时数据分析

这可将并行处理的强大功能与实时数据的价值相结合。例如，使用多个并行运行的不同的 Kinesis Data Streams 应用程序，实时处理网站点击流，然后分析站点可用性参与度。

- 复杂流处理

可以创建 Kinesis Data Streams 应用程序和数据流的有向无环图 (DAG)。这通常会涉及将数据从多个 Kinesis Data Streams 应用程序放入其他流，以供其他 Kinesis Data Streams 应用程序进行下游处理。

## 使用 Kinesis Data Streams 的好处

虽然可使用 Kinesis Data Streams 解决各种流式处理数据问题，但其常见用途是实时聚合数据，然后将聚合数据加载到数据仓库或 map-reduce 群集。

将数据放入 Kinesis Data Streams，以确保持久性和弹性。将记录放入流的时间与可检索记录的时间之间的延迟（put-to-get延迟）通常少于 1 秒。换言之，在添加数据之后，Kinesis Data Streams 应用程序几乎立即可以开始使用流中的数据。Kinesis Data Streams 的托管服务方面可减轻您创建和运行数据引入管道的操作负担。可以创建流式处理 map-reduce 类型应用程序。利用 Kinesis Data Streams 的弹性，可以扩大或缩小流，以便绝不会在数据记录过期前丢失它们。

多个 Kinesis Data Streams 应用程序可以使用流中的数据，以便多个操作（如存档和处理）可以并发且独立进行。例如，两个应用程序可读取同一流中的数据。第一个应用程序计算正在运行的聚合并更新 Amazon DynamoDB 表，第二个应用程序压缩数据并将数据存档至像 Amazon Simple Storage Service (Amazon S3) 这样的数据存储。然后，控制面板将读取带正在运行的聚合的 DynamoDB 表以下内容：up-to-the分钟的报告。

Kinesis 客户端库支持容错使用流中的数据，并提供针对 Kinesis Data Streams 应用程序的扩展支持。