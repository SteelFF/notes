Lambda 函数作者使用扩展将 Lambda 与他们首选的监控、可观察性、安全性和监管工具集成。函数作者可以使用来自 Amazon、[Amazon 合作伙伴](https://docs.amazonaws.cn/lambda/latest/dg/extensions-api-partners.html)和开源项目的扩展。有关使用扩展的更多信息，请参阅 Amazon Lambda 计算博客上的 [Amazon 扩展简介](http://www.amazonaws.cn/blogs/aws/getting-started-with-using-your-favorite-operational-tools-on-aws-lambda-extensions-are-now-generally-available/)。本节介绍如何使用` Lambda 扩展 API`、`Lambda 执行环境生命周期`以及` Lambda 扩展 API 参考`。

![扩展 API 和遥测 API 连接 Lambda 与外部扩展。](https://docs.amazonaws.cn/lambda/latest/dg/images/telemetry-api-concept-diagram.png)

作为扩展作者，您可以使用 Lambda 扩展 API 深入集成到 Lambda [执行环境](https://docs.amazonaws.cn/lambda/latest/dg/lambda-runtime-environment.html)中。您的扩展可以注册函数和执行环境生命周期事件。为响应这些事件，您可以启动新进程、运行逻辑、控制并参与 Lambda 生命周期的所有阶段：初始化、调用和关闭。此外，您可以使用[运行时日志 API](https://docs.amazonaws.cn/lambda/latest/dg/runtimes-logs-api.html) 接收日志流。

扩展作为独立进程在执行环境中运行，并可以在完全处理函数调用后继续运行。由于扩展作为进程运行，因此您可以使用不同于函数的语言来编写它们。我们建议您使用已编译的语言实现扩展。在这种情况下，扩展是一个独立的二进制文件，与支持的运行时兼容。所有 [Lambda 运行时](https://docs.amazonaws.cn/lambda/latest/dg/lambda-runtimes.html) 均支持扩展。如果使用非编译的语言，请确保在扩展中包含兼容的运行时。

Lambda 还支持_内部扩展_。内部扩展作为运行时进程的独立线程运行。运行时启动并停止内部扩展。与 Lambda 环境集成的另一种方法是使用特定于语言的[环境变量和包装脚本](https://docs.amazonaws.cn/lambda/latest/dg/runtimes-modify.html)。您可以使用这些来配置运行时环境并修改运行时进程的启动行为。

您可以通过两种方式将扩展添加到函数。对于部署为 [.zip 文件存档](https://docs.amazonaws.cn/lambda/latest/dg/gettingstarted-package.html#gettingstarted-package-zip)的函数，您可以将扩展部署为[层](https://docs.amazonaws.cn/lambda/latest/dg/configuration-layers.html)。对于定义为容器映像的函数，您可以将[扩展](https://docs.amazonaws.cn/lambda/latest/dg/extensions-configuration.html#invocation-extensions-images)添加到容器映像中。

|注意| |
|---|---|
|有关示例扩展和包装脚本的信息，请参阅 Amazon Lambda Sample GitHub 存储库上的 [Amazon 扩展](https://github.com/aws-samples/aws-lambda-extensions)。| |
###### 主题

- [[#Lambda 执行环境生命周期]]
- [[#扩展 API 参考]]

## Lambda 执行环境生命周期

执行环境的生命周期包括以下阶段：

- **Init**：在此阶段，Lambda 会使用配置的资源创建或取消冻结执行环境、下载函数代码和所有层、初始化任何扩展、初始化运行时，然后运行函数的初始化代码（主处理程序外的代码）。`Init` 阶段发生在第一次调用期间，或者在函数调用之前（如果您已启用[预配置并发](https://docs.amazonaws.cn/lambda/latest/dg/provisioned-concurrency.html)）。
    
    `Init` 阶段分为三个子阶段：`Extension init`、`Runtime init` 和 `Function init`。这些子阶段可确保所有扩展和运行时在函数代码运行之前完成其设置任务。
    
- **Invoke**：在此阶段，Lambda 调用函数处理程序。在函数运行完成后，Lambda 准备处理另一次函数调用。
    
- **Shutdown**：如果 Lambda 函数在一段时间内没有收到任何调用，则触发此阶段。在 `Shutdown` 阶段，Lambda 会关闭运行时，提醒扩展让其完全停止，然后删除环境。Lambda 向每个扩展发送一个 `Shutdown` 事件，通知扩展环境即将关闭。
    

每个阶段都以一个从 Lambda 到运行时和所有已注册扩展的事件开始。运行时和每个扩展信号通过发送 `Next` API 请求来表示完成。Lambda 在每个进程结束并且没有挂起的事件时会冻结执行环境。

![](https://docs.amazonaws.cn/lambda/latest/dg/images/Overview-Full-Sequence.png)

###### 主题

- [[#Init 阶段]]
- [[#调用阶段]]
- [[#关闭阶段]]
- [[#权限和配置]]
- [[#故障处理]]
- [[#扩展故障排除]]

### Init 阶段

在 `Extension init` 阶段，每个扩展都需要在 Lambda 注册才能接收事件。Lambda 会使用扩展的完整文件名来验证扩展是否已完成引导启动序列。因此，每个 `Register` API 调用都必须包含 `Lambda-Extension-Name` 标头以及扩展的完整文件名。

您最多可以为一个函数注册 10 个扩展。此限制通过 `Register` API 调用强制执行。

在每个扩展注册后，Lambda 会启动 `Runtime init` 阶段。运行时进程调用 `functionInit`，启动 `Function init` 阶段。

`Init` 阶段在运行时之后结束，每个注册的扩展通过发送 `Next` API 请求指示完成。

|注意| |
|---|---|
|扩展可以在 `Init` 阶段的任何时间点完成其初始化。| |

![](https://docs.amazonaws.cn/lambda/latest/dg/images/Init-Phase.png)

### 调用阶段

当调用 Lambda 函数来响应 `Next` API 请求时，Lambda 会向运行时和为 `Invoke` 事件注册的每个扩展发送一个 `Invoke` 事件。

在调用过程中，外部扩展与函数并行运行。在函数完成后，它们也会继续运行。这使您能够捕获诊断信息，或将日志、指标和跟踪发送到您选择的位置。

从运行时接收函数响应后，即使扩展仍在运行，Lambda 也会将响应返回给客户端。

`Invoke` 阶段在运行时之后结束，所有扩展都通过发送 `Next` API 请求来指示它们已完成。

![](https://docs.amazonaws.cn/lambda/latest/dg/images/Invoke-Phase.png)

**事件负载**：发送到运行时（和 Lambda 函数）的事件包含整个请求、标头（如 `RequestId`）和负载。发送到每个扩展的事件包含描述事件内容的元数据。此生命周期事件包括事件的类型、函数超时的时间 (`deadlineMs`)、`requestId`、被调用函数的 Amazon 资源名称（ARN）和跟踪标头。

希望访问函数事件主体的扩展可以使用与扩展进行通信的运行时开发工具包。在调用函数时，函数开发人员使用运行时开发工具包将负载发送到扩展。

以下是一个示例负载：
``` json
{
    "eventType": "INVOKE",
    "deadlineMs": 676051,
    "requestId": "3da1f2dc-3222-475e-9205-e2e6c6318895",
    "invokedFunctionArn": "arn:aws:lambda:us-east-1:123456789012:function:ExtensionTest",
    "tracing": {
        "type": "X-Amzn-Trace-Id",
        "value": "Root=1-5f35ae12-0c0fec141ab77a00bc047aa2;Parent=2be948a625588e32;Sampled=1"
    }
}          
```
**持续时间限制**：函数的超时设置会限制整个 `Invoke` 阶段的持续时间。例如，如果将函数超时设置为 360 秒，则该函数和所有扩展都需要在 360 秒内完成。请注意，没有独立的调用后阶段。持续时间是所有调用时间（运行时 + 扩展）的总和，直到函数和所有扩展完成运行之后才计算。

**性能影响和扩展开销**：扩展可能会影响函数性能。作为扩展作者，您可以控制扩展对性能的影响。例如，如果扩展执行计算密集型操作，那么函数的持续时间会增加，这是因为扩展和函数代码共用相同的 CPU 资源。此外，如果扩展在函数调用完成后执行大量操作，则函数持续时间会增加，因为 `Invoke` 阶段会持续，直到所有扩展指示它们都已完成。

|注意| |
|---|---|
|Lambda 以与函数的内存设置成正比的方式分配 CPU 处理能力。在较低的内存设置下，您可能会看到执行和初始化持续时间增加，这是因为函数和扩展进程正在争用相同的 CPU 资源。要缩短执行和初始化持续时间，请尝试增加内存设置。| |

为了帮助识别 `Invoke` 阶段上由扩展引起的性能影响，Lambda 会输出 `PostRuntimeExtensionsDuration` 指标。此指标衡量在运行时 `Next` API 请求与上次扩展 `Next` API 请求之间所花的累积时间。要衡量所使用的内存增加量，请使用 `MaxMemoryUsed` 指标。有关函数指标的更多信息，请参阅[使用 Lambda 函数指标](https://docs.amazonaws.cn/lambda/latest/dg/monitoring-metrics.html)。

函数开发人员可以并行运行不同版本的函数，以了解特定扩展的影响。我们建议扩展作者发布预期的资源消耗，以便函数开发人员更容易选择合适的扩展。

### 关闭阶段

若 Lambda 即将关闭运行时，它会向每个已注册的外部扩展发送一个 `Shutdown`。扩展可以使用此时间执行最终清理任务。`Shutdown` 事件是为了响应 `Next` API 请求而发送的。

**持续时间限制**：`Shutdown` 阶段的最长持续时间取决于已注册扩展的配置：

- 0 毫秒 – 无已注册扩展的函数
    
- 500 毫秒 – 带有注册的内部扩展的函数
    
- 2000 毫秒 – 具有一个或多个注册的外部扩展的函数
    

对于具有外部扩展的函数，Lambda 为运行时进程保留最多 300 毫秒（对于具有内部扩展的运行时，则为 500 毫秒），以执行正常关闭。Lambda 将 2000 毫秒限制的剩余部分分配给要关闭的外部扩展。

如果运行时或扩展没有在限制范围内响应 `Shutdown` 事件，则 Lambda 会使用 `SIGKILL` 信号结束该进程。

![](https://docs.amazonaws.cn/lambda/latest/dg/images/Shutdown-Phase.png)

**事件负载**：`Shutdown` 事件包含关闭原因和剩余时间（以毫秒为单位）。

`shutdownReason` 包括以下值：

- 降速 – 正常关机
    
- 超时 – 持续时间限制超时
    
- 失败 – 错误情况，例如 `out-of-memory` 事件

``` json
{ 
  "eventType": "SHUTDOWN", 
  "shutdownReason": "reason for shutdown", 
  "deadlineMs": "the time and date that the function times out in Unix time milliseconds" 
}        
```
### 权限和配置

在与 Lambda 函数相同的执行环境中运行扩展。扩展还与函数共享资源，例如 CPU、内存和 `/tmp` 磁盘存储。此外，扩展会使用与函数相同的 Amazon Identity and Access Management (IAM) 角色和安全上下文。

**文件系统和网络访问权限**：扩展名在与函数运行时相同的文件系统和网络名称命名空间中运行。这意味着扩展需要与关联的操作系统兼容。如果扩展需要任何其他出站网络流量规则，您必须将这些规则应用于函数配置。

|注意| |
|---|---|
|因为函数代码目录是只读的，所以扩展无法修改函数代码。| |
**环境变量**：扩展可以访问函数的[环境变量](https://docs.amazonaws.cn/lambda/latest/dg/configuration-envvars.html)，但以下特定于运行时进程的变量除外：

- `AWS_EXECUTION_ENV`
- `AWS_LAMBDA_LOG_GROUP_NAME`
- `AWS_LAMBDA_LOG_STREAM_NAME`
- `AWS_XRAY_CONTEXT_MISSING`
- `AWS_XRAY_DAEMON_ADDRESS`
- `LAMBDA_RUNTIME_DIR`
- `LAMBDA_TASK_ROOT`
- `_AWS_XRAY_DAEMON_ADDRESS`
- `_AWS_XRAY_DAEMON_PORT`
- `_HANDLER`

### 故障处理

**初始化失败**：如果扩展失败，Lambda 会重新启动执行环境以强制执行一致的行为，并利于扩展快速失败。此外，对于某些客户来说，扩展必须满足日志记录、安全性、监管和遥测收集等关键任务需求。

**调用失败** （例如内存不足、函数超时）：由于扩展与运行时共享资源，因此内存耗尽会影响到它们。当运行时失败时，所有扩展和运行时本身都会参与 `Shutdown` 阶段。此外，运行时可以作为当前调用的一部分自动重新启动，也可以通过延迟的重新初始化机制重新启动。

如果在 `Invoke` 期间出现故障（例如函数超时或运行时错误），Lambda 服务会执行重置。重置的行为类似于 `Shutdown` 事件。首先，Lambda 会关闭运行时，然后向每个注册的外部扩展发送一个 `Shutdown` 事件。该事件包括关闭的原因。如果此环境用于新调用，则扩展和运行时将作为下一次调用的一部分重新初始化。

![[Pasted image 20230606104739.png]]

有关上图的更详细说明，请参见 [在调用阶段失败](https://docs.amazonaws.cn/lambda/latest/dg/lambda-runtime-environment.html#runtimes-lifecycle-invoke-with-errors)。

**扩展日志**：Lambda 将扩展的日志输出发送到 CloudWatch Logs。Lambda 还会在 `Init` 期间为每个扩展生成一个其他的日志事件。日志事件记录成功时的名称和注册首选项（事件、配置）或失败时的失败原因。

### 扩展故障排除

- 如果 `Register` 请求失败，请确保 `Lambda-Extension-Name` API 调用中的 `Register` 标头包含扩展的完整文件名。   
- 如果 `Register` 请求对于内部扩展失败，请确保请求未注册 `Shutdown` 事件。

## 扩展 API 参考

扩展 API 版本 **2020-01-01** 的 OpenAPI 规范在此处提供：[extensions-api.zip](https://docs.amazonaws.cn/lambda/latest/dg/samples/extensions-api.zip)

您可以从 `AWS_LAMBDA_RUNTIME_API` 环境变量中检索 API 终端节点的值。要发送 `Register` 请求，请在每个 API 路径之前使用前缀 `2020-01-01/`。例如：

`http://${AWS_LAMBDA_RUNTIME_API}/2020-01-01/extension/register` 

###### API 方法

- [[#注册]]
- [[#下一页]]
- [[#Init 错误]]
- [[#退出错误]]

### 注册

在 `Extension init` 期间，所有扩展都需要在 Lambda 注册才能接收事件。Lambda 会使用扩展的完整文件名来验证扩展是否已完成引导启动序列。因此，每个 `Register` API 调用都必须包含 `Lambda-Extension-Name` 标头以及扩展的完整文件名。

内部扩展由运行时进程启动和停止，因此不允许它们注册 `Shutdown` 事件。

**路径** – `/extension/register`

**方法** – **POST**

**Request headers**（请求标头）

- `Lambda-Extension-Name` – 扩展名的完整文件名。必需：是。类型：字符串。
    
- `Lambda-Extension-Accept-Feature` – 在注册期间使用此功能以指定可选的注册功能。必需：否 类型：以逗号分隔的字符串。可使用此设置指定的功能：
    
    - `accountId` – 如果指定，扩展注册响应将包含账户 ID，该 ID 与您为其注册扩展的 Lambda 函数相关联。


###### 请求正文参数

- `events` – 要注册的事件数组。必需：否 类型：字符串数组。有效字符串：`INVOKE`、`SHUTDOWN`。

###### 响应标头

- `Lambda-Extension-Identifier` – 生成所有后续请求所需的唯一代理标识符（UUID 字符串）。

###### 响应代码

- 200 – 响应正文包含函数名称、函数版本和处理程序名称。
- 400 – 错误请求
- 403 – 禁止访问
- 500 – 容器错误。不可恢复状态。扩展应立即退出。

###### 例 示例请求正文
``` json
{
    'events': [ 'INVOKE', 'SHUTDOWN']
}             
```
###### 例 示例响应正文
``` json
{
    "functionName": "helloWorld",
    "functionVersion": "$LATEST",
    "handler": "lambda_function.lambda_handler"
}               
```
###### 例 具有可选 accountId 功能的响应正文示例
``` json
{
    "functionName": "helloWorld",
    "functionVersion": "$LATEST",
    "handler": "lambda_function.lambda_handler",
    "accountId": "123456789012"
}
```
### 下一页

扩展发送 `Next` API 请求以接收下一个事件，此事件可以是 `Invoke` 事件或 `Shutdown` 事件。响应正文包含负载，该负载是包含事件数据的 JSON 文档。

扩展发送 `Next` API 请求，以表示它已准备好接收新事件。这是一个阻止性调用。

不要对 GET 调用设置超时，因为扩展可能暂停一段时间，直到存在要返回的事件。

**路径** – `/extension/event/next`

**方法** – **GET**

###### Request headers（请求标头）

- `Lambda-Extension-Identifier` – 扩展的唯一标识符（UUID 字符串）。必需：是。类型：UUID 字符串。

###### 响应标头

- `Lambda-Extension-Event-Identifier` – 事件的唯一标识符（UUID 字符串）。

###### 响应代码

- 200 – 响应包含有关下一个事件（`EventInvoke` 或 `EventShutdown`）的信息。
- 403 – 禁止访问
- 500 – 容器错误。不可恢复状态。扩展应立即退出。

### Init 错误

扩展使用此方法向 Lambda 报告初始化错误。当扩展在注册后无法初始化时调用它。Lambda 接收到错误后，进一步的 API 调用不会成功。扩展应在收到 Lambda 的响应后退出。

**路径** – `/extension/init/error`

**方法** – **POST**

###### Request headers（请求标头）

- `Lambda-Extension-Identifier` – 扩展名的唯一标识符。必需：是。类型：UUID 字符串。
    
- `Lambda-Extension-Function-Error-Type` – 扩展遇到的错误类型。必需：是。此标头由字符串值组成。Lambda 接受任何字符串，但建议您使用 `<category.reason>` 格式。例如：
    
    - `Extension.NoSuchHandler`
    - `Extension.APIKeyNotFound`
    - `Extension.ConfigInvalid`
    - `Extension.UnknownReason`

###### 请求正文参数

- `ErrorRequest` – 有关错误的其他信息。必需：否

此字段是具有以下结构的 JSON 对象：
``` json
{
      errorMessage: string (text description of the error),
      errorType: string,
      stackTrace: array of strings
}
```
请注意，Lambda 接受任何 `errorType` 值。

以下示例显示了 Lambda 函数错误消息，其中函数无法解析调用中提供的事件数据。

###### 例 函数错误
``` json
{
      "errorMessage" : "Error parsing event data.",
      "errorType" : "InvalidEventDataException",
      "stackTrace": [ ]
}      
```
###### 响应代码

- 202 – 已接受
- 400 – 错误请求
- 403 – 禁止访问
- 500 – 容器错误。不可恢复状态。扩展应立即退出。

### 退出错误

扩展使用此方法在退出前向 Lambda 报告错误。当您遇到意外故障时调用它。Lambda 接收到错误后，进一步的 API 调用不会成功。扩展应在收到 Lambda 的响应后退出。

**路径** – `/extension/exit/error`

**方法** – **POST**

###### Request headers（请求标头）

- `Lambda-Extension-Identifier` – 扩展名的唯一标识符。必需：是。类型：UUID 字符串。
    
- `Lambda-Extension-Function-Error-Type` – 扩展遇到的错误类型。必需：是。此标头由字符串值组成。Lambda 接受任何字符串，但建议您使用 `<category.reason>` 格式。例如：
    
    - `Extension.NoSuchHandler`
    - `Extension.APIKeyNotFound`
    - `Extension.ConfigInvalid`
    - `Extension.UnknownReason`

###### 请求正文参数

- `ErrorRequest` – 有关错误的其他信息。必需：否
    

此字段是具有以下结构的 JSON 对象：
``` json
{
      errorMessage: string (text description of the error),
      errorType: string,
      stackTrace: array of strings
}
```
请注意，Lambda 接受任何 `errorType` 值。

以下示例显示了 Lambda 函数错误消息，其中函数无法解析调用中提供的事件数据。

###### 例 函数错误
``` json
{
      "errorMessage" : "Error parsing event data.",
      "errorType" : "InvalidEventDataException",
      "stackTrace": [ ]
}      
```
###### 响应代码

- 202 – 已接受
- 400 – 错误请求
- 403 – 禁止访问
- 500 – 容器错误。不可恢复状态。扩展应立即退出。
