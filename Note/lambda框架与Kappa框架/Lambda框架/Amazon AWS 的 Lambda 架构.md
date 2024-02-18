
![[Pasted image 20230515161727.png]]


**Batch Layer**：使用 S3 bucket 从各种数据源收集数据，使用 AWS Glue 进行 ETL，输出到 Amazon S3。数据也可以输出到 Amazon Athena （[交互式查询]工具）

**Speed Layer**： 从上图看加速层有三个过程
	Kinesis Stream 从[实时数据流]中处理增量的数据，这部分数据数据输出到 Serving Layer 的 Amazon EMR，也可以输出到 Kinesis Firehose 对增量数据进行后续处理
	Kinesis Firehose 处理增量数据并写入 Amazone S3 中
	Kinesis Analytics 提供 SQL 的能力对增量的数据进行分析

**Serving Layer**：合并层使用基于 Amazon EMR 的 Spark SQL 来合并 Batch Layer 和 Speed Layer 的数据。批处理数据可以从 Amazon S3 加载批处理数据，[实时数据]可以从 Kinesis Stream 直接加载，合并的数据可以写到 Amazone S3。下面是一段[合并数据代码]

![[Pasted image 20230515161822.png]]
