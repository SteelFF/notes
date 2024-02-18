Lambda 提供了用于管理和调用函数的管理控制台和 API。它提供的运行时支持一组标准功能，以便您能够根据需要轻松地在语言和框架之间进行切换。除了函数之外，您还可以创建版本、别名、层和自定义运行时。

###### 功能

- [[#扩缩]]
- [[#并发控制]]
- [[#函数 URL]]
- [[#异步调用]]
- [[#事件源映射]]
- [[#目标]]
- [[#函数蓝图]]
- [[#测试和部署工具]]
- [[#应用程序模板]]

## 扩缩

Lambda 管理运行代码的基础设施，并可根据传入的请求自动扩展。当函数的调用速度快于函数的单个实例可以处理事件的速度时，Lambda 会通过运行其他实例来纵向扩展。当流量减少时，不活动的实例将被冻结或停止。您只需为函数初始化或处理事件的时间付费。

![您的函数根据打开的请求数自动扩展。](https://docs.amazonaws.cn/lambda/latest/dg/images/features-scaling.png)

有关更多信息，请参阅[Lambda 函数扩展](https://docs.amazonaws.cn/lambda/latest/dg/lambda-concurrency.html)。

## 并发控制

使用并发设置来确保您的生产应用程序实现高可用性和快速响应。为了防止函数使用太多并发，并为函数预留账户可用并发的一部分，请使用 _预留并发_ 。预留并发将可用并发池拆分为子集。具有预留并发的函数仅使用其专用池中的并发。

![分配给两个函数的预留并发。](https://docs.amazonaws.cn/lambda/latest/dg/images/features-concurrency-reserved.png)

要使函数能够在延迟不发生波动的情况下进行扩展，请使用 _预配置并发_ 。对于需要很长时间才能初始化的函数，或者对所有调用都需要极低延迟的函数，预配置并发使您能够预先初始化函数的实例并让它们始终运行。Lambda 与 Application Auto Scaling 集成，以支持基于使用率对预配置并发进行自动伸缩。

![使用 Application Auto Scaling 对预配置并发进行自动伸缩。](https://docs.amazonaws.cn/lambda/latest/dg/images/features-scaling-provisioned-auto.png)

有关更多信息，请参阅[配置预留并发](https://docs.amazonaws.cn/lambda/latest/dg/configuration-concurrency.html)。

## 函数 URL

Lambda 通过 _函数 URL_ 提供内置 HTTP(S) 端点支持。通过函数 URL，您可以为 Lambda 函数分配专用的 HTTP 端点。配置函数 URL 后，可以使用其通过 web 浏览器、curl、Postman 或任何 HTTP 客户端调用函数。

可以将函数 URL 添加到现有函数，也可以使用函数 URL 创建新函数。有关更多信息，请参阅 [调用 Lambda 函数 URL](https://docs.amazonaws.cn/lambda/latest/dg/urls-invocation.html)。

## 异步调用

调用函数时，您可以选择同步或异步调用。使用[同步调用](https://docs.amazonaws.cn/lambda/latest/dg/invocation-sync.html)时，您将等待函数处理该事件并返回响应。使用异步调用时，Lambda 会将事件排队等待处理并立即返回响应。

![Lambda 队列在将事件发送到函数之前异步调用事件。](https://docs.amazonaws.cn/lambda/latest/dg/images/features-async.png)

对于异步调用，如果函数返回错误或被限制，则 Lambda 处理重试。要自定义此行为，您可以在函数、版本或别名上配置错误处理设置。您还可以配置 Lambda 以将处理失败的事件发送到死信队列，或将任何调用记录发送到[[#目标]]。

有关更多信息，请参阅[异步调用](https://docs.amazonaws.cn/lambda/latest/dg/invocation-async.html)。

## 事件源映射

要处理流或队列中的项，您可以创建 _事件源映射_ 。事件源映射是 Lambda 中的一个资源，它从 Amazon Simple Queue Service (Amazon SQS) 队列、Amazon Kinesis Stream 或 Amazon DynamoDB Stream 中读取项目，并将项目批量发送到您的函数。您的函数处理的每个事件可以包含数百个或数千个项。

![事件源映射从 Kinesis 流读取记录。](https://docs.amazonaws.cn/lambda/latest/dg/images/features-eventsourcemapping.png)

事件源映射维护未处理项目的本地队列，并在函数返回错误或受到限制时处理重试。您可以配置事件源映射以自定义批处理行为和错误处理，或者将处理失败的项目记录发送到目标。

有关更多信息，请参阅 [Lambda 事件源映射](https://docs.amazonaws.cn/lambda/latest/dg/invocation-eventsourcemapping.html)。

## 目标

目标是接收函数调用记录的 Amazon 资源。对于[异步调用](https://docs.amazonaws.cn/lambda/latest/dg/gettingstarted-features.html#gettingstarted-features-async)，您可以配置 Lambda 以将调用记录发送到队列、主题、函数或事件总线。您可以为成功调用和处理失败的事件配置单独的目标。调用记录包含有关事件、函数的响应和记录发送原因的详细信息。

![Lambda 会根据结果将调用记录发送到队列或事件总线目标。](https://docs.amazonaws.cn/lambda/latest/dg/images/features-destinations.png)

对于从流读取的[事件源映射](https://docs.amazonaws.cn/lambda/latest/dg/gettingstarted-features.html#gettingstarted-features-eventsourcemapping)，您可以配置 Lambda 以将处理失败的批处理记录发送到队列或主题。事件源映射的失败记录包含有关批处理的元数据，并指向流中的项目。

有关更多信息，请参阅 [配置异步调用目标](https://docs.amazonaws.cn/lambda/latest/dg/invocation-async.html#invocation-async-destinations) 以及 [将 Amazon Lambda 与 Amazon DynamoDB 结合使用](https://docs.amazonaws.cn/lambda/latest/dg/with-ddb.html) 和 [将 Amazon Lambda 与 Amazon Kinesis 结合使用](https://docs.amazonaws.cn/lambda/latest/dg/with-kinesis.html) 的错误处理部分。

## 函数蓝图

当您在 Lambda 控制台中创建函数时，可以选择从头开始、使用蓝图或者使用[容器映像](https://docs.amazonaws.cn/lambda/latest/dg/gettingstarted-package.html#gettingstarted-package-images)。蓝图提供了示例代码，展示如何将 Lambda 与Amazon服务或流行的第三方应用程序结合使用。蓝图包括 Node.js 和 Python 运行时的示例代码和函数配置预设。

蓝图是根据 [Amazon 软件许可证](http://www.amazonaws.cn/asl/)提供的。它们仅在 Lambda 控制台中可用。

## 测试和部署工具

Lambda 支持按原样或作为[容器镜像](https://docs.amazonaws.cn/lambda/latest/dg/gettingstarted-package.html#gettingstarted-package-images)部署代码。您可以使用 Amazon 服务以及 Docker 命令行界面 (CLI) 等常用社区工具来创作、构建和部署 Lambda 函数。要设置 Docker CLI，请参阅 Docker Docs 网站上的[获取 Docker](https://docs.docker.com/get-docker)。有关将 Docker 与Amazon结合使用的简介，请参阅 _Amazon Elastic Container Registry 用户指南_中的 [Amazon CLI 与 Amazon ECR 结合使用入门](https://docs.amazonaws.cn/AmazonECR/latest/userguide/getting-started-cli.html)。

[Amazon CLI](https://docs.amazonaws.cn/cli/latest/userguide/getting-started-install.html) 和 [Amazon SAM CLI](https://docs.amazonaws.cn/serverless-application-model/latest/developerguide/serverless-sam-cli-install.html) 是用于管理 Lambda 应用程序堆栈的命令行工具。除了通过 Amazon CloudFormation API 管理应用程序堆栈的命令，Amazon CLI 还支持高级命令，可简化诸如上传部署程序包和更新模板等任务。Amazon SAM CLI 还提供多种其他功能，包括验证模板、本地测试以及与 CI/CD 系统集成。

- [安装 Amazon SAM CLI](https://docs.amazonaws.cn/serverless-application-model/latest/developerguide/serverless-sam-cli-install.html)
    
- [使用 Amazon SAM 测试和调试无服务器应用程序](https://docs.amazonaws.cn/serverless-application-model/latest/developerguide/serverless-test-and-debug.html)
    
- [将 CI/CD 系统与 Amazon SAM 结合使用以部署无服务器应用程序](https://docs.amazonaws.cn/serverless-application-model/latest/developerguide/serverless-deploying.html)
    

## 应用程序模板

您可以使用 Lambda 控制台创建具有持续交付管道的应用程序。Lambda 控制台中的应用程序模板包括一个或多个函数的代码、一个用于定义函数和支持的Amazon资源的应用程序模板，以及一个定义 Amazon CodePipeline 管道的基础设施模板。管道具有构建和部署阶段，在每次将更改推送到包含的 Git 存储库时，都会运行这些阶段。

应用程序模板是根据 [MIT 无署名归属](https://spdx.org/licenses/MIT-0.html)许可证提供给用户使用的。它们仅在 Lambda 控制台中可用。

有关更多信息，请参阅[在 Lambda 控制台中创建具有持续交付功能的应用程序](https://docs.amazonaws.cn/lambda/latest/dg/applications-tutorial.html)。