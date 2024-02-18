

### python包

| 涉及到的包                                                                                                                 | 描述                                                                                                                                                                                                                                                                                                                                                                                           |     |
| -------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --- |
| [azure.storage.blob](https://learn.microsoft.com/zh-cn/python/api/azure-storage-blob/azure.storage.blob?view=azure-python) | blob sdk 包<br>[BlobServiceClient](https://learn.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) 服务客户端类<br>[ContainerClient](https://learn.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.containerclient) 容器类<br>[BlobClient](https://learn.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blobclient)blob客户端类 |     |
| azure.identity                                                                                                             | [DefaultAzureCredential](https://learn.microsoft.com/dotnet/api/azure.identity.defaultazurecredential)身份验证类                                                                                                                                                                                                                                                                               |     |
|                                                                                                                            |                                                                                                                                                                                                                                                                                                                                                                                                |     |                                                                                                                           |                                                                                                                                                                                                                                                                                                                                                                                                |     |
```shell
pip install azure-storage-blob azure-identity
```

### 创建对象
```python
# 导入必要包
from azure.identity import DefaultAzureCredential
from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient

# `BlobServiceClient` 提供检索和配置帐户属性以及列出、创建和删除存储帐户内容器的方法。
# create BlobServiceClient object
def get_blob_service_client(self, account_name):
    account_url = f"https://{account_name}.blob.core.chinacloudapi.cn"
    credential = DefaultAzureCredential()        # identity
    # 创建 BlobServiceClient 
    blob_service_client = BlobServiceClient(account_url, credential=credential)
    return blob_service_client

# `BlobContainerClient` 提供创建、删除或配置容器的方法，并包括列出、上传和删除容器内 blob 的方法。
# creata BlobContainerClient object by BlobServiceClient object
def get_blob_container_client(self, blob_service_client: BlobServiceClient, container_name):
    container_client = blob_service_client.get_container_client(container=container_name)
    return container_client

# Create BlobContainerClient object without BlobServiceClient object
def get_blob_container_client(self, account_name, container_name):
    # Append the container name to the URI
    account_url = f"https://{account_name}.blob.core.chinacloudapi.cn/{container_name}"
    credential = DefaultAzureCredential()
    # Create the client object
    container_client = ContainerClient(account_url, credential=credential)
    return container_client

# `BlobClient` 提供上传、下载、删除和创建 blob 快照的方法。
# Create a BlobClient using the service client object
def get_blob_client(self, blob_service_client: BlobServiceClient, container_name, blob_name):
    blob_client = blob_service_client.get_blob_client(container=container_name, blob=blob_name)
    return blob_client
```

### 容器开发

#### 容器创建
-  [BlobServiceClient.create_container](https://learn.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#azure-storage-blob-blobserviceclient-create-container)
-  [ContainerClient.create_container](https://learn.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.containerclient#azure-storage-blob-containerclient-create-container)
```python
# 创建容器 举例
def create_blob_container(self, blob_service_client: BlobServiceClient, container_name):
    try:
        container_client = blob_service_client.create_container(name=container_name)
    except ResourceExistsError:
        print('A container with this name already exists')
```
#### 根容器创建
$root。 必须显式创建或删除根容器，通过上面的方法创建
```python
# Create the root container if it doesn't already exist
def create_blob_root_container(self, blob_service_client: BlobServiceClient):
    container_client = blob_service_client.get_container_client(container="$root")
    if not container_client.exists():
        container_client.create_container()
```

#### 容器删除
删除容器后，至少在 30 秒内无法使用相同的名称创建容器。
-   [BlobServiceClient.delete_container](https://learn.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#azure-storage-blob-blobserviceclient-delete-container)
-   [ContainerClient.delete_container](https://learn.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.containerclient#azure-storage-blob-containerclient-delete-container)
```python
# 删除以指定的前缀开头的所有容器
def delete_container_prefix(self, blob_service_client: BlobServiceClient, prefix_str):
    container_list = list(blob_service_client.list_containers(name_starts_with=prefix_str))
    assert len(container_list) >= 1

    for container in container_list:
        container_client = blob_service_client.get_container_client(container=container.name)
        container_client.delete_container()
```

#### 容器软删除还原

前提：需要[启用和管理容器的软删除](https://docs.azure.cn/zh-cn/storage/blobs/soft-delete-container-enable)
-   [BlobServiceClient.undelete_container](https://learn.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#azure-storage-blob-blobserviceclient-undelete-container)
```python
# 还原软删除 container 的方法
def restore_deleted_container(self, blob_service_client: BlobServiceClient, container_name):
    container_list = list(       # deleted_container_list
	    blob_service_client.list_containers(include_deleted=True)
	)
    assert len(container_list) >= 1        # if false , AssertionError is raised

    for container in container_list:
        if container.deleted and container.name == container_name:
            restored_container_client = blob_service_client.undelete_container(
                deleted_container_name=container.name, 
                deleted_container_version=container.version
            )
```

#### 容器列出
-   [BlobServiceClient.list_containers](https://learn.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#azure-storage-blob-blobserviceclient-list-containers)
	- `name_starts_with=prefix_str`  返回其名称以该前缀开头的容器
	- `include_metadata=true`            列出所有容器和元数据
	- `results_per_page=n`                  指定每页结果数限制

```
def list_containers(self, blob_service_client: BlobServiceClient):
    i=0
    containers = blob_service_client.list_containers(include_metadata=True)
    for container in containers:
        print(container['name'], container['metadata'])
        
def list_containers_prefix(self, blob_service_client: BlobServiceClient):
    containers = blob_service_client.list_containers(name_starts_with='test-')
    for container in containers:
        print(container['name'])
        
def list_containers_pages(self, blob_service_client: BlobServiceClient):
    i=0
    all_pages = blob_service_client.list_containers(results_per_page=5).by_page()
    for container_page in all_pages:
        i += 1
        print(f"Page {i}")
        for container in container_page:
            print(container['name'])
```



#### 创建和管理容器租用

没看懂

#### 属性&元数据管理

-   [ContainerClient.get_container_properties](https://learn.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.containerclient#azure-storage-blob-containerclient-get-container-properties)  检索元数据
-   [ContainerClient.set_container_metadata](https://learn.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.containerclient#azure-storage-blob-containerclient-set-container-metadata)   设置元数据

```python
# 查询属性  container_client.get_container_properties
def get_properties(self, blob_service_client: BlobServiceClient, container_name):
    container_client = blob_service_client.get_container_client(container=container_name)

    properties = container_client.get_container_properties()

    print(f"Public access type: {properties.public_access}")
    print(f"Lease status: {properties.lease.status}")
    print(f"Lease state: {properties.lease.state}")
    print(f"Has immutability policy: {properties.has_immutability_policy}")

# 设置元数据
def set_metadata(self, blob_service_client: BlobServiceClient, container_name):
    container_client = blob_service_client.get_container_client(container=container_name)
    #  existing metRetrieveadata, if desired
    metadata = container_client.get_container_properties().metadata

    more_metadata = {'docType': 'text', 'docCategory': 'reference'}
    metadata.update(more_metadata)

    # Set metadata on the container
    container_client.set_container_metadata(metadata=metadata)

# 查看元数据
def get_metadata(self, blob_service_client: BlobServiceClient, container_name):
    container_client = blob_service_client.get_container_client(container=container_name)

    # Retrieve existing metadata, if desired
    metadata = container_client.get_container_properties().metadata

    for k, v in metadata.items():
        print(k, v)
```

## Blob开发

#### 上传blob
-   [BlobClient.upload_blob](https://learn.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blobclient#azure-storage-blob-blobclient-upload-blob)  流&二进制对象上传
-   [BlobClient.upload_blob_from_url](https://learn.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blobclient#azure-storage-blob-blobclient-upload-blob-from-url)   从URL上传

```python
# 上传数据到 块blob
def upload_blob_data(self, blob_service_client: BlobServiceClient, container_name):
    blob_client = blob_service_client.get_blob_client(container=container_name, blob="sample-blob.txt")
    data = b"Sample data for blob"

    # Upload the blob data - 默认blob类型是 BlockBlob
    blob_client.upload_blob(data, blob_type="BlockBlob")

# 流式传输上传块 Blob
def upload_blob_stream(self, blob_service_client: BlobServiceClient, container_name):
    blob_client = blob_service_client.get_blob_client(container=container_name, blob="sample-blob.txt")
    input_stream = io.BytesIO(os.urandom(15))   # 生成15个字节的二进制，然后放到内存中成为二进制流
    blob_client.upload_blob(input_stream, blob_type="BlockBlob")

# 本地文件上传
def upload_blob_file(self, blob_service_client: BlobServiceClient, container_name):
    container_client = blob_service_client.get_container_client(container=container_name)
    with open(file=os.path.join('filepath', 'filename'), mode="rb") as data:
        blob_client = container_client.upload_blob(
            name="sample-blob.txt", data=data, overwrite=True
	    )

# 上传带有索引标记的块 blob
def upload_blob_tags(self, blob_service_client: BlobServiceClient, container_name):
    container_client = blob_service_client.get_container_client(container=container_name)
    sample_tags = {"Content": "image", "Date": "2022-01-01"}
    with open(file=os.path.join('filepath', 'filename'), mode="rb") as data:
        blob_client = container_client.upload_blob(
	        name="sample-blob.txt", data=data, tags=sample_tags  # 指定tags
	    )
```

#### 下载 Blob
-   [BlobClient.download_blob](https://learn.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blobclient#azure-storage-blob-blobclient-download-blob)  返回一个 [StorageStreamDownloader](https://learn.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.storagestreamdownloader) 对象。

```python
# 下载到文件
def download_blob_to_file(self, blob_service_client: BlobServiceClient, container_name):
    blob_client = blob_service_client.get_blob_client(container=container_name, blob="sample-blob.txt")
    with open(file=os.path.join('filepath', 'filename'), mode="wb") as sample_blob:
        download_stream = blob_client.download_blob()
        sample_blob.write(download_stream.readall())

# 下载到流  readinto() 下内容到指定的二进制流，返回字节长度
def download_blob_to_stream(self, blob_service_client: BlobServiceClient, container_name):
    blob_client = blob_service_client.get_blob_client(container=container_name, blob="sample-blob.txt")
    stream = io.BytesIO()
    num_bytes = blob_client.download_blob().readinto(stream)
    print(f"Number of bytes: {num_bytes}")

# 分块读取 blob 数据，防止内存溢出
def download_blob_chunks(self, blob_service_client: BlobServiceClient, container_name):
    blob_client = blob_service_client.get_blob_client(container=container_name, blob="sample-blob.txt")
    stream = blob_client.download_blob()    # StorageStreamDownloader
    chunk_list = []

    # 分块读取数据
    for chunk in stream.chunks():
        # 这里可以处理数据，'chunk' is a byte array
        chunk_list.append(chunk)

# 下载到字符串  encoding参数必写，
def download_blob_to_string(self, blob_service_client: BlobServiceClient, container_name):
    blob_client = blob_service_client.get_blob_client(container=container_name, blob="sample-blob.txt")
    downloader = blob_client.download_blob(max_concurrency=1, encoding='UTF-8')
    blob_text = downloader.readall()
    print(f"Blob contents: {blob_text}")
```

#### 复制 Blob

* 复制Blob到其他blob或新blob，要求类型相同（块、追加或页 Blob）
* 名称相同也能复制过去，会覆盖目标表的元数据
* 快照复制到 blob

[官方文档](https://docs.azure.cn/zh-cn/storage/blobs/storage-blob-copy-python#copy-a-blob)  用到再看
-   [BlobClient.start_copy_from_url](https://learn.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blobclient#azure-storage-blob-blobclient-start-copy-from-url)

举例：现有 Blob 的 `BlobClient` 对象，并将其复制到同一存储帐户的不同容器中的新 Blob。 此示例还会在复制之前获取源 blob 上的租用，这样在复制完成且租用中断之前，其他客户端无法修改该 Blob。
```python
def copy_blob(self, blob_service_client: BlobServiceClient):
    source_blob = blob_service_client.get_blob_client(container="source-container", blob="sample-blob.txt")

    # source blob exist
    if source_blob.exists():
        # Lease the source blob during copy to prevent other clients from modifying it
        lease = BlobLeaseClient(client=source_blob)

        # Create an infinite lease by passing -1
        # We'll break the lease after the copy operation finishes
        lease.acquire(-1)

        # Get the source blob properties
        source_blob_properties = source_blob.get_blob_properties()
        print(f"Source blob lease state: {source_blob_properties.lease.state}")

        # Identify the destination blob and begin the copy operation
        destination_blob = blob_service_client.get_blob_client(container="destination-container", blob="sample-blob.txt")
        destination_blob.start_copy_from_url(source_url=source_blob.url)

        # Get the destination blob properties
        destination_blob_properties = destination_blob.get_blob_properties()
        print(f"Copy status: {destination_blob_properties.copy.status}")
        print(f"Copy progress: {destination_blob_properties.copy.progress}")
        print(f"Copy completion time: {destination_blob_properties.copy.completion_time}")
        print(f"Total bytes copied: {destination_blob_properties.size}")

        # Break the lease on the source blob
        if source_blob_properties.lease.state == "leased":
            lease.break_lease()

            # Display updated lease state
            source_blob_properties = source_blob.get_blob_properties()
            print(f"Source blob lease state: {source_blob_properties.lease.state}")
    else:
        print("Source blob does not exist")
```

#### 列出 Blob

扁平列表列出容器中的 blob
-   [ContainerClient.list_blobs](https://learn.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.containerclient#azure-storage-blob-containerclient-list-blobs)（以及名称，你还可以选择性地包括元数据、标记以及与每个 blob 关联的其他信息）
-   [ContainerClient.list_blob_names](https://learn.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.containerclient#azure-storage-blob-containerclient-list-blobs)（仅返回 blob 名称）
分层列表列出容器中的 Blob
-   [ContainerClient.walk_blobs](https://learn.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.containerclient#azure-storage-blob-containerclient-walk-blobs)（以及名称，你还可以选择性地包括元数据、标记以及与每个 blob 关联的其他信息）

```python
# 显示所有blob
def list_blobs_flat(self, blob_service_client: BlobServiceClient, container_name):
    container_client = blob_service_client.get_container_client(container=container_name)
    blob_list = container_client.list_blobs()
    for blob in blob_list:
        print(f"Name: {blob.name}")

# 显示有tag的blob
def list_blobs_flat_options(self, blob_service_client: BlobServiceClient, container_name):
    container_client = blob_service_client.get_container_client(container=container_name)
    blob_list = container_client.list_blobs(include=['tags'])
    for blob in blob_list:
        print(f"Name: {blob['name']}, Tags: {blob['tags']}")
        
# 分层显示，可以显示层级
depth = 0
indent = "  "
def list_blobs_hierarchical(self, container_client: ContainerClient, prefix):
    for blob in container_client.walk_blobs(name_starts_with=prefix, delimiter='/'):
        if isinstance(blob, BlobPrefix):
            # Indentation is only added to show nesting in the output
            print(f"{self.indent * self.depth}{blob.name}")
            self.depth += 1
            self.list_blobs_hierarchical(container_client, prefix=blob.name)
            self.depth -= 1
        else:
            print(f"{self.indent * self.depth}{blob.name}")
```

#### 删除blob
-   [BlobClient.delete_blob](https://learn.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blobclient#azure-storage-blob-blobclient-delete-blob)
```python
# 删除普通blob
def delete_blob(self, blob_service_client: BlobServiceClient, container_name):
    blob_client = blob_service_client.get_blob_client(container=container_name, blob="sample-blob.txt")
    blob_client.delete_blob()

# 删除有关联快照的blob:Blob 具有任何关联的快照，则必须删除其所有快照才能删除该 Blob
def delete_blob_snapshots(self, blob_service_client: BlobServiceClient, container_name):
    blob_client = blob_service_client.get_blob_client(container=container_name, blob="sample-blob.txt")
    blob_client.delete_blob(delete_snapshots="include")

# 软删除blob还原
def restore_deleted_blob(self, blob_service_client: BlobServiceClient, container_name):
    blob_client = blob_service_client.get_blob_client(container=container_name, blob="sample-blob.txt")
    blob_client.undelete_blob()
```

#### 使用 Blob 索引标记来管理和查找数据
设置标记：
-   [BlobClient.set_blob_tags](https://learn.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blobclient#azure-storage-blob-blobclient-set-blob-tags)
获取标记：
-   [BlobClient.get_blob_tags](https://learn.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blobclient#azure-storage-blob-blobclient-get-blob-tags)
查找数据：
-   [ContainerClient.find_blobs_by_tag](https://learn.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.containerclient#azure-storage-blob-containerclient-find-blobs-by-tags)
```python
# 增加或更新标记
def set_blob_tags(self, blob_service_client: BlobServiceClient, container_name):
    blob_client = blob_service_client.get_blob_client(container=container_name, blob="sample-blob.txt")
    # 获取已经存在的tags
    tags = blob_client.get_blob_tags()
    # 增加或更新标记
    updated_tags = {'Sealed': 'false', 'Content': 'image', 'Date': '2022-01-01'}
    tags.update(updated_tags)
    blob_client.set_blob_tags(tags)

# 删除标记 ： 将空 `dict` 对象传递到 `set_blob_tags` 方法来删除所有标记
def clear_blob_tags(self, blob_service_client: BlobServiceClient, container_name):
    blob_client = blob_service_client.get_blob_client(container=container_name, blob="sample-blob.txt")
    tags = dict()
    blob_client.set_blob_tags(tags)

# 查看所有标记
def get_blob_tags(self, blob_service_client: BlobServiceClient, container_name):
    blob_client = blob_service_client.get_blob_client(container=container_name, blob="sample-blob.txt")

    tags = blob_client.get_blob_tags()
    print("Blob tags: ")
    for k, v in tags.items():
        print(k, v)

# 使用 Blob 索引标记查找数据
def find_blobs_by_tags(self, blob_service_client: BlobServiceClient, container_name):
    container_client = blob_service_client.get_container_client(container=container_name)
    
    query = "\"Content\"='image'"
    blob_list = container_client.find_blobs_by_tags(filter_expression=query)
    print("Blobs tagged as images")
    for blob in blob_list:
        print(blob.name)
```

#### blob 租用
 [官网](https://docs.azure.cn/zh-cn/storage/blobs/storage-blob-lease-python)

#### blob 属性和元数据

设置properties 
-   [BlobClient.set_http_headers](https://learn.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blobclient#azure-storage-blob-blobclient-set-http-headers)
检索 blob 上的properties
-   [BlobClient.get_blob_properties](https://learn.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blobclient#azure-storage-blob-blobclient-get-blob-properties)
设置元数据
-   [BlobClient.set_blob_metadata](https://learn.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blobclient#azure-storage-blob-blobclient-set-blob-metadata)
```python
# 设置properties 
def set_properties(self, blob_service_client: BlobServiceClient, container_name):
    blob_client = blob_service_client.get_blob_client(container=container_name, blob="sample-blob.txt")
    # 获取已经存在的 properties
    properties = blob_client.get_blob_properties()
    # 需要设置的 properties
    blob_headers = ContentSettings(
	    content_type="text/plain",
        content_encoding=properties.content_settings.content_encoding,
        content_language="en-US",
        content_disposition=properties.content_settings.content_disposition,
        cache_control=properties.content_settings.cache_control,
        content_md5=properties.content_settings.content_md5
    )
    # 进行设置
    blob_client.set_http_headers(blob_headers)

# 检索 blob 上的properties
def get_properties(self, blob_service_client: BlobServiceClient, container_name):
    blob_client = blob_service_client.get_blob_client(container=container_name, blob="sample-blob.txt")
    properties = blob_client.get_blob_properties()
    print(f"Blob type: {properties.blob_type}")
    print(f"Blob size: {properties.size}")
    print(f"Content type: {properties.content_settings.content_type}")
    print(f"Content language: {properties.content_settings.content_language}")

# 设置元数据
def set_metadata(self, blob_service_client: BlobServiceClient, container_name):
    blob_client = blob_service_client.get_blob_client(container=container_name, blob="sample-blob.txt")
    # 获取已经存在的元数据
    blob_metadata = blob_client.get_blob_properties().metadata
	# 需要添加&更改的元数据
    more_blob_metadata = {'docType': 'text', 'docCategory': 'reference'}
    # 元数据更新
    blob_metadata.update(more_blob_metadata)
    # blob 设置 metadata
    blob_client.set_blob_metadata(metadata=blob_metadata)

# 查找 metadata
def get_metadata(self, blob_service_client: BlobServiceClient, container_name):
    blob_client = blob_service_client.get_blob_client(container=container_name, blob="sample-blob.txt")
    # 获取已经存在的元数据
    blob_metadata = blob_client.get_blob_properties().metadata
    for k, v in blob_metadata.items():
        print(k, v)
```

