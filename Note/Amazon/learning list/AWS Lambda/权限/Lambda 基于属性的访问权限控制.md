借助[基于属性的访问权限控制 (ABAC)](https://docs.amazonaws.cn/IAM/latest/UserGuide/introduction_attribute-based-access-control.html)，您可以使用标签来控制对 Lambda 函数的访问。您可以将标签附加到 Lambda 函数，在某些 API 请求中传递它们，或将它们附加到发出请求的 Amazon Identity and Access Management (IAM) 主体。有关 Amazon 如何授予基于属性的访问权限的更多信息，请参阅《_IAM 用户指南_》中的[使用标签控制对 Amazon 资源的访问](https://docs.amazonaws.cn/IAM/latest/UserGuide/access_tags.html)。

您可以使用 ABAC [授予最低权限](https://docs.amazonaws.cn/IAM/latest/UserGuide/best-practices.html#grant-least-privilege)，而无需在 IAM 策略中指定 Amazon 资源名称 (ARN) 或 ARN 模式。相反，您可以在 IAM 策略的[条件元素](https://docs.amazonaws.cn/IAM/latest/UserGuide/reference_policies_elements_condition.html)中指定一个标签来控制访问。使用 ABAC 可以更轻松地进行扩展，因为您无需在创建新函数时更新 IAM 策略。相反，将标签添加到新函数以控制访问权限。

在 Lambda 中，标签在函数级别工作。层、代码签名配置或事件源映射不支持标签。当您为函数添加标签时，这些标签将应用于与该函数关联的所有版本和别名。有关如何为函数添加标签的信息，请参阅 [在 Lambda 函数上使用标签](https://docs.amazonaws.cn/lambda/latest/dg/configuration-tags.html)。

您可以使用以下条件键来控制函数操作：

- [aws:ResourceTag/tag-key](https://docs.amazonaws.cn/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-resourcetag)：根据附加到 Lambda 函数的标签控制访问权限。
    
- [aws:RequestTag/tag-key](https://docs.amazonaws.cn/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-requesttag)：要求请求中存在标签，例如在创建新函数时。
    
- [aws:PrincipalTag/tag-key](https://docs.amazonaws.cn/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-principaltag)：根据附加到其 IAM [用户](https://docs.amazonaws.cn/IAM/latest/UserGuide/id_tags_users.html)或[角色](https://docs.amazonaws.cn/IAM/latest/UserGuide/id_tags_roles.html)的标签，控制允许 IAM 主体（发出请求的人）执行的操作。
    
- [aws:TagKeys](https://docs.amazonaws.cn/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-tagkeys)：控制是否可以在请求中使用特定的标签键。
    

有关支持 ABAC 的 Lambda 操作的完整列表，请参阅 [函数操作](https://docs.amazonaws.cn/lambda/latest/dg/lambda-api-permissions-ref.html#permissions-resources-function)，然后检查表中的 **Condition**（条件）列。

以下步骤演示了一种使用 ABAC 设置权限的方法。在此示例方案中，您将创建四个 IAM 权限策略。然后，您会将这些策略附加到新的 IAM 角色。最后，您将创建一个 IAM 用户并授予该用户担任新角色的权限。

## 先决条件

确保您具有 [Lambda 执行角色](https://docs.amazonaws.cn/lambda/latest/dg/lambda-intro-execution-role.html)。当您授予 IAM 权限和创建 Lambda 函数时，您将使用此角色。

## 步骤 1：要求新函数具有标签

当将 ABAC 与 Lambda 配合使用时，最佳做法是要求所有函数都具有标签。这有助于确保您的 ABAC 权限策略按预期工作。

[创建类似于以下示例的 IAM 策略](https://docs.amazonaws.cn/IAM/latest/UserGuide/access_policies_create-console.html#access_policies_create-json-editor)。此策略使用 [aws:RequestTag/tag-key](https://docs.amazonaws.cn/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-requesttag) 和 [aws:TagKeys](https://docs.amazonaws.cn/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-tagkeys) 条件键来要求新函数和创建函数的 IAM 主体都具有 `project` 标签。`ForAllValues` 修饰符确保 `project` 是唯一允许的标签。如果您未包括 `ForAllValues` 修饰符，则用户可以将其他标签添加到函数中，只要它们也传递 `project`。

###### 例 – 要求新函数具有标签
``` json
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Allow",
    "Action": [
      "lambda:CreateFunction",
      "lambda:TagResource"
    ],
    "Resource": "arn:aws:lambda:*:*:function:*",
    "Condition": {
      "StringEquals": {
        "aws:RequestTag/project": "${aws:PrincipalTag/project}"
      },
      "ForAllValues:StringEquals": {
        "aws:TagKeys": "project"
      }
    }
  }
}
```

## 步骤 2：允许基于附加到 Lambda 函数和 IAM 主体的标签执行操作

使用 [aws:ResourceTag/tag-key](https://docs.amazonaws.cn/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-resourcetag) 条件键创建第二个 IAM 策略，以要求主体的标签与附加到函数的标签匹配。以下示例策略允许具有 `project` 标签的委托人调用具有 `project` 标签的函数。如果函数具有任何其他标签，则该操作将被拒绝。

###### 例 – 要求函数和 IAM 主体的标签匹配
``` json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "lambda:InvokeFunction",
        "lambda:GetFunction"
      ],
      "Resource": "arn:aws:lambda:*:*:function:*",
      "Condition": {
        "StringEquals": {
          "aws:ResourceTag/project": "${aws:PrincipalTag/project}"
        }
      }
    }
  ]
}
```

## 步骤 3：授予列表权限

创建允许主体列出 Lambda 函数和 IAM 角色的策略。这样，主体就可以在控制台上以及调用 API 操作时查看所有 Lambda 函数和 IAM 角色。

###### 例 – 授予 Lambda 和 IAM 列表权限
``` json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllResourcesLambdaNoTags",
      "Effect": "Allow",
      "Action": [
        "lambda:GetAccountSettings",
        "lambda:ListFunctions",
        "iam:ListRoles"
      ],
      "Resource": "*"
    }
  ]
}
```

## 步骤 4：授予 IAM 权限

创建允许 **iam:PassRole** 的策略。当您将执行角色分配给函数时，需要此权限。在以下示例策略中，将示例 ARN 替换为 Lambda 执行角色的 ARN。

| 注意 |  |
| ------- | ------- |
|不要在策略中将 `ResourceTag` 条件键与 `iam:PassRole` 操作一起使用。您无法在 IAM 角色上使用标签以控制可以传递该角色的用户的访问权限。有关将角色传递给服务所需权限的更多信息，请参阅[授予用户将角色传递给 Amazon 服务的权限](https://docs.amazonaws.cn/IAM/latest/UserGuide/id_roles_use_passrole.html)。|  |

###### 例 – 授予传递执行角色的权限
``` json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "VisualEditor0",
      "Effect": "Allow",
      "Action": [
        "iam:PassRole"
      ],
      "Resource": "arn:aws:iam::111122223333:role/lambda-ex"
    }
  ]
}
```

## 步骤 5：创建 IAM 角色

[使用角色委托权限](https://docs.amazonaws.cn/IAM/latest/UserGuide/best-practices.html#delegate-using-roles)是最佳实践。[创建名为 `abac-project-role` 的 IAM 角色](https://docs.amazonaws.cn/IAM/latest/UserGuide/id_roles_create_for-user.html#roles-creatingrole-user-console)：

- 在**步骤 1：选择可信实体**中：选择 **Amazon account**（Amazon 账户），然后选择 **This account**（此账户）。
    
- 在**步骤 2：添加权限**上：附加您在前面的步骤中创建的四个 IAM 策略。
    
- 在**步骤 3：命名、查看和创建**上：选择 **Add tag**（添加标签）。对于 **Key**（键），输入 `project`。不要输入 **Value**（值）。
    

## 步骤 6：创建 IAM 用户

[创建名为 `abac-test-user` 的 IAM 用户](https://docs.amazonaws.cn/IAM/latest/UserGuide/id_users_create.html#id_users_create_console)。在 **Set permissions**（设置权限）部分中，选择 **Attach existing policies directly**（直接附加现有策略），然后选择 **Create policy**（创建策略）。输入以下策略定义。将 `111122223333` 替换为您的 [Amazon 账户 ID](https://docs.amazonaws.cn/general/latest/gr/acct-identifiers.html#FindingYourAccountIdentifiers)。此策略允许 `abac-test-user` 担任 `abac-project-role`。

###### 例 – 允许 IAM 用户担任 ABAC 角色
``` json
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Allow",
    "Action": "sts:AssumeRole",
    "Resource": "arn:aws:iam::111122223333:role/abac-project-role"
  }
}
```

## 步骤 7：测试权限

1. 以 `abac-test-user` 身份登录到 Amazon 控制台。有关更多信息，请参阅[作为 IAM 用户登录](https://docs.amazonaws.cn/IAM/latest/UserGuide/console.html#user-sign-in-page)。
    
2. 切换到 `abac-project-role` 角色。有关更多信息，请参阅[切换到角色（控制台）](https://docs.amazonaws.cn/IAM/latest/UserGuide/id_roles_use_switch-role-console.html)。
    
3. [创建 Lambda 函数](https://docs.amazonaws.cn/lambda/latest/dg/configuration-tags.html#using-tags-with-the-console)：
    
    - 在 **Permissions**（权限）下，选择 **Change default execution role**（更改默认执行角色），然后对于 **Execution role**（执行角色），选择 **Use an existing role**（使用现有角色）。选择您在 [步骤 4：授予 IAM 权限](https://docs.amazonaws.cn/lambda/latest/dg/attribute-based-access-control.html#abac-iam-permissions) 中使用的相同执行角色。
        
    - 在 **Advanced settings**（高级设置）下，选择 **Enable tags**（启用标签），然后选择 **Add new tag**（添加新标签）。对于 **Key**（键），输入 `project`。不要输入 **Value**（值）。
        
    
4. [测试函数](https://docs.amazonaws.cn/lambda/latest/dg/testing-functions.html)。
    
5. 创建第二个 Lambda 函数并添加其他标签，例如 `environment`。此操作应会失败，因为您在 [步骤 1：要求新函数具有标签](https://docs.amazonaws.cn/lambda/latest/dg/attribute-based-access-control.html#require-tag-on-create) 中创建的 ABAC 策略仅允许主体创建具有 `project` 标签的函数。
    
6. 创建第三个没有标签的函数。此操作应会失败，因为您在 [步骤 1：要求新函数具有标签](https://docs.amazonaws.cn/lambda/latest/dg/attribute-based-access-control.html#require-tag-on-create) 中创建的 ABAC 策略不允许主体创建没有标签的函数。
    

此授权策略允许您控制访问权限，而无需为每个新用户创建新的策略。要向新用户授予访问权限，只需授予他们担任与其所分配项目相对应的角色的权限。