创建 Lambda 函数时，您可将函数代码打包到部署程序包中。Lambda 支持两种类型的部署程序包：[容器镜像](https://docs.amazonaws.cn/lambda/latest/dg/gettingstarted-package.html#gettingstarted-package-images)和 [.zip 文件归档](https://docs.amazonaws.cn/lambda/latest/dg/gettingstarted-package.html#gettingstarted-package-zip)。创建函数的工作流取决于部署包类型。使用 [将 Lambda 函数部署为容器镜像](https://docs.amazonaws.cn/lambda/latest/dg/gettingstarted-images.html) 控制台创建定义为容器镜像的函数。

您可使用 Lambda 控制台和 Lambda API 创建定义为 .zip 文件归档的 Lambda 函数。此外，您还可上载更新的 .zip 文件更改函数代码。

|注意| |
|---|---|
|您无法将现有[容器镜像函数](https://docs.amazonaws.cn/lambda/latest/dg/gettingstarted-images.html)转换为使用 .zip 文件归档。您必须创建新函数。| |

###### 主题

- [[#创建函数]]
- [[#使用控制台代码编辑器]]
- [[#更新函数代码]]
- [[#更改运行时]]
- [[#更改架构]]
- [[#使用 Lambda API]]
- [[#Amazon CloudFormation]]

## 创建函数

创建定义为 .zip 文件归档的 Lambda 函数时，请选择代码模板、语言版本及函数的执行角色。您可以在 Lambda 创建函数后添加函数代码。

###### 创建函数

1. 打开 Lamba 控制台的 [Functions page](https://console.amazonaws.cn/lambda/home#/functions)（函数页面）。
    
2. 选择 **Create function**（创建函数）。
    
3. 选择 **Author from scratch**（从头开始创作）或者 **Use a blueprint**（使用蓝图）创建函数。
    
4. 在 **Basic information**（基本信息）中，执行以下操作：
    
    1. 对于 **Function name**（函数名称），输入函数名称。函数名称的长度限制为 64 个字符。
        
    2. 对于**Runtime**（运行时），请选择函数使用的语言版本。
        
    3. （可选）对于**架构**，选择要用于函数的指令集架构。默认架构为 x86_64。为您的函数构建部署包时，请确保它与此[指令集架构](https://docs.amazonaws.cn/lambda/latest/dg/foundation-arch.html)兼容。
        
5. （可选）在 **Permissions**（权限）下，展开 **Change default execution role**（更改默认执行角色）。您可以使用现有角色，也可以创建一个**执行角色**。
    
6. （可选）展开 **Advanced settings**（高级设置）。您可以为函数选择**代码签名配置**。您还可以为要访问的函数配置 (Amazon VPC)。
    
7. 选择 **Create function**（创建函数）。

## 使用控制台代码编辑器

控制台将使用单个源文件创建一个 Lambda 函数。对于脚本语言，您可以在内置[代码编辑器](https://docs.amazonaws.cn/lambda/latest/dg/foundation-console.html#code-editor)中编辑此文件并添加更多文件。要保存您的更改，请选择 **Save**（保存）。然后，要运行代码，请选择 **Test**（测试）。

|注意|  |
|---|---|
|Lambda 控制台使用 Amazon Cloud9 在浏览器中提供集成开发环境。您还可以使用 Amazon Cloud9 在您自己的环境中开发 Lambda 函数。有关更多信息，请参阅 Amazon Cloud9 用户指南中的[使用 Lambda 函数](https://docs.amazonaws.cn/cloud9/latest/user-guide/lambda-functions.html)。| |

保存函数代码时，Lambda 控制台会创建一个 .zip 文件归档部署包。在控制台外部开发函数代码时（使用 IDE），您需要[创建部署程序包](https://docs.amazonaws.cn/lambda/latest/dg/nodejs-package.html)将代码上载到 Lambda 函数。

## 更新函数代码

对于脚本语言（Node.js、Python 及 Ruby），您可以在嵌入式[编辑器](https://docs.amazonaws.cn/lambda/latest/dg/foundation-console.html#code-editor)中编辑函数代码。如果代码大于 3MB，或者如果需要添加库，或对于编辑器不支持的语言（Java、Go、C#），您必须将函数代码以 .zip 归档上载。如果 .zip 文件归档小于 50 MB，则可以从本地计算机上传 .zip 文件归档。如果文件大于 50MB，请将文件从 Simple Storage Service (Amazon S3) 存储桶上载到函数。

###### 将函数代码以 .zip 归档上载

1. 打开 Lamba 控制台的 [Functions page](https://console.amazonaws.cn/lambda/home#/functions)（函数页面）。
    
2. 选择要更新的函数，然后选择 **Code**（代码）选项卡。
    
3. 在 **Code source**（代码源）下，选择 **Upload from**（上载自）。
    
4. 选择 **.zip file** (.zip 文件)，然后选择 **Upload file** (上载文件)。
    
    1. 在文件选择器中，选择新映像版本，然后依次选择 **Open** (打开)、**Save** (保存)。
        
5. （步骤 4 的替代方案）选择 **Amazon S3 location**（Simple Storage Service (Amazon S3) 位置）。
    
    1. 在文本框中，输入 .zip 文件归档的 S3 链接 URL，然后选择 **Save**（保存）。
        

## 更改运行时

如果您更新函数配置以使用新的运行时版本，则可能需要更新函数代码才能与新的运行时版本兼容。如果您将函数配置更新为使用其他运行时，则**必须**提供与运行时和架构兼容的新函数代码。有关如何为函数代码创建部署包的说明，请参阅函数使用的运行时的处理程序页面。

###### 更改运行时

1. 打开 Lamba 控制台的 [Functions page](https://console.amazonaws.cn/lambda/home#/functions)（函数页面）。
    
2. 选择要更新的函数，然后选择 **Code**（代码）选项卡。
    
3. 向下滚动至位于代码编辑器下方的 **Runtime settings**（运行时设置）部分。
    
4. 选择 **Edit**（编辑）。
    
    1. 对于 **Runtime**（运行时），请选择运行时标识符。
        
    2. 对于 **Handler**（处理程序），请为您的函数指定文件名和处理程序。
        
    3. 对于**架构**，选择要用于您的函数的指令集架构。
        
5. 选择 **Save**（保存）。
    

## 更改架构

在更改指令集架构之前，您需要确保函数的代码与目标架构兼容。

如果您使用 Node.js、Python 或 Ruby 并在嵌入式[编辑器](https://docs.amazonaws.cn/lambda/latest/dg/foundation-console.html#code-editor)中编辑函数代码，则现有代码可以在不修改的情况下运行。

但是，如果您使用 .zip 文件归档部署包提供函数代码，则必须准备一个新的 .zip 文件归档，针对目标运行时和指令集架构正确编译和构建此归档。有关说明，请参阅函数运行时的处理程序页面。

###### 更改指令集架构

1. 打开 Lamba 控制台的 [Functions page](https://console.amazonaws.cn/lambda/home#/functions)（函数页面）。
    
2. 选择要更新的函数，然后选择 **Code**（代码）选项卡。
    
3. 在 **Runtime settings**（运行时设置）中，选择 **Edit**（编辑）。
    
4. 对于**架构**，选择要用于您的函数的指令集架构。
    
5. 选择 **Save**（保存）。
    

## 使用 Lambda API

要创建和配置使用 .zip 文件归档的函数，请使用以下 API 操作：

- [CreateFunction](https://docs.amazonaws.cn/lambda/latest/dg/API_CreateFunction.html)
    
- [UpdateFunctionCode](https://docs.amazonaws.cn/lambda/latest/dg/API_UpdateFunctionCode.html)
    
- [UpdateFunctionConfiguration](https://docs.amazonaws.cn/lambda/latest/dg/API_UpdateFunctionConfiguration.html)
    

## Amazon CloudFormation

您可以使用 Amazon CloudFormation 创建使用 .zip 文件归档的 Lambda 函数。在 Amazon CloudFormation 模板中，`AWS::Lambda::Function` 资源将指定 Lambda 函数。有关 `AWS::Lambda::Function` 资源中属性的描述，请参阅 _Amazon CloudFormation 用户指南_中的 [AWS::Lambda::Function](https://docs.amazonaws.cn/AWSCloudFormation/latest/UserGuide/aws-resource-lambda-function.html)。

在 `AWS::Lambda::Function` 资源中，设置以下属性创建定义为.zip文件归档的函数：

- AWS::Lambda::Function
    
    - PackageType – 设置为 `Zip`。
        
    - 代码 – 在 `S3Bucket` 和 `S3Key` 字段中输入 Amazon S3 存储桶名称和 .zip 文件名。对于 Node.js 或 Python，您可以提供 Lambda 函数的内联源代码。
        
    - 运行时 – 设置运行时值。
        
    - 架构 - 将架构值设置为 `arm64` 以使用 Amazon Graviton2 处理器。默认情况下，架构值为 `x86_64`。
