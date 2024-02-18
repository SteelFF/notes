Lambda 运行函数的实例来处理事件。您可以使用 Lambda API 来直接调用函数，也可以配置 Amazon 服务或资源来调用函数。

###### 概念

- [[#函数]]
- [[#触发器]]
- [[#事件]]
- [[#执行环境]]
- [[#指令集架构]]
- [[#部署程序包]]
- [[#Runtimes]]
- [[#层(Layer)]]
- [[#扩展]]
- [[#并发]]
- [[#限定词]]
- [[#目标位置]]


## 函数

_函数_ 是一种资源，您可以对其调用以在 Lambda 中运行您的代码。函数所具有的代码可以处理您传递给函数或其他 Amazon 服务发送给函数的[[#事件]]。

有关更多信息，请参阅[配置 Amazon Lambda 函数](https://docs.amazonaws.cn/lambda/latest/dg/lambda-functions.html)。

## 触发器

_触发器_ 是调用 Lambda 函数的资源或配置。触发器包括可配置为调用函数的Amazon服务以及事件源映射。事件源映射是 Lambda 中的一种资源，它从流或队列中读取项目并调用函数。有关更多信息，请参阅 [调用 Lambda 函数](https://docs.amazonaws.cn/lambda/latest/dg/lambda-invocation.html) 和 [将 Amazon Lambda 与其他服务一起使用](https://docs.amazonaws.cn/lambda/latest/dg/lambda-services.html)：

## 事件

_事件_ 是 JSON 格式的文档，其中包含要处理的 Lambda 函数的数据。运行时将事件转换为一个对象，并将该对象传递给函数代码。在调用函数时，可以确定事件的结构和内容。

###### 例 自定义事件 – 天气数据

`{   "TemperatureK": 281,   "WindKmh": -3,   "HumidityPct": 0.55,   "PressureHPa": 1020 }`

当 Amazon 服务调用您的函数时，该服务会定义事件的形状。

###### 例 服务事件 – Amazon SNS 通知

`{   "Records": [     {       "Sns": {         "Timestamp": "2019-01-02T12:45:07.000Z",         "Signature": "tcc6faL2yUC6dgZdmrwh1Y4cGa/ebXEkAi6RibDsvpi+tE/1+82j...65r==",         "MessageId": "95df01b4-ee98-5cb9-9903-4c221d41eb5e",         "Message": "Hello from SNS!",         ...`

有关 Amazon 服务中的事件的更多信息，请参阅 [将 Amazon Lambda 与其他服务一起使用](https://docs.amazonaws.cn/lambda/latest/dg/lambda-services.html)。

## 执行环境

_执行环境_ 为您的 Lambda 函数提供一个安全和隔离的运行时环境。执行环境管理运行函数所需的进程和其他资源。执行环境为函数以及与函数关联的任何[扩展](https://docs.amazonaws.cn/lambda/latest/dg/gettingstarted-concepts.html#gettingstarted-concepts-extensions)提供生命周期支持。

有关更多信息，请参阅[Lambda 执行环境](https://docs.amazonaws.cn/lambda/latest/dg/lambda-runtime-environment.html)。

## 指令集架构

_指令集架构_ 确定 Lambda 用于运行函数的计算机处理器类型。Lambda 提供了多种指令集架构以供选择：

- arm64 - 64 位 ARM 架构，适用于 Amazon Graviton2 处理器。
    
- x86_64 - 64 位 x86 架构，适用于基于 x86 的处理器。
    

有关更多信息，请参阅[Lambda 指令集架构](https://docs.amazonaws.cn/lambda/latest/dg/foundation-arch.html)。

## 部署程序包

您可以使用 _部署程序包_ 来部署 Lambda 函数代码。Lambda 支持两种类型的部署程序包：

- 包含函数代码及其依赖项的 .zip 文件存档。Lambda 为函数提供操作系统和运行时。
    
- 与 [Open Container Initiative (OCI)](https://opencontainers.org/) 规范兼容的容器映像。将函数代码和依赖项添加到映像中。还必须包含操作系统和 Lambda 运行时。
    

有关更多信息，请参阅[Lambda 部署程序包](https://docs.amazonaws.cn/lambda/latest/dg/gettingstarted-package.html)。

## Runtimes

_Runtimes_ 提供在执行环境中运行的语言特定环境。Runtimes在 Lambda 与函数之间中继调用事件、上下文信息和响应。您可以使用 Lambda 提供的Runtimes，或构建您自己的Runtimes。如果要将代码打包为 .zip 文件存档，则必须将您的函数配置为使用与编程语言匹配的Runtimes。对于容器映像，映像构建时会包括Runtimes。

有关更多信息，请参阅[Lambda Runtimes](https://docs.amazonaws.cn/lambda/latest/dg/lambda-runtimes.html)。

## 层(Layer)

Lambda _层_ 是可以包含其他代码或其他内容的 .zip 文件归档。层可以包含库、[自定义Runtimes](https://docs.amazonaws.cn/lambda/latest/dg/runtimes-custom.html)、数据或配置文件。

层提供了一种方便的方法来打包库和其他可与 Lambda 函数搭配使用的依赖项。使用层可以缩小上传的部署存档的大小，并加快代码的部署速度。层还可促进代码共享和责任分离，以便您可以更快地迭代编写业务逻辑。

每个函数最多可以包含五个层。层计入标准 Lambda [部署大小配额](https://docs.amazonaws.cn/lambda/latest/dg/gettingstarted-limits.html)。在函数中包含图层时，内容将提取到执行环境中的 `/opt` 目录中。

默认情况下，您创建的层是 Amazon 账户私有的。您可以选择与其他账户共享层，也可以选择将层设为公有。如果您的函数使用其他账户发布的层，则函数可以在删除层版本后或在撤消访问该层的权限后继续使用该层版本。但是，您无法使用已删除的层版本创建新函数或更新函数。

作为容器映像部署的函数不使用层。相反，在构建映像时，您可以将首选运行时、库和其他依赖项打包到容器映像。

有关更多信息，请参阅[创建和共享 Lambda 层](https://docs.amazonaws.cn/lambda/latest/dg/configuration-layers.html)。

## 扩展

Lambda _扩展_ 使您能够增强函数。例如，您可以使用扩展将函数与您首选的监控、可观察性、安全性和监管工具集成。您可以从 [Amazon Lambda 合作伙伴](http://www.amazonaws.cn/lambda/partners/)提供的一系列工具中进行选择，也可以[创建自己的 Lambda 扩展](https://docs.amazonaws.cn/lambda/latest/dg/runtimes-extensions-api.html)。

内部扩展在运行时进程中运行，并与运行时共享相同的生命周期。外部扩展作为一个单独的进程在执行环境中运行。外部扩展在调用函数之前初始化，与函数的运行时并行运行，并在函数调用完成后继续运行。

有关更多信息，请参阅[Lambda 扩展](https://docs.amazonaws.cn/lambda/latest/dg/lambda-extensions.html)。

## 并发

_并发性_ 是您的函数在任何给定时间所服务的请求的数目。在调用函数时，Lambda 会预配置其实例以处理事件。当函数代码完成运行时，它会处理另一个请求。如果当仍在处理请求时再次调用函数，则预配置另一个实例，从而增加该函数的并发性。

并发性受 Amazon 区域级别[配额](https://docs.amazonaws.cn/lambda/latest/dg/gettingstarted-limits.html)的约束。您还可以配置各个函数来限制其并发性，或使得它们达到特定级别的并发性。有关更多信息，请参阅[配置预留并发](https://docs.amazonaws.cn/lambda/latest/dg/configuration-concurrency.html)。

## 限定词

当您调用或查看某个函数时，可以包含 _限定符_ 来指定版本或别名。_版本_ 是具有数字限定符的函数代码和配置的不可变快照。例如，`my-function:1`。_别名_ 是版本的指针，您可以更新该版本以映射到其他版本，或者在两个版本之间拆分流量。例如，`my-function:BLUE`。可以将版本和别名一起使用，为客户端提供用于调用您的函数的稳定接口。

有关更多信息，请参阅[Lambda 函数版本](https://docs.amazonaws.cn/lambda/latest/dg/configuration-versions.html)。

## 目标位置

_目标_ 是 Lambda 可以从异步调用发送事件的一种Amazon资源。您可以为处理失败的事件配置目标。某些服务还支持为处理成功的事件配置目标。

有关更多信息，请参阅[配置异步调用目标](https://docs.amazonaws.cn/lambda/latest/dg/invocation-async.html#invocation-async-destinations)。