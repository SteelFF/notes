作为基于行为的沟通的基础之一，用户操作（事件）将帮助您组织受众群体并设计相关的双向消息传递。

Infobip 数据平台可让您跟踪两种类型的事件：来自您的网站、应用程序或其他系统的自定义事件，以及您在我们的产品内与客户沟通时发生的标准事件： [时刻](https://www.infobip.com/docs/moments)、[对话](https://www.infobip.com/docs/conversations)和[答案](https://www.infobip.com/docs/answers)。  

## 自定义事件

这些事件是在其他平台上发生的客户活动。例如，配置自定义事件后，您可以查看客户打开了哪些页面以及打开了多少次。此活动将被记录并存储在人员模块中。

要快速概览个人资料活动，请单击  人员个人资料内的[“属性、标签和事件”页面选项卡。](https://www.infobip.com/docs/people/get-started#attributes-tags-and-events-person-profile)

### 特性

[属性是可选的，它们允许您在定义](https://www.infobip.com/docs/people/events#define-custom-events)事件时包含附加信息。这里有些例子： 

|   |   |   |
|---|---|---|
|**事件**|**财产**|**财产**|
|商品已添加到购物车|项目名|商品价格|
|表格已填写|字段已满||
|已购买的机票|旅行类型||
|支持票已关闭|票证编号|截止日期|

您可以选择文本、真/假、日期或数字作为事件属性中的参数类型。

### 定义

在发送事件之前，必须在“ _创建新事件定义”_ 页面上对其进行配置：输入事件标题、定义 ID、事件描述和事件属性。定义 ID 将用于跟踪。

这些定义适用于主账户及其子账户。

### 追踪

跟踪事件有三种方式：[Web SDK](https://www.infobip.com/docs/people/synchronize-your-audience#web-sdk-create-and-update)、API 和 Mobile SDK。在本节中，您将找到所有这些指南。

但首先，继续“练习”。转到人员模块，然后在**事件**页面上单击**Web SDK**选项卡。滚动到页面底部以选择事件定义和与其相关的人员。发送事件。

[![创建测试事件](https://www.infobip.com/docs/images/uploads/articles/mam-custom-event-try-it.png)](https://www.infobip.com/docs/images/uploads/articles/mam-custom-event-try-it.png)

该活动将显示在该人的个人资料页面上。

[![](https://www.infobip.com/docs/images/uploads/articles/events-on-person-card.png)](https://www.infobip.com/docs/images/uploads/articles/events-on-person-card.png)

#### 网页版SDK

Web SDK 允许您从网站前端捕获事件。要了解如何将 Web SDK 代码添加到您的站点并使用它来跟踪自定义事件，请查看文档的[Web SDK部分。](https://www.infobip.com/docs/people/synchronize-your-audience#web-sdk-create-and-update)

#### 事件API

当涉及从任何后端系统向 Infobip 发送事件时，API 通常被认为是更安全的方法。有关实现细节，请参阅[开发者指南](https://dev.infobip.com/#customer-engagement/events)。  
  
以下是全部设置的快速步骤：

1. 通过访问Web 界面中的**开发人员** 页面来创建公共 API 密钥 。

[![人们创建 api 密钥](https://www.infobip.com/docs/images/uploads/articles/people-create-api-key.png)](https://www.infobip.com/docs/images/uploads/articles/people-create-api-key.png)

2. 要通过 API 发送事件，请遵守我们的 API 规范并使用以下内容：

- {{personId}} - 唯一的个人标识符（电子邮件、pushRegId、电话）
- {{definitionId}} -**事件定义**页面中的定义 ID
- {{AppKey}} -**开发者** 页面的 API 密钥
- {{您的个人基本网址}} -登录后可以获得的[基本网址](http://dev.infobip.com/getting-started/base-url)

应用POST方法：  
 

- JSON

```json
POST /persons/{{personId}}/definitions/{{definitionId}}/events
Host: {{your personal Base URL}}/peopleevents/1
Authorization: App {{AppKey}}
Accept: application/json
{
"properties": {
 "property_key": "property_value”,
}
} 
```


|笔记| |
|---|---|
|如果属性值是字符串，请将其放在引号内。如果不是，请勿使用引号。| |

##### 重要的

当您想要发送没有属性的事件时，请发送带有空属性的 JSON 正文。

**与 JS SDK 不同，API 允许您发送与People**平台上已存在的人员标识符相关的事件 。如果您发送的事件的人员标识符不存在于人员中，您将收到错误消息。

#### 移动软件开发工具包

移动 SDK 用于捕获来自移动应用程序的事件。它涉及标准事件和自定义事件。

**标准事件**已为所有在其移动应用程序中实施了 Mobile SDK 的 Moments 用户启用，并且您无需执行任何其他操作即可跟踪这些事件。查看[频道事件](https://www.infobip.com/docs/people/events#channel-events-standard-events)部分以查看可用移动应用程序事件的列表。  

**自定义事件**是您自己定义并希望通过 Mobile SDK 跟踪的事件。您可以使用自定义事件来跟踪用户活动。例如，当用户将商品添加到购物车、购买商品或完成入职流程中的具体步骤时，通过 Mobile SDK 发送自定义事件。

[详细了解如何在iOS](https://github.com/infobip/mobile-messaging-sdk-ios/wiki/Events)、  [Android 和华为](https://github.com/infobip/mobile-messaging-sdk-android/wiki/Events) SDK 以及 [React Native](https://github.com/infobip/mobile-messaging-react-native-plugin/wiki/Events)、  [Cordova](https://github.com/infobip/mobile-messaging-cordova-plugin/wiki/Events)和 [Flutter](https://github.com/infobip/mobile-messaging-flutter-plugin/wiki/Events) 插件 上设置移动事件跟踪。

## 模板事件

我们提供了许多默认事件模板，可以立即使用它们来跟踪个人资料行为和活动。

模板预先填充了 各种事件所需的[属性](https://www.infobip.com/docs/people/events#properties-custom-events) ，包括查看的商品、购买的商品、添加到购物车的商品等。 

要查看 Web 界面中的可用事件模板，请单击 人员模块中的**事件 > 模板事件****。**

|模板名称|描述|特性|
|---|---|---|
|itemViewed|指示由给定配置文件创建的给定项目的详细视图的事件|' **itemID** ': 数字 - 强制属性<br><br>'**持续时间**'：数字  - 选修的|
|itemPurchased|指示由给定配置文件购买给定商品的事件|'**项目ID** '： number - 强制属性<br><br>'**金额**'：  数量 - 可选<br><br>'**价格**'：数量  - 选修的<br><br>'**利润**'：数量  - 选修的|
|ratingAdded|指示给定配置文件对给定项目进行评分的事件|' **itemID** ': 数字 - 强制属性<br><br>'**评级**'： 小数 - 可选|
|addedToCart|指示由给定配置文件添加给定商品的购物车的事件|' **itemID** ': 数字  - 强制属性<br><br>'**金额**'：数量  - 选修的<br><br>'**价格**'：数量 - 选修的|
|addedToFavourites|指示给定配置文件为给定项目添加书签的事件|' **itemID** ': 数字  - 强制属性|
|viewPortionSet|指示会话中的个人资料对项目（例如视频或文章）的部分视图的事件|' **itemID** ': 数字 - 强制属性<br><br>'**部分**'：小数- 选修的 *<br><br>'**会话ID** '：字符串 - 选修的 **<br><br>* 部分是指项目的已查看部分。它是一个介于 0.0（未查看的项目）和 1.0（已查看完整项目）之间的数值。<br><br>无论该项目是如何找到的，该参数都是该项目的已查看部分。<br><br>例如，如果用户立即打开视频中途但只观看了 10%，则该部分将为 0.1，而不是 0.6。<br><br>** 用户查看项目的会话 ID。默认值为 **null**。|

## 标准活动

当您的客户与 Infobip 提供的服务交互时生成的事件称为标准事件。它们可用于 Infobip 解决方案中所有与事件相关的功能。

标准事件包括以下内容： 

- [人物活动](https://www.infobip.com/docs/people/events#people-events-standard-events)
- [频道活动](https://www.infobip.com/docs/people/events#channel-events-standard-events)
- [产品活动](https://www.infobip.com/docs/people/events#product-events-standard-events)

### 使能够

选择您想要跟踪的事件，它们将可供您拥有的 Infobip 产品使用：

1. 转至  Infobip Web 界面上的**“人员”页面。**

2. 单击 **“人员**事件”。

3. 导航到事件类别（人员、渠道或产品事件），然后选中要跟踪的事件旁边的复选框并将其激活。 

[![人物-事件-选择](https://www.infobip.com/docs/images/uploads/articles/people-people-events-select.png)](https://www.infobip.com/docs/images/uploads/articles/people-people-events-select.png)

### 人物活动

根据客户资料更新创建消息。这些更新涉及人员活动、标准属性和自定义属性的更改。

#### 人员活动 

跟踪您网站上的人员活动，并根据用户会话和页面浏览活动创建事件。您可以为需要在人员的事件区域中激活的以下活动创建事件：

- 会话开始
- 页面预览
- 人物合并
- 会议结束

通过使用事件的组合，您可以创建强大的场景，例如： 

- _跟踪匿名访客的网站访问情况。_ 跟踪客户从匿名访客到潜在客户和客户转化的旅程。 
- _有针对性的提供_。如果某人在一个月内访问“抵押”页面 5 次，请向他们发送一封电子邮件并提供建议。
- _特定类别的促销代码。_ 当有人在一周内查看“鞋子类别”页面 3 次时，请发送一条包含鞋子促销代码的消息。
- _重新激活。_ 启动重新激活活动并包括过去 45 天内未访问过您的网页的用户。
- _被遗弃的购物车。_ 在用户访问“搜索票证”页面，然后访问“选择票证”页面（但不是“结帐页面”）后发送通信。
- _针对活跃客户的调查。_ 如果某人在 30 天内的页面浏览量超过 100 次，请向他们发送调查。

可以使用基于 Javascript 的 Web SDK 将人员活动事件添加到您的网站，您可以将其添加到您的网站以跟踪人员活动。请参阅 [Web SDK](https://www.infobip.com/docs/people/synchronize-your-audience#web-sdk-create-and-update) 以了解更多信息。 

[![人-人-活动-选择器](https://www.infobip.com/docs/images/uploads/articles/people-person-activity-selector.png)](https://www.infobip.com/docs/images/uploads/articles/people-person-activity-selector.png)

**Session Started -** 当用户访问网站时进行记录。

[每个会话启动事件将包含由Web SDK](https://www.infobip.com/docs/people/synchronize-your-audience#web-sdk-create-and-update)自动填充的以下属性 [](https://www.infobip.com/docs/people/synchronize-your-audience#web-sdk-create-and-update)

|财产|描述|
|---|---|
|Session ID|唯一的会话标识符|
|Session Start|会话开始的时间。日/月/年 时:分:秒|
|Session Type|网络|
|UTM Parameters|对于网络会话；如果未设置，则 utm_source 使用“direct”。<br><br>- utm_源<br>- utm_medium<br>- utm_campaign<br>- utm_term<br>- utm_内容|

**Page View** - 根据人们的网页访问来吸引他们。 

每个页面视图事件将包含以下属性，这些属性将由 Web SDK 自动填充：

|财产|类型|描述|
|---|---|---|
|pageTitle|String|当前页面的标题。|
|url|String|为 `PageView` 加载的页面的 URL。|
|domain|String|请求 URL 的域部分。|
|path | String |URL 的路径部分。该值应以斜杠 (/) 字符开头。|
|deviceType|String|用于加载页面的设备类型：未知、移动设备、平板电脑、台式机、蜘蛛、电视或控制台。|
|timestamp|Datetime|交互发生的时间（日期、小时、分钟、秒）。|
|browserName|String|从 HTTP 请求的 User-Agent 标头获取的浏览器名称，例如 Chrome、Firefox。|
|browserVersion|String|从 HTTP 请求的 User-Agent 标头获取的浏览器报告的软件版本。|
|browserOS|String|浏览器报告的运行它的操作系统，例如 Windows 或 Linux。这是从 HTTP 请求的 User-Agent 标头获取的。|
|referrer | String |Referrer HTTP 请求标头包含发出请求的页面的绝对或部分地址。|
|browserLanguage|String|浏览器语言。|
|websiteLanguage|String|网站语言。|

**Session End -** 当某人停止在网站上活动时进行记录。 

[每个会话结束事件将包含由Web SDK](https://www.infobip.com/docs/people/synchronize-your-audience#web-sdk-create-and-update)自动填充的以下属性[](https://www.infobip.com/docs/people/synchronize-your-audience#web-sdk-create-and-update)

|财产|描述|
|---|---|
|会话ID|唯一的会话标识符|
|会议结束|会话结束的时间。日/月/年 时:分:秒|
|会议时长|会话结束 - 会话开始。很快。|
|会话类型|网络|
|UTM参数|对于网络会话；如果未设置，则 utm_source 使用“direct”<br><br>- utm_源<br>- utm_medium<br>- utm_campaign<br>- utm_term<br>- utm_内容|

**Person Merge** - 在人员配置文件合并时获取信息。

#### 标准属性

与您可以选择跟踪的标准客户属性相关的活动：

- 名称变更
- 地点变更
- 其他变化，例如出生日期或性别认同
- 标签变更
- 联系信息

#### 联系信息

推送目的地事件提供有关涉及移动应用程序的用户活动的信息，并且它们在我们的数据平台上的人员个人资料中可见。它们是通过 Mobile SDK 生成的。 

以下是可以在人员的个人资料中找到的事件：

|联系信息类型|活动|
|---|---|
|Email|已添加电子邮件地址<br><br>电子邮件地址已更改<br><br>电子邮件地址已删除|
|Push|添加推送联系人<br><br>推送联系人已更改<br><br>推送联系人已删除|
|Facebook|添加了 Facebook 联系人<br><br>Facebook 联系方式已更改<br><br>Facebook 联系人已删除|
|SMS/Voice/MMS|已添加电话号码<br><br>电话号码已更改<br><br>电话号码已删除|
|Other contact information|联系方式已更改|

#### 自定义属性 

这些属性涵盖与您的客户及其兴趣相关的更具体的数据，以帮助您以知情的方式进行联系。它们应该在**人员模块的****配置**页面上[定义](https://www.infobip.com/docs/people/manage-data#custom-attributes)。完成配置后，转到**“事件”**页面并选中该自定义属性旁边的复选框。

[![跟踪自定义事件](https://www.infobip.com/docs/images/uploads/articles/track-custom-in-events.png)](https://www.infobip.com/docs/images/uploads/articles/track-custom-in-events.png)

##### 提示

我们建议您不要包含您 并不真正打算使用的[属性；](https://www.infobip.com/docs/people/manage-data#attributes)它们只会使界面变得混乱。

### 频道活动

当前使用此功能的所有渠道都有一组事件，当您想要跟踪消息的状态以及客户对消息的反应时，您可以选择这些事件。 

默认情况下，所有通道事件在系统中均处于活动状态，因此您无需在配置过程中进行任何更改。

请看下面按渠道查看事件：   

|CHANNEL|EVENT NAME|
|---|---|
|[Email](https://www.infobip.com/docs/email)|Sent<br><br>Delivered<br><br>Opened<br><br>Clicked<br><br>Unsubscribed<br><br>Complained|
|[Push](https://www.infobip.com/docs/mobile-app-messaging)|Sent<br><br>Delivered<br><br>Opened|
|[SMS](https://www.infobip.com/docs/sms)|Sent<br><br>Delivered<br><br>Clicked|
|[WhatsApp](https://www.infobip.com/docs/whatsapp)|Sent<br><br>Delivered<br><br>Opened<br><br>Clicked|
|[Viber](https://www.infobip.com/docs/viber)|Sent<br><br>Delivered<br><br>Opened<br><br>Clicked|
|[Messenger](https://www.infobip.com/docs/facebook-messenger)|Sent<br><br>Delivered<br><br>Opened<br><br>Clicked|
|[Mobile App](https://www.infobip.com/docs/mobile-app-messaging)|Personalize <br><br>Depersonalize<br><br>Install<br><br>Uninstall<br><br>App Launch|
|[Voice](https://www.infobip.com/docs/voice-and-video)|Voice Call Initiated<br><br>Voice Call Answered<br><br>Voice Call Not Answered<br><br>Voice Busy<br><br>Voice Call Answered By Machine<br><br>Voice Inbound Call Answered<br><br>Call Forwarded Busy<br><br>Call Forwarded Not Answered<br><br>Voice IVR Played<br><br>Voice IVR Information collected<br><br>Voice User Hang Up|
|[LINE](https://www.infobip.com/docs/LINE)|Sent<br><br>Link Clicked|
|[MMS](https://www.infobip.com/docs/mms)|Sent<br><br>Delivered|

  
 

### 产品活动

属于特定 Infobip 产品的事件称为产品事件：

- 在 [Moments](https://www.infobip.com/docs/moments)中，产品事件将显示人员何时进入或退出特定的沟通活动。此事件根据客户属性和活动发生，最重要的是，根据您的设置发生。  
- 在[Forms](https://www.infobip.com/docs/forms)中，如果已为特定表单启用[处理规则](https://www.infobip.com/docs/forms/data-processing)，我们将跟踪提交表单事件。然后可以使用该信息将请求的信息发送给表单提交者。   

## 受众细分中的事件

在“人员”的 **“细分”** 页面上，事件将帮助您创建特定的受众群体并相应地组织通信，无论您是要根据此事件发送消息、检查客户致电您的联络中心的次数还是其他内容。创建细分时，结合事件和属性以实现更相关的细分。 

下例中选定的客户属性是“国家/地区”，事件是“已添加标签”：

[![细分受众群中的事件](https://www.infobip.com/docs/images/uploads/articles/events-in-audience-segments.png)](https://www.infobip.com/docs/images/uploads/articles/events-in-audience-segments.png)

## Moments事件

在Moments中，您可以根据之前定义的自定义事件触发活动，也可以选择预定义的标准事件。

例如，开始[创建通信流](https://www.infobip.com/docs/moments/manage-flow#create) 并使用[People-Real Time](https://www.infobip.com/docs/moments/flow-elements#triggers-entry-points)事件触发器。您的自定义“触发事件”可以是_“项目添加到购物车”。添加_沟通[渠道](https://www.infobip.com/docs/moments/flow-elements#channels) 并向执行该操作的人员发送消息。

[![事件触发的营销活动时刻](https://www.infobip.com/docs/images/uploads/articles/events-triggered-campaign-moments.png)](https://www.infobip.com/docs/images/uploads/articles/events-triggered-campaign-moments.png)

如果您想稍后在通信流中使用事件，以下元素将帮助您做到这一点：[等待事件](https://www.infobip.com/docs/moments/flow-elements#wait-for-an-event-functions)和[分段受众](https://www.infobip.com/docs/moments/flow-elements#segment-audience-functions)。

  
 4. 指定下一步。例如，选择“ **等待事件”** 元素，然后选择  _“结帐完成”_ 作为您将等待的事件 _。_ 输入您想要等待此操作发生的时间。

##### 提醒

您需要启用要跟踪的标准事件。

### 事件运营商

添加“[等待事件”](https://www.infobip.com/docs/moments/flow-elements#functions) 元素时，请包含一个将显示事件是否发生的运算符。 

_发生_意味着一旦某个事件发生在一个人身上，沟通触发器就会被激活。您还可以设置出现的时间和次数。例如，如果您想触发流程并在 7 天的时间范围内每 5 次发生某个事件时发送通信，您可以将时间指定为“过去 7 天内”，将发生次数指定为“等于 5 次” ”。

使用_“未发生”_ 作为确认用户未发生事件的方法。例如，如果用户在一段时间后没有启动应用程序。

未发生的情况应始终与发生的情况一起使用。这是因为我们只知道事件是为谁而发生的。它没有表明该事件不是为谁发生的。如果您创建一个具有“事件 1 发生”和“事件 2 未发生”两个条件的块，那么您可以检查哪些人同时发生了事件 1 而没有发生事件 2。 

各种事件及其运算符可以组合在同一元素中，如下所示。  
[![等待事件应用程序启动](https://www.infobip.com/docs/images/uploads/articles/wait-for-event-app-launch.png)](https://www.infobip.com/docs/images/uploads/articles/wait-for-event-app-launch.png)

如果某人已经满足了您在流程中包含的条件，则该人将不会进入流程，至少目前不会。但是，如果该流程是[重复出现的](https://www.infobip.com/docs/moments/manage-flow#frequency-settings)，则同一个人如果将来某个时候碰巧符合设定的标准，则可以稍后进入通信。

​​​如果您仍然想知道哪种参与策略最适合您的公司，请浏览[用例](https://www.infobip.com/docs/use-cases?category:products=15)。

## 人员分析中的事件

[事件细分](https://www.infobip.com/docs/moments/analytics#events-segmentation-analytics)和[漏斗分析](https://www.infobip.com/docs/moments/analytics#funnel-analytics)都提供了使用标准事件的可能性。例如，在“人员”模块中，选择“漏斗分析”，然后从下拉列表中选择您要使用的标准事件。

[![事件分析漏斗](https://www.infobip.com/docs/images/uploads/articles/events-analytics-funnel.png)](https://www.infobip.com/docs/images/uploads/articles/events-analytics-funnel.png)

您选择的事件将用作漏斗中的步骤。如果您选择事件细分，标准事件将允许您创建要分析的特定受众群体。   
  
 

## 外部系统中的事件

如果您想在外部系统中使用事件数据，您可以使用 People API 将事件作为压缩文件导出到您的 Amazon Web Services (AWS) S3 存储桶。 

使用事件导出 API 可以：

- [提交导出事件请求](https://www.infobip.com/docs/api/customer-engagement/people/create-events-export-request) （发布）
- [检查您的导出请求的状态](https://www.infobip.com/docs/api/customer-engagement/people/get-events-export-request-status) （获取）
- [异步取消导出请求](https://www.infobip.com/docs/api/customer-engagement/people/cancel-events-export-request) (Del)
- [获取事件定义列表](https://www.infobip.com/docs/api/customer-engagement/people/get-events-definitions-for-export) （Get）

### 事件导出数据格式

当您提交导出事件请求以将事件导出到 AWS S3 存储桶时，需要考虑许多数据和文件格式。

以下信息假设您已经配置了 AWS S3 存储桶。如果您尚未这样做，请查看 [Amazon AWS 文档](https://docs.aws.amazon.com/AmazonS3/latest/userguide/GetStartedWithS3.html) ，了解如何设置 AWS S3 存储桶。

#### 设置S3存储桶权限

设置 S3 存储桶后，使用 AWS Identity and Access Management (IAM) 创建一个角色，Events Export 应用程序将使用该角色来访问 S3 存储桶。

该角色需要两个策略：

1. S3 访问策略以允许访问您的 S3 存储桶。
2. 允许 Events Export 应用程序用户使用 AWS Security Token Service 获取临时会话的信任策略。可以从Infobip Web 界面的[“事件导出”](https://portal.infobip.com/people/events/export)页面检索此代码。

设置适当的权限后，您的 S3 存储桶将准备好接受从 Infobip 导出的文件。

#### 导出文件

按照提交导出事件请求中定义的架构 [提交导出事件请求](https://www.infobip.com/docs/api/customer-engagement/people/create-events-export-request)。   

请求完成后，包含事件的文件将导出到指定的 S3 存储桶。您可以指定目录路径，文件将保存在 S3 存储桶的特定目录中。

每个文件都经过压缩以 `gzip` 格式化。文件名格式为 `<requestid>-events-<starttime(yyyymmddhhmmsssss)>-<endtime(yyyymmddhhmmsssss)>.json.gz</endtime(yyyymmddhhmmsssss)></starttime(yyyymmddhhmmsssss)></requestid>`.

解压缩后，该文件最多包含 50000 行。每行恰好包含 1 个 JSON 格式的事件。

#### 事件架构

每个事件都具有无论事件类型如何都始终存在的必填字段以及特定于事件类型的附加字段。

**必须填写**

|字段名称|数据类型|例子|笔记|
|---|---|---|---|
|`definitionId`|String|“人员进入流程”|事件的定义（类型）。请参阅 [获取事件定义端点列表](https://www.infobip.com/docs/api/customer-engagement/people/get-events-definitions-for-export) 。|
|`eventId`|String|“fent_100000000270199_1_1647967373262”|事件的唯一 ID。|
|`sessionId`|String|“61bc52b9ba3ae43407dbb246”|[通过People Web SDK](https://github.com/infobip/people-web-sdk)跟踪的事件的 Web 会话的唯一 ID   （否则为 NULL）|
|`personId`|String|2342|注册事件的配置文件的唯一内部 ID。|
|`externalPersonId`|String|“CS26_342”|注册事件的配置文件的外部 ID（如果存在，否则为 NULL）。|
|`occuredTime`|String|“2022-09-14T15:00:35.495+0000”|UTC 表示系统接收事件时的时间戳，采用 ISO-8601 格式。|

**事件特定字段**

每个事件可能具有一个或多个附加属性，这些属性随一种事件类型（定义）而异，并携带有关活动的附加信息。

您可以通过将事件定义 ID 添加到以下基本 URL 来查看任何标准人员事件的属性：  [https://portal.infobip.com/people/events/definitions/](https://portal.infobip.com/people/events/definitions/)。例如，输入 [https://portal.infobip.com/people/events/definitions/emailLinkClicked](https://portal.infobip.com/people/events/definitions/emailLinkClicked) 可查看标准电子邮件链接单击事件的属性列表。

##### 笔记

要获取帐户可用事件定义 ID 的列表，请使用 [获取事件定义列表](https://www.infobip.com/docs/api/customer-engagement/people/get-events-definitions-for-export)

##### 笔记

如果任何特定字段名称与任何强制字段名称冲突，则该特定字段将以定义 id 为前缀。   
  
例如，如果自定义事件的定义 ID 为“myCustomDefinitionID”，并且此定义的一个字段为：“externalPersonId”（这是必填字段的名称），则事件特定字段将以定义 ID 为前缀，如下所示：“`myCustomDefinitionID_externalPersonId`”。

您可以使用有关活动和消息传递流元素的附加信息来丰富与朋友圈活动相关的消息传递事件（例如电子邮件已发送）：

|字段名称|数据类型|例子|笔记|
|---|---|---|---|
|`campaignName`|String|“通讯名称56”|活动的名称（如果存在）|
|`elementName`|String|“发电子邮件”|流的元素名称（如果存在）|
|`campaignStart`|String|“2022-06-14T10:54:30.033+0000”|活动的开始日期和时间（如果存在）|
|`flowElementId`|String|“10000005271386-12”|Flow 元素的 id（如果存在）|

[要启用此功能，请在提交导出事件请求](https://www.infobip.com/docs/api/customer-engagement/people/create-events-export-request)时将enrichWithMomentsInfo 参数设置为True  。