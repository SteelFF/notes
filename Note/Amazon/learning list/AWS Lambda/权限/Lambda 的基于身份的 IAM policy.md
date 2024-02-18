您可以使用 Amazon Identity and Access Management (IAM) 中基于身份的策略授予您账户中的用户访问 Lambda 的权限。基于身份的策略可以直接应用于用户，也可以应用于与用户相关的组和角色。您也可以授予另一个账户中的用户在您的账户中代入角色和访问您的 Lambda 资源的权限。此页面显示了有关如何将基于身份的策略用于函数开发的示例。

Lambda 提供Amazon托管策略，授予对 Lambda API 操作的访问权限，在某些情况下还可以访问其他Amazon服务，用于开发和管理 Lambda 资源。Lambda 根据需要更新这些托管策略，确保您的用户有权在新功能发布时进行访问。

- **AWSLambda_FullAccess** - 授予对 Lambda 操作和其他用于开发和维护 Lambda 资源的 Amazon 服务的完全访问权。此策略是通过缩小先前策略 **AWSLambdaFullAccess** 的范围创建的。
    
- **AWSLambda_ReadOnlyAccess** – 授予对 Lambda 资源的只读访问权限。此策略是通过缩小先前策略 **AWSLambdaReadOnlyAccess** 的范围创建的。
    
- **AWSLambdaRole** – 授予调用 Lambda 函数的权限。
    

Amazon 托管策略授予 API 操作的权限，而不限制用户可以修改的 Lambda 函数或层。要进行更精细的控制，您可以创建自己的策略来限制用户权限的范围。

###### 小节目录

