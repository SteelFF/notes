

## DBFS工作区实用程序

##### 文件工具
[DBFS官网命令](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/databricks-utils#file-system-utility-dbutilsfs)
```Python
dbutils.help()         # 查看所有工具
dbutils.fs.help()      # 查看所有可操作DBFS的工具
dbutils.fs.help("cp")  # 显示 cp 命令用法

# 文件实用工具  dbutils.fs
dbutils.fs.cp("/FileStore/old_file.txt", "/tmp/new/new_file.txt")  # 复制
dbutils.fs.mv("/FileStore/my_file.txt", "/tmp/parent/child/grandchild")              # 移动
dbutils.fs.put("/tmp/hello_db.txt", "Hello, Databricks!", True)   # 上传，存在则覆盖
dbutils.fs.rm("/tmp/hello_db.txt")            # 删除
 
dbutils.fs.head("/tmp/my_file.txt", 25)  # 显示前25个bytes
dbutils.fs.ls("/tmp")
dbutils.fs.mkdirs("/tmp/parent/child/grandchild")

dbutils.fs.refreshMounts()      # 确保它们能接收最新的信息
dbutils.fs.mount(               # 挂载
  source = "wasbs://<container-name>@<storage-account-name>.blob.core.windows.net",
  mount_point = "/mnt/<mount-name>",
  extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})
dbutils.fs.unmount("/mnt/<mount-name>")  # 删除挂载
dbutils.fs.updateMount(
  source = "wasbs://<container-name>@<storage-account-name>.blob.core.windows.net",
  mount_point = "/mnt/<mount-name>",
  extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})           # 更新挂载
dbutils.fs.mounts()             # 查看挂载内容
```

##### 作业工具

[dbutils.jobs](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/databricks-utils#--jobs-utility-dbutilsjobs)

##### 笔记本实用工具 
[dbutils.notebook](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/databricks-utils#--notebook-utility-dbutilsnotebook)

##### 机密实用工具
[dbutils.secrets](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/databricks-utils#--secrets-utility-dbutilssecrets)

## Databricks 个人令牌 personal token
获取databricks token（[文档](https://docs.microsoft.com/zh-cn/azure/databricks/dev-tools/api/latest/authentication)）

个人令牌 非 Azure AD令牌
个人令牌存储
*   [机密管理](https://learn.microsoft.com/zh-cn/azure/databricks/security/secrets/)，并使用[机密实用程序 (dbutils.secrets)](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/databricks-utils#dbutils-secrets)在笔记本中检索令牌。
-   本地密钥存储，并使用 [Python keyring](https://pypi.org/project/keyring/) 包在运行时检索令牌。

#### 生成个人访问令牌
要为 Azure Databricks 用户创建 Azure Databricks 个人访问令牌，请执行以下操作：

1.  在 Azure Databricks 工作区中，单击顶部栏中 Azure Databricks 用户名，然后从下拉列表中选择“用户设置”。
2.  在“访问令牌”选项卡上，单击“生成新令牌”。
3.  （可选）输入有助于将来识别此令牌的注释，并将令牌的默认生存期更改为 90 天。 若要创建没有生存期的令牌（不建议），请将“生存期(天)”框留空（保留空白）。
4.  单击“生成” 。
5.  复制显示的令牌，然后单击“完成”。

#### 管理个人访问令牌

为工作区启用创建个人访问令牌的功能后，工作区管理员还可以监视令牌、控制哪些非管理员用户可以创建令牌和使用令牌，并为新令牌设置最长生命周期。
[官方文档](https://learn.microsoft.com/zh-cn/azure/databricks/administration-guide/access-control/tokens#requirements)

#### 使用个人访问令牌访问 Databricks REST API

###### 在 `.netrc` 文件中存储令牌并在 `curl` 中使用
使用 `machine`、`login` 和 `password` 属性创建 [.netrc](https://everything.curl.dev/usingcurl/netrc) 文件：
```ini
# .netrc
machine <databricks-instance>
login token
password <token-value>

# e.g. 多个计算机/令牌条目，请为每个条目添加一行
machine adb-1234567890123456.7.azuredatabricks.net login token password dapi1234567890ab1cde2f3ab456c7d89efa
machine adb-2345678901234567.8.azuredatabricks.net login token password dapi2345678901cd2efa3b4cd567e8f90abc
machine adb-3456789012345678.9.azuredatabricks.net login token password dapi3456789012de3fab4c5de678f9a01bcd
```
```sh
# <databricks-instance>  :如果工作区 URL 为 `https://adb-1234567890123456.7.azuredatabricks.net`，则 `<databricks-instance>` 为 `adb-1234567890123456.7.azuredatabricks.net`。
# `token` 固定值，就是字符串 token
# `<token-value>` 是令牌的值，例如 `dapi1234567890ab1cde2f3ab456c7d89efa`
```
```bash
# 使用 `--netrc`（也可以使用 `-n`）来调用 `.netrc` 文件。
curl --netrc -X GET https://adb-1234567890123456.7.azuredatabricks.net/api/2.0/clusters/list
```

###### 将令牌传递到 `Bearer` 身份验证
使用 `Bearer` 身份验证将令牌包含在标头中， 也可将此方法用于 `curl` 或你构建的任何客户端。
```shell
export DATABRICKS_TOKEN=dapi1234567890ab1cde2f3ab456c7d89efa

curl -X GET --header "Authorization: Bearer $DATABRICKS_TOKEN" \
https://adb-1234567890123456.7.azuredatabricks.net/api/2.0/clusters/list
```

## Databricks REST API

#### 概述

- 访问 Databricks REST API，必须[进行身份验证](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/api/latest/authentication)
	- 个人令牌身份验证
	- [AD身份验证](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/api/latest/aad/)
- 不同版本执行不同任务
	-   [2.0](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/api/2.0/) 和 [2.1](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/api/2.1/)，用于常规管理
	-   [1.2](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/api/1.2/)，用于直接在 Azure Databricks 运行命令
		-   帐户 API [2.0](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/api/latest/account)
		-   群集 API [2.0](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/api/latest/clusters)
		-   群集策略 API [2.0](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/api/latest/policies)
		-   数据世系 API [2.0](https://learn.microsoft.com/zh-cn/azure/databricks/data-governance/unity-catalog/data-lineage)
		-   Databricks SQL 查询、仪表板和警报 API [2.0](https://learn.microsoft.com/zh-cn/azure/databricks/sql/api/queries-dashboards)
		-   Databricks SQL 查询历史记录 API [2.0](https://learn.microsoft.com/zh-cn/azure/databricks/sql/api/query-history)
		-   Databricks SQL 仓库 API [2.0](https://learn.microsoft.com/zh-cn/azure/databricks/sql/api/sql-endpoints)
		-   DBFS API [2.0](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/api/latest/dbfs)
		-   Delta Live Tables API [2.0](https://learn.microsoft.com/zh-cn/azure/databricks/workflows/delta-live-tables/delta-live-tables-api-guide)
		-   Git 凭据 API [2.0](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/api/latest/gitcredentials)
		-   全局初始化脚本 API [2.0](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/api/latest/global-init-scripts)
		-   组 API [2.0](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/api/latest/groups)
		-   实例池 API [2.0](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/api/latest/instance-pools)
		-   IP 访问列表 API [2.0](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/api/latest/ip-access-list)
		-   作业 API [2.1](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/api/latest/jobs)、[2.0](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/api/2.0/jobs)
		-   库 API [2.0](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/api/latest/libraries)
		-   MLflow API [2.0](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/api/latest/mlflow)
		-   权限 API [2.0](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/api/latest/permissions)
		-   存储库 API [2.0](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/api/latest/repos)
		-   SCIM API [2.0](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/api/latest/scim/)
		-   机密 API [2.0](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/api/latest/secrets)
		-   令牌 API [2.0](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/api/latest/tokens)
		-   令牌管理 API [2.0](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/api/latest/token-management)
		-   Unity Catalog API [2.1](https://api-docs.databricks.com/rest/latest/unity-catalog-api-specification-2-1.html)、[2.0](https://api-docs.databricks.com/rest/latest/unity-catalog-api-specification-2-0.html)
		-   工作区 API [2.0](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/api/latest/workspace)
		-   API [1.2](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/api/1.2/)

#### 文件集群示例
##### 获取群集的 gzip 压缩列表
```sh
curl -n -H "Accept-Encoding: gzip" https://<databricks-instance>/api/2.0/clusters/list > clusters.gz
```
##### 将大文件上传到 DBFS
```python
import json
import requests
import base64

DOMAIN = '<databricks-instance>'
TOKEN = '<your-token>'
BASE_URL = 'https://%s/api/2.0/dbfs/' % (DOMAIN)

def dbfs_rpc(action, body):
  """ A helper function to make the DBFS API request, request/response is encoded/decoded as JSON """
  response = requests.post(
    BASE_URL + action,
    headers={'Authorization': 'Bearer %s' % TOKEN },
    json=body
  )
  return response.json()

# Create a handle that will be used to add blocks
handle = dbfs_rpc("create", {"path": "/temp/upload_large_file", "overwrite": "true"})['handle']
with open('/a/local/file') as f:
  while True:
    # A block can be at most 1MB
    block = f.read(1 << 20)
    if not block:
        break
    data = base64.standard_b64encode(block)
    dbfs_rpc("add-block", {"handle": handle, "data": data})
# close the handle to finish uploading
dbfs_rpc("close", {"handle": handle})
```
##### 创建 Python 3 群集
```python
import requests

DOMAIN = '<databricks-instance>'
TOKEN = '<your-token>'

response = requests.post(
  'https://%s/api/2.0/clusters/create' % (DOMAIN),
  headers={'Authorization': 'Bearer %s' % TOKEN},
  json={
    "cluster_name": "my-cluster",
    "spark_version": "5.5.x-scala2.11",
    "node_type_id": "Standard_D3_v2",
    "spark_env_vars": {
      "PYSPARK_PYTHON": "/databricks/python3/bin/python3",
    }
  }
)

if response.status_code == 200:
  print(response.json()['cluster_id'])
else:
  print("Error launching cluster: %s: %s" % (response.json()["error_code"], response.json()["message"]))
```

#### 作业 API 示例

##### 创建 Python 作业

* Databricks Runtime 包括 Spark 和很多其他组件
* Databricks Light 相对于 Databricks Runtime 少了很多组件
* 版本字符串：
	* Runtime : "spark_version": "7.3.x-scala2.12" [详解](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/api/#--runtime-version-strings)
	* Light :  "spark_version": "apache-spark-2.4.x-scala2.11" [详解](https://learn.microsoft.com/zh-cn/azure/databricks/dev-tools/api/#databricks-light)

```shell
# 1 将 py 文件上传到 dbfs
dbfs cp pi.py dbfs:/docs/pi.py

# 2.1 使用 Databricks Runtime 创建作业
curl -n -X POST -H 'Content-Type: application/json' -d \
'{
  "name": "SparkPi Python job",
  "new_cluster": {
    "spark_version": "7.3.x-scala2.12",
    "node_type_id": "Standard_D3_v2",
    "num_workers": 2
  },
  "spark_python_task": {
    "python_file": "dbfs:/docs/pi.py",
    "parameters": [
      "10"
    ]
  }
}' https://<databricks-instance>/api/2.0/jobs/create

# 2.2 也可以使用 Databricks Light 创建
curl -n -X POST -H 'Content-Type: application/json' -d
'{
    "name": "SparkPi Python job",
    "new_cluster": {
     "spark_version": "apache-spark-2.4.x-scala2.11",
     "node_type_id": "Standard_D3_v2",
     "num_workers": 2
    },
    "spark_python_task": {
     "python_file": "dbfs:/docs/pi.py",
     "parameters": [
       "10"
     ]
  }
}' https://<databricks-instance>/api/2.0/jobs/create
# 返回 `job-id`，随后可使用它来运行作业。
```
##### 创建 spark-submit 作业
```sh
# jar 包上传
dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar

# 创建作业
curl -n \
-X POST -H 'Content-Type: application/json' \
-d '{
     "name": "SparkPi spark-submit job",
     "new_cluster": {
       "spark_version": "7.3.x-scala2.12",
       "node_type_id": "Standard_DS3_v2",
       "num_workers": 2
       },
    "spark_submit_task": {
       "parameters": [
         "--class",
         "org.apache.spark.examples.SparkPi",
         "dbfs:/docs/sparkpi.jar",
         "10"
      ]
    }
}' https://<databricks-instance>/api/2.0/jobs/create
# 返回 `job-id`，随后可使用它来运行作业。
```
##### 运行作业
```sh
curl -n \
-X POST -H 'Content-Type: application/json' \
-d '{ "job_id": <job-id> }' https://<databricks-instance>/api/2.0/jobs/run-now

# 到 `https://<databricks-instance>/#job/<job-id>`，你将可以看到作业正在运行。
```

#### 工作区示例

列出、创建、删除、导出和导入工作区对象以及获取其相关信息

##### 列出笔记本或文件夹
```sh
curl -n -X GET -H 'Content-Type: application/json' -d \
'{
  "path": "/Users/user@example.com/"
}' https://<databricks-instance>/api/2.0/workspace/list
```
```json
// 响应信息
{
  "objects": [
    {
     "object_type": "DIRECTORY",
     "path": "/Users/user@example.com/folder"
    },
    {
     "object_type": "NOTEBOOK",
     "language": "PYTHON",
     "path": "/Users/user@example.com/notebook1"
    },
    {
     "object_type": "NOTEBOOK",
     "language": "SCALA",
     "path": "/Users/user@example.com/notebook2"
    }
  ]
}
```
##### 创建文件夹
```sh
# 创建一个文件夹，以递归方式创建文件夹
curl -n -X POST -H 'Content-Type: application/json' -d \
'{
  "path": "/Users/user@example.com/new/folder"
}' https://<databricks-instance>/api/2.0/workspace/mkdirs
```
##### 删除笔记本或文件夹
```shell
# `recursive` 是否递归删除
curl -n -X POST -H 'Content-Type: application/json' -d \
'{
  "path": "/Users/user@example.com/new/folder",
  "recursive": "false"
}' https://<databricks-instance>/api/2.0/workspace/delete
# 成功返回 空字符串
```
##### 导出笔记本或文件夹
```shell
# 导出格式  笔记本：`SOURCE`、`HTML`、`JUPYTER`、`DBC`。 文件夹 `DBC`。
curl -n  -X GET \
-d '{ "path": "/Users/user@example.com/notebook", "format": "SOURCE" }' \
https://<databricks-instance>/api/2.0/workspace/export

# 或者，可以直接下载导出的笔记本。
curl -n -X GET "https://<databricks-instance>/api/2.0/workspace/export?format=SOURCE&direct_download=true&path=/Users/user@example.com/notebook"
```
##### 导入笔记本或目录
```shell
# 支持格式（`SOURCE`、`HTML`、`JUPYTER`、`DBC`）
#  `SOURCE`必须指定 `language`
#  `content` 参数包含 base64 编码的笔记本内容。
#  `overwrite` 来覆盖现有笔记本
curl -n -X POST -H 'Content-Type: application/json' -d \
'{
  "path": "/Users/user@example.com/new-notebook",
  "format": "SOURCE",
  "language": "SCALA",
  "content": "Ly8gRGF0YWJyaWNrcyBub3RlYm9vayBzb3VyY2UKcHJpbnQoImhlbGxvLCB3b3JsZCIpCgovLyBDT01NQU5EIC0tLS0tLS0tLS0KCg==",
  "overwrite": "false"
}' https://<databricks-instance>/api/2.0/workspace/import

# 或者，可以通过多部分窗体发布导入笔记本

curl -n -X POST https://<databricks-instance>/api/2.0/workspace/import \
       -F path="/Users/user@example.com/new-notebook" -F format=SOURCE -F language=SCALA -F overwrite=true -F content=@notebook.scala
```