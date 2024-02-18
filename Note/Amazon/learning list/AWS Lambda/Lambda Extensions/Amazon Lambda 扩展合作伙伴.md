Amazon Lambda 已与多个第三方实体结成合作伙伴，提供多种扩展以与您的 Lambda 函数集成。以下列表详细列出了可供您随时使用的第三方扩展。

- [AppDynamics](https://docs.appdynamics.com/display/PRO20X/Use+the+AppDynamics+AWS+Lambda+Extension+to+Instrument+Serverless+APM+at+Runtime) – 提供 Node.js 或 Python Lambda 函数的自动分析，从而提供有关函数性能的可见性和提示。
    
- [Check Point CloudGuard](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk172491&partition=Advanced&product=CloudGuard) – 一种基于扩展的运行时解决方案，可为无服务器应用程序提供完整的生命周期安全性。
    
- [Datadog](https://docs.datadoghq.com/serverless/datadog_lambda_library/extension/) – 通过使用指标、跟踪和日志，为无服务器应用程序提供全面、实时的可见性。
    
- [Dynatrace](https://www.dynatrace.com/support/help/technology-support/cloud-platforms/amazon-web-services/integrations/deploy-oneagent-as-lambda-extension/) – 提供跟踪和指标的可见性，并利用 AI 在整个应用程序堆栈中进行自动错误检测和根本原因分析。
    
- [Elastic](https://www.elastic.co/guide/en/apm/agent/nodejs/current/lambda.html) – 提供应用程序性能监控（APM），以便使用相关的跟踪、指标和日志来识别和解决根本原因问题。
    
- [Epsagon](https://docs.epsagon.com/docs/environment-monitoring/lambda/intro) – 侦听调用事件、存储跟踪，并将它们并行发送到 Lambda 函数执行。
    
- [Fastly](https://docs.fastly.com/signalsciences/install-guides/paas/aws-lambda/)– 保护您的 Lambda 函数免受可疑活动的侵害，例如注入式攻击、通过凭证填充获取账户、恶意机器人和 API 滥用。
    
- [HashiCorp Vault](https://learn.hashicorp.com/tutorials/vault/aws-lambda) – 管理秘密并使开发人员可以在函数代码中使用这些秘密，而无需通知函数文件库。
    
- [Honeycomb](https://docs.honeycomb.io/getting-data-in/integrations/aws/aws-lambda/) – 用于调试应用程序堆栈的可观察性工具。
    
- [Lumigo](https://docs.lumigo.io/docs/lambda-extensions) – 分析 Lambda 函数调用情况并收集用于在无服务器和微服务环境中排除问题的指标。
    
- [New Relic](https://docs.newrelic.com/docs/serverless-function-monitoring/aws-lambda-monitoring/get-started/monitoring-aws-lambda-serverless-monitoring) – 与 Lambda 函数一起运行，自动收集、增强遥测数据，并将这些数据传输到 New Relic 的统一可观察性平台。
    
- [Sentry](https://docs.sentry.io/product/integrations/aws-lambda/) – 诊断、修复和优化 Lambda 函数的性能。
    
- [Site24x7](https://www.site24x7.com/help/aws/lambda-execution-logs.html) – 实现对 Lambda 环境的实时可观察性
    
- [Splunk](https://github.com/signalfx/lambda-layer-versions/tree/master/lambda-extension) – 收集高分辨率、低延迟的指标，以便高效且有效地监控 Lambda 函数。
    
- [Sumo Logic](https://help.sumologic.com/03Send-Data/Collect-from-Other-Data-Sources/Collect_AWS_Lambda_Logs_using_an_Extension) – 提供对无服务器应用程序的运行状况和性能的可见性。
    
- [Thundra](https://apm.docs.thundra.io/performance/zero-overhead-with-lambda-extensions) – 提供异步遥测报告，例如跟踪、指标和日志。
    

## Amazon 托管式扩展

Amazon 提供了其自己的托管式扩展，包括：

- [Amazon AppConfig](https://docs.amazonaws.cn/appconfig/latest/userguide/appconfig-integration-lambda-extensions.html#appconfig-integration-lambda-extensions-enabling) - 使用功能标志和动态数据更新 Lambda 函数。您还可以使用此扩展来更新其他动态配置，例如运维节流和优化。
    
- [Amazon CodeGuru Profiler](https://docs.amazonaws.cn/codeguru/latest/profiler-ug/python-lambda-layers.html) - 通过查明应用程序最宝贵的代码行并提供改进代码的建议，来提高应用程序性能并降低成本。
    
- [CloudWatch Lambda Insights](https://docs.amazonaws.cn/AmazonCloudWatch/latest/monitoring/Lambda-Insights.html) - 通过自动化控制面板监控、故障排除和优化 Lambda 函数的性能。
    
- [Amazon Distro for OpenTelemetry（ADOT）](http://www.amazonaws.cn/otel) – 启用函数将跟踪数据发送到 Amazon 监控服务（例如 Amazon X-Ray），以及支持 OpenTelemetry 的目的地（例如 Honeycomb 和 Lightstep）。
    
- **Amazon 参数和密钥** – 可让客户安全地从 [Amazon Systems Manager Parameter Store](https://docs.amazonaws.cn/systems-manager/latest/userguide/ps-integration-lambda-extensions.html) 检索参数和从 [Amazon Secrets Manager](https://docs.amazonaws.cn/secretsmanager/latest/userguide/retrieving-secrets_lambda.html) 检索密钥。