- [[#函数开发]]
- [[#层开发和使用]]
- [[#跨账户角色]]
- [[#用于 VPC 设置的条件键]]
## 函数开发

可以使用基于身份的策略，允许用户对 Lambda 函数进行操作。

###### 注意

对于定义为容器镜像的函数，必须在 Amazon 弹性容器注册表中配置用户访问图像的权限。有关示例，请参阅 [Amazon ECR 权限。](https://docs.amazonaws.cn/lambda/latest/dg/gettingstarted-images.html#configuration-images-permissions)

以下显示具有有限范围的权限策略示例。该策略允许用户创建和管理名称前带指定前缀 (`intern-`) 并用指定执行角色配置的 Lambda 函数。

###### 例 函数开发策略
``` json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ReadOnlyPermissions",
            "Effect": "Allow", 
            "Action": [
                "lambda:GetAccountSettings",
                "lambda:GetEventSourceMapping",
                "lambda:GetFunction",
                "lambda:GetFunctionConfiguration",           
                "lambda:GetFunctionCodeSigningConfig",
                "lambda:GetFunctionConcurrency",                
                "lambda:ListEventSourceMappings",
                "lambda:ListFunctions",      
                "lambda:ListTags",
                "iam:ListRoles"
            ],
            "Resource": "*"
        },
        {
            "Sid": "DevelopFunctions",
            "Effect": "Allow", 
            "NotAction": [
                "lambda:AddPermission",
                "lambda:PutFunctionConcurrency"
            ],
            "Resource": "arn:aws:lambda:*:*:function:intern-*"
        },
        {
            "Sid": "DevelopEventSourceMappings",
            "Effect": "Allow", 
            "Action": [
                "lambda:DeleteEventSourceMapping",
                "lambda:UpdateEventSourceMapping",
                "lambda:CreateEventSourceMapping"
            ],
            "Resource": "*",
            "Condition": {
                "StringLike": {
                    "lambda:FunctionArn": "arn:aws:lambda:*:*:function:intern-*"
                }
            }
        },
        {
            "Sid": "PassExecutionRole",
            "Effect": "Allow", 
            "Action": [
                "iam:ListRolePolicies",
                "iam:ListAttachedRolePolicies",
                "iam:GetRole",
                "iam:GetRolePolicy",
                "iam:PassRole",
                "iam:SimulatePrincipalPolicy"
            ],
            "Resource": "arn:aws:iam::*:role/intern-lambda-execution-role"
        },
        {
            "Sid": "ViewLogs",
            "Effect": "Allow", 
            "Action": [
                "logs:*"
            ],
            "Resource": "arn:aws:logs:*:*:log-group:/aws/lambda/intern-*"
        }
    ]
}
```

策略中的权限基于它们所支持的[资源和条件](https://docs.amazonaws.cn/lambda/latest/dg/lambda-api-permissions-ref.html)组织成语句。

- `ReadOnlyPermissions` – 当您浏览和查看函数时，Lambda 控制台使用这些权限。它们不支持资源模式或条件。
``` json
{
            "Action": [
                "lambda:GetAccountSettings",
                "lambda:GetEventSourceMapping",
                "lambda:GetFunction",
                "lambda:GetFunctionConfiguration",           
                "lambda:GetFunctionCodeSigningConfig",
                "lambda:GetFunctionConcurrency",                
                "lambda:ListEventSourceMappings",
                "lambda:ListFunctions",      
                "lambda:ListTags",
                "iam:ListRoles"
            ],
            "Resource": "*"
}
```
- `DevelopFunctions` – 使用任何对前缀为 `intern-` 的函数执行的 Lambda 操作，但 `AddPermission` 和 `PutFunctionConcurrency` _除外_。`AddPermission` 修改函数上[基于资源的策略](https://docs.amazonaws.cn/lambda/latest/dg/access-control-resource-based.html)并可能影响安全性。`PutFunctionConcurrency` 保留函数的扩展容量并且可以从其他函数那里取得容量。
``` json
{
            "NotAction": [
                "lambda:AddPermission",
                "lambda:PutFunctionConcurrency"
            ],
            "Resource": "arn:aws:lambda:*:*:function:intern-*"
}
```
- `DevelopEventSourceMappings` – 管理前缀为 `intern-` 的函数的事件源映射。虽然这些操作在事件源映射上运行，但您可以通过附带_条件_的函数限制它们。
``` json
{
            "Action": [
                "lambda:DeleteEventSourceMapping",
                "lambda:UpdateEventSourceMapping",
                "lambda:CreateEventSourceMapping"
            ],
            "Resource": "*",
            "Condition": {
                "StringLike": {
                    "lambda:FunctionArn": "arn:aws:lambda:*:*:function:intern-*"
                }
            }
}
```
- `PassExecutionRole` – 查看并仅传递名为 `intern-lambda-execution-role` 的角色，该角色必须由具有 IAM 权限的用户创建和管理。当您为函数分配执行角色时，使用 `PassRole`。
``` json
{
            "Action": [
                "iam:ListRolePolicies",
                "iam:ListAttachedRolePolicies",
                "iam:GetRole",
                "iam:GetRolePolicy",
                "iam:PassRole",
                "iam:SimulatePrincipalPolicy"
            ],
            "Resource": "arn:aws:iam::*:role/intern-lambda-execution-role"
}
```
- `ViewLogs` – 使用 CloudWatch Logs 查看前缀为 `intern-` 的函数的日志。
``` json
{
            "Action": [
                "logs:*"
            ],
            "Resource": "arn:aws:logs:*:*:log-group:/aws/lambda/intern-*"
}
```

该策略允许用户开始使用 Lambda，而不会将其他用户的资源置于风险之下。它不允许用户配置能被触发或调用其他 Amazon 服务的函数，这需要更广的 IAM 权限。它也不包含对于不支持有限范围策略的服务（如 CloudWatch 和 X-Ray）的权限。将只读策略用于这些服务以便让用户能够访问指标和跟踪数据。

当您为您的函数配置触发器时，需要有权使用调用您的函数的 Amazon 服务。例如，要配置 Amazon S3 触发器，您需要权限来使用管理存储桶通知的 Amazon S3 操作。其中很多权限包括在 **AWSLambdaFullAccess** 托管策略中。示例策略可从本指南的 [GitHub 存储库](https://github.com/awsdocs/aws-lambda-developer-guide/tree/master/iam-policies)中找到。

## 层开发和使用

以下策略授予用户创建层并通过函数使用层的权限。资源模式允许用户在任何 Amazon 区域和任何层版本中工作，只要图层的名称以 `test-` 开头即可。

##### 例 层开发策略
``` json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublishLayers",
            "Effect": "Allow",
            "Action": [
                "lambda:PublishLayerVersion"
            ],
            "Resource": "arn:aws:lambda:*:*:layer:test-*"
        },
        {
            "Sid": "ManageLayerVersions",
            "Effect": "Allow",
            "Action": [
                "lambda:GetLayerVersion",
                "lambda:DeleteLayerVersion"
            ],
            "Resource": "arn:aws:lambda:*:*:layer:test-*:*"
        }
    ]
}
```

您还可以附加 `lambda:Layer` 条件以在函数创建和配置过程中强制使用层。例如，您可以防止用户使用其他账户发布的层。以下策略在 `CreateFunction` 和 `UpdateFunctionConfiguration` 操作中添加一个条件，以要求任何指定层来自账户 `123456789012`。
``` json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ConfigureFunctions",
            "Effect": "Allow",
            "Action": [
                "lambda:CreateFunction",
                "lambda:UpdateFunctionConfiguration"
            ],
            "Resource": "*",
            "Condition": {
                "ForAllValues:StringLike": {
                    "lambda:Layer": [
                        "arn:aws:lambda:*:123456789012:layer:*:*"
                    ]
                }
            }
        }
    ]
}
```
为确保条件应用，应验证没有其他语句向用户授予这些操作的权限。

## 跨账户角色

您可以将任何上述策略和语句应用于一个角色，然后与另一个账户共享该角色，使其可以访问您的 Lambda 资源。与用户不同，角色没有用于身份验证的凭据。相反，它具有_信任策略_，可以指定谁能够代入该角色并使用其权限。

可以使用跨账户角色来允许您信任的账户访问 Lambda 操作和资源。如果您只是想授予调用函数或使用层的权限，请改为使用[基于资源的策略](https://docs.amazonaws.cn/lambda/latest/dg/access-control-resource-based.html)。

有关更多信息，请参阅 _IAM 用户指南_中的 [IAM 角色](https://docs.amazonaws.cn/IAM/latest/UserGuide/id_roles.html)。

## 用于 VPC 设置的条件键

您可以对 VPC 设置使用条件键来为您的 Lambda 函数提供额外的权限控制。例如，您可以强制企业中的所有 Lambda 函数都连接到 VPC。您还可以指定允许函数使用或拒绝其使用的子网和安全组。

有关更多信息，请参阅[将 IAM 条件键用于 VPC 设置](https://docs.amazonaws.cn/lambda/latest/dg/configuration-vpc.html#vpc-conditions)。