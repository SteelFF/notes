
## Spark 开发

Azure Databricks 从 Azure Cosmos DB 读写数据 [官方文档](https://learn.microsoft.com/zh-cn/azure/cosmos-db/nosql/quickstart-spark?tabs=python#bk_working_with_connector)

#### Prerequisites

* 订阅的 Azure 帐户
* 包含 Spark 3.2.1 的 Databricks
*  SLF4J（可选）

#### 创建数据库和容器

* [创建 Endpoint](https://learn.microsoft.com/zh-cn/azure/cosmos-db/how-to-configure-private-endpoints#create-a-private-endpoint-by-using-the-azure-portal)
	* 获取 Endpoint，就是 privatelink
	* [Endpoint 在各个API上的使用](https://learn.microsoft.com/zh-cn/azure/cosmos-db/how-to-configure-private-endpoints#private-zone-name-mapping)
		* NoSQL  Sql  : ==privatelink==.documents.azure.com

```python
# 首先设置 Azure Cosmos DB 帐户凭据，还有 db 和 container 名字
cosmosEndpoint = "https://privatelink.documents.azure.com:443/"  # 连接NoSQL
cosmosMasterKey = "privatelink"
cosmosDatabaseName = "sampleDB"
cosmosContainerName = "sampleContainer"

cfg = {
  "spark.cosmos.accountEndpoint" : cosmosEndpoint,
  "spark.cosmos.accountKey" : cosmosMasterKey,
  "spark.cosmos.database" : cosmosDatabaseName,
  "spark.cosmos.container" : cosmosContainerName,
}

# 使用 Catalog Api 创建 Azure Cosmos DB 数据库和容器
spark.conf.set("spark.sql.catalog.cosmosCatalog", "com.azure.cosmos.spark.CosmosCatalog")
spark.conf.set("spark.sql.catalog.cosmosCatalog.spark.cosmos.accountEndpoint", cosmosEndpoint)
spark.conf.set("spark.sql.catalog.cosmosCatalog.spark.cosmos.accountKey", cosmosMasterKey)

# 建 库
spark.sql("CREATE DATABASE IF NOT EXISTS cosmosCatalog.{};".format(cosmosDatabaseName))

# 建 container
spark.sql("CREATE TABLE IF NOT EXISTS cosmosCatalog.{}.{} using cosmos.oltp TBLPROPERTIES(partitionKeyPath = '/id', manualThroughput = '1100')".format(cosmosDatabaseName, cosmosContainerName))
```
#### 插入&查询数据
```python
spark.createDataFrame((("cat-alive", "Schrodinger cat", 2, True), ("cat-dead", "Schrodinger cat", 2, False))) \
   .toDF("id","name","age","isAlive") \
   .write\
   .format("cosmos.oltp")\      # 写入数据 cosmos.oltp
   .options(**cfg)\
   .mode("APPEND")\
   .save()
   
# 查询数据 
from pyspark.sql.functions import col
df = spark.read.format("cosmos.oltp").options(**cfg) \
  .option("spark.cosmos.read.inferSchema.enabled", "true") \
  .load()
df.filter(col("isAlive") == True)\
  .show()
```
#### option参数配置大全

* [参数详解](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/cosmos/azure-cosmos-spark_3_2-12/docs/configuration-reference.md#generic-configuration)
| Config Property Name                            | Mean                                                                                                                  |
| ----------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| `spark.cosmos.accountEndpoint`                  | Cosmos DB Account Endpoint Uri                                                                                        |
| `spark.cosmos.accountKey`                       | Cosmos DB Account Key                                                                                                 |
| `spark.cosmos.database`                         | database name                                                                                                         |
| `spark.cosmos.container`                        | container name                                                                                                        |
| `spark.cosmos.write.strategy`                   | Cosmos DB 项目写入策略<br/>`ItemOverwrite` 默认 upsert<br/>`ItemAppend` 追加<br/> `ItemPatch`根据补丁配置更新所有文档 |
| `spark.cosmos.write.bulk.enabled`               | 批量写入                                                                                                              |
| `spark.cosmos.write.patch.defaultOperationType` |                                                                                                                       |

``
#### 批量参数更新
```python
cfgPatch = {"spark.cosmos.accountEndpoint": cosmosEndpoint,
          "spark.cosmos.accountKey": cosmosMasterKey,
          "spark.cosmos.database": cosmosDatabaseName,
          "spark.cosmos.container": cosmosContainerName,
          "spark.cosmos.write.strategy": "ItemPatch",
          "spark.cosmos.write.bulk.enabled": "false",
          "spark.cosmos.write.patch.defaultOperationType": "Set",
          "spark.cosmos.write.patch.columnConfigs": "[col(name).op(set)]"
          }

id = "<document-id>"
query = "select * from cosmosCatalog.{}.{} where id = '{}';".format(
    cosmosDatabaseName, cosmosContainerName, id)

dfBeforePatch = spark.sql(query)
print("document before patch operation")
dfBeforePatch.show()

data = [{"id": id, "name": "Joel Brakus"}]
patchDf = spark.createDataFrame(data)

patchDf.write.format("cosmos.oltp").mode("Append").options(**cfgPatch).save()

dfAfterPatch = spark.sql(query)
print("document after patch operation")
dfAfterPatch.show()
```

#### schema-自动判断
```python
df = spark.read.format("cosmos.oltp").options(**cfg)\
 .option("spark.cosmos.read.inferSchema.enabled", "true")\ # 没有指定Schema时，开启Schema的自动推断
 .load()

df.printSchema()


# 可以传递自定义架构，来读取数据
customSchema = StructType([
      StructField("id", StringType()),
      StructField("name", StringType()),
      StructField("type", StringType()),
      StructField("age", IntegerType()),
      StructField("isAlive", BooleanType())
    ])
df = spark.read.schema(customSchema).format("cosmos.oltp").options(**cfg)\
 .load()
df.printSchema()

# 如果未指定自定义架构并禁用架构推断，则生成的数据将返回项目的原始 Json 内容:
df = spark.read.format("cosmos.oltp").options(**cfg)\
 .load()
df.printSchema()

```

## Python开发

#### 创建  Cosmos DB 帐户&设置

[创建  Cosmos DB 帐户&设置](https://learn.microsoft.com/zh-cn/azure/cosmos-db/nosql/quickstart-python?tabs=azure-portal%2Cpasswordless%2Cwindows%2Csign-in-azure-cli%2Csync#create-an-azure-cosmos-db-account)
* 10. 记录“URI”和“主密钥”字段中的值后续会使用到

#### Python环境&包

##### 无密码（推荐）

```python
pip install azure-cosmos
pip install azure-identity 
```
```shell
# 代码中用“URI”和“主密钥”值，要保存到运行应用程序的本地计算机上的新环境变量。从上一步获取
$env:COSMOS_ENDPOINT = "<cosmos-account-URI>"
$env:COSMOS_KEY = "<cosmos-account-PRIMARY-KEY>"  
```
**资源交互**
-   [`CosmosClient`](https://learn.microsoft.com/zh-cn/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) - 此客户端对象用于对服务进行配置和执行请求。
-   [`DatabaseProxy`](https://learn.microsoft.com/zh-cn/python/api/azure-cosmos/azure.cosmos.database.databaseproxy) - 在访问数据库或对其执行操作时，会在服务器端验证该数据库。
-   [`ContainerProxy`](https://learn.microsoft.com/zh-cn/python/api/azure-cosmos/azure.cosmos.container.containerproxy) - 在使用该容器时，会在服务器端对其进行验证。

##### 使用连接字符串
```shell
pip install azure-cosmos
```
```shell
# 代码中用“URI”和“主密钥”值，要保存到运行应用程序的本地计算机上的新环境变量。从上一步获取
$env:COSMOS_ENDPOINT = "<cosmos-account-URI>"
$env:COSMOS_KEY = "<cosmos-account-PRIMARY-KEY>"  
```

#### 验证客户端

##### 无密码
使用 `DefaultAzureCredential` ，将自动发现并使用你以前在登录时使用的帐户
[DefaultAzureCredential 概述](https://learn.microsoft.com/zh-cn/azure/developer/python/sdk/authentication-overview#defaultazurecredential)
```shell
### 1 使用Azure CLI 或 PowerShell 创建自己的角色
az cosmosdb sql role definition create 
    --account-name passwordlessnosql
    --resource-group  passwordlesstesting 
    --body '{
    "RoleName": "PasswordlessReadWrite",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ]
    }]
}'
# 从 `name` 字段中复制 ID 值，保存

# 将你创建的角色分配给将连接到 Cosmos DB 的用户帐户或服务主体。
# 使用 `az ad user` 命令检索帐户的详细信息，复制id
az ad user show --id "<your-email-address>"

# 使用 `az cosmosdb sql role assignment create` 命令和先前复制的 ID 将你创建的自定义角色分配给你的用户帐户
az cosmosdb sql role assignment create 
    --account-name passwordlessnosql
    --resource-group passwordlesstesting
    --scope "/" 
    --principal-id <your-user-id>   # 复制的id
    --role-definition-id <your-custom-role-id>

### 2 使用 DefaultAzureCredential 进行身份验证  使用Azure CLI
az login   # 登录
```
   其他登录方式：[查看其他登录方式：VS Code 或 Azure PowerShell](https://learn.microsoft.com/zh-cn/azure/cosmos-db/nosql/quickstart-python?tabs=azure-portal%2Cpasswordless%2Cwindows%2Csign-in-azure-cli%2Csync#authenticate-using-defaultazurecredential)
```python
# 登录后，打开 app.py 文件，进行如下编辑
import os
import json
import asyncio
from azure.cosmos.aio import CosmosClient
from azure.identity.aio import DefaultAzureCredential   # 登录了之后这边自动验证

endpoint = os.environ["COSMOS_ENDPOINT"]
DATABASE_NAME = "cosmicworks"
CONTAINER_NAME = "products"

credential = DefaultAzureCredential()
client = CosmosClient(url=endpoint, credential=credential)  # 创建新的客户端实例。  
```

##### 使用连接字符串
```python
# 项目目录中，打开 app.py 文件。
import os
import json
from azure.cosmos import CosmosClient, PartitionKey

ENDPOINT = os.environ["COSMOS_ENDPOINT"]
KEY = os.environ["COSMOS_KEY"]

DATABASE_NAME = "cosmicworks"
CONTAINER_NAME = "products"

client = CosmosClient(url=ENDPOINT, credential=KEY)
```

#### 资源对象操作

##### 无密码（推荐）

```shell
### Azure CLI
# 创建数据库 
az cosmosdb sql database create \
    --account-name <cosmos-db-account-name> \
    --resource-group <resource-group-name> \
    --name cosmicworks

# 创建容器
az cosmosdb sql container create \
    --account-name <cosmos-db-account-name> \
    --resource-group <resource-group-name> \
    --database-name cosmicworks \
    --partition-key-path "/categoryId" \
    --name products
```
##### 使用连接字符串
```python
# 创建数据库  异步加上 await，同步没有
database = client.create_database_if_not_exists(id=DATABASE_NAME)  # 同步创建
print("Database\t", database.id)

# 创建容器
key_path = PartitionKey(path="/categoryId")
container = await database.create_container_if_not_exists(    # 异步创建
    id=CONTAINER_NAME, partition_key=key_path, offer_throughput=400
)
print("Container\t", container.id)
```
#### Item 操作
```python
# 创建项
new_item = {
    "id": "70b63682-b93a-4c77-aad2-65501347265f",
    "categoryId": "61dba35b-4f02-45c5-b648-c6badc0cbd79",
    "categoryName": "gear-surf-surfboards",
    "name": "Yamba Surfboard",
    "quantity": 12,
    "sale": False,
}
await container.create_item(new_item)  # 异步创建
```

```python
# 获取item
# 使用唯一标识符 (`id`) 和分区键字段来执行点读取操作
	 existing_item = await container.read_item(       # 异步加上 await，同步没有
    item="70b63682-b93a-4c77-aad2-65501347265f",
    partition_key="61dba35b-4f02-45c5-b648-c6badc0cbd79",
)
print("Point read\t", existing_item["name"])
```


```python
# sql-like 查询 Item
# 参数设置
QUERY = "SELECT * FROM products p WHERE p.categoryId = @categoryId"
CATEGORYID = "61dba35b-4f02-45c5-b648-c6badc0cbd79"
params = [dict(name="@categoryId", value=CATEGORYID)]  
# 查询
results = container.query_items(
    query=QUERY, parameters=params, enable_cross_partition_query=False
)
# 遍历查询结果
items = [item async for item in results]
output = json.dumps(items, indent=True)
print("Result list\t", output)
```

#### 运行代码
```shell
python app.py
```
  
#### 清理资源

通过删除资源组来清理资源

[Azure Portal 可视化删除](https://learn.microsoft.com/zh-cn/azure/cosmos-db/nosql/quickstart-python?tabs=azure-portal%2Cconnection-string%2Cwindows%2Csign-in-azure-cli%2Csync#clean-up-resources)
![[Pasted image 20230328154813.png]]
```shell
# Azure Cli
az group delete --name $resourceGroupName

# PowerShell
$parameters = @{
    Name = $RESOURCE_GROUP_NAME
}
Remove-AzResourceGroup @parameters
```

