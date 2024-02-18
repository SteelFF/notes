您可以通过在 Amazon Identity and Access Management (IAM) 策略中指定资源和条件来限制用户权限的范围。策略中的每个操作都支持资源和条件类型的组合，这些类型根据操作的行为而有所不同。

每条 IAM 策略语句为对一个资源执行的一个操作授予权限。如果操作不对指定资源执行操作，或者您授予对所有资源执行操作的权限，则策略中资源的值为通配符 (`*`)。对于许多操作，可以通过指定资源的 Amazon 资源名称（ARN）或与多个资源匹配的 ARN 模式来限制用户可修改的资源。

要按资源限制权限，请指定资源的 ARN。

###### Lambda 资源 ARN 格式

- 函数 – `` arn:aws:lambda:`us-west-2`:`123456789012`:function:`my-function` ``
    
- 函数版本 – `` arn:aws:lambda:`us-west-2`:`123456789012`:function:`my-function`:`1` ``
    
- 函数别名 – `` arn:aws:lambda:`us-west-2`:`123456789012`:function:`my-function`:`TEST` ``
    
- 事件源映射 – `` arn:aws:lambda:`us-west-2`:`123456789012`:event-source-mapping:`fa123456-14a1-4fd2-9fec-83de64ad683de6d47` ``
    
- 层 – `` arn:aws:lambda:`us-west-2`:`123456789012`:layer:`my-layer` ``
    
- 层版本 – `` arn:aws:lambda:`us-west-2`:`123456789012`:layer:`my-layer`:`1` ``
    

例如，以下策略允许 Amazon Web Services 账户 `123456789012` 中的用户调用美国西部（俄勒冈）Amazon 区域中名为 `my-function` 的函数。

