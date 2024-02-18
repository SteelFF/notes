可以使用 Amazon Identity and Access Management (IAM) 来管理对 Lambda API 和资源（如函数和层）的访问。对于您账户中使用 Lambda 的用户和应用程序，您可以创建适用于用户、组或角色的 IAM policy。

每个 Lambda 函数都有一个称为[执行角色](https://docs.amazonaws.cn/lambda/latest/dg/lambda-intro-execution-role.html)的 IAM 角色。在此角色中，您可以附加一个策略，该策略定义您的函数访问其他 Amazon 服务和资源所需的权限。您的函数至少需要访问 Amazon CloudWatch Logs 来实现日志流式传输。如果您的函数使用 Amazon SDK 调用其他服务 API，您必须在执行角色的策略中包含必要的权限。使用[事件源映射](https://docs.amazonaws.cn/lambda/latest/dg/invocation-eventsourcemapping.html)调用您的函数时，Lambda 还将使用执行角色获取从事件源读取的权限。

要授予其他账户和 Amazon 服务使用您的 Lambda 资源的权限，请使用[基于资源的策略](https://docs.amazonaws.cn/lambda/latest/dg/access-control-resource-based.html)。Lambda 资源包括函数、版本、别名和层版本等。当用户尝试访问 Lambda 资源时，Lambda 会同时考虑用户的[基于身份的策略](https://docs.amazonaws.cn/lambda/latest/dg/access-control-identity-based.html)和资源的基于资源的策略。当 Amazon Simple Storage Service (Amazon S3) 等 Amazon 服务调用您的 Lambda 函数时，Lambda 仅考虑基于资源的策略。

要管理您账户中的用户和应用程序的权限，我们建议使用 [Amazon 托管策略](https://docs.amazonaws.cn/lambda/latest/dg/access-control-identity-based.html)。您可以按原样使用这些托管策略，也可以作为编写自己的限制性更强策略的起点。策略可以通过操作影响的资源以及其他可选条件来限制用户权限。有关更多信息，请参阅[Lambda 操作的资源和条件](https://docs.amazonaws.cn/lambda/latest/dg/lambda-api-permissions-ref.html)。

如果您的 Lambda 函数包含对其他 Amazon 资源的调用，您可能还希望限制哪些函数可以访问这些资源。要做到这一点，请在 IAM 基于身份的策略或服务控制策略（SCP）中包含 `lambda:SourceFunctionArn` 条件键。有关更多信息，请参阅[使用 Lambda 执行环境凭证](https://docs.amazonaws.cn/lambda/latest/dg/lambda-intro-execution-role.html#permissions-executionrole-source-function-arn)。

有关 IAM 的更多信息，请参阅 _[IAM 用户指南](https://docs.amazonaws.cn/IAM/latest/UserGuide/introduction.html)_。

有关向 Lambda 应用程序应用安全原则的更多信息，请参阅《_Amazon Lambda 操作员指南_》中的[安全](https://docs.amazonaws.cn/lambda/latest/operatorguide/security-ops.html)。

###### 主题

- [[Lambda 执行角色]]
- [[Lambda 的基于身份的 IAM policy]]
- [[Lambda 基于属性的访问权限控制]]
- [[将基于资源的策略用于 Lambda]]
- [[Lambda 操作的资源和条件]]
- [[使用 Amazon Lambda 应用程序的权限边界]]