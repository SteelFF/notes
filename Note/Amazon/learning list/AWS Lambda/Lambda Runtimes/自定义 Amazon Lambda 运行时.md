您可以采用任何编程语言实施 Amazon Lambda 运行时。运行时是一个程序，调用函数时，改程序将运行 Lambda 函数的处理程序方法。您可以采用可执行文件（名为 `bootstrap`）的形式, 将运行时包含在函数的部署程序包中。

运行时负责运行函数的设置代码、从环境变量读取处理程序名称以及从 Lambda 运行时 API 读取调用事件。运行时会将事件数据传递到函数处理程序，并将来自处理程序的响应发布回 Lambda。

您的自定义运行时在标准 Lambda [执行环境](https://docs.amazonaws.cn/lambda/latest/dg/lambda-runtimes.html)中运行。既可以是 Shell 脚本（采用包含在 Amazon Linux 中的语言），也可以是在 Amazon Linux 中编译的二进制可执行文件。

要开始使用自定义运行时，请参阅[教程 – 发布自定义运行时](https://docs.amazonaws.cn/lambda/latest/dg/runtimes-walkthrough.html)。您还可在 GitHub 的 [awslabs/aws-lambda-cpp](https://github.com/awslabs/aws-lambda-cpp) 上了解以 C++ 实现的自定义运行时。

###### 主题

- [[#使用自定义运行时]]
- [[#构建自定义运行时]]
## 使用自定义运行时

要使用自定义运行时，请将函数的运行时设置为 `provided.al2`。该运行时可包含在函数的部署程序包中，或包含在[层](https://docs.amazonaws.cn/lambda/latest/dg/configuration-layers.html)中。

###### 例 function.zip

`. ├── bootstrap ├── function.sh`

如果部署程序包中存在一个名为 `bootstrap` 的文件，Lambda 将运行该文件。如果不存在，Lambda 将在函数的层中寻找运行时。如果引导文件未找到或不是可执行文件，您的函数在调用后将返回错误。

## 构建自定义运行时

自定义运行时的入口点是一个名为 `bootstrap` 的可执行文件。引导文件可以是运行时，也可以调用创建运行时的另一个文件。以下示例使用捆绑版本的 Node.js 在名为 `runtime.js` 的单独文件中运行 JavaScript 运行时。

###### 例 引导

`#!/bin/sh 
`cd $LAMBDA_TASK_ROOT
`./node-v11.1.0-linux-x64/bin/node runtime.js`

您的运行时代码负责完成一些初始化任务。然后，它将在一个循环中处理调用事件，直到它被终止。初始化任务将对[函数的每个实例](https://docs.amazonaws.cn/lambda/latest/dg/lambda-runtime-environment.html)运行一次以准备用于处理调用的环境。

###### 初始化任务

- **检索设置** – 读取环境变量以获取有关函数和环境的详细信息。
    
    - `_HANDLER` – 处理程序的位置（来自函数的配置）。标准格式为 `` `file`.`method` ``，其中 `file` 是没有表达式的文件的名称，`method` 是在文件中定义的方法或函数的名称。
        
    - `LAMBDA_TASK_ROOT` – 包含函数代码的目录。
        
    - `AWS_LAMBDA_RUNTIME_API` – 运行时 API 的主机和端口。
        
    
    有关可用变量的完整列表，请参阅 [定义运行时环境变量](https://docs.amazonaws.cn/lambda/latest/dg/configuration-envvars.html#configuration-envvars-runtime)：
    
- **初始化函数** – 加载处理程序文件并运行它包含的任何全局或静态代码。函数应该创建静态资源（如开发工具包客户端和数据库连接）一次，然后将它们重复用于多个调用。
    
- **处理错误** – 如果出现错误，请调用[初始化错误](https://docs.amazonaws.cn/lambda/latest/dg/runtimes-api.html#runtimes-api-initerror) API 并立即退出。
    

计入收费的执行时间和超时的初始化计数。当执行触发您的函数的新实例的初始化时，您可以在日志和 [Amazon X-Ray 跟踪](https://docs.amazonaws.cn/lambda/latest/dg/services-xray.html)中看到初始化时间。

###### 例 日志

`REPORT RequestId: f8ac1208... Init Duration: 48.26 ms   Duration: 237.17 ms   Billed Duration: 300 ms   Memory Size: 128 MB   Max Memory Used: 26 MB`

其运行时，运行时将使用 [Lambda 运行时界面](https://docs.amazonaws.cn/lambda/latest/dg/runtimes-api.html)来管理传入事件和报告错误。完成初始化任务后，运行时将在一个循环中处理传入事件。在运行时代码中，按顺序执行下面的步骤。

###### 处理任务

- **获取事件** – 调用[下一个调用](https://docs.amazonaws.cn/lambda/latest/dg/runtimes-api.html#runtimes-api-next) API 来获取下一个事件。响应正文包含事件数据。响应标头包含请求 ID 和其他信息。
    
- **传播跟踪标头** – 从 API 响应中的 `Lambda-Runtime-Trace-Id` 标头获取 X-Ray 跟踪标头。使用相同的值在本地设置 `_X_AMZN_TRACE_ID` 环境变量。X-Ray SDK 使用此值在服务之间连接追踪数据。
    
- **创建上下文对象** – 使用来自 API 响应中的环境变量和标头的上下文信息创建一个对象。
    
- **调用函数处理程序** – 将事件和上下文对象传递到处理程序。
    
- **处理响应** – 调用[调用响应](https://docs.amazonaws.cn/lambda/latest/dg/runtimes-api.html#runtimes-api-response) API 以发布来自处理程序的响应。
    
- **处理错误** – 如果出现错误，请调用[调用错误](https://docs.amazonaws.cn/lambda/latest/dg/runtimes-api.html#runtimes-api-invokeerror) API。
    
- **清理** – 释放未使用的资源，将数据发送到其他服务，或在获取下一个事件之前执行其他任务。
    

您可以在函数的部署程序包中包含运行时，也可以在函数层中单独分配运行时。有关示例演练的信息，请参阅[教程 – 发布自定义运行时](https://docs.amazonaws.cn/lambda/latest/dg/runtimes-walkthrough.html)。