###### 例 调用函数策略
``` json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Invoke",
            "Effect": "Allow",
            "Action": [
                "lambda:InvokeFunction"
            ],
            "Resource": "arn:aws:lambda:us-west-2:123456789012:function:my-function"
        }
    ]
}
```
是一种特殊情形，其中，操作标识符 (`lambda:InvokeFunction`) 不同于 API 操作 ([Invoke](https://docs.amazonaws.cn/lambda/latest/dg/API_Invoke.html))。对于其他操作，操作标识符为操作名称加上 `lambda:` 前缀。

###### 小节目录

- [[#策略条件]]
- [[#函数资源名称]]
- [[#函数操作]]
- [[#事件源映射操作]]
- [[#层操作]]

## 策略条件

条件是可选的策略元素，它应用其他逻辑来确定是否允许执行操作。除了所有操作支持的公用[条件](https://docs.amazonaws.cn/IAM/latest/UserGuide/reference_policies_elements_condition.html)之外，Lambda 定义了一些条件类型，您可以用来限制某些操作的额外参数的值。

例如，`lambda:Principal` 条件允许您限制用户可以根据函数的[基于资源的策略](https://docs.amazonaws.cn/lambda/latest/dg/access-control-resource-based.html)授予调用访问权限的服务或账户。以下策略允许用户授予对 Amazon Simple Notification Service (Amazon SNS) 主题的权限，以调用名为 `test` 的函数。

###### 例 管理函数策略权限
``` json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ManageFunctionPolicy",
            "Effect": "Allow",
            "Action": [
                "lambda:AddPermission",
                "lambda:RemovePermission"
            ],
            "Resource": "arn:aws:lambda:us-west-2:123456789012:function:test:*",
            "Condition": {
                "StringEquals": {
                    "lambda:Principal": "sns.amazonaws.com"
                }
            }
        }
    ]
}
```
该条件要求委托人是 Amazon SNS 而不是其他服务或账户。资源模式要求函数名称为 `test` 并包含版本号或别名。例如，`test:v1`。

有关 Lambda 和其他 Amazon 服务的资源和条件的更多信息，请参阅_服务授权参考_中的 [Amazon 服务的操作、资源和条件键](https://docs.amazonaws.cn/service-authorization/latest/reference/reference_policies_actions-resources-contextkeys.html)。

## 函数资源名称

您可以使用 Amazon Resource Name（ARN）在策略语句中引用 Lambda 函数。函数 ARN 的格式取决于您是要引用整个函数（无限定）、某个函数[版本](https://docs.amazonaws.cn/lambda/latest/dg/configuration-versions.html)，还是[别名](https://docs.amazonaws.cn/lambda/latest/dg/configuration-aliases.html)（限定）。

调用 Lambda API 时，用户可以通过在 [GetFunction](https://docs.amazonaws.cn/lambda/latest/dg/API_GetFunction.html) `FunctionName` 参数中传递版本 ARN 或别名 ARN，或者通过在 [GetFunction](https://docs.amazonaws.cn/lambda/latest/dg/API_GetFunction.html) `Qualifier` 参数中设置值，来指定一个版本或别名。Lambda 通过比较 IAM 策略中的资源元素与在 API 调用中传递的 `FunctionName` 和 `Qualifier` 来做出授权决策。如果不匹配，Lambda 会拒绝该请求。

无论您是允许还是拒绝某个函数操作，都必须在策略声明中使用正确的函数 ARN 类型才能获得预期的结果。例如，假设您的策略引用了非限定 ARN，Lambda 会接受引用非限定 ARN 的请求，但拒绝引用限定 ARN 的请求。

|注意|  |
|----|----|
| 不能使用通配符 * 匹配账户 ID。有关接受的语法的更多信息，请参阅《_IAM 用户指南_》中的 [IAM JSON 策略参考](https://docs.amazonaws.cn/IAM/latest/UserGuide/reference_policies.html)。|  |

###### 例 允许调用非限定 ARN
``` json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "lambda:InvokeFunction",
            "Resource": "arn:aws:lambda:us-west-2:123456789012:function:myFunction"
        }
    ]
}
```
如果您的策略引用了特定的限定 ARN，Lambda 会接受引用该 ARN 的请求，但拒绝引用非限定 ARN 的请求（例如 `myFunction:2`）。

###### 例 允许调用特定的限定 ARN
``` json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "lambda:InvokeFunction",
            "Resource": "arn:aws:lambda:us-west-2:123456789012:function:myFunction:1"
        }
    ]
}
```
如果您的策略引用了任何限定 ARN`:*`，Lambda 会接受任何限定 ARN，但拒绝引用非限定 ARN 的请求。

###### 例 允许调用任何限定 ARN
``` json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "lambda:InvokeFunction",
            "Resource": "arn:aws:lambda:us-west-2:123456789012:function:myFunction:*"
        }
    ]
}
```
如果您的策略引用了任何使用 `*` 的 ARN，Lambda 会接受任何限定或非限定 ARN。

###### 例 允许调用任何限定或非限定 ARN
``` json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "lambda:InvokeFunction",
            "Resource": "arn:aws:lambda:us-west-2:123456789012:function:myFunction*"
        }
    ]
}
```

## 函数操作

对函数进行的操作可以通过函数、版本或别名 ARN 限制为特定函数，如下表中所述。将为所有资源 (`*`) 授予不支持资源限制的操作。

|函数操作|   |   |
|---|---|---|
|操作|资源|Condition|
|[AddPermission](https://docs.amazonaws.cn/lambda/latest/dg/API_AddPermission.html)<br><br>[RemovePermission](https://docs.amazonaws.cn/lambda/latest/dg/API_RemovePermission.html)|函数<br><br>函数版本<br><br>函数别名|`lambda:Principal`<br><br>`aws:ResourceTag/${TagKey}`<br><br>`lambda:FunctionUrlAuthType`|
|[调用](https://docs.amazonaws.cn/lambda/latest/dg/API_Invoke.html)<br><br>**权限：**`lambda:InvokeFunction`|函数<br><br>函数版本<br><br>函数别名|`aws:ResourceTag/${TagKey}`|
|[CreateFunction](https://docs.amazonaws.cn/lambda/latest/dg/API_CreateFunction.html)|函数|`lambda:CodeSigningConfigArn`<br><br>`lambda:Layer`<br><br>`lambda:VpcIds`<br><br>`lambda:SubnetIds`<br><br>`lambda:SecurityGroupIds`<br><br>`aws:ResourceTag/${TagKey}`<br><br>`aws:RequestTag/${TagKey}`<br><br>`aws:TagKeys`|
|[UpdateFunctionConfiguration](https://docs.amazonaws.cn/lambda/latest/dg/API_UpdateFunctionConfiguration.html)|函数|`lambda:CodeSigningConfigArn`<br><br>`lambda:Layer`<br><br>`lambda:VpcIds`<br><br>`lambda:SubnetIds`<br><br>`lambda:SecurityGroupIds`<br><br>`aws:ResourceTag/${TagKey}`|
|[CreateAlias](https://docs.amazonaws.cn/lambda/latest/dg/API_CreateAlias.html)<br><br>[DeleteAlias](https://docs.amazonaws.cn/lambda/latest/dg/API_DeleteAlias.html)<br><br>[DeleteFunction](https://docs.amazonaws.cn/lambda/latest/dg/API_DeleteFunction.html)<br><br>[DeleteFunctionCodeSigningConfig](https://docs.amazonaws.cn/lambda/latest/dg/API_DeleteFunctionCodeSigningConfig.html)<br><br>[DeleteFunctionConcurrency](https://docs.amazonaws.cn/lambda/latest/dg/API_DeleteFunctionConcurrency.html)<br><br>[GetAlias](https://docs.amazonaws.cn/lambda/latest/dg/API_GetAlias.html)<br><br>[GetFunction](https://docs.amazonaws.cn/lambda/latest/dg/API_GetFunction.html)<br><br>[GetFunctionCodeSigningConfig](https://docs.amazonaws.cn/lambda/latest/dg/API_GetFunctionCodeSigningConfig.html)<br><br>[GetFunctionConcurrency](https://docs.amazonaws.cn/lambda/latest/dg/API_GetFunctionConcurrency.html)<br><br>[GetFunctionConfiguration](https://docs.amazonaws.cn/lambda/latest/dg/API_GetFunctionConfiguration.html)<br><br>[GetPolicy](https://docs.amazonaws.cn/lambda/latest/dg/API_GetPolicy.html)<br><br>[ListProvisionedConcurrencyConfigs](https://docs.amazonaws.cn/lambda/latest/dg/API_ListProvisionedConcurrencyConfigs.html)<br><br>[ListAliases](https://docs.amazonaws.cn/lambda/latest/dg/API_ListAliases.html)<br><br>[ListTags](https://docs.amazonaws.cn/lambda/latest/dg/API_ListTags.html)<br><br>[ListVersionsByFunction](https://docs.amazonaws.cn/lambda/latest/dg/API_ListVersionsByFunction.html)<br><br>[PublishVersion](https://docs.amazonaws.cn/lambda/latest/dg/API_PublishVersion.html)<br><br>[PutFunctionCodeSigningConfig](https://docs.amazonaws.cn/lambda/latest/dg/API_PutFunctionCodeSigningConfig.html)<br><br>[PutFunctionConcurrency](https://docs.amazonaws.cn/lambda/latest/dg/API_PutFunctionConcurrency.html)<br><br>[UpdateAlias](https://docs.amazonaws.cn/lambda/latest/dg/API_UpdateAlias.html)<br><br>[UpdateFunctionCode](https://docs.amazonaws.cn/lambda/latest/dg/API_UpdateFunctionCode.html)|函数|`aws:ResourceTag/${TagKey}`|
|[CreateFunctionUrlConfig](https://docs.amazonaws.cn/lambda/latest/dg/API_CreateFunctionUrlConfig.html)<br><br>[DeleteFunctionUrlConfig](https://docs.amazonaws.cn/lambda/latest/dg/API_DeleteFunctionUrlConfig.html)<br><br>[GetFunctionUrlConfig](https://docs.amazonaws.cn/lambda/latest/dg/API_GetFunctionUrlConfig.html)<br><br>[UpdateFunctionUrlConfig](https://docs.amazonaws.cn/lambda/latest/dg/API_UpdateFunctionUrlConfig.html)|函数<br><br>函数别名|`lambda:FunctionUrlAuthType`<br><br>`lambda:FunctionArn`<br><br>`aws:ResourceTag/${TagKey}`|
|[ListFunctionUrlConfigs](https://docs.amazonaws.cn/lambda/latest/dg/API_ListFunctionUrlConfigs.html)|函数|`lambda:FunctionUrlAuthType`|
|[DeleteFunctionEventInvokeConfig](https://docs.amazonaws.cn/lambda/latest/dg/API_DeleteFunctionEventInvokeConfig.html)<br><br>[GetFunctionEventInvokeConfig](https://docs.amazonaws.cn/lambda/latest/dg/API_GetFunctionEventInvokeConfig.html)<br><br>[ListFunctionEventInvokeConfigs](https://docs.amazonaws.cn/lambda/latest/dg/API_ListFunctionEventInvokeConfigs.html)<br><br>[PutFunctionEventInvokeConfig](https://docs.amazonaws.cn/lambda/latest/dg/API_PutFunctionEventInvokeConfig.html)<br><br>[UpdateFunctionEventInvokeConfig](https://docs.amazonaws.cn/lambda/latest/dg/API_UpdateFunctionEventInvokeConfig.html)|函数|`aws:ResourceTag/${TagKey}`|
|[DeleteProvisionedConcurrencyConfig](https://docs.amazonaws.cn/lambda/latest/dg/API_DeleteProvisionedConcurrencyConfig.html)<br><br>[GetProvisionedConcurrencyConfig](https://docs.amazonaws.cn/lambda/latest/dg/API_GetProvisionedConcurrencyConfig.html)<br><br>[PutProvisionedConcurrencyConfig](https://docs.amazonaws.cn/lambda/latest/dg/API_PutProvisionedConcurrencyConfig.html)|函数别名<br><br>函数版本|`aws:ResourceTag/${TagKey}`|
|[GetAccountSettings](https://docs.amazonaws.cn/lambda/latest/dg/API_GetAccountSettings.html)<br><br>[ListFunctions](https://docs.amazonaws.cn/lambda/latest/dg/API_ListFunctions.html)|`*`|None（无）|
|[TagResource](https://docs.amazonaws.cn/lambda/latest/dg/API_TagResource.html)|函数|`aws:ResourceTag/${TagKey}`<br><br>`aws:RequestTag/${TagKey}`<br><br>`aws:TagKeys`|
|[UntagResource](https://docs.amazonaws.cn/lambda/latest/dg/API_UntagResource.html)|函数|`aws:ResourceTag/${TagKey}`<br><br>`aws:TagKeys`|

## 事件源映射操作

对于[事件源映射](https://docs.amazonaws.cn/lambda/latest/dg/invocation-eventsourcemapping.html)，您可以将删除和更新权限限制为特定事件源。`lambda:FunctionArn` 条件允许您限制用户可配置事件源以调用的函数。

对于这些操作，资源是事件源映射，因此 Lambda 提供了一个条件，允许您根据事件源映射调用的函数来限制权限。

|事件源映射操作|   |   |
|---|---|---|
|操作|资源|Condition|
|[DeleteEventSourceMapping](https://docs.amazonaws.cn/lambda/latest/dg/API_DeleteEventSourceMapping.html)<br><br>[UpdateEventSourceMapping](https://docs.amazonaws.cn/lambda/latest/dg/API_UpdateEventSourceMapping.html)|事件源映射|`lambda:FunctionArn`|
|[CreateEventSourceMapping](https://docs.amazonaws.cn/lambda/latest/dg/API_CreateEventSourceMapping.html)|`*`|`lambda:FunctionArn`|
|[GetEventSourceMapping](https://docs.amazonaws.cn/lambda/latest/dg/API_GetEventSourceMapping.html)<br><br>[ListEventSourceMappings](https://docs.amazonaws.cn/lambda/latest/dg/API_ListEventSourceMappings.html)|`*`|无|

## 层操作

通过层操作，您可以限制用户可通过函数管理或使用的层。与层使用和权限相关的操作作用于层的版本，而 `PublishLayerVersion` 作用于层名称。两者都可以与通配符一起使用，以通过名称限制用户可以使用的层。

###### 注意

[GetLayerVersion](https://docs.amazonaws.cn/lambda/latest/dg/API_GetLayerVersion.html) 操作还涵盖 [GetLayerVersionByArn](https://docs.amazonaws.cn/lambda/latest/dg/API_GetLayerVersionByArn.html)。Lambda 不支持 `GetLayerVersionByArn` 作为 IAM 操作。

|层操作|   |    |
|---|---|---|
|操作|资源|Condition|
|[AddLayerVersionPermission](https://docs.amazonaws.cn/lambda/latest/dg/API_AddLayerVersionPermission.html)<br><br>[RemoveLayerVersionPermission](https://docs.amazonaws.cn/lambda/latest/dg/API_RemoveLayerVersionPermission.html)<br><br>[GetLayerVersion](https://docs.amazonaws.cn/lambda/latest/dg/API_GetLayerVersion.html)<br><br>[GetLayerVersionPolicy](https://docs.amazonaws.cn/lambda/latest/dg/API_GetLayerVersionPolicy.html)<br><br>[DeleteLayerVersion](https://docs.amazonaws.cn/lambda/latest/dg/API_DeleteLayerVersion.html)|层版本|无|
|[ListLayerVersions](https://docs.amazonaws.cn/lambda/latest/dg/API_ListLayerVersions.html)<br><br>[PublishLayerVersion](https://docs.amazonaws.cn/lambda/latest/dg/API_PublishLayerVersion.html)|层|无|
|[ListLayers](https://docs.amazonaws.cn/lambda/latest/dg/API_ListLayers.html)|`*`|无|

