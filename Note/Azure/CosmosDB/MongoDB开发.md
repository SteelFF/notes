## 概述

基于 RU & 基于 vCore
* 基于 RU 更适合云原生
* 基于 vCore 更适合 mongoDB 迁移

## Account 创建

[要看文档](https://learn.microsoft.com/zh-cn/azure/cosmos-db/mongodb/quickstart-python?tabs=azure-cli%2Cvenv-windows%2Cwindows#create-an-azure-cosmos-db-account)

## 获取 MongoDB 连接字符串

[文档](https://learn.microsoft.com/zh-cn/azure/cosmos-db/mongodb/quickstart-python?tabs=azure-cli%2Cvenv-windows%2Cwindows#get-mongodb-connection-string)

## 开发代码


```python
import os
import sys
from random import randint

import pymongo
from dotenv import load_dotenv

# 从 .env 文件中定义的环境变量获取连接信息
load_dotenv()
CONNECTION_STRING = os.environ.get("COSMOS_CONNECTION_STRING") # 连接字符串
# 定义常量
DB_NAME = "adventureworks"   # DB name
COLLECTION_NAME = "products" # 集合名

# 连接到 Azure Cosmos DB
client = pymongo.MongoClient(CONNECTION_STRING)

# 获取数据库
# Create database if it doesn't exist
db = client[DB_NAME]
if DB_NAME not in client.list_database_names():
    # 400 RU 吞吐量  可以共享
    db.command({"customAction": "CreateDatabase", "offerThroughput": 400})
    print("Created db '{}' with shared throughput.\n".format(DB_NAME))
else:
    print("Using database: '{}'.\n".format(DB_NAME))

# 获取集合 表
# Create collection if it doesn't exist
collection = db[COLLECTION_NAME]
if COLLECTION_NAME not in db.list_collection_names():
    # 创建一个使用数据库共享吞吐量的未分片集合
    db.command(
        {"customAction": "CreateCollection", "collection": COLLECTION_NAME}
    )
    print("Created collection '{}'.\n".format(COLLECTION_NAME))
else:
    print("Using collection: '{}'.\n".format(COLLECTION_NAME))

### 创建索引
indexes = [
    {"key": {"_id": 1}, "name": "_id_1"},
    {"key": {"name": 2}, "name": "_id_2"},
]
db.command(
    {
        "customAction": "UpdateCollection",
        "collection": COLLECTION_NAME,
        "indexes": indexes,
    }
)
print("Indexes are: {}\n".format(sorted(collection.index_information())))

### 创建文档 行
"""Create new document and upsert (create or replace) to collection"""
product = {
    "category": "gear-surf-surfboards",
    "name": "Yamba Surfboard-{}".format(randint(50, 5000)),
    "quantity": 1,
    "sale": False,
}
result = collection.update_one(
    {"name": product["name"]}, {"$set": product}, upsert=True
)
print("Upserted document with _id {}\n".format(result.upserted_id))

### 获取文档
doc = collection.find_one({"_id": result.upserted_id})
print("Found a document with _id {}: {}\n".format(result.upserted_id, doc))

"""Query for documents in the collection"""
print("Products with category 'gear-surf-surfboards':\n")
allProductsQuery = {"category": "gear-surf-surfboards"}
for doc in collection.find(allProductsQuery).sort(
    "name", pymongo.ASCENDING
):
    print("Found a product with _id {}: {}\n".format(doc["_id"], doc))
```

## spark 数据迁移
```python
from pyspark.sql import SparkSession

sourceConnectionString = "mongodb://<USERNAME>:<PASSWORD>@<HOST>:<PORT>/<AUTHDB>"
sourceDb = "<DB NAME>"
sourceCollection =  "<COLLECTIONNAME>"
targetConnectionString = "mongodb://<ACCOUNTNAME>:<PASSWORD>@<ACCOUNTNAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&retrywrites=false&maxIdleTimeMS=120000&appName=@<ACCOUNTNAME>@"
targetDb = "<DB NAME>"
targetCollection =  "<COLLECTIONNAME>"

my_spark = SparkSession \
    .builder \
    .appName("myApp") \
    .getOrCreate()

df = my_spark.read.format("com.mongodb.spark.sql.DefaultSource") \
	.option("uri", sourceConnectionString) \
	.option("database", sourceDb) \
	.option("collection", sourceCollection) \
	.load()

df.write.format("mongo") \
    .mode("append") \
    .option("uri", argetConnectionString) \
    .option("maxBatchSize",2500) \
    .option("database", targetDb) \
    .option("collection", targetCollection) \
    .save()
```