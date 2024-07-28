
Spark是一个计算框架，与MapReduce相当，但是是为了解决MapReduce使用场景较为单一，且效率不高的问题所开发的一个开源框架。

Spark基于spark core提供的多种api接口，这些接口支持不同的功能，实现了spark生态。

其中常见的spark接口有**Spark sql, Spark streaming** , Spark MLlib, GraphX


### Spark为什么快？

#### 1、消除了冗余的HDFS读写

Hadoop每次shuffle操作后，必须写到磁盘，而Spark在shuffle后不一定落盘，可以cache到内存中，以便迭代时使用。如果操作复杂，很多的shufle操作，那么Hadoop的读写IO时间会大大增加。

#### 2、消除了冗余的MapReduce阶段

Hadoop的shuffle操作一定连着完整的MapReduce操作，冗余繁琐。而Spark基于RDD提供了丰富的算子操作，且reduce操作产生shuffle数据，可以缓存在内存中。

#### 3、JVM的优化

Hadoop每次MapReduce操作，启动一个Task便会启动一次JVM，基于进程的操作。而Spark每次MapReduce操作是基于线程的，只在启动Executor是启动一次JVM，内存的Task操作是在线程复用的。

每次启动JVM的时间可能就需要几秒甚至十几秒，那么当Task多了，这个时间Hadoop不知道比Spark慢了多少。
