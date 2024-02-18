
## 特点

* 完全托管的 NoSQL 数据库
* 提供多种数据库 API: NoSQL、MongoDB、PostgreSQL、Cassandra、Gremlin 和表，为真实数据建模。

## 开发需要的验证信息

[Endpoint 专用终结点](https://learn.microsoft.com/zh-cn/azure/cosmos-db/how-to-configure-private-endpoints#create-a-private-endpoint-by-using-the-azure-portal) ：privatelink，Spark开发需要

[cosmos-account-URI](https://learn.microsoft.com/zh-cn/azure/cosmos-db/nosql/quickstart-python?tabs=azure-portal%2Cpasswordless%2Cwindows%2Csign-in-azure-cli%2Csync#create-an-azure-cosmos-db-account)  COSMOS_ENDPOINT 
[cosmos-account-PRIMARY-KEY](https://learn.microsoft.com/zh-cn/azure/cosmos-db/nosql/quickstart-python?tabs=azure-portal%2Cpasswordless%2Cwindows%2Csign-in-azure-cli%2Csync#create-an-azure-cosmos-db-account)  第十步生成， Python&Spark开发需要

[NoSQL连接字符串](https://learn.microsoft.com/zh-cn/powershell/module/az.cosmosdb/get-azcosmosdbaccountkey?view=azps-9.5.0) ：Python开发需要

[MongoDB 连接字符串](https://learn.microsoft.com/zh-cn/azure/cosmos-db/mongodb/quickstart-python?tabs=azure-cli%2Cvenv-windows%2Cwindows#get-mongodb-connection-string)  Python 开发 MongoDB API Cosmos 需要

## 角色
[要看创建自定义角色文档](https://learn.microsoft.com/zh-cn/azure/cosmos-db/nosql/quickstart-python?tabs=azure-portal%2Cpasswordless%2Cwindows%2Csign-in-azure-cli%2Csync#create-the-custom-role)

* 角色包含一组权限或是允许用户执行的一组操作（如读取、写入和删除）
* 阅读有关[配置基于角色的访问控制 (RBAC)](https://learn.microsoft.com/zh-cn/azure/cosmos-db/how-to-setup-rbac) 的更多信息

## Cosmos DB 资源模型

![[Pasted image 20230327101428.png|275]]![[Pasted image 20230327102522.png|350]]![[Pasted image 20230327101441.png|575]]

* **==Account==**
	* 一个 Azure 订阅下创建 50 个 Azure Cosmos DB 帐户
	* 要管理数据和预配吞吐量，可以在帐户中创建一个或多个数据库,然后可以创建一个或多个容器用于存储数据。
* **==Databases==**
	*  数据库只是一组容器。
* **==Containers==**
	* 用于存储数据
		* 分区：数据存储在一个或多个服务器上
			* 创建容器要分区键
			* WHERE 高效数据检索
	* 为容器注册[存储过程、触发器、用户定义的函数 (UDF)](https://learn.microsoft.com/zh-cn/azure/cosmos-db/nosql/stored-procedures-triggers-udfs) 和[合并过程](https://learn.microsoft.com/zh-cn/azure/cosmos-db/nosql/how-to-manage-conflicts?tabs=dotnetv2%2Capi-async%2Casync)。
	* [生存时间 (TTL)](https://learn.microsoft.com/en-us/azure/cosmos-db/nosql/time-to-live)
		* [配置TTL](https://learn.microsoft.com/en-us/azure/cosmos-db/nosql/how-to-time-to-live?tabs=dotnet-sdk-v3)
	* 吞吐量配置 throughput
		* **专用吞吐量**：对容器预配的吞吐量专门保留给该容器使用。
		* **共享吞吐量**：
	*   [Azure Cosmos DB 容器的属性](https://learn.microsoft.com/zh-cn/azure/cosmos-db/resource-model#properties-of-an-azure-cosmos-db-container)
* **==Items==**
	* 容器中的存数据的items
	* [通用属性](https://learn.microsoft.com/zh-cn/azure/cosmos-db/resource-model#properties-of-an-item)

## 唯一键

* Unique key
* 使用唯一键可确保逻辑分区内一个或多个值的唯一性。
* 用了就不能改
* ![[Pasted image 20230328140243.png|250]]

## 分区键

* **逻辑分区**由一组具有相同分区键的项构成。每个逻辑分区最多可保存 20 GB 的数据。
* 一个或多个逻辑分区映射到一个**物理分区**。每个单独的物理分区最多可以存储 50 GB 数据
* 

## 吞吐量

* Throughput 
* RU = 1kb

## 分析存储

![[Pasted image 20230327134518.png]]

## 索引策略

* IndexPolicy

#### 索引了解

==项 -> 树 -> 属性路径==

```json
	// item 如下
	{
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 },
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }  
    // 实际存储中对应的树结构如下
```
	![[Pasted image 20230328133005.png|500]]
```sh
	# 实际存储的的路径如下
   /locations/0/country:"Germany"
   /locations/0/city:"Berlin"
   /locations/1/country:"France"
   /locations/1/city:"Paris"
   /headquarters/country:"Belgium"
   /headquarters/employees:250
   /exports/0/city:"Moscow"
   /exports/1/city:"Athens"
```

#### 索引模式

* **一致 Consistent**：创建、更新或删除项时，索引将以同步方式更新。
* **无 none**：容器禁用索引。

* **默认索引**
	* **automatic** ， 默认为 true，写入项时自动为其编制索引。可以关闭，false

#### 索引类型

##### 范围索引 range-index

* 基于**已排序的树形结构**，可用于**标量值**（字符串或数字，可比较大小）
* 
```json
// 范围索引举例
{
    "indexingMode": "consistent",
    "includedPaths": [            // 范围索引不包含的路径
        {
            "path": "/path/to/included/property/?"
        },
        {
            "path": "/path/to/root/of/multiple/included/properties/*"
        }
    ],
    "excludedPaths": [            // 范围索引包含的路径
        {
            "path": "/*"
        }
    ]
}
```
##### 空间索引 spatial-index

* 空间索引可对地理空间对象（例如点、线、多边形和多面）进行有效查询。
* [官方文档](https://learn.microsoft.com/zh-cn/azure/cosmos-db/index-overview#spatial-index)
```json
{
    "automatic": true,
    "indexingMode": "Consistent",
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/*",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ],
    "excludedPaths": []
}
```
##### 组合索引 composite-indexes

* 多个字段执行操作时，组合索引可提高效率。
* 使用时需要前后位置对应
* 
```json
//  name asc, age desc 的组合索引
{  
    "automatic":true,
    "indexingMode":"Consistent",
    "includedPaths":[  
        {  
            "path":"/*"
        }
    ],
    "excludedPaths":[],
    "compositeIndexes":[  
        [  
            {  
                "path":"/name",
                "order":"ascending"
            },
            {  
                "path":"/age",
                "order":"descending"
            }
        ]
    ]
}
```
#### 无索引
```json
{
	"indexingMode":"none",
}
```
#### 自定义索引策略

*  **包含和排除属性路径**
	* **标量值的路径**以 `/?` 结尾：`headquarters` 的 `employees` 路径是 `/headquarters/employees/?`
	- **数组**值的路径通过 `/[]` 表示： `locations` 的 `country` 路径是 `/locations/[]/country/?`
	- 使用 `/*` 匹配**任意元素**：  `headquarters` 下的任何内容的路径是 `/headquarters/*`

*  `ORDER BY` 子句（该子句包含两个或更多个属性）的查询需要一个**组合索引**。
	*   两个或更多个属性路径。 属性路径的定义顺序非常重要。
	-   顺序（升序或降序）。
	- 



## 生存时间 (TTL)

* 依赖索引，indexmode != none