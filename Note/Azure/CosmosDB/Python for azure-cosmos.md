## 概述

#### 源码

* [GitHub](https://github.com/Azure/azure-sdk-for-python)
* [Pypi下载](https://pypi.org/project/azure-cosmos/)
* [微软 Cosmos DB包搜索引擎](https://learn.microsoft.com/zh-cn/python/api/azure-cosmos/?view=azure-python)

#### azure-cosmos 包解析

==用于常用的与CosmosDB交互的场景==

* [examples.py](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/cosmos/azure-cosmos/samples/examples.py) 
	* 创建数据库、容器、数据库用户
	* Item 的 CRUD  
	* 查询项目的容器 
* [database_management.py](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/cosmos/azure-cosmos/samples/database_management.py) 数据库操作
	* Database 的 CRUD 
	* 查询 指定|所有 Database
* [container_management.py](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/cosmos/azure-cosmos/samples/container_management.py)   容器操作
	* Contariner 的 CRUD 
	* 查询 指定|所有 容器 
	* 管理容器配置的吞吐量 
* [item_management.py](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/cosmos/azure-cosmos/samples/document_management.py)  Item 的 CRUD 
* [index_management.py](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/cosmos/azure-cosmos/samples/index_management.py) 对未分区容器中的项目资源的基本 CRUD 操作
* [change_feed_management.py](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/cosmos/azure-cosmos/samples/change_feed_management.py) 使用 Change Feed 并迭代结果的示例
* [access_cosmos_with_resource_token.py](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/cosmos/azure-cosmos/samples/access_cosmos_with_resource_token.py)  如何获取和使用允许限制访问数据的资源令牌
* [multi-master operations](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/cosmos/azure-cosmos/samples/MultiMasterOperations) 多主机操作

#### 前置&安装

* [Azure Cosmos account](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python#create-a-database-account)
* `pip install azure-cosmos`


## azure-cosmos

### example.py

#### CosmosClient
```python
# [create_client]
from azure.cosmos import exceptions, CosmosClient, PartitionKey
import os

url = os.environ["ACCOUNT_URI"]
key = os.environ["ACCOUNT_KEY"]
client = CosmosClient(url, key)
# [END create_client]
```
#### create database & container & user
```python
# [create_database]  建database，id存在抛异常
database_name = "testDatabase"
try:
    database = client.create_database(id=database_name)
except exceptions.CosmosResourceExistsError:
    database = client.get_database_client(database=database_name)
# [END create_database]


# [create_container] 建container，id存在抛异常
container_name = "products"
try:
    container = database.create_container(
        id=container_name, partition_key=PartitionKey(path="/productName")
    )
except exceptions.CosmosResourceExistsError:
    container = database.get_container_client(container_name)
# [END create_container]

# [create_container_with_settings] 建container，并添加主键和TTL配置
customer_container_name = "customers"
try:
    customer_container = database.create_container(
        id=customer_container_name,
        partition_key=PartitionKey(path="/city"),
        default_ttl=200,
    )
except exceptions.CosmosResourceExistsError:
    customer_container = database.get_container_client(customer_container_name)
# [END create_container_with_settings]

# [create_user] 创建用户
try:
    database.create_user(dict(id="Walter Harp"))
except exceptions.CosmosResourceExistsError:
    print("A user with that ID already exists.")
except exceptions.CosmosHttpResponseError as failure:
    print("Failed to create user. Status code:{}".format(failure.status_code))
# [END create_user]

```
#### get & list container
```python
# [get_container] 通过 (client->database->container) 获取container, 没有会报异常
database = client.get_database_client(database_name)
container = database.get_container_client(container_name)
# [END get_container]

# [list_containers] 显示所有container
database = client.get_database_client(database_name)
for container in database.list_containers():
    print("Container ID: {}".format(container['id']))
# [END list_containers]
```
#### item upsert
```python
# [upsert_items] 增删 item
container = database.get_container_client(container_name)
for i in range(1, 10):
    container.upsert_item(
        dict(id="item{}".format(i), productName="Widget", productModel="Model {}".format(i))
    )
# [END upsert_items]

# [update_item] 修改已经存在的 item
item = container.read_item("item2", partition_key="Widget")
item["productModel"] = "DISCONTINUED"
updated_item = container.upsert_item(item)
# [END update_item]
```
#### item query 
```python
# [开始 query_items] 使用 sql-like 语法查询 item
import json
for item in container.query_items(
    query='SELECT * FROM products p WHERE p.productModel <> "DISCONTINUED"',
    enable_cross_partition_query=True,
):
    print(json.dumps(item, indent=True))
# [END query_items]

# [开始 query_items_param] 使用参数化查询 item
discontinued_items = container.query_items(
    query='SELECT * FROM products p WHERE p.productModel = @model AND p.productName="Widget"',
    parameters=[dict(name="@model", value="DISCONTINUED")],
)
for item in discontinued_items:
    print(json.dumps(item, indent=True))
# [END query_items_param]
```
#### item delete 
```python
# [delete_items] 删除 item
for item in container.query_items(
    query='SELECT * FROM products p WHERE p.productModel = "DISCONTINUED" AND p.productName="Widget"'
):
    container.delete_item(item, partition_key="Widget")
# [END delete_items]
```
#### properties get database 
```python
# [get_database_properties]  显示数据库的属性
properties = database.read()
print(json.dumps(properties, indent=True))
# [END get_database_properties]
```
#### properties reset container 
```python
# [START reset_container_properties] 修改 container 的属性， TTL 变为 1h
database.replace_container(container, partition_key=PartitionKey(path='/productName'), default_ttl=3600)
# 显示容器被 修改后的TTL属性
container_props = database.get_container_client(container_name).read()
print("New container TTL: {}".format(json.dumps(container_props['defaultTtl'])))
# [END reset_container_properties]
```

### database_management.py

#### import env
```python
import azure.cosmos.cosmos_client as cosmos_client
import azure.cosmos.exceptions as exceptions
from azure.cosmos import ThroughputProperties
import config
```
#### QueryDatabases 
```python
# 查询所有数据库
HOST = config.settings['host']
MASTER_KEY = config.settings['master_key']
DATABASE_ID = config.settings['database_id']
def find_database(client, id):
    print('1. Query for Database')
    databases = list(client.query_databases({
        "query": "SELECT * FROM r WHERE r.id=@id",
        "parameters": [
            { "name":"@id", "value": id }
        ]
    }))
    if len(databases) > 0:
        print('Database with id \'{0}\' was found'.format(id))
    else:
        print('No database with id \'{0}\' was found'. format(id))

```
#### CreateDatabase
```python
# 创建数据库
def create_database1(client, id):
    print("\n2. Create Database")
    try:
        client.create_database(id=id)
        print('Database with id \'{0}\' created'.format(id))
    except exceptions.CosmosResourceExistsError:
        print('A database with id \'{0}\' already exists'.format(id))

# 创建数据库，使用自动缩放设置
def create_database2(client, id):
    print("\n2. Create Database - With auto scale settings")
    try:
        client.create_database(
            id=id,
            offer_throughput=ThroughputProperties(
	            auto_scale_max_throughput=5000, 
	            auto_scale_increment_percent=0
	        )
        )
        print('Database with id \'{0}\' created'.format(id))
    except exceptions.CosmosResourceExistsError:
        print('A database with id \'{0}\' already exists'.format(id))
```
#### readDatabase
```python
# 通过 id 获取 database
def read_database(client, id):
    print("\n3. Get a Database by id")
    try:
        database = client.get_database_client(id)
        database.read()
        print('Database with id \'{0}\' was found, it\'s link is {1}'.format(id, database.database_link))
    except exceptions.CosmosResourceNotFoundError:
        print('A database with id \'{0}\' does not exist'.format(id))
```
List Databases
```python
# 获取所有databases
def list_databases(client):
    print("\n4. List all Databases on an account")

    print('Databases:')

    databases = list(client.list_databases())

    if not databases:
        return

    for database in databases:
        print(database['id'])
```
#### DeleteDatabase
```python
# 删除数据库
def delete_database(client, id):
    print("\n5. Delete Database")
    try:
        client.delete_database(id)
        print('Database with id \'{0}\' was deleted'.format(id))
    except exceptions.CosmosResourceNotFoundError:
        print('A database with id \'{0}\' does not exist'.format(id))
```
#### Test
``` python
def run_sample():
    client = cosmos_client.CosmosClient(HOST, {'masterKey': MASTER_KEY} )
    try:
        # query for a database
        find_database(client, DATABASE_ID)

        # create a database
        create_database2(client, DATABASE_ID)

        # get a database using its id
        read_database(client, DATABASE_ID)

        # list all databases on an account
        list_databases(client)

        # delete database by id
        delete_database(client, DATABASE_ID)

    except exceptions.CosmosHttpResponseError as e:
        print('\nrun_sample has caught an error. {0}'.format(e.message))

    finally:
        print("\nrun_sample done")

if __name__ == '__main__':
    run_sample()
```
### container_management.py

#### import env
```python
import azure.cosmos.cosmos_client as cosmos_client
import azure.cosmos.exceptions as exceptions
from azure.cosmos.partition_key import PartitionKey
from azure.cosmos import ThroughputProperties
import config
```
#### Query for Container
```python
# 查找 container
HOST = config.settings['host']
MASTER_KEY = config.settings['master_key']
DATABASE_ID = config.settings['database_id']
CONTAINER_ID = config.settings['container_id']

def find_container(db, id):
    print('1. Query for Container')
    containers = list(db.query_containers(
        {
            "query": "SELECT * FROM r WHERE r.id=@id",
            "parameters": [
                { "name":"@id", "value": id }
            ]
        }
    ))
    if len(containers) > 0:
        print('Container with id \'{0}\' was found'.format(id))
    else:
        print('No container with id \'{0}\' was found'. format(id))
```
#### Container Basic Create
```python
# 默认创建 Container，有400个RU，具有不同的索引、分区和存储选项
def create_container1(db, id):
    partition_key = PartitionKey(path='/id', kind='Hash')
    print("\n2.1 Create Container - Basic")
    try:
        db.create_container(id=id, partition_key=partition_key)
        print('Container with id \'{0}\' created'.format(id))
    except exceptions.CosmosResourceExistsError:
        print('A container with id \'{0}\' already exists'.format(id))
```
#### Container with custom IndexPolicy
```python
# 使用 自定义索引策略 创建container
def create_container2(db, id):
    print("\n2.2 Create Container - With custom index policy")
    try:
        coll = {
            "id": id+"_container_custom_index_policy",
            "indexingPolicy": {
                "automatic": False   # 写入项时不自动为其编制索引
            }
        }
        container = db.create_container(
            id=coll['id'],
            partition_key=partition_key,
            indexing_policy=coll['indexingPolicy']  # 设置索引策略
        )
        properties = container.read()
        print('Container with id \'{0}\' created'.format(container.id))
        print('IndexPolicy Mode - \'{0}\''.format(properties['indexingPolicy']['indexingMode']))
        print('IndexPolicy Automatic - \'{0}\''.format(properties['indexingPolicy']['automatic']))
    except exceptions.CosmosResourceExistsError:
        print('A container with id \'{0}\' already exists'.format(coll['id']))
```
#### Container With provisioned throughput
```python
# 创建Container时自定义配置的吞吐量
def create_container3(db, id):
    print("\n2.3 Create Container - With custom provisioned throughput")
    try:
        container = db.create_container(
            id=id+"_container_custom_throughput",
            partition_key=partition_key,
            offer_throughput=400          # 指定 throughput
        )
        print('Container with id \'{0}\' created'.format(container.id))
    except exceptions.CosmosResourceExistsError:
        print('A container with id \'{0}\' already exists'.format(coll['id']))
```
#### Container With Unique keys
```python
# 创建Container时，指定唯一key
def create_container4(db, id):
    print("\n2.4 Create Container - With Unique keys")
    try:
        container = db.create_container(
            id= id+"_container_unique_keys",
            partition_key=partition_key,
            # 两个属性，指定唯一键
            unique_key_policy={'uniqueKeys': [{'paths': ['/field1/field2', '/field3']}]}
        )
        properties = container.read()
        unique_key_paths = properties['uniqueKeyPolicy']['uniqueKeys'][0]['paths']
        print('Container with id \'{0}\' created'.format(container.id))
        print('Unique Key Paths - \'{0}\', \'{1}\''.format(unique_key_paths[0], unique_key_paths[1]))
    except exceptions.CosmosResourceExistsError:
        print('A container with id \'container_unique_keys\' already exists')
```
#### Container with Partition key V2
```python
# 默认的，创建Contaienr时指定 分区key
def create_container5(db, id):
    print("\n2.5 Create Container - With Partition key V2 (Default)")
    try:
        container = db.create_container(
            id=id+"_container_partition_key_v2",
            partition_key=PartitionKey(path='/id', kind='Hash')
        )
        properties = container.read()
        print('Container with id \'{0}\' created'.format(container.id))
        print('Partition Key - \'{0}\''.format(properties['partitionKey']))
    except exceptions.CosmosResourceExistsError:
        print('A container with id \'container_partition_key_v2\' already exists')
```
#### Container with Partition key V1
```python
# 默认的，创建Contaienr时指定 分区key
def create_container6(db, id):
    print("\n2.6 Create Container - With Partition key V1")
    try:
        container = db.create_container(
            id=id+"_container_partition_key_v1",
            partition_key=PartitionKey(path='/id', kind='Hash', version=1)
        )
        properties = container.read()
        print('Container with id \'{0}\' created'.format(container.id))
        print('Partition Key - \'{0}\''.format(properties['partitionKey']))
    except exceptions.CosmosResourceExistsError:
        print('A container with id \'container_partition_key_v1\' already exists')
```
#### Container With analytical store enabled
```python
# 分析存储，analytical_storage_ttl 为 None 关闭分析存储， -1 打开分析存储，并且没有TTL
# 分析存储只能在启用 Synapse Link 的帐户上启用
def create_container7(db, id):
    print("\n2.7 Create Container - With analytical store enabled")
    try:
        container = db.create_container(
            id=id+"_container_analytical_store",
            partition_key=PartitionKey(path='/id', kind='Hash'), 
            analytical_storage_ttl=None         # 关闭分析存储
        )
        properties = container.read()
        print('Container with id \'{0}\' created'.format(container.id))
        print('Partition Key - \'{0}\''.format(properties['partitionKey']))
    except exceptions.CosmosResourceExistsError:
        print('A container with id \'_container_analytical_store\' already exists')
```
#### Container With auto scale settings
```python
# 自动缩放设置
def create_container8(db, id):
    print("\n2.8 Create Container - With auto scale settings")
    try:
        container = db.create_container(
            id=id+"_container_auto_scale_settings",
            partition_key=partition_key,
            offer_throughput=ThroughputProperties(
	            auto_scale_max_throughput=5000, 
	            auto_scale_increment_percent=0
	        )
        )
        print('Container with id \'{0}\' created'.format(container.id))

    except exceptions.CosmosResourceExistsError:
        print('A container with id \'{0}\' already exists'.format(coll['id']))
```
#### Throughput get
```python
# 获取容器吞吐量， Container 与 Offer 吞吐量对象 松耦合
# Offer.offerResourceId == Container._rid
# Offer.resource == Container._self
def get_provisioned_throughput(db, id):
    print("\n3.1 Get Container provisioned throughput (RU/s)")
    try:
        container = db.get_container_client(container=id)
        offer = container.get_throughput()
        print('Found Offer \'{0}\' for Container \'{1}\' and its throughput is \'{2}\''.format(offer.properties['id'], container.id, offer.properties['content']['offerThroughput']))
    except exceptions.CosmosResourceExistsError:
        print('A container with id \'{0}\' does not exist'.format(id))
```

#### Throughput Change provisioned 
```python
# 修改容器吞吐量
def change_provisioned_throughput(db, id):
    print("\n3.2 Change Provisioned Throughput of Container")
    offer = container.replace_throughput(offer.offer_throughput + 100)
    print('Replaced Offer. Provisioned Throughput is now \'{0}\''.format(offer.properties['content']['offerThroughput']))
```
#### Get a Container
```python
# 通过 id 返回指定容器
def read_Container(db, id):
    print("\n4. Get a Container by id")
    try:
        container = db.get_container_client(id)
        container.read()
        print('Container with id \'{0}\' was found, it\'s link is {1}'.format(container.id, container.container_link))
    except exceptions.CosmosResourceNotFoundError:
        print('A container with id \'{0}\' does not exist'.format(id))

```
#### List all Container
```python
# 返回db中的所有容器
def list_Containers(db):
    print("\n5. List all Container in a Database")
    print('Containers:')
    containers = list(db.list_containers())
    if not containers:
        return
    for container in containers:
        print(container['id'])
```
#### Delete Container
```python
# 删除指定 id 的容器
def delete_Container(db, id):
    print("\n6. Delete Container")
    try:
        db.delete_container(id)
        print('Container with id \'{0}\' was deleted'.format(id))
    except exceptions.CosmosResourceNotFoundError:
        print('A container with id \'{0}\' does not exist'.format(id))
```
#### Test
```python
def run_sample():
    client = cosmos_client.CosmosClient(HOST, {'masterKey': MASTER_KEY} )
    try:
		# get db 
        try:
            db = client.create_database(id=DATABASE_ID)
        except exceptions.CosmosResourceExistsError:
            db = client.get_database_client(DATABASE_ID)
            
        find_container(db, CONTAINER_ID)
        create_container1(db, CONTAINER_ID)
        get_provisioned_throughput(db, CONTAINER_ID)
        change_provisioned_throughput(db, CONTAINER_ID)
        read_Container(db, CONTAINER_ID)
        list_Containers(db)
        delete_Container(db, CONTAINER_ID)        # delete container by id
        try:
            client.delete_database(db)        # cleanup database after sample
        except exceptions.CosmosResourceNotFoundError:
            pass
    except exceptions.CosmosHttpResponseError as e:
        print('\nrun_sample has caught an error. {0}'.format(e.message))
    finally:
            print("\nrun_sample done")

if __name__ == '__main__':
    run_sample()
```
### item_management.py

#### import env

```python
import azure.cosmos.cosmos_client as cosmos_client
import azure.cosmos.exceptions as exceptions
from azure.cosmos.partition_key import PartitionKey
import datetime
import config

HOST = config.settings['host']
MASTER_KEY = config.settings['master_key']
DATABASE_ID = config.settings['database_id']
CONTAINER_ID = config.settings['container_id']
```
#### 生成订单对象的方法
```python
# 普通订单
def get_sales_order(item_id):
    order1 = {'id' : item_id,
            'account_number' : 'Account1',
            'purchase_order_number' : 'PO18009186470',
            'order_date' : datetime.date(2005,1,10).strftime('%c'),
            'subtotal' : 419.4589,
            'tax_amount' : 12.5838,
            'freight' : 472.3108,
            'total_due' : 985.018,
            'items' : [
                {'order_qty' : 1,
                    'product_id' : 100,
                    'unit_price' : 418.4589,
                    'line_price' : 418.4589
                }
                ],
            'ttl' : 60 * 60 * 24 * 30
            }

    return order1
# 新订单，有新字段添加
def get_sales_order_v2(item_id):
    order2 = {'id' : item_id,
            'account_number' : 'Account2',
            'purchase_order_number' : 'PO15428132599',
            'order_date' : datetime.date(2005,7,11).strftime('%c'),
            'due_date' : datetime.date(2005,7,21).strftime('%c'),        # new
            'shipped_date' : datetime.date(2005,7,15).strftime('%c'),    # new 
            'subtotal' : 6107.0820,
            'tax_amount' : 586.1203,
            'freight' : 183.1626,
            'discount_amt' : 1982.872,                                   # new
            'total_due' : 4893.3929,
            'items' : [
                {'order_qty' : 3,                  
                    'product_code' : 'A-123',      # replace
                    'product_name' : 'Product 1',  # new 
                    'currency_symbol' : '$',       # new
                    'currency_code' : 'USD',       
                    'unit_price' : 17.1,          
                    'line_price' : 5.7
                }
                ],
            'ttl' : 60 * 60 * 24 * 30
            }

    return order2
```
#### Create Items
```python
# 可以创建不同 schema 的 item
def create_items(container):
    print('Creating Items')
    print('\n1.1 Create Item\n')

    sales_order = get_sales_order("SalesOrder1")
    container.create_item(body=sales_order)

    sales_order2 = get_sales_order_v2("SalesOrder2")
    container.create_item(body=sales_order2)

```
#### Read Item
```python
# 读 Item，需要指定 分区键
def read_item(container, doc_id):
    print('\n1.2 Reading Item by Id\n')
    response = container.read_item(item=doc_id, partition_key=doc_id)

    print('Item read by Id {0}'.format(doc_id))
    print('Account Number: {0}'.format(response.get('account_number')))
    print('Subtotal: {0}'.format(response.get('subtotal')))

```
#### read_all_items(max_item_count)
```python
# 读取指定个数 | 全部 Item
def read_items(container):
    print('\n1.3 - Reading all items in a container\n')
    item_list = list(container.read_all_items(max_item_count=10))
    print('Found {0} items'.format(item_list.__len__()))
    for doc in item_list:
        print('Item Id: {0}'.format(doc.get('id')))

```
#### sql-like query Item
```python
# 通过 sql-like + 参数，进行item 查询
def query_items(container, doc_id):
    print('\n1.4 Querying for an  Item by Id\n')
    items = list(container.query_items(
        query="SELECT * FROM r WHERE r.id=@id",
        parameters=[
            { "name":"@id", "value": doc_id }
        ],
        enable_cross_partition_query=True  # 容器分区时，应设为true
    ))
    print('Item queried by Id {0}'.format(items[0].get("id")))

```
#### Replace Item
```python
# 更改 Item
def replace_item(container, doc_id):
    print('\n1.5 Replace an Item\n')
    read_item = container.read_item(item=doc_id, partition_key=doc_id)
    read_item['subtotal'] = read_item['subtotal'] + 1
    response = container.replace_item(item=read_item, body=read_item) ##更换item
    print('Replaced Item\'s Id is {0}, new subtotal={1}'.format(response['id'], response['subtotal']))
```
#### Upsert Item
```python
# 增删 Item
def upsert_item(container, doc_id):
    print('\n1.6 Upserting an item\n')
    read_item = container.read_item(item=doc_id, partition_key=doc_id)
    read_item['subtotal'] = read_item['subtotal'] + 1
    response = container.upsert_item(body=read_item)  # upsert_item
    print('Upserted Item\'s Id is {0}, new subtotal={1}'.format(response['id'], response['subtotal']))

```
#### Deleted item
```python
# 删除 Item
def delete_item(container, doc_id):
    print('\n1.7 Deleting Item by Id\n')
    response = container.delete_item(item=doc_id, partition_key=doc_id)
    print('Deleted item\'s Id is {0}'.format(doc_id))
```

#### Test
```python
def run_sample():
    client = cosmos_client.CosmosClient(HOST, {'masterKey': MASTER_KEY} )
    try:
        # setup database for this sample
        db = client.create_database_if_not_exists(id=DATABASE_ID)
        # setup container for this sample
        container = db.create_container_if_not_exists(id=CONTAINER_ID, partition_key=PartitionKey(path='/id', kind='Hash'))

        create_items(container)
        read_item(container, 'SalesOrder1')
        read_items(container)
        query_items(container, 'SalesOrder1')
        replace_item(container, 'SalesOrder1')
        upsert_item(container, 'SalesOrder1')
        delete_item(container, 'SalesOrder1')

        # cleanup database after sample
        try:
            client.delete_database(db)

        except exceptions.CosmosResourceNotFoundError:
            pass

    except exceptions.CosmosHttpResponseError as e:
        print('\nrun_sample has caught an error. {0}'.format(e.message))

    finally:
            print("\nrun_sample done")


if __name__ == '__main__':
    run_sample()

```