您可以通过上传 zip 文件归档或创建并上载容器镜像，将代码部署到 Lambda 函数中。

## .zip 文件归档

.zip 文件归档包括您的应用程序代码及其依赖项。当您使用 Lambda 控制台或工具包编写函数时，Lambda 会自动创建代码的 .zip 文件归档。

使用 Lambda API、命令行工具或 Amazon 软件开发工具包创建函数时，您必须创建部署程序包。如果函数使用已编译的语言，您也必须创建部署程序包，或者为函数添加依赖项。要部署函数的代码，您需要从 Amazon Simple Storage Service (Amazon S3) 或本地计算机上载部署程序包。

您可以使用 Lambda 控制台、Amazon Command Line Interface (Amazon CLI) 将 .zip 文件作为部署程序包上载，或将其上载至 Amazon Simple Storage Service (Amazon S3) 存储桶。

## 容器镜像

您可以使用 Docker 命令行界面 (CLI) 等工具将代码和依赖项打包为容器镜像。然后，您可以将镜像上载到在 Amazon Elastic Container Registry (Amazon ECR) 上托管的容器注册表。

Amazon 提供了一组开源基本镜像，您可以使用这些镜像为函数代码构建容器镜像。您还可以使用其他容器注册表的备选基本镜像。Amazon 还提供了一个开源运行时客户端，您可以将其添加到备选基本镜像以使其与 Lambda 服务兼容。

此外，Amazon 还为您提供了运行时接口仿真器，以便使用 Docker CLI 等工具在本地测试函数。

|注意| |
|---|---|
|您可以创建每个容器镜像以与 Lambda 支持的某个指令集架构兼容。Lambda 为每个指令集架构提供基本镜像，还提供同时支持这两种架构的基本镜像。| |

您为函数构建的镜像只能针对其中一个架构。

将函数打包为容器映像并进行部署无需额外付费。当调用部署为容器镜像的函数时，您需要为调用请求和执行持续时间付费。在 Amazon ECR 中存储容器镜像则会产生相应费用。有关更多信息，请参阅 [Amazon ECR 定价](http://www.amazonaws.cn/ecr/pricing/)。

###### 主题

- [[将 Lambda 函数部署为 .zip 文件归档]]
- [[将 Lambda 函数部署为容器镜像]]