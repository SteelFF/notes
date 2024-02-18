创建 Lambda 函数时，您可将函数代码打包到部署程序包中。Lambda 支持两种类型的部署程序包：[容器镜像](https://docs.amazonaws.cn/lambda/latest/dg/gettingstarted-package.html#gettingstarted-package-images)和 [.zip 文件归档](https://docs.amazonaws.cn/lambda/latest/dg/gettingstarted-package.html#gettingstarted-package-zip)。创建函数的工作流程因部署包类型而异。要配置定义为 .zip 文件归档的函数，请参阅 [将 Lambda 函数部署为 .zip 文件归档](https://docs.amazonaws.cn/lambda/latest/dg/configuration-function-zip.html)。

您可以使用 Lambda 控制台和 Lambda API 创建定义为容器镜像的函数、更新和测试镜像代码以及配置其他函数设置。

|注意| |
|---|---|
|您无法转换现有 [.zip 文件归档函数](https://docs.amazonaws.cn/lambda/latest/dg/configuration-function-zip.html)以使用容器镜像。您必须创建新函数。| |

使用镜像标签选择镜像时，Lambda 会将该标签转换为底层镜像摘要。要检索镜像的摘要，请使用 [GetFunctionConfiguration](https://docs.amazonaws.cn/lambda/latest/dg/API_GetFunctionConfiguration.html) API 操作。要将函数更新为较新的镜像版本，您必须使用 Lambda 控制台[更新函数代码](https://docs.amazonaws.cn/lambda/latest/dg/gettingstarted-images.html#configuration-images-update)或使用 [UpdateFunctionCode](https://docs.amazonaws.cn/lambda/latest/dg/API_UpdateFunctionCode.html) API 操作。配置操作（例如 [UpdateFunctionConfiguration](https://docs.amazonaws.cn/lambda/latest/dg/API_UpdateFunctionConfiguration.html)）不会更新函数的容器镜像。

###### 主题

- [[#先决条件]]
- [[#权限]]
- [[#测试函数]]
- [[#覆盖容器设置]]
- [[#更新函数代码]]
- [[#使用 Lambda API]]
- [[#Amazon CloudFormation]]
## 先决条件

要完成以下步骤，您需要命令行终端或 Shell，以便运行命令。在单独的数据块中列出了命令和预期输出：

`aws --version` 

您应看到以下输出：

`aws-cli/2.0.57 Python/3.7.4 Darwin/19.6.0 exe/x86_64`

对于长命令，使用转义字符 (`\`) 将命令拆分为多行。

在 Linux 和 macOS 中，可使用您首选的 shell 和程序包管理器。

|注意| |
|---|---|
|在 Windows 中，操作系统的内置终端不支持您经常与 Lambda 一起使用的某些 Bash CLI 命令（例如 `zip`）。[安装 Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/install-win10)，获取 Ubuntu 和 Bash 与 Windows 集成的版本。本指南中的示例 CLI 命令使用 Linux 格式。如果您使用的是 Windows CLI，则必须重新格式化包含内联 JSON 文档的命令。| |

在创建函数之前，必须[创建一个容器镜像并将其上载到 Amazon ECR](https://docs.amazonaws.cn/lambda/latest/dg/images-create.html)。

## 权限

确保创建函数的用户或角色的权限包含 Amazon 托管策略 `GetRepositoryPolicy` 和 `SetRepositoryPolicy`。

例如，使用 IAM 控制台创建具有以下策略的角色：
``` json
{
"Version": "2012-10-17",
"Statement": [
  {
  "Sid": "VisualEditor0",
  "Effect": "Allow",
  "Action": ["ecr:SetRepositoryPolicy","ecr:GetRepositoryPolicy"],
  "Resource": "arn:aws:ecr:<region>:<account>:repository/<repo name>/"
  }
]
}     
```
### Amazon ECR 权限

要将相同账户中的函数用作 Amazon ECR 中的容器镜像，您可以向 Amazon ECR 存储库添加 `ecr:BatchGetImage` 和 `ecr:GetDownloadUrlForLayer` 权限。以下示例显示最小策略：
``` json
{
    "Sid": "LambdaECRImageRetrievalPolicy",
    "Effect": "Allow",
    "Principal": {
	    "Service": "lambda.amazonaws.com"
    },
    "Action": [
	    "ecr:BatchGetImage",
		"ecr:GetDownloadUrlForLayer"
	]
}   
```
有关更多 Amazon ECR 存储库权限的信息，请参阅 _Amazon Elastic Container Registry 用户指南_中的 [Amazon ECR 存储库策略](https://docs.amazonaws.cn/AmazonECR/latest/userguide/repository-policies.html)。

如果 Amazon ECR 存储库中不包含这些权限，Lambda 会为 `ecr:BatchGetImage` 和 `ecr:GetDownloadUrlForLayer` 添加容器镜像存储库权限。仅当 Lambda 的主要调用具有 `ecr:getRepositoryPolicy` 和 `ecr:setRepositoryPolicy` 权限时，Lambda 才能添加这些权限。

要查看或编辑您的 Amazon ECR 存储库权限，请参阅 _Amazon Elastic Container Registry 用户指南_中的[设置存储库策略声明](https://docs.amazonaws.cn/AmazonECR/latest/userguide/set-repository-policy.html)。

#### Amazon ECR 跨账户权限

相同区域中的不同账户可以创建一个使用您的账户拥有的容器镜像的函数。在以下示例中，Amazon ECR 存储库权限策略需要以下语句才能向账号 123456789012 授予访问权限。

- **CrossAccountPermission** – 允许账号 123456789012 创建和更新使用此 ECR 存储库中的镜像的 Lambda 函数。
    
- **LambdaECRImageCrossAccountRetrievalPolicy** – 如果在延迟期内未调用 Lambda，则 Lambda 最终会将函数的状态设置为不活动。此语句是必需的，以便 Lambda 可以检索容器镜像，代表 123456789012 拥有的函数进行优化和缓存。
    

###### 例 添加对存储库的跨账户权限
``` json
{"Version": "2012-10-17",
    "Statement": [
      {
        "Sid": "CrossAccountPermission",
        "Effect": "Allow",
        "Action": [
          "ecr:BatchGetImage",
          "ecr:GetDownloadUrlForLayer"
        ],
        "Principal": {
          "AWS": "arn:aws:iam::123456789012:root"
        } 
      },
      {
        "Sid": "LambdaECRImageCrossAccountRetrievalPolicy",
        "Effect": "Allow",
        "Action": [
          "ecr:BatchGetImage",
          "ecr:GetDownloadUrlForLayer"
        ],
        "Principal": {
            "Service": "lambda.amazonaws.com"
        },
        "Condition": {
          "StringLike": {
            "aws:sourceARN":
              "arn:aws:lambda:us-east-1:123456789012:function:*"
          } 
        }
      }
    ]
}        
```
要授予对多个账户的访问权限，您可以在 `CrossAccountPermission` 策略中将该账户 ID 添加到主体列表，并在 `LambdaECRImageCrossAccountRetrievalPolicy` 中将该账户 ID 添加到条件评估列表。

如果您在 Amazon Organization 中使用多个账户，我们建议您在 ECR 权限策略中枚举每个账户 ID。此方法遵循在 IAM policy 中设置窄权限的 Amazon 安全最佳实践。

## 创建函数

要创建定义为容器镜像的函数，必须首先[创建镜像](https://docs.amazonaws.cn/lambda/latest/dg/images-create.html)，然后将镜像存储在 Amazon ECR 存储库中。

###### 创建函数

1. 打开 Lamba 控制台的[函数页面](https://console.amazonaws.cn/lambda/home#/functions)。
    
2. 选择 **Create function**（创建函数）。
    
3. 选择 **Container image（容器映像）**选项。
    
4. 在 **Basic information**（基本信息）中，执行以下操作：
    
    1. 对于 **Function name**（函数名称），输入函数名称。
        
    2. 对于**容器镜像 URI**，提供与函数代码所需的指令集架构兼容的容器镜像。
        
        您可以输入 Amazon ECR 镜像 URI 或浏览 Amazon ECR 镜像。
        
        - 输入 Amazon ECR 镜像 URI。
            
        - 或者，要浏览镜像的 Amazon ECR 存储库，选择 **Browse images**（浏览镜像）。从下拉列表中选择 Amazon ECR 存储库，然后选择镜像。
            
5. （可选）要覆盖包含在 Dockerfile 中的配置设置，请展开 **Container image overrides（容器映像覆盖）**。您可以覆盖以下任意设置：
    
    - 对于 **Entrypoint**，输入运行时可执行文件的完整路径。以下示例显示了 Node.js 函数的入口点：
        
        `"/usr/bin/npx", "aws-lambda-ric"`
        
    - 对于 **Command**，输入要使用 **Entrypoint** 传递到映像的其他参数。以下示例显示了 Node.js 函数的命令：
        
        `"app.handler"`
        
    - 对于 **Working directory**，输入函数工作目录的完整路径。以下示例显示了 Lambda 的Amazon基本镜像的工作目录：
        
        `"/var/task"`
           
|注意| |
|---|---|
|对于覆盖设置，请确保将每个字符串括在引号 (" ") 中| |
    
6. （可选）对于**架构**，选择该函数的指令集架构。默认架构为 x86_64。注意：在为函数构建容器映像时，请确保该映像与此[指令集架构](https://docs.amazonaws.cn/lambda/latest/dg/foundation-arch.html)兼容。
    
7. （可选）在 **Permissions（权限）**下，展开 **Change default execution role（更改默认执行角色）**。然后，选择创建新的**执行角色**，或使用现有角色。
    
8. 选择 **Create function**（创建函数）。
    

Lambda 创建函数和[执行角色](https://docs.amazonaws.cn/lambda/latest/dg/lambda-intro-execution-role.html)，该角色授予函数上载日志的权限。在您调用函数时，Lambda 担任执行角色，并使用该执行角色为Amazon开发工具包创建凭证和从事件源读取数据。

当您将代码作为容器镜像部署到 Lambda 函数时，镜像将为在 Lambda 上运行而进行优化。此过程可能需要几秒钟，在此期间函数处于挂起状态。优化过程完成后，函数将进入活动状态。

## 测试函数

使用控制台中提供的示例事件数据调用 Lambda 函数。

###### 调用函数

1. 选择函数后，选择 **Test**（测试）选项卡。
    
2. 在 **Test event action**（测试事件操作）下，选择 **Create new event**（创建新事件）。
    
3. 对于 **Event name**（事件名称），输入测试事件的名称。
    
4. 在 **Event sharing settings**（事件共享设置）下，选择 **Private**（私有）。
    
5. 在 **Template**（模板）中，保留默认的 **hello-world** 选项。
    
6. 选择 **Save changes**（保存更改），然后选择 **Test**（测试）。Lambda 代表您运行函数。函数处理程序接收并处理示例事件。
    
7. 查看 **Execution result**（执行结果）和 **Details**（详细信息）：
    
    - **Summary**（摘要）部分显示了调用日志中 `REPORT` 行的关键信息。
        
    - **Log output**（日志输出）部分显示了完整的调用日志。Lambda 会将所有调用日志写入 Amazon CloudWatch。
        
    

## 覆盖容器设置

您可以使用 Lambda 控制台或 Lambda API 覆盖以下容器镜像设置：

- ENTRYPOINT – 指定应用程序入口点的绝对路径。
    
- CMD – 指定要通过 ENTRYPOINT 传入的参数。
    
- WORKDIR – 指定工作目录的绝对路径。
    
- ENV – 为 Lambda 函数指定环境变量。
    

您在 Lambda 控制台或 Lambda API 中提供的任何值都将覆盖 [Dockerfile](https://docs.amazonaws.cn/lambda/latest/dg/images-create.html#images-parms) 中的值。

###### 要覆盖容器映像中的配置值

1. 打开 Lamba 控制台的[函数页面](https://console.amazonaws.cn/lambda/home#/functions)。
    
2. 选择要更新的函数。
    
3. 在 **Image configuration（映像配置）**下，选择 **Edit（编辑）**。
    
4. 为任意覆盖设置输入新值，然后选择 **Save（保存）**。
    
5. （可选）要添加或覆盖环境变量，请在 **Environment variables（环境变量）**下选择 **Edit（编辑）**。
    
    有关更多信息，请参阅[使用 Amazon Lambda 环境变量](https://docs.amazonaws.cn/lambda/latest/dg/configuration-envvars.html)。
    

## 更新函数代码

将容器映像部署到函数后，该映像是只读的。要更新函数代码，必须首先部署新的映像版本。[创建新的镜像版本](https://docs.amazonaws.cn/lambda/latest/dg/images-create.html)，然后将镜像存储在 Amazon ECR 存储库中。

###### 要将函数配置为使用更新的容器映像

1. 打开 Lamba 控制台的[函数页面](https://console.amazonaws.cn/lambda/home#/functions)。
    
2. 选择要更新的函数。
    
3. 在 **Image（映像）**下，选择 **Deploy new image（部署新映像）**。
    
4. 选择 **Browse images（浏览映像）**。
    
5. 在 **Select container image**（选择容器镜像）对话框中，从下拉列表中选择 Amazon ECR 存储库，然后选择新镜像版本。
    
6. 选择 **Save**（保存）。
    

### 函数版本 $LATEST

在您发布函数版本时，代码和大多数配置设置都会被锁定，从而为该版本的用户维持一致的体验。只能在函数的未发布版本上更改代码和许多配置设置。默认情况下，控制台会显示函数未发布版本的配置信息。要查看函数的版本，请选择 **Qualifiers（限定符）**。未发布版本的名称为 **$LATEST**。

请注意，Amazon Elastic Container Registry (Amazon ECR) 还可使用_最新_标签来表示容器镜像的最新版本。请务必小心，不要将此标签与 **$LATEST** 函数版本混淆。

有关管理版本的更多信息，请参阅[Lambda 函数版本](https://docs.amazonaws.cn/lambda/latest/dg/configuration-versions.html)。

## 使用 Lambda API

要管理定义为容器映像的函数，请使用以下 API 操作：

- [CreateFunction](https://docs.amazonaws.cn/lambda/latest/dg/API_CreateFunction.html)
    
- [UpdateFunctionCode](https://docs.amazonaws.cn/lambda/latest/dg/API_UpdateFunctionCode.html)
    
- [UpdateFunctionConfiguration](https://docs.amazonaws.cn/lambda/latest/dg/API_UpdateFunctionConfiguration.html)
    

要创建定义为容器映像的函数，请使用 `create-function` 命令。将 `package-type` 设置为 `Image` 并用 `code` 参数指定容器映像 URI。

在创建函数时，您可以指定指令集架构。默认架构为 `x86-64`。确保容器映像中的代码与架构兼容。

您可以从相同账户创建函数作为容器注册表，或者从相同区域中的不同账户创建函数作为 Amazon ECR 中的容器注册表。对于跨账户访问，请调整镜像的 [Amazon ECR 权限](https://docs.amazonaws.cn/lambda/latest/dg/gettingstarted-images.html#configuration-images-xaccount-permissions)。

`aws lambda create-function --region sa-east-1 --function-name my-function \     --package-type Image  \     --code ImageUri=<ECR Image URI>   \     --role arn:aws:iam::123456789012:role/lambda-ex` 

要更新函数代码，请使用 `update-function-code` 命令。请使用 `image-uri` 参数指定容器映像位置。

|注意| |
|---|---|
|您无法更改函数的 `package-type`。| |

`aws lambda update-function-code --region sa-east-1 --function-name my-function \     --image-uri <ECR Image URI>   \`

要更新函数参数，请使用 `update-function-configuration` 操作。指定 `EntryPoint` 和 `Command` 作为字符串数组，指定 `WorkingDirectory` 作为字符串。

`aws lambda update-function-configuration  --function-name my-function \ --image-config '{"EntryPoint": ["/usr/bin/npx", "aws-lambda-ric"],  \                  "Command":   ["app.handler"] ,          \                   "WorkingDirectory": "/var/task"}'`  

## Amazon CloudFormation

您可以使用 Amazon CloudFormation 创建定义为容器镜像的 Lambda 函数。在 Amazon CloudFormation 模板中，`AWS::Lambda::Function` 资源将指定 Lambda 函数。有关 `AWS::Lambda::Function` 资源中属性的描述，请参阅 _Amazon CloudFormation 用户指南_中的 [AWS::Lambda::Function](https://docs.amazonaws.cn/AWSCloudFormation/latest/UserGuide/aws-resource-lambda-function.html)。

在 `AWS::Lambda::Function` 资源中，设置以下属性以创建定义为容器映像的函数：

- AWS::Lambda::Function
    
    - PackageType – 设置为 `Image`。
        
    - Code – 在 `ImageUri` 字段中输入容器镜像 URI。
        
    - ImageConfig –（可选）覆盖容器镜像配置属性。
        
    

`AWS::Lambda::Function` 中的 `ImageConfig` 属性包含以下字段：

- Command – 指定要通过 `EntryPoint` 传入的参数。
    
- EntryPoint – 指定应用程序的入口点。
    
- WorkingDirectory – 指定工作目录。
    

|注意| |
|---|---|
|如果在 `ImageConfig` 模板中声明 Amazon CloudFormation 属性，则必须为全部三个 `ImageConfig` 属性提供值。| |

有关更多信息，请参阅 _Amazon CloudFormation 用户指南_中的 [ImageConfig](https://docs.amazonaws.cn/AWSCloudFormation/latest/UserGuide/aws-resource-lambda-function.html#cfn-lambda-function-imageconfig)。