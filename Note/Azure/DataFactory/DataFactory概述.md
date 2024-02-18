https://docs.azure.cn/zh-cn/data-factory/

### 项目概述
* 自动化定时Job 
* 调用服务
	* Functions
		* 通过Fucntions 把文件分割放到 blob，同时放到队列里，通过 Functions 进行解析，最后放到Event Hubs里
	* databricks notebook 自动调用集群创建job
	* hdinsight spark 

![[Pasted image 20230327141502.png]]

### 连接和收集
* 收集数据到云上进行集中数据存储，并配合监控、警示、报警等功能
	* **数据管道**：执行任务单元的活动的逻辑分组
* 使用 Azure HDInsight Hadoop 群集在 Azure Blob 存储中收集数据并稍后对其进行转换。

### 转换和扩充
* 对数据进行转换，有ADF 映射数据流
	* ADF：数据转换逻辑图
* 可以使用 ADF，它支持外部的 Spark,Hadoop 进行转化

可以连接不同的组件
