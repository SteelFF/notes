当您开始使用 Amazon Kinesis Data Streams 时，了解其架构和术语将大有裨益。

###### 主题

- [[#Kinesis Data Streams 高级架构]]
- [[#Kinesis Data Streams 术语]]

## Kinesis Data Streams 高级架构

下图演示 Kinesis Data Streams 的高级别架构。这些区域有：_生产商们_不断将数据推送到 Kinesis Data Streams，_消费者们_实时处理数据。使用者（如在 Amazon EC2 上运行的自定义应用程序或 Amazon Kinesis Data Firehose 传输流）可以使用Amazon服务，例如 Amazon DynamoDB、Amazon Redshift 或 Amazon S3。

![Kinesis Data Streams 高级架构图](https://docs.amazonaws.cn/streams/latest/dev/images/architecture.png)

## Kinesis Data Streams 术语

### Kinesis Data Streams

一个 _Kinesis Data Streams_ 是一套[分片](https://docs.amazonaws.cn/streams/latest/dev/key-concepts.html#shard). 每个分片都有一系列数据记录。每个数据记录都有一个[序列号](https://docs.amazonaws.cn/streams/latest/dev/key-concepts.html#sequence-number)这是由 Kinesis Data Streams 分配的。

### 数据记录

一个_数据记录_是存储在中的数据单位[Kinesis Data Streams](https://docs.amazonaws.cn/streams/latest/dev/key-concepts.html#stream). 数据记录由[序列号](https://docs.amazonaws.cn/streams/latest/dev/key-concepts.html#sequence-number)，a[分区键](https://docs.amazonaws.cn/streams/latest/dev/key-concepts.html#partition-key)和数据 Blob，这是一个不可变的字节序列。Kinesis Data Streams 不会以任何形式检查、解释或更改 Blob 中的数据。数据 Blob 最大可达 1 MB。

### 容量模式

数据流_容量模式_确定如何管理容量以及如何为数据流的使用量收费。目前，在 Kinesis Data Streams 中，您可以在**按需**模式和**供应**数据流的模式。有关更多信息，请参阅 [选择数据流容量模式](https://docs.amazonaws.cn/streams/latest/dev/how-do-i-size-a-stream.html)。

使用**按需**模式下，Kinesis Data Streams 自动管理分片以提供必要的吞吐量。您只需为使用的实际吞吐量付费，而 Kinesis Data Streams 在工作负载上升或下降时自动适应它们的吞吐量需求。有关更多信息，请参阅 [按需模式](https://docs.amazonaws.cn/streams/latest/dev/how-do-i-size-a-stream.html#ondemandmode)。

使用**供应**模式，必须指定数据流的分片数。数据流的总容量是其分片容量的总和。您可以根据需要增加或减少数据流中的分片数，并按小时费率支付分片数。有关更多信息，请参阅 [预置模式](https://docs.amazonaws.cn/streams/latest/dev/how-do-i-size-a-stream.html#provisionedmode)。

### 保留周期

_保留期_ 是数据记录在添加到流中后可供访问的时间长度。在创建之后，流的保留期设置为默认值 24 小时。您可以将保留期增加至8760小时（365天) 使用[IncreaseStreamRetentionPeriod](https://docs.amazonaws.cn/kinesis/latest/APIReference/API_IncreaseStreamRetentionPeriod.html)操作，并使用将保留期缩短至 24 小时（最小值）[DecreaseStreamRetentionPeriod](https://docs.amazonaws.cn/kinesis/latest/APIReference/API_DecreaseStreamRetentionPeriod.html)operation. 对于保留期设置为 24 小时以上的流，将收取额外费用。有关更多信息，请参阅 [Amazon Kinesis 数据流定价](http://www.amazonaws.cn/kinesis/pricing/)。

### 创建者

_创建者_将记录放入 Amazon Kinesis Data Streams 中。例如，发送日志数据到流的 Web 服务器是创建器。

### 使用者

_使用者_从 Amazon Kinesis Data Streams 获取记录并进行处理。这些使用者称为 [Amazon Kinesis Data Streams 应用程序](https://docs.amazonaws.cn/streams/latest/dev/key-concepts.html#enabled-application)。

### Amazon Kinesis Data Streams 应用程序

网络 ACL 和安全组都允许 (因此可到达您的实例) 的发起 ping 的_Amazon Kinesis Data Streams 应用程序_是通常在 EC2 实例队列上运行的流的使用者。

可以开发的使用者有两种：共享扇出功能使用者和增强型扇出功能使用者。要了解它们之间的区别，以及了解如何创建每种使用者，请参阅[从 Amazon Kinesis Data Streams 中](https://docs.amazonaws.cn/streams/latest/dev/building-consumers.html)。

Kinesis Data Streams 应用程序的输出可能是另一个流的输入，这使您能够创建实时处理数据的复杂拓扑。应用程序也可以将数据发送到各种其他应用程序。Amazon服务。一个流可以有多个应用程序，每个应用程序可同时单独使用流中的数据。

### 分片

_分片_ 是流中数据记录的唯一标识序列。一个流由一个或多个分片组成，每个分片提供一个固定的容量单位。每个分片最多可以支持每秒 5 个事务处理用于读取，最高总数据读取速率为每秒 2 MB，写入最多每秒 1000 条记录，最高总数据写入速率为每秒 1 MB（包括分区键）。流的数据容量是您为流指定的分片数的函数。流的总容量是其分片容量的总和。

如果数据速率增加，您可以增加或减少分配给流的分片数量。有关更多信息，请参阅 [对流进行重新分片](https://docs.amazonaws.cn/streams/latest/dev/kinesis-using-sdk-java-resharding.html)。

### 分区键

一个_分区键_用于按分片对流中的数据进行分组。Kinesis Data Streams 将属于一个流的数据记录隔离到多个分片中。它使用与每个数据记录关联的分区键确定指定数据记录属于哪个分片。分区键是 Unicode 字符串，每个键的最大长度限制为 256 个字符。MD5 哈希函数用于将分区键映射到 128 位整数值，并使用分片的哈希键范围将关联的数据记录映射到分片。当应用程序将数据放入流中时，它必须指定一个分区键。

### 序列号

每个数据记录都有一个_序列号_在分片中，每个分区键都是唯一的。在您使用写入流之后，Kinesis Data Streams 将分配序列号`client.putRecords`要么`client.putRecord`. 同一分区键的序列号通常会随时间推移增加。写入请求之间的时间段越长，序列号越大。

|注意| |
|---|---|
|序列号不能用作相同流中的数据集的索引。为了在逻辑上分隔数据集，请使用分区键或者为每个数据集创建单独的流。| |

### Kinesis 客户端库

Kinesis 客户端库已编译成应用程序，从而支持以容错方式使用流中的数据。Kinesis 客户端库确保每个分片都有一个运行和处理该分片的记录处理器。库还可以简化流中的数据读取。Kinesis 客户端库使用 Amazon DynamoDB 表存储控制数据。它会为每个正在处理数据的应用程序创建一个表。

Kinesis 客户端库有两个主要版本。使用哪个版本取决于要创建的使用者的类型。有关更多信息，请参阅 [从 Amazon Kinesis Data Streams 中](https://docs.amazonaws.cn/streams/latest/dev/building-consumers.html)。

### Application Name

Amazon Kinesis Data Streams 应用程序的名称标识应用程序。每个应用程序必须具有一个唯一名称，此名称的范围限定于Amazon应用程序使用的账户和地区。此名称用作 Amazon DynamoDB 中的控制表名称和 Amazon 的命名空间。CloudWatch指标。

### 服务器端加密

当创建者将敏感数据输入流时，Amazon Kinesis Data Streams 可以自动加密这些数据。使用 Kinesis Data Streams[Amazon KMS](https://docs.amazonaws.cn/kms/latest/developerguide/)主密钥进行加密。有关更多信息，请参阅 [Amazon Kinesis Data Streams 保护](https://docs.amazonaws.cn/streams/latest/dev/server-side-encryption.html)。

|注意| |
|---|---|
|要读取或写入加密的流、创建者和使用者应用程序必须有权访问主密钥。有关向创建者应用程序和使用者应用程序授予权限的信息，请参阅[使用用户生成的 KMS 主密钥的权限](https://docs.amazonaws.cn/streams/latest/dev/permissions-user-key-KMS.html)。| |

|注意| |
|---|---|
|使用服务器端加密将产生 Amazon Key Management Service (Amazon KMS) 费用。有关更多信息，请参阅 。[AmazonKey Management Service](http://www.amazonaws.cn/kms/pricing).| |

