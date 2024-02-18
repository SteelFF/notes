Lambda 架构包含三层，Batch Layer、Speed Layer 和 Serving Layer。架构图如下：

![[Pasted image 20230515160004.png]]

#### 基本概念：
	Batch Layer：批处理层，对离线的历史数据进行预计算，为了下游能够快速查询想要的结果。由于批处理基于完整的历史数据集，因此准确性可以得到保证。批处理层可以用 Hadoop、Spark 和 Flink 等框架计算
	Speed Layer：加速处理层，处理实时的增量数据，这一层重点在于低延迟。加速层的数据不如批处理层那样完整和准确，但是可以填补批处理高延迟导致的数据空白。加速层可以用 Storm、Spark streaming 和 Flink 等框架计算
	Serving Layer：合并层，计算历史数据和实时数据都有了， 合并层的工作自然就是将两者数据合并，输出到数据库或者其他介质，供下游分析。

这里涉及到数据合并的问题，如果查询函数满足Monoid性质（结合律，（a+b）+c = a + (b + c)），只需要简单的合并Batch View和Realtime View中的经过数据集。否则，需要把查询函数转换为多个满足Monoid性质的查询函数的运算，单独对每个满足Monoid性质的查询函数进行Batch View和Realtime View中的结果数据集合并，然后再计算得到最终的结果数据集。也可以根据业务自身特性，运用业务自身的规则来对Batch View和Realtime View中的结果数据集合并。
