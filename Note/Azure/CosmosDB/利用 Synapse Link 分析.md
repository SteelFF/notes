## Cosmos DB 分析存储

* 是完全独立的列存储，可以进行大型数据分析
* 分析存储已与事务存储已经分离，不会影响事务工作负载的性能不会影响事务工作负载的性能
* 数据 会自动同步到单独的列存储 -> Azure Synapse Link
  ![[Pasted image 20230327134518.png]]


## 利用 Synapse Link 与 Synapse Analytics 集成

[官方文档](https://learn.microsoft.com/zh-cn/azure/synapse-analytics/quickstart-connect-synapse-link-cosmos-db)

#### Prerequisites

* CosmosDB create Account
* Synapse create workspace
	* [创建 Synapse 工作区](https://learn.microsoft.com/zh-cn/azure/synapse-analytics/quickstart-create-workspace#create-a-synapse-workspace)
	* [打开](https://learn.microsoft.com/zh-cn/azure/synapse-analytics/quickstart-create-workspace#open-synapse-studio)

#### 在 Cosmos 中启用 Synapse Link

**Cosmos启用了 还用不了，要创建启用 分析存储的容器**

##### Azure 门户中启用
![[Pasted image 20230328171059.png|475]]
##### Azure CLI中启用
```shell
az cosmosdb update --name my-cosmos-db --resource-group my-rg \
    --enable-analytical-storage true
```
##### Azure PowerShell中启用
```shell
Update-AzCosmosDBAccount -Name "my-cosmos-db" -ResourceGroupName "my-rg" -EnableAnalyticalStorage 1
```

#### 创建启用了分析存储的容器
##### Azure 门户中创建
![[Pasted image 20230328171616.png|500]]

##### Azure 门户中为现有容器启用分析存储支持
![[Pasted image 20230328171652.png|500]]

##### 使用 Azure CLI
```shell
#  `-analytical-storage-ttl` 指定为 -1 可永久保留分析数据

az cosmosdb sql container create \
    --resource-group my-rg \
    --account-name my-cosmos-db \
    --database-name my-db \
    --name my-container \
    --partition-key-path "/productID" \
    --analytical-storage-ttl -1
```

##### Azure PowerShell
```shell
New-AzCosmosDBSqlContainer -ResourceGroupName "my-rg" -AccountName "my-cosmos-db" -DatabaseName "my-db" -Name "my-container" -PartitionKeyKind "hash" -PartitionKeyPath "/productID" -AnalyticalStorageTtl -1
```

#### 为 Cosmos DB 创建链接服务

有一个能分析存储的 Cosmos DB 容器时，可在 Synapse Analytics 工作区中创建链接服务以连接到它。
![[Pasted image 20230328172059.png|500]]

**连接到 Azure Cosmos DB 数据库，可以使用以下任一身份验证选项：**
-   帐户密钥：为 Cosmos DB 帐户指定身份验证密钥。
-   服务主体：使用 Azure Synapse Analytics 服务的标识。
-   系统分配的托管标识：使用系统分配的托管标识。
-   用户托管标识：使用用户定义的托管标识。

**创建链接服务后**
![[Pasted image 20230328172149.png|500]]

**用户界面的图标区别**
![[Pasted image 20230328172235.png|500]]



##  Synapse Analytics 工作区中用 Spark 3 与 Cosmos 交互

#### 分析数据加载到 DF
```python
# 建 Dataframe
# 如果要选择 regions 列表，添加"spark.cosmos.preferredRegions", "<Region1>,<Region2>"
df = spark.read
     .format("cosmos.olap")\                                       # 读取分析数据
     .option("spark.synapse.linkedService", "my_linked_service")\  # 链接服务的名字
     .option("spark.cosmos.container", "my-container")\            # 容器的名字
     .load()

display(df.limit(10))
```

### DF 写入Cosmos DB 容器
```python
# 向 container 中写入 DF
# 可以写入指定的Regins "spark.cosmos.preferredRegions", "<Region1>,<Region2>"
df.write.format("cosmos.oltp") \           # 写入数据
    .option("spark.synapse.linkedService", "<enter linked service name>") \
    .option("spark.cosmos.container", "<enter container name>") \
    .mode('append') \
    .save()
```
### Spark SQL 查询 Cosmos DB 分析数据
```sql
%%sql
-- Create a logical database in the Spark metastore
CREATE DATABASE mydb;
USE mydb;

-- Create a table from the Cosmos DB container
CREATE TABLE products
using cosmos.olap                                    -- cosmos.olap
options (
    spark.synapse.linkedService 'my_linked_service',  -- 指定链接名
    spark.cosmos.container 'my-container'             -- 指定容器名
);

-- Query the table
SELECT productID, productName
FROM products;
```

### 使用 Synapse SQL 查询 Cosmos DB

[learn 教程](https://learn.microsoft.com/zh-cn/training/modules/configure-azure-synapse-link-with-azure-cosmos-db/6-query-with-sql)