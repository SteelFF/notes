
##  DBFS

1. 分布式文件系统  类似  Hadoop
2. 使用目录和文件语义与[对象存储进行交互](https://learn.microsoft.com/zh-cn/azure/databricks/dbfs/#interact-files)
3. DBFS 根是 Spark 和 DBFS 命令的根路径。 其中包括:
	-   Spark SQL
	-   DataFrame
	-   `dbutils.fs`
	-   `%fs`
	
## 工作区 Workspace

### 创建笔记本
1.  在边栏中单击 ![工作区图标](https://learn.microsoft.com/zh-cn/azure/databricks/_static/images/icons/workspace-icon.png)“工作区”。
2.  在“工作区”文件夹中，选择 ![向下箭头图标](https://learn.microsoft.com/zh-cn/azure/databricks/_static/images/icons/down-caret.png)“创建”>“笔记本”。
   ![[Pasted image 20230323094220.png|450]]
3. 对话框中有默认语言的选择 **'SQL'**，
4. 笔记本工具栏中的**群集选择器**，然后从下拉菜单中选择你的群集。


### 建表

1. 工作区notebook建表 使用选择的默认语言SQL
   ```sql
DROP TABLE IF EXISTS diamonds;

CREATE TABLE diamonds USING CSV OPTIONS (path "/databricks-datasets/Rdatasets/data-001/csv/ggplot2/diamonds.csv", header "true")
```

2. 指定语言
   ```python
%python
diamonds = (spark.read
  .format("csv")
  .option("header", "true")
  .option("inferSchema", "true")
  .load("/databricks-datasets/Rdatasets/data-001/csv/ggplot2/diamonds.csv")
)

# 以 Delta Lake 格式写出
diamonds.write.format("delta").save("/mnt/delta/diamonds")
```

```sql
DROP TABLE IF EXISTS diamonds;
-- 读取 delta lake 格式的文件，创建表
CREATE TABLE diamonds USING DELTA LOCATION '/mnt/delta/diamonds/'
```

### 查询表

* sql select

### 显示数据

按颜色显示钻石平均价格图表。
1.  单击“条形图”图标 ![[Pasted image 20230323100038.png]]。
2.  单击“绘图选项”。
    -   将“颜色”拖放到“键”框中。
    -   将“价格”拖放到“值”框中。
    -   在“聚合”下拉菜单中，选择“平均值”。
        ![[Pasted image 20230323095949.png|250]]
        
3.  单击“应用”以显示条形图。
   ![[Pasted image 20230323100015.png|475]]

### 笔记中的Magic命令
```sh
%python
%r
%scala
%sql
%sh   支持shell代码
%fs   支持`dbutils` 文件系统命令  如 dbutils.fs.ls
%md   markdown 文档
```
## Repos
源代码管理
可以使用 Git 功能
* 

## 集群 Compute

### 创建群集
1.  在边栏中单击 ![计算图标](https://learn.microsoft.com/zh-cn/azure/databricks/_static/images/icons/clusters-icon.png)“Compute”。
2.  在“Compute”页上单击“创建计算”。
3.  在“New Compute”页上，从 `Databricks runtime version`下拉列表中选择“Runtime: 11.3 LTS ML (Scala 2.12、Spark 3.3.0)”。
4.  单击“创建群集”。


### 集群功能

#### 通用群集和作业群集

* 通用群集：多个用户共享，并且最适合用于执行即席分析、数据浏览或开发。
* 作业群集：作业群集上运行完成的开发代码。

#### 群集模式

* 标准 ：仅对单个用户使用标准群集
* 高并发：适合需要共享资源或运行临时作业的用户组，建议为高并发群集启用自动缩放
* 单节点：适合少量数据或非分布式工作负载

#### Databricks Runtime 版本

* 就是 Databricks 版本


## 工作流 workflow

#### 创建作业
[官网说明](https://learn.microsoft.com/zh-cn/azure/databricks/workflows/jobs/jobs#--create-a-job)
-   单击边栏中![[Pasted image 20230323163926.png]]“工作流”，然后点击![[Pasted image 20230323164007.png]]。
-   在边栏中，单击![[Pasted image 20230323164023.png]]“新建”，然后选择“作业”。


#### 为运行管道的作业定义计划
1.  单击边栏中的 ![[Pasted image 20230323163926.png]]“工作流”。
2.  在“名称”列中单击作业名称。 边侧面板将显示“作业详细信息”。
3.  单击“作业详细信息”面板中的“编辑计划”并将“计划类型”设置为“已计划” 。
4.  指定时间段、开始时间和时区。 （可选）选中“显示 Cron 语法”复选框以使用 [Quartz Cron 语法](http://www.quartz-scheduler.org/documentation/quartz-2.3.0/tutorials/crontrigger.html)显示和编辑计划。
5.  单击“ **保存**”。

## Delta Lake

  Delta Lake 提供的功能包括事务日志记录、数据类型约束以及将流式处理数据合并到关系表中的功能。

