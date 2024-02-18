## 1. Lambda架构模型

![[Pasted image 20230515161523.png]]

数据流进入系统后，同时发往Batch Layer和Speed Layer处理。Batch Layer以不可变模型离线存储所有数据集，通过在全体数据集上不断重新计算构建查询所对应的Batch View. Speed Layer处理增量的实时数据流，不断更新查询所对应的Realtime Views. Serving Layer响应用户的查询请求，合并Batch View和Realtime View中的结果数据集到最终的数据集。

## 2. Lambda逻辑架构

![[Pasted image 20230515161546.png]]

数据从底层的数据源开始，经过各种各样的格式进入大数据平台，在大数据平台中经过Kafka、Flume等数据组件进行收集，然后分成两条线进行计算。一条线是进入流式计算平台（例如 Flink或者Spark Streaming），去计算实时的一些指标；另一条线进入批量数据处理离线计算平台（例如Mapreduce、Hive，Spark SQL），去计算T+1的相关业务指标，这些指标需要隔日才能看见。同时实时数据和离线数据进行合并，提供全量（含当天）的指标数据展示。

## 3. 组件选型

![[Pasted image 20230515161602.png]]

数据流存储可选用基于不可变日志的分布式消息系统Kafka；Batch Layer数据集的存储可选用Hadoop的HDFS，或者是阿里云的ODPS；Batch View的预计算可以选用MapReduce或Spark；Batch View自身结果数据的存储可使用MySQL（查询少量的最近结果数据），或HBase（查询大量的历史结果数据）。Speed Layer增量数据的处理可选用Flink或Spark Streaming；Realtime View增量结果数据集为了满足实时更新的效率，可选用Redis等内存NoSQL。

![[Pasted image 20230515161616.png]]

Batch Layer数据集的存储可选用Hadoop的HDFS，存储在HDFS的数据不再转存到其它组件，而是采用impala/sparkSQL基于内存查询的SQL引擎直接读取HDFS中的数据。Speed Layer增量数据的处理可选用Flink或Spark Streaming处理后存储到支持高吞吐低延时的列式存储系统中，比如HBase。ServingLayer阶段，数据在HDFS中进行合并，最终由impala负责提供即时查询。
