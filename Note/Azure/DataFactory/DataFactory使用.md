## 创建 DataFactory

[官方文档](https://docs.azure.cn/zh-cn/data-factory/quickstart-create-data-factory#advanced-creation-in-the-azure-portal)

## DataFactory 使用模板

[官方文档](https://learn.microsoft.com/zh-cn/azure/data-factory/solution-templates-introduction)

## 使用ADF转换数据

可视化数据流图，进行数据管道的建立，并进行数据的转换：
[官方文档 part1](https://docs.azure.cn/zh-cn/data-factory/data-flow-create?tabs=data-factory#steps-to-create-a-new-data-flow) ：建立 data flow pipeline 数据流管道
[官方文档 part2](https://docs.azure.cn/zh-cn/data-factory/tutorial-data-flow) ：在管道中进行数据转换

#### Blob -> Azure SQL
[使用复制工具复制内容到SQL](https://docs.azure.cn/zh-cn/data-factory/tutorial-copy-data-tool)
-   创建数据工厂。
-   使用“复制数据”工具创建管道。
-   监视管道和活动运行。

 **SQL Server -> Azure SQL**
[详细文档](https://docs.azure.cn/zh-cn/data-factory/tutorial-incremental-copy-multiple-tables-portal)
-   准备源和目标数据存储。
-   创建数据工厂。
-   创建自承载 Integration Runtime (IR)。
-   安装 Integration Runtime。
-   创建链接服务。
-   创建源、接收器和 watermark 数据集。
-   创建、运行和监视管道。
-   查看结果。
-   在源表中添加或更新数据。
-   重新运行和监视管道。
-   查看最终结果。

## 增量方式加载数据

### Azure SQL -> Blob
![[Pasted image 20230327144637.png|500]]

重要步骤：
* 选择 watermark 列，一般为 自增id 或 last_modify_time 时间字段
* 在 数据存储 中存储 watermark
* 创建工作流管道
	* 创建两个 Lookup 活动：第一个 Lookup 活动检索上一个水印值，第二个 Lookup 活动检索新的水印值。
	* 创建 Copy 活动：复制源数据存储中其水印列值大于旧水印值但小于新水印值的行。
	* 创建 StoredProcedure 活动
[接下来看官方文档](https://docs.azure.cn/zh-cn/data-factory/tutorial-incremental-copy-portal#create-a-data-source-table-in-your-sql-database)

## 数据捕获时发生改变 (CDC)
操作：
1.  创建**查找活动**，计算 CDC 表中变更的记录数量，然后将其传递给“IF 条件”活动。
2.  创建 **If 条件**，来检查是否有变更的记录，如果有，则调用“复制活动”。
3.  创建**复制活动**，来将插入/更新/删除的数据从 CDC 表复制到 Azure Blob 存储。
[官方文档](https://docs.azure.cn/zh-cn/data-factory/tutorial-incremental-copy-change-data-capture-feature-portal#high-level-solution)

**官方案例**
-   创建数据工厂。
-   使用“复制数据”工具创建管道。
-   监视管道和活动运行。

## 创建使用 spark 的管道

-   创建数据工厂。
-   创建使用 Spark 活动的管道。
-   触发管道运行。
-   监视管道运行。
[官方文档](https://learn.microsoft.com/zh-cn/azure/data-factory/tutorial-transform-data-spark-portal#create-linked-services)

## 创建使用 Databricks 的管道

-   创建数据工厂。
-   创建使用 Databricks Notebook 活动的管道。
-   触发管道运行。
-   监视管道运行。
[官方文档](https://learn.microsoft.com/zh-cn/azure/data-factory/transform-data-using-databricks-notebook#create-linked-services)

## 管道分支和链接

-   利用参数传递和系统变量
-   将活动的输出发送到后续活动
*   使用 Logic Apps 配合，成功失败发的邮件不一样
[官方文档](https://learn.microsoft.com/zh-cn/azure/data-factory/tutorial-control-flow-portal)  没看明白，需要自己看一下

## 触发器定时调度  trigger

[官方文档](https://learn.microsoft.com/zh-cn/azure/data-factory/how-to-create-schedule-trigger?tabs=data-factory)

* 可以自定义触发器