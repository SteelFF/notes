
Azure Function
https://learn.microsoft.com/en-us/azure/azure-functions/functions-overview
https://docs.azure.cn/zh-cn/azure-functions/


## 项目

通过Fucntions 把文件分割放到 blob，同时放到队列里，通过 Functions 进行解析，最后放到Event Hubs里

## 概述

* 可使用 Azure Functions 将 Azure 服务和其他资源进行连接
	* **触发器** ：会导致函数运行，一个函数有一个触发器。
		* 触发器有：不同的队列，Timer，Event Grid，HTTP等
	* **绑定到函数** ： 将另一个资源连接到该函数，一个函数可以有多个绑定（输入，输出绑定）。
		* 多个资源，[详细概述](https://docs.azure.cn/zh-cn/azure-functions/functions-triggers-bindings?tabs=csharp#supported-bindings)
		* 
* 

## VSCode开发第一个Functions

[第一个Function待完成](https://docs.azure.cn/zh-cn/azure-functions/create-first-function-vs-code-python?pivots=python-mode-configuration)

## 连接到 Azure 存储

[连接到 Azure Storage 待完成](https://docs.azure.cn/zh-cn/azure-functions/functions-add-output-binding-storage-queue-vs-code?pivots=programming-language-python&tabs=in-process%2Cv1)

## 触发器和绑定概念

* **==绑定表达式==** ：在 _function.json_ 文件、函数参数和代码中，可以使用表达式解析为各种源的值。


## 触发器和绑定举例

### Blob 存储

* `host.json` 用于扩展捆绑包
* `function.json` 指定了Blob 触发器绑定以及使用该绑定的 Python 代码

#### 扩展捆绑包

```json
//// host.json
// 3.0 捆绑包版本，不包含表存储绑定，需要表存储绑定，使用2.0
{
    "version": "2.0",
    "extensions": {
        "blobs": {
            "maxDegreeOfParallelism": 4  // 每个 blob 触发器函数允许使用的并发调用数
        }
    },
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[3.3.0, 4.0.0)"     // Functions版本 3.3.0 到 4.0.0(不包含) 都可用
    }
}
```

* [2.0 官网详解](https://docs.azure.cn/zh-cn/azure-functions/functions-host-json)
```json
// 2.0 所有属性都是可选的
{
    "version": "2.0",
    "aggregator": {                    // 可能不用
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "concurrency": { 
            "dynamicConcurrencyEnabled": true, 
            "snapshotPersistenceEnabled": true 
        },
    "extensions": {
        "blobs": {},
        "cosmosDb": {},
        "durableTask": {},
        "eventHubs": {},
        "http": {},
        "queues": {},
        "sendGrid": {},
        "serviceBus": {}
    },
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {               
            "samplingSettings": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 20,
              "evaluationInterval": "01:00:00",
              "initialSamplingPercentage": 100.0, 
              "samplingPercentageIncreaseTimeout" : "00:00:01",
              "samplingPercentageDecreaseTimeout" : "00:00:01",
              "minSamplingPercentage": 0.1,
              "maxSamplingPercentage": 100.0,
              "movingAverageRatio": 1.0,
              "excludedTypes" : "Dependency;Event",
              "includedTypes" : "PageView;Trace"
            },
            "dependencyTrackingOptions": {
                "enableSqlCommandTextInstrumentation": true
            },
            "enableLiveMetrics": true,
            "enableDependencyTracking": true,
            "enablePerformanceCountersCollection": true,            
            "httpAutoCollectionOptions": {
                "enableHttpTriggerExtendedInfoCollection": true,
                "enableW3CDistributedTracing": true,
                "enableResponseHeaderInjection": true
            },
            "snapshotConfiguration": {
                "agentEndpoint": null,
                "captureSnapshotMemoryWeight": 0.5,
                "failedRequestLimit": 3,
                "handleUntrackedExceptions": true,
                "isEnabled": true,
                "isEnabledInDeveloperMode": false,
                "isEnabledWhenProfiling": true,
                "isExceptionSnappointsEnabled": false,
                "isLowPrioritySnapshotUploader": true,
                "maximumCollectionPlanSize": 50,
                "maximumSnapshotsRequired": 3,
                "problemCounterResetInterval": "24:00:00",
                "provideAnonymousTelemetry": true,
                "reconnectInterval": "00:15:00",
                "shadowCopyFolder": null,
                "shareUploaderProcess": true,
                "snapshotInLowPriorityThread": true,
                "snapshotsPerDayLimit": 30,
                "snapshotsPerTenMinutesLimit": 1,
                "tempFolder": null,
                "thresholdForSnapshotting": 1,
                "uploaderProxy": null
            }
        }
    },
    "managedDependency": {
        "enabled": true
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ],
    "watchFiles": [ "myFile.txt" ]
}
```
#### blob 触发器
```json
// function.json
{
    "scriptFile": "__init__.py",                  // 绑定 functions
    "disabled": false,
    "bindings": [
        {
            "name": "myblob",
            "type": "blobTrigger",                // blob 触发器
            "direction": "in",                    // 必须设置为 `in`
            "path": "samples-workitems/{name}",   // samples-workitems容器下更新或新增blob触发 functions
            // path 是某种 blob 名称模式 ： "path": "input/{blobname}.{类型如txt}"
            //    按文件筛 "path": "input/original-{name}",
            //    按类型   "path": "samples/{name}.png",
            //    文件名中有大括号 ： "path": "images/{{20140101}}-{name}",
            
            "connection":"MyStorageAccountAppSetting"
            // connection 指定应用应如何连接到 Azure Blob
		}
    ]
}
``` 
* [connection 连接字符串](https://docs.azure.cn/zh-cn/azure-functions/functions-bindings-storage-blob-trigger?tabs=in-process&pivots=programming-language-python#connection-string)
* [connection  基于标识的连接](https://docs.azure.cn/zh-cn/azure-functions/functions-bindings-storage-blob-trigger?tabs=in-process&pivots=programming-language-python#identity-based-connections)

#### Blob输入输出
```json
//// function.json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",                               // 绑定到 py脚本
      "type": "queueTrigger",                           // 队列触发器
      "direction": "in"                                 // 触发器为 in
    },
    {
      "name": "inputblob",                             // 绑定到 py脚本                   
      "type": "blob",                                  // 输入类型为 blob
      "dataType": "binary",                            
      // dataType 输入类型的绑定
      //      binary  将 输入 转换为python的 bytes 对象
      //      string  将 输入 转换为string  (默认)
      //      stream  
      "path": "samples-workitems/{queueTrigger}",      // Blob 的路径。
      "connection": "MyStorageConnectionAppSetting",   
      "direction": "in"                                // 输入绑定
    },
    {
      "name": "$return",                                // 函数代码中的 Blob 的变量
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",  // blob 路径
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"                                // 输出绑定
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"                 // 脚本文件
}
```

```python
# python 脚本  __init__.py
import logging
import azure.functions as func

def main(
		 queuemsg: func.QueueMessage,      # 绑定触发器名
		 inputblob: bytes,                 # 绑定输入 blob 名
		 outputblob: func.Out[bytes]       # ？？？？？？
		 ):
    logging.info(f'Python Queue trigger function processed {len(inputblob)} bytes')
    outputblob.set(inputblob)              # 是不是这个设为 $return
```

可以将函数参数声明为以下类型以写出到 Blob 存储：
-   字符串为 `func.Out[str]`
-   流为 `func.Out[func.InputStream]`

### Event Grid

#### host.json
```json

{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[3.3.0, 4.0.0)"
    }
}
```

#### function.json
```json

{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "type": "eventGridTrigger",                  // eventGrid触发器
      "name": "eventGridEvent",                    // 对接脚本 name
      "direction": "in"                            // 触发器 in
    },
    {
      "type": "eventGrid",                               // 输出绑定为 eventGrid
      "name": "outputEvent",                             // 对接 py 脚本
      "topicEndpointUri": "MyEventGridTopicUriSetting",  // 自定义主题 URI
      "topicKeySetting": "MyEventGridTopicKeySetting",   // 自定义主题访问密钥
      "direction": "out"                                 // 输出  out
    }
  ],
  "disabled": false
}
```


```python
# py 脚本  __init__.py
import logging
import azure.functions as func
import datetime

def main(eventGridEvent: func.EventGridEvent,       # 触发器name
         outputEvent: func.Out[func.EventGridOutputEvent]) -> None: # out name

    logging.log("eventGridEvent: ", eventGridEvent)   

    outputEvent.set(
        func.EventGridOutputEvent(                        
            id="test-id",
            data={"tag1": "value1", "tag2": "value2"},
            subject="test-subject",
            event_type="test-event-1",
            event_time=datetime.datetime.utcnow(),
            data_version="1.0"))
```

* 设置为 `$return`。 使用此配置时，函数的返回值将作为事件网格消息保留。
* 值传递给声明为 [Out](https://learn.microsoft.com/python/api/azure-functions/azure.functions.out) 类型的参数的 [set](https://learn.microsoft.com/python/api/azure-functions/azure.functions.out#set-val--t-----none) 方法。 传递给 `set` 的值将作为事件网格消息保留。

### Event Hubs

* `host.json` 用于扩展捆绑包
* `function.json` 指定了Blob 触发器绑定以及使用该绑定的 Python 代码

#### 扩展捆绑包

* 设置
```json
//// host.json
// bind 3.0
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[3.3.0, 4.0.0)"
    },
    "extensions": {
        "eventHubs": {
            "maxEventBatchSize" : 10,
            "batchCheckpointFrequency" : 1,
            "prefetchCount" : 300,
            "transportType" : "amqpWebSockets",
            "webProxy" : "https://proxyserver:8080",
            "customEndpointAddress" : "amqps://company.gateway.local",
            "initialOffsetOptions" : {
                "type" : "fromStart",
                "enqueuedTimeUtc" : ""
            },
            "clientRetryOptions":{
                "mode" : "exponential",
                "tryTimeout" : "00:01:00",
                "delay" : "00:00:00.80",
                "maximumDelay" : "00:01:00",
                "maximumRetries" : 3
            }
        }
    }
}
```
#### function.json
```json
{
  "scriptFile": "__init__.py",
  "bindings": [
		{
		  "type": "eventHubTrigger",                         // 触发器
		  "name": "event",                                   // name
		  "direction": "in",                             
		  "eventHubName": "MyEventHub",                      // eventhub name
		  "connection": "myEventHubReadConnectionAppSetting" 
		},
		{
		    "type": "eventHub",                              // 绑定 eventhub 输出
		    "name": "$return",                               // 绑定py return值
		    "eventHubName": "myeventhub",                    
		    "connection": "MyEventHubSendAppSetting",
		    "direction": "out"
		}
  ],
  "disabled": false
}
```

```python
import datetime
import logging
import azure.functions as func


def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp)
    return 'Message created at: {}'.format(timestamp)
```