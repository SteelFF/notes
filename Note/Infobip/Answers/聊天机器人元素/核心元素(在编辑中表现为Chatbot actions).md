核心元素与渠道无关。这些元素存在于 聊天机器人编辑器的**聊天机器人接收** 和 **聊天机器人操作**部分中。

要查看特定于通道的元素，请参阅[通道元素](https://www.infobip.com/docs/answers/chatbot-elements/channels)部分。

### 目录
- [[#API]]
- [[#Attribute]]
- [[#Authentication]]
- [[#Close Session]]
- [[#Code]]
- [[#Conditions]]
- [[#CSAT survey]]
- [[#Delay]]
- [[#People]]
- [[#To Agent]]
	- [[#Tags]]
- [[#To Dialog]]
- [[#User input]]
- [[#Webhook]]


## API

当您需要通过聊天机器人检索信息并向外部系统发送信息时，请使用 API 元素。该元素由简单的请求和响应参数组成。

首先定义请求方法，该方法可以是任何标准 API 方法（POST、GET、PUT、PATCH、DELETE），然后向外部系统提供基本 URL 以及所需的端点。

使用 **转义** 选项替换所有被 Web 标准视为“不安全”的字符。例如，如果 URL 中的参数将返回空格，则该字符将转义并返回有效的 URL。

API 调用有多种内容类型选项，根据您的选择，您可以选择使用 **键值对** 和/或**编辑器**来编写 API 调用的主体。  

- **None** - 如果您的 API 没有正文（只有标头），请选择
- **Raw** - 用于非结构化请求（例如仅包含数字或文本块）。此外，可用于预定义列表中不可用的内容类型（例如 text/xml）
- **JSON**  - 通常为大多数 REST API 调用选择。对于正文，您可以选择键值对或在编辑器中写入
- **Text** - 仅通过编辑器支持
- **XML**  - 仅通过编辑器支持
- **URL-encoded** - 在一次查询中将正文作为键值对发送
- **Form Data** - 在发送二进制（非字母数字）数据或非常大的有效载荷时使用

**Header** 是可选的，但这是在 URL 中发送数据的地方。数据以键值格式输入。 **Body** 也是可选的，它包含与 URL 一起发送的数据。Body 以 Key-Value 格式输入或使用编辑器手动添加正文。这些选项取决于您选择的内容类型，例如，您只能将 Body 用于原始数据。

 **对于除None** 和 **Raw**之外的所有内容类型，标头键值对将具有指定类型的预定义值。之后，如果您需要在标头中添加其他值，您可以从最常用的标头选项（授权、缓存控制、cookie 等）列表中选择它们

该列表不是最终的。如果键值对不在预定义列表中，请使用 **“添加”** 选项添加 API 调用所需的值。

对 API 调用的响应保存到属性中。要保存您期望收到的响应，请转到 **“响应”** 选项卡。如果您想要将响应类型保存到 API（无论是成功、错误、未经授权等），您可以从响应 **代码** 下拉列表中选择一个属性。

当您期望 API 调用的返回正文中包含值并希望保存它们时，请使用 **+Add 属性** 并从现有属性列表中选择适当的一项（或多个，具体取决于您的 API）。

您还需要输入属性的路径。该路径是该属性值所在的 JSON 文件层次结构中的位置。根据属性所在的级别，之前的所有级别都需要包含在属性路径中，并用点“分隔” **.**”。

超时 **（** 以秒为单位）是您要等待响应的时间 - 默认设置为 10 秒，您可以设置的最大值为 60 秒，最小值为 1 秒。

如果您想了解如何保存收到的 XML 文件，请查看 有关该主题的[部分](https://www.infobip.com/docs/answers/how-to#-save-received-xml-files) 。

**SOAP API** 调用只能以 XML 格式发送，这意味着该文件中只能有一个根元素，充当信封。

有两种方法可以在答案中为 SOAP API 调用编写 XML： 

- 内容类型为 **Raw** ，并在标头中输入键值对：Content-Type + text/xml
- 内容类型 **XML** ，您将在其中为所选内容类型 (application/xml) 提供预定义标头值

在两者之间做出决定时，请考虑如果用户可以阅读您的 XML 文档（源 XML 文档），那么 text/xml 优于 application/xml。无论哪种情况，只有 **编辑器** 可用于 xml 配置。

大多数 SOAP API 使用 [WSDL](https://www.w3.org/TR/wsdl.html)  （Web 服务定义语言）。WSDL 定义了 xml 的结构，例如，元素或属性是否可以出现多次、是必需的还是可选的、或者元素的顺序。如果您要连接的服务具有 WSDL，则连接起来会容易得多。

SOAP 响应 xml 将遵循与传入请求相同的逻辑，如果出现错误，将包含消息详细信息。

考虑到 SOAP API 比 REST API 更慢、更复杂，它们无法处理除 XML 文件之外的任何内容，并且不缓存信息，因此建议在 Answers 中使用 REST 通信协议。

目前，SOAP 调用接受 **text**、  **xml** 和 **xml list**的值。选择取决于您希望稍后在 Coding 元素中使用值的方式（是否有您希望从收到的响应中提取的 xml 标记）。

## Attribute

 通过提供请求并允许用户响应，使用属性针对您在 **“属性”** 选项卡中添加的属性捕获和存储信息 。

响应针对属性存储，可以使用、引用，并可以作为客户旅程中聊天机器人逻辑的一部分。

attribute 元素的工作方式与发送通用文本元素的方式相同，但您还需要定义存储响应的属性。

您必须在 **“属性”选项** 卡中创建属性才能使用此元素。或者您可以直接在聊天机器人编辑器中使用快速创建选项来添加属性。现有属性类型在此不可配置。

如果您的聊天机器人正在使用 NER 属性并且该属性已经具有预填充值，请使用 **跳过** 选项。这是因为客户之前已经提到了相关信息，并且启用 NER 的聊天机器人将捕获该信息。

如果您在按钮元素或回复或快速回复后使用属性元素，请确保将属性中的文本字段保留为空，因为之前定义的回发值将用作要保存到属性的用户响应。 

**链接预览** 用于在聊天中显示支持此功能的频道的预览。

验证 **正则表达式模式** 允许您输入自己的正则表达式模式来验证属性值。基于正则表达式的验证支持 BRE ( **B** asic  **R** egular  **Expressions** ) 标准。正则表达式在您想要检查、匹配或验证不同输入的情况下非常有用，例如检查字符串是否包含数字，或验证输入是否为电话号码。

**Repeat** 选项 允许您设置在触发回退选项之前用户可以提供无法识别的响应或操作的次数。设置他们可以重复尝试响应的次数。

**回退** 用于向用户提供未知响应或操作的情况。设置如果发生这种情况是否将它们发送到另一个对话，或者是否将它们转移到代理，以及可选消息（如果需要）。

## Authentication

Authentication 元素仅可用于 [Authentication 对话框](https://www.infobip.com/docs/answers/create-dialogs#authenticated-dialogs) ，用于在经过身份验证的对话流结束时触发身份验证。

[使用身份验证设置安全聊天机器人](https://www.infobip.com/docs/answers/create-chatbot#authenticated-dialogs-secured-)

## Close Session

使用此元素终止整个会话。此元素不可配置，仅在最终用户到达对话的此阶段时结束与最终用户的聊天时起作用。

如果用户继续与已关闭会话的聊天机器人交谈，聊天机器人会将其视为新会话并重新开始。 

它不需要配置 - 但请记住，当您将其添加到对话框时，该元素将完成最终用户和聊天机器人之间的对话。如果用户想继续与机器人交谈，他们必须从头开始对话。

## Code

编码元素为您在设计对话框以及如何使用属性值时提供了更大的灵活性，并且还提供了加密功能。

目前，有两个类可用：

- `attributeAPI`使您能够使用以下函数处理平台中的属性：
	- GET
	- PUT

- `cryptoUtils`目前支持以下哈希函数：
	- MD5
	- SHA-1
	- SHA-256算法
	- SHA-512

您可以将该元素用于以下用途：

- 可用信息的随机化

``` js
const items = [1, 2, 3, 4, 5];  
const item = items[Math.floor(Math.random() * items.length)];  
attributeApi.set('random', item);
```

- 计数器的使用（后面可以跟上 Conditions 元素来对结果进行分支）

``` js
let correct = attributeApi.get('correct');  
correct = correct == null ? 1 : (correct + 1);  
attributeApi.set('correct', correct);
```

- 将大型 `json` 文件传输到变量中（以便更轻松地操作属性）

``` js
const qa = attributeApi.get('qa');  
let answers = [...qa.incorrect_answers, qa.correct_answer];  
answers.sort();  
   
const answerId = answers.findIndex(a => a === qa.correct_answer);  
   
attributeApi.set('answers', answers);  
attributeApi.set('answerId', String(answerId + 1));
```
 

- 属性的安全散列

``` js
const encryptedMd5 = encryptionUtils.md5('test');  
const encryptedSha1 = encryptionUtils.sha1('test');  
const encryptedSha256 = encryptionUtils.sha256('test');  
const encryptedSha512 = encryptionUtils.sha512('test');
```


![带有示例代码的编码元素](https://www.infobip.com/docs/images/uploads/articles/coding-element-attributeapi.png)

Answers平台编译验证代码语法是否正确（不会显示错误位置）。验证在您激活机器人时进行，如果验证失败，将会出现错误。

``` js
const encryptedMd5 = encryptionUtils.md5('test');

const encryptedSha1 = encryptionUtils.sha1('test');

const encryptedSha256 = encryptionUtils.sha256('test');

const encryptedSha512 = encryptionUtils.sha512('test');

const qa = attributeApi.get('qa');

let answers = [...qa.incorrect_answers, qa.correct_answer];

answers.sort();

const answerId = answers.findIndex(a => a === qa.correct_answer);

attributeApi.set('answers', answers);

attributeApi.set('answerId', String(answerId + 1));const encryptedMd5 = encryptionUtils.md5('test');  
const encryptedSha1 = encryptionUtils.sha1('test');  
const encryptedSha256 = encryptionUtils.sha256('test');  
const encryptedSha512 = encryptionUtils.sha512('test');
```

 |笔记| |
|---|---|
|如果您想查看某些聊天机器人用例的编码示例，请检查 [编码元素中的代码](https://www.infobip.com/docs/answers/code-in-coding-element)。| |

## Conditions

使用条件根据从用户收到的属性值设置分支逻辑。这允许您的聊天机器人在获得所需信息后沿着不同的路线前进。

将条件元素拖到您希望收到响应的点之后的聊天旅程中，并根据可能的输入添加尽可能多的条件。

首先选择条件中的属性，然后选择要使用的运算符（匹配、包含、不包含、不匹配、空）。可用的运算符根据属性类型而变化（例如，如果属性是布尔值，则只能选择“是”和“不是”）。

然后在值字段中提供匹配数据以运行条件。空运算符不需要任何匹配数据。

添加完所有必需的条件后，您可以通过在编辑器中的每个条件下拖动相关渠道元素来继续构建客户旅程。

当条件可以与任何内容匹配时，条件始终显示 **Else 路由。**使用此路由告诉聊天机器人在这种情况下要做什么（到另一个对话框、发送消息等）

每个对话框只能有一个条件，每个分支最多只能有两个条件。

如果您不添加 **To dialog** 元素，NLP 引擎将通过解析意图并选择适当的对话来处理对话，以继续对话以进行意图驱动的聊天机器人。

## CSAT survey

当对话即将结束时，客户 **满意****度**调查会添加到对话末尾。它们用于从客户那里收集有关他们通过聊天机器人获得的服务水平的重要反馈，从而充当强大的优化工具。

CSAT 调查通常由数字分数、文本和反馈问题组成。分数始终是必需的，并且可以采用以下配置：1-2、1-5 或 1-10。根据需要使用文本字段作为您想要的任何信息，或解释评分逻辑。

设置 **反馈问题** ，让客户以个人备注进行回应。使用 **后备消息** 告诉客户他们的输入未被识别。

答案将根据 CSAT 分数假设以下逻辑：

- 1 - 2 scale: 1 = negative | 2 = affirmative
- 1 - 5 scale: 1 to 3 = unsatisfied | 4 to 5 = satisfied
- 1 - 10 scale: 1 to 6 = unsatisfied | 7 to 10 = satisfied

## Custom Event

使用自定义事件元素将事件从 Answers 传递给人们。然后，您可以在最终用户再次与您的聊天机器人交互时使用此信息，或在其他 Infobip 解决方案中使用它。

要配置此元素，请执行以下操作：

1. 从**聊天机器人操作**中拖放**自定义事件**元素。
2. 找到您要在其中保存事件的人员配置文件。请执行下列操作：
    1. 在**联系信息属性**字段中，输入包含客户联系信息的 Answers 属性。示例：客户联系人。
    2. 在**联系人信息属性类型**字段中，选择个人资料的标识符。示例：如果您想使用最终用户的电话号码来标识他们的个人资料，请使用属性类型电话号码。**将答案属性与联系人信息属性类型**  
          
        字段进行匹配时，答案支持以下字段值：电话号码、电子邮件、内部 ID 或外部 ID。如果要在人员中使用不同的标识符来关联自定义事件，请首先使用**人员**元素从人员配置文件中获取受支持的属性之一。People元素支持从其他 ID 格式进行检索**。**  
        [![自定义事件元素](https://www.infobip.com/docs/images/uploads/articles/answers-elements-custom-event.png)](https://www.infobip.com/docs/images/uploads/articles/answers-elements-custom-event.png)
3. 在**Custom event configuration**部分，指定将针对 Person 配置文件保存的事件。
    1. 单击**添加自定义事件**。  
        [![添加自定义事件](https://www.infobip.com/docs/images/uploads/articles/answers-elements-custom-event-add-event.png)](https://www.infobip.com/docs/images/uploads/articles/answers-elements-custom-event-add-event.png)
    2. 从人员中选择相关的自定义事件。  
        [![选择活动](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-map-event-1.png)](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-map-event-1.png)
4. （可选）向事件添加属性。
    1. 单击**添加属性映射**。  
        [![将 Answers 属性映射到 People 属性](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-map-event-2.png)](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-map-event-2.png)
    2. 选择一个或多个包含事件属性的 Answers 属性。示例：addedCart、productID、productName 和 productPrice。  
        [![选择答案属性](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-map-event-3.png)](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-map-event-3.png)
    3. 对于每个答案属性，选择等效的人员事件属性。示例：addedCart、productID、productName 和 ProductPrice。确保 Answers 属性类型和 People 事件属性[兼容](https://www.infobip.com/docs/answers/integrations/people#attribute-mapping-attributes)。  
          
        答案不支持**dateTime**属性。如果要将日期时间值传递到事件中，请将该值保存在答案中的文本属性中，并将该属性映射到文本属性类型。  
        [![选择人员属性](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-map-event-4.png)](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-map-event-4.png)
    4. 单击**添加**。

该事件及其属性保存在“人员”中最终用户的个人资料中。  
[![事件保存在人物中](https://www.infobip.com/docs/images/uploads/articles/answers-elements-custom-event-in-people.png)](https://www.infobip.com/docs/images/uploads/articles/answers-elements-custom-event-in-people.png)

有关在聊天机器人中使用自定义事件元素的更多信息，请参阅[人员](https://www.infobip.com/docs/answers/integrations/people#custom-events)文档中的自定义事件。

## Delay

使用延迟元素根据需要添加任意长或短的暂停，以模仿与人交谈的自然节奏。延迟只是在元素之间添加暂停。

在适合正常对话的任何元素之间插入延迟，并以秒为单位设置延迟时间。

## People

连接到 Infobip People 解决方案，该解决方案允许您的聊天机器人使用您针对客户个人资料存储的现有信息，查找客户所需的数据。这些信息可以在客户旅程中使用，并通过更新人员档案来更新。

通过连接到人员，您还可以在找不到配置文件时更新和创建新的客户配置文件。这使您可以轻松捕获和存储信息。

将“人员”元素添加到对话框中，并选择您需要系统在此阶段执行的操作。您可以根据情况选择去查找信息或创建/更新配置文件。

[设置与 People 集成的聊天机器人](https://www.infobip.com/docs/answers/integrations#people)

## To Agent

当存在聊天机器人无法管理的复杂问题或请求时，使用 **To Agent**元素将聊天转移给人工代理。

示例：聊天机器人未设计或训练来回答特定问题，或者聊天机器人无法理解最终用户的消息。

您必须启用以下选项之一才能使用此选项。

- Infobip [对话](https://www.infobip.com/docs/conversations) 解决方案
- Infobip [Conversations API解决方案与](https://www.infobip.com/docs/conversations-api)[外部联络中心解决方案](https://www.infobip.com/docs/answers/integrations/external-contact-center-solutions)集成 。

当您重定向到客服人员时，客服人员将接管聊天。聊天内容无法发送回聊天机器人。

### Tags

向对话添加标签，以将支持请求或聊天路由到对话中的正确队列。 

示例：如果您添加标签 _account_management_，请求将路由到 对话中的_account_management_ 队列。

仅当您使用对话解决方案时，标签才适用。

#### 创建标签

1. 在**“所有标签”**部分中，单击**“创建标签”**。  
    [![创建标签](https://www.infobip.com/docs/images/uploads/articles/answers-elements-to-agent-create-tag-1.png)](https://www.infobip.com/docs/images/uploads/articles/answers-elements-to-agent-create-tag-1.png)/li>
2. 输入标签的名称。  
    [![为标签添加名称](https://www.infobip.com/docs/images/uploads/articles/answers-elements-to-agent-create-tag-2.png)](https://www.infobip.com/docs/images/uploads/articles/answers-elements-to-agent-create-tag-2.png)

#### 为对话添加标签

在 **“所有标签”** 部分中，选择一个或多个标签以添加到对话中。选定的标签显示在**选定的对话标签**部分。

[![向对话添加标签](https://www.infobip.com/docs/images/uploads/articles/answers-elements-to-agent-add-tag.png)](https://www.infobip.com/docs/images/uploads/articles/answers-elements-to-agent-add-tag.png)

## To Dialog

当您准备好在用户到达对话的这一点时将用户转到另一个对话时使用此元素。它可以是旨在结束对话的对话，也可以只是基于最终用户要求新信息的不同意图而转换到新对话。

对于 [意图驱动的](https://www.infobip.com/docs/answers/chatbot-types) 聊天机器人，您可以选择将哪些属性转移到其他对话。使用 **共享属性** 选项启用属性选择器。

您可以添加已设置的任意数量的属性，但请务必考虑意图中使用的属性以及具有可传输值的属性。一旦选择，该属性就不再可选。

## User input

通过获取用户响应的值并对聊天机器人应用进一步的逻辑来使用用户输入。有几种方法可以设置用户输入逻辑：

- 关键字识别
- 自然语言处理
- 重复和回退

**您需要在“关键字”** 选项卡中添加并设置关键字 。您的聊天机器人将识别与您配置的关键字匹配的用户输入的关键字。这允许您根据用户的响应将对话框路由到下一步。

使用 **+添加** 选项将新关键字添加到用户输入元素，然后从现有关键字的下拉列表中进行选择。

除了使用关键字映射来指导对话外，您还可以启用 NLP 引擎来处理输入，以防未输入正确的关键字或关键字同义词。

[您可以在高级选项 - NLP](https://www.infobip.com/docs/answers/advanced-options)下了解有关 NLP 引擎如何处理数据的更多信息 。

聊天机器人总是需要设置逻辑，以防它们不知道如何处理响应请求。第一个选项是要求用户再次尝试回答，然后再将其转到后备选项。配置 **重复** 设置以确定他们可以再次尝试回答的次数以及与之相关的任何文本。

**链接预览** 用于在聊天中显示支持此功能的频道的预览。

最后，配置后备选项，以便聊天机器人知道在用户输入不成功的情况下该怎么做。使用后备，您可以将用户路由到另一个对话，或者如果您有支持中心，则可以让他们与人工联系。

## Webhook

Webhook 是网页中用户定义的 HTTP 回调，根据特定和定义的事件触发，例如当用户需要提供授权标准时。它们通常用于实时通知和数据同步。

当触发器发生时，Webhook 会注册事件，收集所需的数据，并将其发送回 HTTP 请求中指定的 URL。使用上面的示例，触发事件将要求用户提供授权标准以及您希望将该信息发送到的位置。

在 Answers 中，webhook 通常跟在 **API** 元素之后，您可以使用 API 元素将 webhook URL 发送到外部系统。一旦请求的数据或条件可用，webhook 目标 URL 就会发回请求。然后 webhook 保存响应并将其保存到定义的属性中。

**首先** 定义 webhook URL 类型以将 **sessionID** 用作参数 或在请求正文**中** 使用它 。Webhook  **URL** 是预定义的， 不应 更改，根据 webhook URL 类型，它会自行更新

**将方法** 设置 为 **POST** 或 **GET** ，然后定义将保存值 **的属性。** 如果使用 **主体参数 sessionID，** 则只能将其与 **POST** 方法一起使用，并且需要定义 **sessionID****主体参数** 名称将自动添加到 **Webhook URL**中。

如果您使用 **POST方法，您可以从****属性**下拉列表 中选择一个属性，在这种情况下，您需要定义的是保存该值的参数的路径。  

路径是 JSON 文件层次结构中该属性值所在的位置，级别由点“.”分隔的条目数定义。

每个端点的最大吞吐量为每秒 100 个请求。每个 IP 每个端点的最大吞吐量为每秒 50 个请求。

如果使用 **GET** 方法，并且您想要将值保存到属性，请从 **“属性”** 下拉列表中选择该属性，并定义 包含所需值的**查询参数。** 

Webhook 请求代码：

- **200**  - 成功
- **40401**  - 失败。没有会话
- **40026**  - 不成功。会话无法处理请求

**_示例：Webhook 元素与 API 元素结合使用_**

_您需要使用外部授权服务授权您的最终用户，因此您使用 **API** 元素向包含 webhook URL 的服务发送授权请求，然后等待响应_

_Answers 收到带有用户授权 URL 的响应，您将该 URL 存储在 Answers 的一个属性中（属性在 API 元素中定义）_

_下一步是要求用户通过您从外部授权服务收到的 URL 对自己进行授权。您可以使用 **Text** 元素将授权链接发送给用户_

_用户对自己进行授权后，Webhook 会收到来自外部授权服务的请求，其中包含用户已授权的令牌，并且该令牌也存储在属性中（如果需要，可用于进一步的授权需要）_

_为了接收用户授权并保存令牌，您可以使用 **Webhook** 元素，在其中定义了要保存令牌值的方法和属性_