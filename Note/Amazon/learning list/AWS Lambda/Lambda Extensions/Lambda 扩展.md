您可以使用 Lambda 扩展来增强您的 Lambda 函数。例如，使用 Lambda 扩展将函数与您首选的监控、可观察性、安全性和监管工具集成。您可以从 [Amazon Lambda 合作伙伴](http://www.amazonaws.cn/lambda/partners/)提供的一系列工具中进行选择，也可以[创建自己的 Lambda 扩展](https://docs.amazonaws.cn/lambda/latest/dg/runtimes-extensions-api.html)。

Lambda 支持内部和外部扩展。外部扩展作为独立进程在执行环境中运行，并在完全处理函数调用后继续运行。由于扩展作为单独的进程运行，因此您可以使用不同于函数的语言来编写它们。所有 [Lambda 运行时](https://docs.amazonaws.cn/lambda/latest/dg/lambda-runtimes.html) 均支持扩展。

内部扩展作为运行时进程的一部分运行。您的函数通过使用包装脚本或进程内机制（如 `JAVA_TOOL_OPTIONS`）访问内部扩展。有关更多信息，请参阅[修改运行时环境](https://docs.amazonaws.cn/lambda/latest/dg/runtimes-modify.html)。

您可以使用 Lambda 控制台、Amazon Command Line Interface (Amazon CLI) 或基础设施即代码 (IaC) 服务和工具（如 Amazon CloudFormation、Amazon Serverless Application Model (Amazon SAM) 和 Terraform）向函数添加扩展。

您需要按扩展所占用的执行时间（以 1 毫秒为增量）付费。有关扩展的定价的更多信息，请参阅 [Amazon Lambda 定价](http://www.amazonaws.cn/lambda/pricing/) 有关合作伙伴扩展的定价信息，请参阅这些合作伙伴的网站。安装自己的扩展没有任何费用。

###### 主题

- [[#执行环境]]
- [[#对性能和资源的影响]]
- [[#权限]]
- [[#配置 Lambda 扩展]]
- [[#Amazon Lambda 扩展合作伙伴]]
- [[#Lambda 扩展 API]]
- [[#Lambda 遥测 API]]

## 执行环境

Lambda 在[执行环境](https://docs.amazonaws.cn/lambda/latest/dg/lambda-runtime-environment.html)中调用您的函数，该环境提供一个安全和隔离的运行时环境。执行环境管理运行函数所需的资源，并为函数的运行时和扩展提供生命周期支持。

执行环境的生命周期包括以下阶段：

- `**Init**`：在此阶段，Lambda 会使用配置的资源创建或取消冻结执行环境、下载函数代码和所有层、初始化任何扩展、初始化运行时，然后运行函数的初始化代码（主处理程序外的代码）。`Init` 阶段发生在第一次调用期间，或者在函数调用之前（如果您已启用[预配置并发](https://docs.amazonaws.cn/lambda/latest/dg/provisioned-concurrency.html)）。
    
    `Init` 阶段分为三个子阶段：`Extension init`、`Runtime init` 和 `Function init`。这些子阶段可确保所有扩展和运行时在函数代码运行之前完成其设置任务。
    
    激活 [Lambda SnapStart](https://docs.amazonaws.cn/lambda/latest/dg/snapstart.html) 后，在您发布一个函数版本时会发生 `Init` 阶段。Lambda 保存初始化的执行环境的内存和磁盘状态的快照，永久保存加密快照并对其进行缓存以实现低延迟访问。如果您具有 `beforeCheckpoint` [运行时挂钩](https://docs.amazonaws.cn/lambda/latest/dg/snapstart-runtime-hooks.html)，则该代码将在 `Init` 阶段结束时运行。
    
- `**Restore**`（仅限 SnapStart）：当您首次调用 [SnapStart](https://docs.amazonaws.cn/lambda/latest/dg/snapstart.html) 函数时，随着该函数的纵向扩展，Lambda 会从永久保存的快照中恢复新的执行环境，而不是从头开始初始化函数。如果您有 `afterRestore()` [运行时挂钩](https://docs.amazonaws.cn/lambda/latest/dg/snapstart-runtime-hooks.html)，则代码将在 `Restore` 阶段结束时运行。`afterRestore()` 运行时挂钩执行期间将产生费用。必须加载运行时（JVM），并且 `afterRestore()` 运行时挂钩必须在超时限制（10 秒）内完成。否则，您将收到 SnapStartTimeoutException。`Restore` 阶段完成后，Lambda 将调用函数处理程序（`Invoke` 阶段）。
    
- `**Invoke**`：在此阶段，Lambda 调用函数处理程序。在函数运行完成后，Lambda 准备处理另一次函数调用。
    
- `**Shutdown**`：如果 Lambda 函数在一段时间内没有收到任何调用，则触发此阶段。在 `Shutdown` 阶段，Lambda 会关闭运行时，提醒扩展让其完全停止，然后删除环境。Lambda 向每个扩展发送一个 `Shutdown` 事件，通知扩展环境即将关闭。
    

在 `Init` 阶段，Lambda 将包含扩展的层提取到执行环境中的 `/opt` 目录中。Lambda 在 `/opt/extensions/` 目录中查找扩展，将每个文件解释为启动扩展的可执行引导程序，然后并行启动所有扩展。

## 对性能和资源的影响

函数的扩展的大小将计入部署包大小限制。对于 .zip 文件存档，函数和所有扩展解压后的总大小不得超过解压后的部署程序包大小限制，即 250 MB。

扩展可能会影响函数的性能，因为它们共享函数资源，如 CPU、内存和存储。例如，如果扩展执行计算密集型操作，那么您可能会发现函数的执行时间增加。

每个扩展都必须在 Lambda 调用函数之前完成其初始化。因此，占用大量初始化时间的扩展可能会增加函数调用的延迟。

要测量扩展在函数执行后所花的额外时间，可以使用`PostRuntimeExtensionsDuration` [函数指标](https://docs.amazonaws.cn/lambda/latest/dg/monitoring-metrics.html)。要衡量所使用的内存增加量，可以使用 `MaxMemoryUsed` 指标。要了解特定扩展的影响，您可以并行运行不同版本的函数。

## 权限

扩展可以访问与函数相同的资源。由于扩展是在与函数相同的环境中执行的，因此在函数和扩展之间共享权限。

对于 .zip 文件归档，您可以创建一个 Amazon CloudFormation 模板，以简化将相同扩展配置（包括 Amazon Identity and Access Management (IAM) 权限）附加到多个函数的任务。