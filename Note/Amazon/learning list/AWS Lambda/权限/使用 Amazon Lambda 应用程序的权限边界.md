当您在 Amazon Lambda 控制台中[创建应用程序](https://docs.amazonaws.cn/lambda/latest/dg/applications-tutorial.html)时，Lambda 会将_权限边界_应用到应用程序的 IAM 角色。权限边界限定了应用程序模板为其每个函数创建的[执行角色](https://docs.amazonaws.cn/lambda/latest/dg/lambda-intro-execution-role.html)范围，以及您添加到模板的任何角色。权限边界可以防止对应用程序 Git 存储库具有写入访问权限的用户将应用程序的权限提升到自身资源的范围之外。

Lambda 控制台中的应用程序模板包含一个全局属性，该属性将权限边界应用于通过这些模板创建的所有函数。
``` yaml
Globals:
  Function:
    PermissionsBoundary: !Sub 'arn:${Amazon::Partition}:iam::${Amazon::AccountId}:policy/${AppId}-${Amazon::Region}-PermissionsBoundary'
```
边界限定了函数角色的权限。您可以在模板中向函数的执行角色添加权限，但该权限只有在权限边界允许的范围内有效。对于 Amazon CloudFormation 在部署应用程序时代入的角色，强制使用权限边界。该角色只有创建和传递已附加应用程序权限边界的角色的权限。

默认情况下，应用程序权限边界允许函数对应用程序中的资源执行操作。例如，如果应用程序包含 Amazon DynamoDB 表，则对于可限制为在具有资源级权限的特定表上操作的任何 API 操作，边界允许对这些 API 操作进行访问。只有在边界中专门允许的情况下，才能使用不支持资源级权限的操作。这些包括用于日志记录和跟踪的 Amazon CloudWatch logs 和 Amazon X-Ray API 操作。

###### 例 权限边界
``` json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "*"
            ],
            "Resource": [
                "arn:aws:lambda:us-east-2:123456789012:function:my-app-getAllItemsFunction-*",
                "arn:aws:lambda:us-east-2:123456789012:function:my-app-getByIdFunction-*",
                "arn:aws:lambda:us-east-2:123456789012:function:my-app-putItemFunction-*",
                "arn:aws:dynamodb:us-east-1:123456789012:table/my-app-SampleTable-*"
            ],
            "Effect": "Allow",
            "Sid": "StackResources"
        },
        {
            "Action": [
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:DescribeLogGroups",
                "logs:PutLogEvents",
                "xray:Put*"
            ],
            "Resource": "*",
            "Effect": "Allow",
            "Sid": "StaticPermissions"
        },
        ...
    ]
}
```
要访问其他资源或 API 操作，您或管理员必须扩展权限边界，在其中包括这些资源。您可能还需要更新应用程序的执行角色或部署角色，允许使用其他操作。

- **权限边界** – 在您向应用程序添加资源时，或执行角色需要访问更多操作时，扩展应用程序的权限边界。在 IAM 中，向边界添加资源，以便允许使用支持该资源类型的资源级权限的 API 操作。对于不支持资源级权限的操作，将这些操作添加到不限定为任何资源范围的语句中。
    
- **执行角色** – 在需要使用其他操作时，扩展函数的执行角色。在应用程序模板中，将策略添加到执行角色。向函数授予边界与执行角色的权限交集。
    
- **部署角色** – 在应用程序需要其他权限来创建或配置资源时，扩展应用程序的部署角色。在 IAM 中，将策略添加到应用程序的部署角色。部署角色需要与您在 Amazon CloudFormation 中部署或更新应用程序时所需的相同用户权限。
    

有关演练如何向应用程序添加资源并扩展其权限的教程，请参阅[在 Lambda 控制台中创建具有持续交付功能的应用程序](https://docs.amazonaws.cn/lambda/latest/dg/applications-tutorial.html)。

有关更多信息，请参阅 IAM 用户指南 中的 [IAM 实体的权限边界](https://docs.amazonaws.cn/IAM/latest/UserGuide/access_policies_boundaries.html)。