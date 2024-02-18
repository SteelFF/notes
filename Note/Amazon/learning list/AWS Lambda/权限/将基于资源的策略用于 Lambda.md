Lambda 支持将基于资源的权限策略用于 Lambda 函数和层。基于资源的策略允许您向其他 Amazon 账户或组织授予基于资源的使用权限。您也可以使用基于资源的策略来允许 Amazon 服务代表您调用您的函数。

对于 Lambda 函数，您可以[授予账户权限](https://docs.amazonaws.cn/lambda/latest/dg/access-control-resource-based.html#permissions-resource-xaccountinvoke)以便其可以调用或管理这些函数。您还可以使用单个基于资源的策略向 Amazon Organizations 中的整个组织授予权限。您还可以使用基于资源的策略向 [Amazon 服务](https://docs.amazonaws.cn/lambda/latest/dg/access-control-resource-based.html#permissions-resource-serviceinvoke)授予调用权限，以允许该服务响应您账户中的活动来调用函数。

###### 查看函数的基于资源的策略

1. 打开 Lamba 控制台的 [Functions](https://console.amazonaws.cn/lambda/home#/functions)（函数）页面。
    
2. 选择函数。
    
3. 选择 **Configuration (配置)**，然后选择 **Permissions (权限)**。
    
4. 向下滚动到 **Resource-based policy (基于资源的策略)**，然后选择 **View policy document (查看策略文档)**。基于资源的策略显示了在其他账户或 Amazon 服务尝试访问该函数时应用的权限。以下示例显示了一个语句，该语句允许 Amazon S3 调用为账户 `123456789012` 中名为 `my-bucket` 的存储桶调用名为 `my-function` 的函数。
    
    ###### 例 基于资源的策略
    ``` json
    {
	    "Version": "2012-10-17",
	    "Id": "default",
	    "Statement": [
	        {
	            "Sid": "lambda-allow-s3-my-function",
	            "Effect": "Allow",
	            "Principal": {
		            "Service": "s3.amazonaws.com.cn"
	            },
	            "Action": "lambda:InvokeFunction",
	            "Resource": 
	            "arn:aws:lambda:cn-north-1:123456789012:function:my-function",
	            "Condition": {
		            "StringEquals": {
					"AWS:SourceAccount": "123456789012"
		        },
	            "ArnLike": {
	                "AWS:SourceArn": "arn:aws:s3:::my-bucket"
	            }
            }
	    ]
	}
	```
	对于 Lambda 层，只能对特定层版本（而不是整个层）使用基于资源的策略。除了向单个或多个账户授权的策略，对于层，您还可以向组织中的所有账户授权。

|注意|  |
|--------|--------|
|您只能为 [AddPermission](https://docs.amazonaws.cn/lambda/latest/dg/API_AddPermission.html) 和 [AddLayerVersionPermission](https://docs.amazonaws.cn/lambda/latest/dg/API_AddLayerVersionPermission.html) API 操作范围内的 Lambda 资源更新基于资源的策略。目前，您无法为您在 JSON 中的 Lambda 资源编写策略，也不能使用没有映射到这些操作的参数的条件。|  |

基于资源的策略应用于单个函数、版本、别名或层版本。它们向一个或多个服务和账户授权。对于您希望其能够访问多个资源或使用基于资源的策略不支持的 API 操作的信任账户，您可以使用[跨账户角色](https://docs.amazonaws.cn/lambda/latest/dg/access-control-identity-based.html)。

###### 主题

- [[#向 Amazon 服务授予函数访问权]]
- [[#向组织授予函数访问权限]]
- [[#向其他账户授予函数访问权]]
- [[#向其他账户授予层访问权]]
- [[#清除基于资源的策略]]

## 向 Amazon 服务授予函数访问权

当您[使用 Amazon 服务调用您的函数](https://docs.amazonaws.cn/lambda/latest/dg/lambda-services.html)时，可以用基于资源的策略语句授权。您可以将语句应用于要调用或管理的整个函数，也可以仅将语句应用到单个版本或别名。

|注意|  |
|------|------|
|当您通过 Lambda 控制台向函数添加触发器时，该控制台会更新函数的基于资源的策略以允许服务调用它。要向 Lambda 控制台中不可用的其他账户或服务授予权限，可以使用 Amazon CLI。|  |

使用 `add-permission` 命令添加一条语句。最简单的基于资源的策略语句是允许一个服务调用某个函数。以下命令授予 Amazon SNS 调用名为 `my-function` 的函数的权限。
``` shell
aws lambda add-permission --function-name my-function --action lambda:InvokeFunction --statement-id sns \
--principal sns.amazonaws.com.cn --output text
```
您应看到以下输出：
``` json
{"Sid":"sns","Effect":"Allow","Principal":{"Service":"sns.amazonaws.com.cn"},"Action":"lambda:InvokeFunction","Resource":"arn:aws:lambda:cn-north-1:123456789012:function:my-function"}
```
这允许 Amazon SNS 为该函数调用 `lambda:Invoke` API，但不会限制触发该调用的 Amazon SNS 主题。为确保您的函数只被特定资源调用，请使用 `source-arn` 选项指定资源的 Amazon Resource Name (ARN)。以下命令只允许 Amazon SNS 调用名为 `my-topic` 的主题的订阅函数。
``` shell
aws lambda add-permission --function-name my-function --action lambda:InvokeFunction --statement-id sns-my-topic \
--principal sns.amazonaws.com.cn --source-arn arn:aws:sns:cn-north-1:123456789012:my-topic
```
有些服务可以调用其他账户中的函数。如果您指定的一个源 ARN 中包含您的账户 ID，这不是问题。但对于 Amazon S3 来说，源是其 ARN 中不包含账户 ID 的存储桶。有可能是您删除了该存储桶，而另一个账户用同样的名称创建了这样一个存储桶。使用 `source-account` 选项以及您的账户 ID 以确保只有您账户中的资源可以调用该函数。
``` shell
aws lambda add-permission --function-name my-function --action lambda:InvokeFunction --statement-id s3-account \
--principal s3.amazonaws.com.cn --source-arn arn:aws:s3:::my-bucket-123456 --source-account 123456789012
```
## 向组织授予函数访问权限

要向 Amazon Organizations 中的组织授予权限，请将组织 ID 指定为 `principal-org-id`。以下 [AddPermission](https://docs.amazonaws.cn/lambda/latest/dg/API_AddPermission.html) Amazon CLI 命令将向组织 `o-a1b2c3d4e5f` 中的所有用户授予调用访问权限。
``` shell
aws lambda add-permission --function-name example \
--statement-id PrincipalOrgIDExample --action lambda:InvokeFunction \
--principal * --principal-org-id o-a1b2c3d4e5f 
```

|注意|  |
|----|---|
|在此命令中，`Principal` 为 `*`。这意味着组织 `o-a1b2c3d4e5f` 中的所有用户都获得了函数调用权限。如果您将某个 Amazon 账户或角色指定为 `Principal`，则只有该主体会获得函数调用权限，但前提是他们也是 `o-a1b2c3d4e5f` 组织的成员。|  |
此命令会创建一个基于资源的策略，如下所示：
``` json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PrincipalOrgIDExample",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "lambda:InvokeFunction",
            "Resource": "arn:aws:lambda:us-west-2:123456789012:function:example",
            "Condition": {
                "StringEquals": {
                    "aws:PrincipalOrgID": "o-a1b2c3d4e5f"
                }
            }
        }
    ]
}
```

有关更多信息，请参阅《Amazon Identity and Access Management 用户指南》中的 [aws:PrincipalOrgID](https://docs.amazonaws.cn/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-principalorgid)。

## 向其他账户授予函数访问权

要向另一个 Amazon 账户授权，请将账户 ID 指定为 `principal`。以下示例向账户 `111122223333` 授权以 `my-function` 别名调用 `prod`。
``` shell
aws lambda add-permission --function-name my-function:prod --statement-id xaccount --action lambda:InvokeFunction \
--principal 111122223333 --output text
```
您应看到以下输出：
``` json
{"Sid":"xaccount","Effect":"Allow","Principal":{"AWS":"arn:aws:iam::111122223333:root"},"Action":"lambda:InvokeFunction","Resource":"arn:aws:lambda:us-east-2:123456789012:function:my-function"}
```
基于资源的策略向另一个账户授予访问此函数的权限，但不允许该账户中的用户超出其权限。另一个账户中的用户必须具有相应的[用户权限](https://docs.amazonaws.cn/lambda/latest/dg/access-control-identity-based.html)才能使用 Lambda API。

要限制对另一个账户中用户或角色的访问，请将身份的完整 ARN 指定为主体。例如，`arn:aws:iam::123456789012:user/developer`。

[别名](https://docs.amazonaws.cn/lambda/latest/dg/configuration-aliases.html)限制了其他账户可以调用哪个版本。它要求另一个账户在函数 ARN 中包括该别名。
``` shell
aws lambda invoke --function-name arn:aws:lambda:us-west-2:123456789012:function:my-function:prod out
```
您应看到以下输出：
``` json
{
    "StatusCode": 200,
    "ExecutedVersion": "1"
}
```
然后，函数所有者可以更新别名以指向新版本，而无需调用者更改他们调用函数的方式。这可以确保另一个账户不需要更改其代码即可使用新版本，并且它只具有调用与别名关联的函数版本的权限。

您可以授予对[作用于现有函数](https://docs.amazonaws.cn/lambda/latest/dg/lambda-api-permissions-ref.html#permissions-resources-function)的大多数 API 操作的跨账户访问权。例如，您可以授予 `lambda:ListAliases` 权限，以允许一个账户获得别名列表，或授予 `lambda:GetFunction` 权限，以让它们下载您的函数代码。分别添加每个权限，或使用 `lambda:*` 授予有关指定函数的所有操作的权限。

###### 跨账户 API

Lambda 不支持通过基于资源的策略对其所有 API 进行跨账户操作。支持以下 API：

- [Invoke](https://docs.amazonaws.cn/lambda/latest/dg/API_Invoke.html)
- [GetFunction](https://docs.amazonaws.cn/lambda/latest/dg/API_GetFunction.html)
- [GetFunctionConfiguration](https://docs.amazonaws.cn/lambda/latest/dg/API_GetFunctionConfiguration.html)
- [UpdateFunctionCode](https://docs.amazonaws.cn/lambda/latest/dg/API_UpdateFunctionCode.html)
- [DeleteFunction](https://docs.amazonaws.cn/lambda/latest/dg/API_DeleteFunction.html)
- [PublishVersion](https://docs.amazonaws.cn/lambda/latest/dg/API_PublishVersion.html)
- [ListVersionsByFunction](https://docs.amazonaws.cn/lambda/latest/dg/API_ListVersionsByFunction.html)
- [CreateAlias](https://docs.amazonaws.cn/lambda/latest/dg/API_CreateAlias.html)
- [GetAlias](https://docs.amazonaws.cn/lambda/latest/dg/API_GetAlias.html)
- [ListAliases](https://docs.amazonaws.cn/lambda/latest/dg/API_ListAliases.html)
- [UpdateAlias](https://docs.amazonaws.cn/lambda/latest/dg/API_UpdateAlias.html)
- [DeleteAlias](https://docs.amazonaws.cn/lambda/latest/dg/API_DeleteAlias.html)
- [GetPolicy](https://docs.amazonaws.cn/lambda/latest/dg/API_GetPolicy.html)
- [PutFunctionConcurrency](https://docs.amazonaws.cn/lambda/latest/dg/API_PutFunctionConcurrency.html)
- [DeleteFunctionConcurrency](https://docs.amazonaws.cn/lambda/latest/dg/API_DeleteFunctionConcurrency.html)
- [ListTags](https://docs.amazonaws.cn/lambda/latest/dg/API_ListTags.html)
- [TagResource](https://docs.amazonaws.cn/lambda/latest/dg/API_TagResource.html)
- [UntagResource](https://docs.amazonaws.cn/lambda/latest/dg/API_UntagResource.html)


要授予其他账户对多个函数的权限，或不对某个函数执行的操作的权限，请使用 [IAM 角色](https://docs.amazonaws.cn/lambda/latest/dg/access-control-identity-based.html)。

## 向其他账户授予层访问权

要向另一个账户授予层使用权限，请使用 **add-layer-version-permission** 命令向层版本的权限策略添加语句。在每个语句中，您可以向单个账户、所有账户或组织授予权限。
``` shell
aws lambda add-layer-version-permission --layer-name xray-sdk-nodejs --statement-id xaccount \
--action lambda:GetLayerVersion  --principal 111122223333 --version-number 1 --output text
```

您应该可以看到类似于如下所示的输出内容：
``` json
e210ffdc-e901-43b0-824b-5fcd0dd26d16    {"Sid":"xaccount","Effect":"Allow","Principal":{"AWS":"arn:aws:iam::111122223333:root"},"Action":"lambda:GetLayerVersion","Resource":"arn:aws:lambda:us-east-2:123456789012:layer:xray-sdk-nodejs:1"}
```
权限仅适用于单个层版本。每次创建新的层版本时都需重复此过程。

要向一个组织中的所有账户授予权限，请使用 `organization-id` 选项。以下示例向组织中的所有账户授予层的版本 3 的使用权限。
``` shell
aws lambda add-layer-version-permission --layer-name my-layer \
--statement-id engineering-org --version-number 3 --principal '*' \
--action lambda:GetLayerVersion --organization-id o-t194hfs8cz --output text
```

您应看到以下输出：
``` json
b0cd9796-d4eb-4564-939f-de7fe0b42236    {"Sid":"engineering-org","Effect":"Allow","Principal":"*","Action":"lambda:GetLayerVersion","Resource":"arn:aws:lambda:us-east-2:123456789012:layer:my-layer:3","Condition":{"StringEquals":{"aws:PrincipalOrgID":"o-t194hfs8cz"}}}"
```
要向所有 Amazon 账户授予权限，请将 `*` 用于主体并忽略组织 ID。对于多个账户或组织，您需要添加多个语句。

## 清除基于资源的策略

要查看函数的基于资源的策略，请使用 `get-policy` 命令。
``` shell
aws lambda get-policy --function-name my-function --output text
```
您应看到以下输出：
``` json
{"Version":"2012-10-17","Id":"default","Statement":[{"Sid":"sns","Effect":"Allow","Principal":{"Service":"s3.amazonaws.com.cn"},"Action":"lambda:InvokeFunction","Resource":"arn:aws:lambda:cn-north-1:123456789012:function:my-function","Condition":{"ArnLike":{"AWS:SourceArn":"arn:aws:sns:us-east-2:123456789012:lambda*"}}}]}      7c681fc9-b791-4e91-acdf-eb847fdaa0f0
```

对于版本和别名，请在函数名后面附加版本号或别名。
``` shell
aws lambda get-policy --function-name my-function:PROD
```

要从函数中删除权限，请使用 `remove-permission`。
``` shell
aws lambda remove-permission --function-name example --statement-id sns
```

使用 `get-layer-version-policy` 命令可查看层上的权限。
``` shell
aws lambda get-layer-version-policy --layer-name my-layer --version-number 3 --output text
```

您应看到以下输出：
``` json
b0cd9796-d4eb-4564-939f-de7fe0b42236    {"Sid":"engineering-org","Effect":"Allow","Principal":"*","Action":"lambda:GetLayerVersion","Resource":"arn:aws:lambda:us-west-2:123456789012:layer:my-layer:3","Condition":{"StringEquals":{"aws:PrincipalOrgID":"o-t194hfs8cz"}}}"
```

使用 `remove-layer-version-permission` 可从策略中删除语句。
``` shell
aws lambda remove-layer-version-permission --layer-name my-layer --version-number 3 --statement-id engineering-org
```