[世纪互联文档](https://docs.azure.cn/zh-cn/storage/blobs/)

## 概述

#### 基本概念

* Azure 的适用于云的对象存储解决方案
* 最适合存储巨量的非结构化数据。
	* 如文本或二进制数据 
	* 比如apache arvo 结构的数据
* 用于
	* 提供图像或文档
	* 存储文件
	* 视频和音频进行流式处理
	* 日志文件进行写入
	* 灾备、存档的数据
* 支持 Azure Data Lake Storage Gen2 ，提供了分层文件系统
	*  低成本分层存储
	-   高可用性
	-   非常一致性
	-   灾难恢复功能

#### Blob 存储资源

-   **存储帐户**  
-   存储帐户中的容器   文件目录
-   容器中的 Blob         目录中的文件
![[Pasted image 20230323182245.png|375]]

##### 账户
* 唯一命名空间
* 比如是 mystorageaccount
	* 则 `http://mystorageaccount.blob.core.chinacloudapi.cn` 为Blob存储默认节点
##### 容器
* 类似于文件系统中的目录
* 容器的 URI 类似于
	* `https://myaccount.blob.core.chinacloudapi.cn/mycontainer`
##### Blob
* 类似于文件
* 有三种blob
	* 块 Blob 存储文本和二进制数据
	* 追加 Blob 也由块构成，可追加
	* 页 Blob 用于存储最大 8 TiB 的随机访问文件
* URL类似于：`https://myaccount.blob.core.chinacloudapi.cn/mycontainer/myblob`

### Blob索引标记

可以给 Blob 打上索引标记，方便数据量大的时候进行
-   使用键值索引标记对 Blob 进行动态分类
-   在整个存储帐户中快速查找带有标记的特定 Blob
-   根据索引标记的计算来指定 Blob API 的条件行为
-   将索引标记用于功能上的高级控件（例如 [Blob 生命周期管理](https://docs.azure.cn/zh-cn/storage/blobs/lifecycle-management-overview)）


# AzCopy使用

向/从存储帐户复制 Blob 或文件

### 下载压缩文件，解压即可，然后配置 env

[下载地址](https://docs.azure.cn/zh-cn/storage/common/storage-use-azcopy-v10#download-azcopy)

### AzCopy 授权

两种授权方式
* [Azure Active Directory](https://docs.azure.cn/zh-cn/active-directory/)
	* Azure AD 一次验证，然后正常使用
* SAS令牌
	* 每次执行命令时，将令牌附在命令上
```shell
# 把令牌附在命令上
https://<storage-account-name>.blob.core.chinacloudapi.cn/<container-name><SAS-token>

# 使用举例
azcopy copy "C:\local\path" "https://account.blob.core.chinacloudapi.cn/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

### 上传下载命令 copy

假设已经使用了Azure AD进行验证

```shell
## 创建容器&文件
# 建容器
azcopy make 'https://<storage-account-name>.<blob_or_dfs>.core.chinacloudapi.cn/<container-name>'
azcopy make 'https://mystorageaccount.blob.core.chinacloudapi.cn/mycontainer'

# 上传文件 可以使用通配符如 'C:\myDirectory\*.txt'` 或 `C:\my*\*.txt`
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.chinacloudapi.cn/mycontainer/myTextFile.txt'
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.chinacloudapi.cn/mycontainer/myTextFile.txt'
# 下载文件
azcopy copy 'https://mystorageaccount.blob.core.chinacloudapi.cn/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'
azcopy copy 'https://mystorageaccount.blob.core.chinacloudapi.cn/mycontainer/myBlobDirectory/*' 'C:\myDirectory'

# 上传目录
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.chinacloudapi.cn/mycontainer' --recursive
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.chinacloudapi.cn/mycontainer' --recursive

# 下载目录
azcopy copy 'https://mystorageaccount.blob.core.chinacloudapi.cn/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive

# 上传目录中指定文件  --include-path
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.chinacloudapi.cn/mycontainer' 
--include-path 'photos;documents\myFile.txt' --recursive
# 下载目录中指定文件
azcopy copy 'https://mystorageaccount.blob.core.chinacloudapi.cn/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive

# 上传目录中指定文件  --include-pattern 可以使用通配符
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.chinacloudapi.cn/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'
# 下载 --include-pattern 
azcopy copy 'https://mystorageaccount.blob.core.chinacloudapi.cn/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'

# 上传日期范围的文件  `--include-before` 或 `--include-after`
azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.chinacloudapi.cn/<container-or-directory-name>' --include-after <Date-Time-in-ISO-8601-format>
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.chinacloudapi.cn/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'
# 下载同理，位置替换一下

# 上传文件添加索引标记  使用`--blob-tags` 添加 URL 编码的键值对
#   键 `my tag` 值 `my tag value` ：`--blob-tags='my%20tag=my%20tag%20value'`
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.chinacloudapi.cn/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'

# 可选标志
--blob-type=[BlockBlob|PageBlob|AppendBlob] # 将文件作为追加 Blob 或页 Blob 上传
--block-blob-tier=[None|Hot|Cool|Archive] # 上传到特定访问层（如存档层）
```
[azcopy copy 所有选项文档](https://docs.azure.cn/zh-cn/storage/common/storage-ref-azcopy-copy#options)


### 存储同步
将本地文件系统的内容与 Blob 容器同步，同步是单向的。需要指定source 和 target

```python
# 语法：
# path的两种选择
#      https://<storage-account-name>.blob.core.chinacloudapi.cn/<container-name>
#      本地路径
azcopy sync '<source-path>' '<target-path>' --recursive

# source container -> target local dir
azcopy sync 'https://mystorageaccount.blob.core.chinacloudapi.cn/mycontainer' 'C:\myDirectory' --recursive

# source container1 -> target container2  按需求进行SAS验证
azcopy sync 'https://mysourceaccount.blob.core.chinacloudapi.cn/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.chinacloudapi.cn/mycontainer' --recursive

# source container1 dir -> target container2 dir
azcopy sync 'https://mysourceaccount.blob.core.chinacloudapi.cn/<container-name>/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.chinacloudapi.cn/mycontainer/myDirectory' --recursive

# 同步可选标志
# 下载时应验证 MD5 哈希的严格程度
--check-md5=[NoCheck|LogOnly|FailIfDifferent|FailIfDifferentOrMissing]
# 排除文件
--exclude-path
# 同步相关的日志的生成设置
--log-level=[WARNING|ERROR|INFO|NONE]
```

### 不同云直接的复制

[Amazon S3 -> Azure Blob官方文档](https://docs.azure.cn/zh-cn/storage/common/storage-use-azcopy-s3?toc=%2Fstorage%2Fblobs%2Ftoc.json)

[Google Cloud Storage -> Azure Blob官方文档](https://docs.azure.cn/zh-cn/storage/common/storage-use-azcopy-google-cloud?toc=%2Fstorage%2Fblobs%2Ftoc.json)

# DataFactory -> Blob
https://docs.azure.cn/zh-cn/data-factory/connector-azure-blob-storage?toc=%2Fstorage%2Fblobs%2Ftoc.json
