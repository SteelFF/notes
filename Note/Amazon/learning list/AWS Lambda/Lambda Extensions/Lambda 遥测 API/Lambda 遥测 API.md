使用 Lambda 遥测 API，您的扩展可以直接从 Lambda 接收遥测数据。在函数初始化和调用期间，Lambda 会自动捕获遥测数据，例如日志、平台指标和平台跟踪。借助遥测 API，扩展可以近乎实时地直接从 Lambda 获取遥测数据。

您可以直接从 Lambda 执行环境中为 Lambda 扩展订阅遥测流。订阅后，Lambda 会自动将所有遥测数据流式传输到您的扩展。然后，您可以处理、筛选这些数据，并将其传输到首选目标，例如 Amazon Simple Storage Service（Amazon S3）存储桶或第三方可观测性工具提供商。

下图显示了扩展 API 和遥测 API 如何从执行环境中将扩展连接到 Lambda。运行时 API 还将您的运行时和函数连接到 Lambda。

![将 Lambda 连接到执行环境中的进程的扩展、遥测和运行时 API。](https://docs.amazonaws.cn/lambda/latest/dg/images/telemetry-api-concept-diagram.png)

###### 重要

Lambda 遥测 API 取代 Lambda Logs API。**尽管 Logs API 仍然功能完备，但我们建议您今后仅使用遥测 API。**您可以使用遥测 API 或 Logs API 为扩展订阅遥测流。使用其中一个 API 进行订阅后，任何使用其他 API 进行订阅的尝试都会返回错误。

扩展可以使用遥测 API 订阅三种不同的遥测流：

- **平台遥测** – 日志、指标和跟踪，描述与执行环境运行时生命周期、扩展生命周期和函数调用相关的事件和错误。
    
- **函数日志** – Lambda 函数代码生成的自定义日志。
    
- **扩展日志** – Lambda 扩展代码生成的自定义日志。
    

###### 注意

即使扩展订阅了遥测流，Lambda 也会将日志和指标发送到 CloudWatch，并将跟踪发送到 X-Ray（如果您已激活跟踪）。

###### 小节目录

- [[#使用遥测 API 创建扩展]]
- [[#注册扩展]]
- [[#创建遥测侦听器]]
- [[#指定目标协议]]
- [[#配置内存使用量和缓冲]]
- [[#向遥测 API 发送订阅请求]]
- [[#入站遥测 API 消息]]
- [[#Lambda 遥测 API 参考]]
- [[#Lambda 遥测 API Event 架构参考]]
- [[#将 Lambda 遥测 API Event 对象转换为 OpenTelemetry 跨度]]
- [[#Lambda Logs API]]

## 使用遥测 API 创建扩展

Lambda 扩展作为独立进程在执行环境中运行，并可以在函数调用完成后继续运行。由于扩展作为单独的进程，因此您可以使用与函数代码不同的语言来编写这些扩展。我们建议使已编译的语言（如 Golang 或 Rust）来实施扩展。通过这种方式，扩展为独立的二进制文件，与任何支持的运行时兼容。

下图说明了创建使用遥测 API 接收和处理遥测数据的扩展的四步过程。

![注册扩展，创建侦听器，订阅流，然后获取遥测数据。](https://docs.amazonaws.cn/lambda/latest/dg/images/telemetry-api-creation-steps.png)

以下是每个步骤的详细内容：

1. 使用 [Lambda 扩展 API](https://docs.amazonaws.cn/lambda/latest/dg/runtimes-extensions-api.html) 注册扩展。这步骤为您提供了 `Lambda-Extension-Identifier`，您需要在以下步骤中使用。有关如何注册扩展的更多信息，请参阅 [注册扩展](https://docs.amazonaws.cn/lambda/latest/dg/telemetry-api.html#telemetry-api-registration)。
2. 创建遥测侦听器。这可以是基本的 HTTP 或 TCP 服务器。Lambda 使用遥测侦听器的 URI 向扩展发送遥测数据。有关更多信息，请参阅[创建遥测侦听器](https://docs.amazonaws.cn/lambda/latest/dg/telemetry-api.html#telemetry-api-listener)。
3. 使用遥测 API 中的订阅 API，为您的扩展订阅所需的遥测流。在此步骤中，您需要遥测侦听器的 URI。有关更多信息，请参阅[向遥测 API 发送订阅请求](https://docs.amazonaws.cn/lambda/latest/dg/telemetry-api.html#telemetry-api-subscription)。
4. 通过遥测侦听器从 Lambda 获取遥测数据。您可以对这些数据进行任何的自定义处理，例如将数据分派到 Amazon S3 或外部可观测性服务。

###### 注意

Lambda 函数的执行环境可以作为其[生命周期](https://docs.amazonaws.cn/lambda/latest/dg/runtimes-extensions-api.html#runtimes-extensions-api-lifecycle)的一部分多次启动和停止。通常，您的扩展代码在函数调用期间运行，在关闭阶段也最多运行 2 秒。我们建议在遥测数据到达侦听器时对其进行批处理，并使用 `Invoke` 和 `Shutdown` 生命周期事件将每个批次分派到所需的目标。

## 注册扩展

在订阅接收遥测数据之前，您必须注册 Lambda 扩展。注册发生在[扩展初始化阶段](https://docs.amazonaws.cn/lambda/latest/dg/runtimes-extensions-api.html#runtimes-extensions-api-reg)。以下示例显示了注册扩展的 HTTP 请求。
``` HTTP
POST http://${AWS_LAMBDA_RUNTIME_API}/2020-01-01/extension/register
 Lambda-Extension-Name: lambda_extension_name
{
    'events': [ 'INVOKE', 'SHUTDOWN']
}
```
如果请求成功，订阅者将收到一条 HTTP 200 成功响应。响应标头包含 `Lambda-Extension-Identifier`。响应正文包含函数的其他属性。
``` http
HTTP/1.1 200 OK
Lambda-Extension-Identifier: a1b2c3d4-5678-90ab-cdef-EXAMPLE11111
{
    "functionName": "lambda_function",
    "functionVersion": "$LATEST",
    "handler": "lambda_handler",
    "accountId": "123456789012"
}
```
有关更多信息，请参阅 [扩展 API 参考](https://docs.amazonaws.cn/lambda/latest/dg/runtimes-extensions-api.html#runtimes-extensions-registration-api)。

## 创建遥测侦听器

Lambda 扩展必须有一个侦听器来处理来自遥测 API 的传入请求。以下代码显示了 Golang 中的示例遥测侦听器实施：
``` go
// Starts the server in a goroutine where the log events will be sent
func (s *TelemetryApiListener) Start() (string, error) {
	address := listenOnAddress()
	l.Info("[listener:Start] Starting on address", address)
	s.httpServer = &http.Server{Addr: address}
	http.HandleFunc("/", s.http_handler)
	go func() {
		err := s.httpServer.ListenAndServe()
		if err != http.ErrServerClosed {
			l.Error("[listener:goroutine] Unexpected stop on Http Server:", err)
			s.Shutdown()
		} else {
			l.Info("[listener:goroutine] Http Server closed:", err)
		}
	}()
	return fmt.Sprintf("http://%s/", address), nil
}

// http_handler handles the requests coming from the Telemetry API.
// Everytime Telemetry API sends log events, this function will read them from the response body
// and put into a synchronous queue to be dispatched later.
// Logging or printing besides the error cases below is not recommended if you have subscribed to
// receive extension logs. Otherwise, logging here will cause Telemetry API to send new logs for
// the printed lines which may create an infinite loop.
func (s *TelemetryApiListener) http_handler(w http.ResponseWriter, r *http.Request) {
	body, err := ioutil.ReadAll(r.Body)
	if err != nil {
		l.Error("[listener:http_handler] Error reading body:", err)
		return
	}

	// Parse and put the log messages into the queue
	var slice []interface{}
	_ = json.Unmarshal(body, &slice)

	for _, el := range slice {
		s.LogEventsQueue.Put(el)
	}

	l.Info("[listener:http_handler] logEvents received:", len(slice), " LogEventsQueue length:", s.LogEventsQueue.Len())
	slice = nil
}
```
## 指定目标协议

使用 Telemetry API 订阅接收遥测数据时，除了目标 URI 之外，您还可以指定目标协议：
``` json
{
    "destination": {
        "protocol": "HTTP",
        "URI": "http://sandbox.localdomain:8080"
    }
}
```
Lambda 接受两种接收遥测数据的协议：

- **HTTP**（推荐）– Lambda 会将遥测数据作为 JSON 格式的记录数组传送到本地 HTTP 端点（`http://sandbox.localdomain:${PORT}/${PATH}`）。`$PATH` 参数是可选的。Lambda 仅支持 HTTP，不支持 HTTPS。Lambda 通过 POST 请求传送遥测数据。
    
- **TCP** – Lambda 会将遥测数据以[换行符分隔的 JSON（NDJSON）格式](https://github.com/ndjson/ndjson-spec)传送到 TCP 端口。
    

|注意| |
|---|---|
|我们强烈建议使用 HTTP 而不是 TCP。使用 TCP 时，Lambda 平台无法在其将遥测数据传送到应用层时进行确认。因此，如果扩展崩溃，遥测数据可能会丢失。HTTP 则无此限制。| |

订阅接收遥测数据前，设置本地 HTTP 侦听器或 TCP 端口。在安装期间，请注意以下事项：

- Lambda 只会将遥测发送到执行环境内的目标。
- 如果没有侦听器，或者 POST 请求导致错误，Lambda 会尝试重试发送遥测数据（退避尝试）。如果遥测侦听器崩溃，侦听器会在 Lambda 重新启动执行环境后继续接收遥测。
- Lambda 会预留端口 9001。没有其他端口号限制或建议。

## 配置内存使用量和缓冲

执行环境的内存使用量随着订阅者数量的增加而线性增加。订阅会消耗内存资源，因为每个订阅都会打开一个新的内存缓冲区来存储遥测数据。缓冲区内存使用量计入执行环境中的总内存消耗量。

使用遥测 API 订阅接收遥测数据时，您可以缓冲遥测数据并将其批量传送给订阅者。为了帮助优化内存使用量，您可以指定缓冲配置：
``` json
{
    "buffering": {
        "maxBytes": 256*1024,
        "maxItems": 1000,
        "timeoutMs": 100
    }
}
```

|缓冲配置设置|   |   |
|---|---|---|
|参数|描述|默认值和限制|
|`maxBytes`|内存中缓冲的最大遥测量（以字节为单位）。|默认值：262144<br><br>最小值：262144<br><br>最大值：1048576。|
|`maxItems`|内存中缓冲的最大事件数。|默认值：10000<br><br>最小值：1000<br><br>最大值：10000|
|`timeoutMs`|缓冲批次的最长时间（以毫秒为单位）。|默认值：1000<br><br>最小值：25<br><br>最大值：30000|

缓冲配置时，请注意以下几点：

- 如果任何输入流关闭，Lambda 会刷新日志。例如，如果运行时崩溃，就会发生这种情况。
- 在订阅请求中，每个订阅者都可以指定不同的缓冲配置。
- 考虑读取数据所需的缓冲区大小。预计接收与 `2 * maxBytes + metadataBytes` 一样大的有效负载，其中 `maxBytes` 是缓冲配置的一部分。若要了解应该考虑接收多少 `metadataBytes`，请查看以下元数据。Lambda 向每条记录添加与此类似的元数据：
	```json
	{
   "time": "2022-08-20T12:31:32.123Z",
   "type": "function",
   "record": "Hello World"
	}
	```
- 如果订阅者处理传入遥测数据的速度不够快，Lambda 可能会删除记录以保持内存利用率限制。发生这种情况时，Lambda 会发送 `platform.logsDropped` 事件。

## 向遥测 API 发送订阅请求

Lambda 扩展可以通过向遥测 API 发送订阅请求来订阅接收遥测数据。订阅请求应包含有关您希望扩展订阅的事件类型的信息。此外，请求可以包含[传送目标信息](https://docs.amazonaws.cn/lambda/latest/dg/telemetry-api.html#telemetry-api-destination)和[缓冲配置](https://docs.amazonaws.cn/lambda/latest/dg/telemetry-api.html#telemetry-api-buffering)。

在发送订阅请求之前，您必须拥有扩展 ID（`Lambda-Extension-Identifier`）。[使用扩展 API 注册扩展](https://docs.amazonaws.cn/lambda/latest/dg/telemetry-api.html#telemetry-api-registration)时，您会从 API 响应中获得扩展 ID。

订阅发生在[扩展初始化阶段](https://docs.amazonaws.cn/lambda/latest/dg/runtimes-extensions-api.html#runtimes-extensions-api-reg)。以下示例显示了订阅所有三个遥测流的 HTTP 请求：平台遥测、函数日志和扩展日志。
``` http
PUT http://${AWS_LAMBDA_RUNTIME_API}/2022-07-01/telemetry HTTP/1.1
{
   "schemaVersion": "2022-07-01",
   "types": [
        "platform",
        "function",
        "extension"
   ],
   "buffering": {
        "maxItems": 1000,
        "maxBytes": 256*1024,
        "timeoutMs": 100
   },
   "destination": {
        "protocol": "HTTP",
        "URI": "http://sandbox.localdomain:8080"
   }
}
```
如果请求成功，订阅者将收到 HTTP 200 成功响应。
``` http
HTTP/1.1 200 OK
"OK"
```
## 入站遥测 API 消息

使用遥测 API 订阅后，扩展会自动开始通过对遥测侦听器的 POST 请求接收来自 Lambda 的遥测数据。每个 POST 请求正文都包含 `Event` 对象数组。`Event` 是具有以下架构的 JSON 对象：
``` json
{
   time: String,
   type: String,
   record: Object
}
```

- `time` 属性定义 Lambda 平台何时生成事件。这与实际发生事件的时间不同。`time` 的字符串值是 ISO 8601 格式的时间戳。
    
- `type` 属性定义事件类型。下表介绍了所有可能的值。
    
- `record` 属性定义包含遥测数据的 JSON 对象。此 JSON 对象的架构取决于 `type`。
    

下表汇总了所有类型的 `Event` 对象，并链接到每种事件类型的[遥测 API Event 架构参考](https://docs.amazonaws.cn/lambda/latest/dg/telemetry-schema-reference.html)。

|遥测 API 消息类型|   |   |   |
|---|---|---|---|
|类别|事件类型|描述|事件记录架构|
|平台事件|`platform.initStart`|函数初始化已启动。|[platform.initStart](https://docs.amazonaws.cn/lambda/latest/dg/telemetry-schema-reference.html#platform-initStart) 架构|
|平台事件|`platform.initRuntimeDone`|函数初始化已完成。|[platform.initRuntimeDone](https://docs.amazonaws.cn/lambda/latest/dg/telemetry-schema-reference.html#platform-initRuntimeDone) 架构|
|平台事件|`platform.initReport`|函数初始化的报告。|[platform.initReport](https://docs.amazonaws.cn/lambda/latest/dg/telemetry-schema-reference.html#platform-initReport) 架构|
|平台事件|`platform.start`|函数调用已启动。|[platform.start](https://docs.amazonaws.cn/lambda/latest/dg/telemetry-schema-reference.html#platform-start) 架构|
|平台事件|`platform.runtimeDone`|运行时通过成功或失败的方式完成对事件的处理。|[platform.runtimeDone](https://docs.amazonaws.cn/lambda/latest/dg/telemetry-schema-reference.html#platform-runtimeDone) 架构|
|平台事件|`platform.report`|函数调用的报告。|[platform.report](https://docs.amazonaws.cn/lambda/latest/dg/telemetry-schema-reference.html#platform-report) 架构|
|平台事件|`platform.restoreStart`|运行时恢复已开始。|[platform.restoreStart](https://docs.amazonaws.cn/lambda/latest/dg/telemetry-schema-reference.html#platform-restoreStart) 架构|
|平台事件|`platform.restoreRuntimeDone`|运行时恢复已完成。|[platform.restoreRuntimeDone](https://docs.amazonaws.cn/lambda/latest/dg/telemetry-schema-reference.html#platform-restoreRuntimeDone) 架构|
|平台事件|`platform.restoreReport`|运行时恢复报告。|[platform.restoreReport](https://docs.amazonaws.cn/lambda/latest/dg/telemetry-schema-reference.html#platform-restoreReport) 架构|
|平台事件|`platform.telemetrySubscription`|扩展订阅了遥测 API。|[platform.telemetrySubscription](https://docs.amazonaws.cn/lambda/latest/dg/telemetry-schema-reference.html#platform-telemetrySubscription) 架构|
|平台事件|`platform.logsDropped`|Lambda 删除了日志条目。|[platform.logsDropped](https://docs.amazonaws.cn/lambda/latest/dg/telemetry-schema-reference.html#platform-logsDropped) 架构|
|函数日志|`function`|来自函数代码的日志行。|[function](https://docs.amazonaws.cn/lambda/latest/dg/telemetry-schema-reference.html#telemetry-api-function) 架构|
|扩展日志|`extension`|来自扩展代码的日志行。|[extension](https://docs.amazonaws.cn/lambda/latest/dg/telemetry-schema-reference.html#telemetry-api-extension) 架构|
