Lambda 函数的执行角色是一个 Amazon Identity and Access Management (IAM) 角色，用于向函数授予访问Amazon服务和资源的权限。例如，您可以创建一个有权将日志发送到 Amazon CloudWatch 并将跟踪数据上传到 Amazon X-Ray 的执行角色。此页面提供有关如何创建、查看和管理 Lambda 函数执行角色的信息。

您在创建函数时提供一个执行角色。**在调用函数时，Lambda 会通过代入此角色自动为函数提供临时凭证**。您无需在函数代码中调用 `sts:AssumeRole`。

为了让 Lambda 正确担任执行角色，**该角色的[信任策略](https://docs.amazonaws.cn/lambda/latest/dg/lambda-intro-execution-role.html#permissions-executionrole-api)必须将 Lambda 服务主体（`lambda.amazonaws.com`）指定为可信服务。**

###### 查看函数的执行角色

1. 打开 Lamba 控制台的[函数页面](https://console.amazonaws.cn/lambda/home#/functions)。
    
2. 选择一个函数的名称。
    
3. 选择 **Configuration**（配置），然后选择 **Permissions**（权限）。
    
4. 在 **Resource summary**（资源摘要）下，查看函数可访问的服务和资源。
    
5. 从下拉列表中选择一个服务以查看与该服务相关的权限。
    

可以随时在函数的执行角色中添加或删除权限，或配置您的函数以使用不同的角色。为您的函数使用Amazon开发工具包调用的任何服务以及为 Lambda 用来启用可选功能的服务添加权限。

向函数添加权限时，请也更新其代码或配置。这会强制停止并替换函数的具有过时凭证的运行实例。

###### 主题

- [[#在 IAM 控制台中创建执行角色]]
- [[#授予对 Lambda 执行角色的最低访问权限]]
- [[#使用 IAM API 管理角色]]
- [[#临时安全凭证的会话持续时间]]
- [[#Lambda 功能的Amazon托管式策略]]
- [[#使用 Lambda 执行环境凭证]]

## 在 IAM 控制台中创建执行角色

预设情况下，当您[在 Lambda 控制台中创建函数](https://docs.amazonaws.cn/lambda/latest/dg/getting-started.html#getting-started-create-function)时，Lambda 会创建具有最少权限的执行角色。您也可以在 IAM 控制台中创建执行角色。

###### 在 IAM 控制台中创建执行角色

1. 在 IAM 控制台中，打开 [Roles](https://console.amazonaws.cn/iam/home#/roles)（角色）页面。
    
2. 选择 **Create role**（创建角色）。
    
3. 在 **Use case（使用案例）**下，选择 **Lambda**。
    
4. 选择 **Next**（下一步）。
    
5. 选择 Amazon 托管策略 **AWSLambdaBasicExecutionRole** 和 **AWSXRayDaemonWriteAccess**。
    
6. 选择 **Next**（下一步）。
    
7. 输入**角色名称**，然后选择**创建角色**。
    

有关详细说明，请参阅 _IAM 用户指南_中的[为Amazon服务（控制台）创建一个角色](https://docs.amazonaws.cn/IAM/latest/UserGuide/id_roles_create_for-service.html#roles-creatingrole-service-console)。

## 授予对 Lambda 执行角色的最低访问权限

在开发阶段首次为 Lambda 函数创建 IAM 角色时，有时授予的权限可能超出所需权限。在生产环境中发布函数之前，最佳实践是调整策略，使其仅包含所需权限。有关更多信息，请参阅《_IAM 用户指南_》中的[应用最低权限许可](https://docs.amazonaws.cn/IAM/latest/UserGuide/best-practices.html#grant-least-privilege)。

使用 IAM 访问分析器帮助确定 IAM 执行角色策略所需的权限。IAM 访问分析器将检查您指定的日期范围内的 Amazon CloudTrail 日志，并生成仅具有该函数在该时间内使用的权限的策略模板。您可以使用模板创建具有精细权限的托管策略，然后将其附加到 IAM 角色。这样，您仅需授予角色与特定使用案例中的 Amazon 资源进行交互所需的权限。

有关更多信息，请参阅《_IAM 用户指南_》中的[基于访问活动生成策略](https://docs.amazonaws.cn/IAM/latest/UserGuide/access_policies_generate-policy.html)。

## 使用 IAM API 管理角色

要使用 Amazon Command Line Interface (Amazon CLI) 创建执行角色，请使用 **create-role** 命令。在使用此命令时，您可以指定内联[信任策略](https://docs.amazonaws.cn/IAM/latest/UserGuide/id_roles_terms-and-concepts.html#delegation)。角色的信任策略会向指定主体授予代入该角色的权限。在以下示例中，您向 Lambda 服务主体授予代入角色的权限。请注意，JSON 字符串中对转义引号的要求可能因 shell 而异。

``` shell
 aws iam create-role --role-name lambda-ex --assume-role-policy-document '{"Version": "2012-10-17","Statement": [{ "Effect": "Allow", "Principal": {"Service": "lambda.amazonaws.com"}, "Action": "sts:AssumeRole"}]}' 
```

您还可以使用单独的 JSON 文件为角色定义信任策略。在下面的示例中，`trust-policy.json` 是位于当前目录中的一个文件。

###### 例 trust-policy.json

``` json
{   
	"Version": "2012-10-17",
	"Statement": [
		{       
		"Effect": "Allow",
		"Principal": {
		    "Service": "lambda.amazonaws.com"
		    },
	    "Action": "sts:AssumeRole"
	    }   
	] 
}
```

``` shell
aws iam create-role --role-name lambda-ex --assume-role-policy-document file://trust-policy.json
```

您应看到以下输出：

``` json
{
	"Role": {
	    "Path": "/",
		"RoleName": "lambda-ex",
        "RoleId": "AROAQFOXMPL6TZ6ITKWND",
        "Arn": "arn:aws:iam::123456789012:role/lambda-ex",
        "CreateDate": "2020-01-17T23:19:12Z",
        "AssumeRolePolicyDocument": {
            "Version": "2012-10-17",
            "Statement": [
				{
					"Effect": "Allow",
					"Principal": {
					    "Service": "lambda.amazonaws.com"
					    },
					"Action": "sts:AssumeRole"
				}             
			]         
		}    
	} 
}
```

| 注意 |  |
|-------|------|
| 当您调用函数时，Lambda 会自动代入您的执行角色。您应该避免在函数代码中手动调用 `sts:AssumeRole`。如果您的使用案例要求角色能代入自己，则必须将角色本身作为可信主体包含在角色的信任策略中。有关如何修改角色信任策略的更多信息，请参阅《IAM 用户指南》中的[修改角色信任策略（控制台）](https://docs.amazonaws.cn/IAM/latest/UserGuide/roles-managingrole-editing-console.html#roles-managingrole_edit-trust-policy)。 |  |

要向角色添加权限，请使用 **attach-policy-to-role** 命令。首先添加 `AWSLambdaBasicExecutionRole` 托管策略。

``` shell
aws iam attach-role-policy --role-name lambda-ex --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole
```

## 临时安全凭证的会话持续时间

Lambda 会代入与您的函数关联的执行角色以获取临时安全凭证，这些凭证随后可在函数调用期间作为环境变量使用。如果您在 Lambda 之外使用这些临时凭证，例如创建预签名的 Amazon S3 URL，则无法控制会话持续时间。IAM 最长会话持续时间设置不适用于由 Amazon 服务（例如 Lambda）代入的会话。如果需要对会话持续时间有更多控制，请使用 [sts:AssumeRole](https://docs.amazonaws.cn/STS/latest/APIReference/API_AssumeRole.html) 操作。

## Lambda 功能的Amazon托管式策略

以下 Amazon 托管式策略提供使用 Lambda 函数所需的权限。

|更改|说明|日期|
|---|---|---|
|**[AWSLambdaMSKExecutionRole](https://console.amazonaws.cn/iam/home#policies/arn:aws:iam::aws:policy/service-role/AWSLambdaMSKExecutionRole)**：Lambda 将 [kafka:DescribeClusterV2](https://docs.amazonaws.cn/MSK/2.0/APIReference/v2-clusters-clusterarn.html#v2-clusters-clusterarnget) 权限添加到了此政策。|`AWSLambdaMSKExecutionRole` 授予读取和访问 Amazon Managed Streaming for Apache Kafka（Amazon MSK）集群中的记录、管理网络接口 (ENI) 并写入到 CloudWatch Logs 的权限。|2022 年 6 月 17 日|
|**[AWSLambdaBasicExecutionRole](https://console.amazonaws.cn/iam/home#policies/arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole)** – Lambda 开始跟踪对此策略所做的更改。|`AWSLambdaBasicExecutionRole` 授予将日志上传至 CloudWatch 的权限。|2022 年 2 月 14 日|
|**[AWSLambdaDynamoDBExecutionRole](https://console.amazonaws.cn/iam/home#policies/arn:aws:iam::aws:policy/service-role/AWSLambdaDynamoDBExecutionRole)** – Lambda 开始跟踪对此策略所做的更改。|`AWSLambdaDynamoDBExecutionRole` 授予读取 Amazon DynamoDB 流中的记录并写入到 CloudWatch Logs 的权限。|2022 年 2 月 14 日|
|**[AWSLambdaKinesisExecutionRole](https://console.amazonaws.cn/iam/home#policies/arn:aws:iam::aws:policy/service-role/AWSLambdaKinesisExecutionRole)** – Lambda 开始跟踪对此策略所做的更改。|`AWSLambdaKinesisExecutionRole` 授予读取 Amazon Kinesis 数据流中的事件并写入到 CloudWatch Logs 的权限。|2022 年 2 月 14 日|
|**[AWSLambdaMSKExecutionRole](https://console.amazonaws.cn/iam/home#policies/arn:aws:iam::aws:policy/service-role/AWSLambdaMSKExecutionRole)** – Lambda 开始跟踪对此策略所做的更改。|`AWSLambdaMSKExecutionRole` 授予读取和访问 Amazon Managed Streaming for Apache Kafka (Amazon MSK) 集群中的记录、管理网络接口 (ENI) 并写入到 CloudWatch Logs 的权限。|2022 年 2 月 14 日|
|**[AWSLambdaSQSQueueExecutionRole](https://console.amazonaws.cn/iam/home#policies/arn:aws:iam::aws:policy/service-role/AWSLambdaSQSQueueExecutionRole)** – Lambda 开始跟踪对此策略所做的更改。|`AWSLambdaSQSQueueExecutionRole` 授予读取 Amazon Simple Queue Service (Amazon SQS) 队列中的消息并写入到 CloudWatch Logs 的权限。|2022 年 2 月 14 日|
|**[AWSLambdaVPCAccessExecutionRole](https://console.amazonaws.cn/iam/home#policies/arn:aws:iam::aws:policy/service-role/AWSLambdaVPCAccessExecutionRole)** – Lambda 开始跟踪对此策略所做的更改。|`AWSLambdaVPCAccessExecutionRole` 授予管理 Amazon VPC 中的 ENI 并写入到 CloudWatch Logs 的权限。|2022 年 2 月 14 日|
|**[AWSXRayDaemonWriteAccess](https://console.amazonaws.cn/iam/home#policies/arn:aws:iam::aws:policy/AWSXRayDaemonWriteAccess)** – Lambda 开始跟踪对此策略所做的更改。|`AWSXRayDaemonWriteAccess` 授予将跟踪数据上传到 X-Ray 的权限。|2022 年 2 月 14 日|
|**[CloudWatchLambdaInsightsExecutionRolePolicy](https://console.amazonaws.cn/iam/home#policies/arn:aws:iam::aws:policy/CloudWatchLambdaInsightsExecutionRolePolicy)** – Lambda 开始跟踪对此策略所做的更改。|`CloudWatchLambdaInsightsExecutionRolePolicy` 授予将运行时指标写入到 CloudWatch Lambda Insights 的权限。|2022 年 2 月 14 日|
|**[AmazonS3ObjectLambdaExecutionRolePolicy](https://console.amazonaws.cn/iam/home#policies/arn:aws:iam::aws:policy/service-role/AmazonS3ObjectLambdaExecutionRolePolicy)** – Lambda 开始跟踪对此策略所做的更改。|`AmazonS3ObjectLambdaExecutionRolePolicy` 授予与 Amazon Simple Storage Service (Amazon S3) 对象 Lambda 交互并写入到 CloudWatch Logs 的权限。|2022 年 2 月 14 日|

对于某些功能，Lambda 控制台会尝试在客户管理型策略中向执行角色添加缺失的权限。这些策略可能会变得很多。为避免创建额外的策略，在启用功能之前，请将相关的 Amazon 托管策略添加到您的执行角色。

当您使用[事件源映射](https://docs.amazonaws.cn/lambda/latest/dg/invocation-eventsourcemapping.html)调用您的函数时，Lambda 将使用执行角色读取事件数据。例如，Kinesis 的事件源映射从数据流读取事件并将事件成批发送到您的函数。

当服务在您的账户中担任角色时，您的角色信任策略中可以包含 `aws:SourceAccount` 和 `aws:SourceArn` 全局条件上下文密钥，以将对角色的访问限制为仅由预期资源生成的请求。有关更多信息，请参阅[防止 Amazon Security Token Service 跨服务混淆代理](https://docs.amazonaws.cn/IAM/latest/UserGuide/confused-deputy.html#cross-service-confused-deputy-prevention)。

可以将事件源映射用于以下服务：

###### Lambda 从中读取事件的服务

- [Amazon DynamoDB](https://docs.amazonaws.cn/lambda/latest/dg/with-ddb.html)
    
- [Amazon Kinesis](https://docs.amazonaws.cn/lambda/latest/dg/with-kinesis.html)
    
- [Amazon MQ](https://docs.amazonaws.cn/lambda/latest/dg/with-mq.html)
    
- [Amazon Managed Streaming for Apache Kafka (Amazon MSK)](https://docs.amazonaws.cn/lambda/latest/dg/with-msk.html)
    
- [自行管理的 Apache Kafka](https://docs.amazonaws.cn/lambda/latest/dg/with-kafka.html)
    
- [Amazon Simple Queue Service (Amazon SQS)](https://docs.amazonaws.cn/lambda/latest/dg/with-sqs.html)
    
- [Amazon DocumentDB（与 MongoDB 兼容）（Amazon DocumentDB）](https://docs.amazonaws.cn/lambda/latest/dg/with-documentdb.html)
    

除了Amazon托管式策略，Lambda 控制台还提供模板，以创建包含用于额外使用案例的权限的自定义策略。当您在 Lambda 控制台中创建函数时，可以选择利用来自一个或多个模板的权限创建新的执行角色。当您从蓝图创建函数，或者配置需要访问其他服务的选项时，也会自动应用这些模板。示例模板可从本指南的 [GitHub 存储库](https://github.com/awsdocs/aws-lambda-developer-guide/tree/master/iam-policies)中找到。

## 使用 Lambda 执行环境凭证

您的 Lambda 函数代码通常会向其他 Amazon 服务发出 API 请求。为了发出这些请求，Lambda 通过承担您的函数的执行角色来生成一组临时凭证。这些凭证在函数调用期间可用作环境变量。使用 Amazon 开发工具包时，无需直接在代码中为开发工具包提供凭证。默认情况下，凭证提供程序链会按顺序检查每个可以设置凭证的位置，然后选择第一个可用位置，通常是环境变量（`AWS_ACCESS_KEY_ID`、`AWS_SECRET_ACCESS_KEY` 和 `AWS_SESSION_TOKEN`）。

如果请求是来自您执行环境中的 Amazon API 请求，Lambda 会将源函数 ARN 注入到凭证上下文中。Lambda 还会为其在执行环境之外代表您发出的以下 Amazon API 请求注入源函数 ARN：

|服务|操作|Reason|
|---|---|---|
|CloudWatch Logs|`CreateLogGroup`, `CreateLogStream`, `PutLogEvents`|将日志存储到 CloudWatch Logs 日志组|
|X-Ray|`PutTraceSegments`|将跟踪数据发送到 X-Ray|
|Amazon EFS|`ClientMount`|将函数连接到 Amazon Elastic File System（Amazon EFS）文件系统|

使用相同执行角色在执行环境之外代表您进行的其他 Amazon API 调用不包含源函数 ARN。执行环境之外的此类 API 调用示例包括：

- 调用 Amazon Key Management Service (Amazon KMS) 以自动加密和解密您的环境变量。
    
- 调用 Amazon Elastic Compute Cloud（Amazon EC2），为启用 VPC 的函数创建弹性网络接口（ENI）。
    
- 调用 Amazon Simple Queue Service（Amazon SQS）等 Amazon 服务，从设置为[事件源映射](https://docs.amazonaws.cn/lambda/latest/dg/invocation-eventsourcemapping.html)的事件源进行读取。
    

使用凭证上下文中的源函数 ARN，您可以验证对您的资源的调用是否来自特定 Lambda 函数的代码。要对此进行验证，请在 IAM 基于身份的策略或服务控制策略（SCP）中使用 `lambda:SourceFunctionArn` 条件键。

###### 注意

您不能在基于资源的策略中使用 `lambda:SourceFunctionArn` 条件键。

在基于身份的策略或 SCP 中使用此条件键，您可以为您的函数代码对其他 Amazon 服务执行的 API 操作实施安全控制。这有一些关键的安全应用程序，例如帮助您识别凭证泄漏的来源。

###### 注意

`lambda:SourceFunctionArn` 条件键与 `lambda:FunctionArn` 和 `aws:SourceArn` 条件键不同。`lambda:FunctionArn` 条件键仅适用于[事件源映射](https://docs.amazonaws.cn/lambda/latest/dg/invocation-eventsourcemapping.html)，并帮助定义您的事件源可以调用哪些函数。`aws:SourceArn` 条件键仅适用于以您的 Lambda 函数为目标资源的策略，并帮助定义哪些其他 Amazon 服务和资源可以调用该函数。`lambda:SourceFunctionArn` 条件键可应用于任何基于身份的策略或 SCP，以定义有权对其他资源进行特定 Amazon API 调用的特定 Lambda 函数。

要在您的策略中使用 `lambda:SourceFunctionArn`，请将其作为条件包含在任何 [ARN 条件运算符](https://docs.amazonaws.cn/IAM/latest/UserGuide/reference_policies_elements_condition_operators.html#Conditions_ARN)中。密钥的值必须是有效的 ARN。

例如，假设您的 Lambda 函数代码进行了针对特定 Amazon S3 存储桶的 `s3:PutObject` 调用。您可能希望仅允许一个特定 Lambda 函数让 `s3:PutObject` 访问该存储桶。在这种情况下，您的函数的执行角色应附加如下所示的策略：

###### 例 授予特定 Lambda 函数访问 Amazon S3 资源的权限的策略

``` json
{     
	"Version": "2012-10-17",
		"Statement": [
		    {             
			    "Sid": "ExampleSourceFunctionArn",
			    "Effect": "Allow",
			    "Action": "s3:PutObject",
			    "Resource": "arn:aws:s3:::lambda_bucket/*",
			    "Condition": {
			        "ArnEquals": {
			            "lambda:SourceFunctionArn": 
			            "arn:aws:lambda:us-east-1:123456789012:function:source_lambda"
			        }             
			    }         
			}     
		] 
	}
}
```

如果源是具有 ARN `arn:aws:lambda:us-east-1:123456789012:function:source_lambda` 的 Lambda 函数，则此策略仅允许 `s3:PutObject` 访问。此策略不允许 `s3:PutObject` 访问任何其他调用身份。即使不同的函数或实体使用相同的执行角色进行 `s3:PutObject` 调用也是如此。

您还可以在[服务控制策略](https://docs.amazonaws.cn/organizations/latest/userguide/orgs_manage_policies_scps.html)中使用 `lambda:SourceFunctionArn`。例如，假设您希望将对存储桶的访问限制为单个 Lambda 函数的代码或来自特定虚拟私有云（VPC）的调用。以下 SCP 对此进行了说明。

###### 例 在特定条件下拒绝访问 Amazon S3 的策略

``` json
{
	"Version": "2012-10-17",
	"Statement": [
	    {             
		    "Action": [
		        "s3:*"             
		    ],
		    "Resource": "arn:aws:s3:::lambda_bucket/*",
		    "Effect": "Deny",
		    "Condition": {
		        "StringNotEqualsIfExists": {
		        "aws:SourceVpc": [
		            "vpc-12345678" 
		            ]
		        },
		        "ArnNotEqualsIfExists": {
					"lambda:SourceFunctionArn":
					"arn:aws:lambda:us-east-1:123456789012:function:source_lambda"
				}             
			}
		}     
	] 
}
```

此策略将拒绝所有 S3 操作，除非它们来自具有 ARN `arn:aws:lambda:*:123456789012:function:source_lambda` 的特定 Lambda 函数，或者它们来自指定的 VPC。`StringNotEqualsIfExists` 运算符告诉 IAM 仅当请求中存在 `aws:SourceVpc` 键时才处理此条件。同样，仅当存在 `lambda:SourceFunctionArn` 时，IAM 才会考虑 `ArnNotEqualsIfExists` 运算符。