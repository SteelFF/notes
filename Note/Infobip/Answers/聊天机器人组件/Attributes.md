属性是可以捕获和存储信息的数据对象。

以下是使用属性的一些方法：

- 从最终用户消息中捕获信息。示例：电话号码
- 个性化您发送给最终用户的消息。示例：您可以说“Hello, {firstName}”，而不是说“Hello”，其中firstName 是一个属性。
- 为对话添加上下文。示例：您可以说“Are you contacting us about order number {orderNumber}?”，而不是说“Are you contacting us about your order?”，其中 orderNumber 是一个属性。
- 从 Answers 元素中捕获有效负载，例如快速回复和回复按钮。有关详细信息，请参阅[操作方法](https://www.infobip.com/docs/answers/how-to#save-postback-values-from-buttons-and-quick-replies)部分。
- 根据属性值引导聊天机器人流程。_示例：如果属性值包含单词booking_ ，则使用[Conditions](https://www.infobip.com/docs/answers/chatbot-elements/core-elements#conditions)元素将聊天机器人流程引导至 Booking 对话框。
- 与其他解决方案共享信息。示例：通过 Answers 从最终用户那里获取更新的电子邮件地址，将其保存在 Answers 中的一个属性中，将此属性传递给[People](https://www.infobip.com/docs/answers/integrations/people#attributes)，并在 People 中更新 Person 个人资料中的电子邮件地址。

## 属性如何工作

以下示例演示如何使用属性来捕获最终用户的响应并根据响应引导流程。

在 Attribute 元素中，product_name 属性捕获并保存最终用户的消息。在“条件”元素中，聊天机器人检查属性值是否满足特定条件，并相应地引导流程。

[![如何使用属性](https://www.infobip.com/docs/images/uploads/articles/answers-attributes-example.png)](https://www.infobip.com/docs/images/uploads/articles/answers-attributes-example.png)

最终用户的消息是“我想了解 Answers 中的功能”。product_name 属性捕获并保存此消息。在 Conditions 元素中，满足条件“{product_name} contains Answers”。因此，聊天机器人将流程定向到 Answers 对话框。

[![如何使用属性 - 在模拟器中测试](https://www.infobip.com/docs/images/uploads/articles/answers-attributes-example-simulator.png)](https://www.infobip.com/docs/images/uploads/articles/answers-attributes-example-simulator.png)

## 属性类型

### 标准属性

标准属性是您可以创建并与任何类型的聊天机器人一起使用的属性。这些属性出现在[“属性”选项卡](https://www.infobip.com/docs/answers/chatbot-structure/attributes#attributes-tab)中。

#### 标准属性的数据类型

每个标准属性都必须是预定义的数据类型。为避免错误，请使用正确的数据类型。示例：要保存名片中的联系人信息，请使用联系人数据类型。

根据渠道，标准属性的数据类型可以是入站或出站。如果要在发送给最终用户的消息中使用该属性，请使用出站数据类型。如果您希望使用该属性来捕获来自最终用户的响应，或者当您希望最终用户向您发送文件、图像或视频等附件时，请使用入站数据类型。

标准属性可以具有以下数据类型之一。

- **Text**
- **Number** : 用数字写的数字
- **Phone number**
- **Boolean**
- **Date**：入站属性类型。日期以 YYYY-MM-DD 格式存储。如果最终用户以不同的格式发送日期，则会将其视为无效输入。
- **Send location**：来自 Google 地图的位置。以图钉或经纬度的形式共享。
- **URL**
- **Email**
- **List**
- **JSON**
- **XML**
- **XML list**
- **Audio**
- **Contacts**
- **Document**
- **Image**
- **Receive location**：来自 Google 地图的位置。以图钉或经纬度的形式共享。有关如何在模拟器中共享位置的信息，请参阅[模拟](https://www.infobip.com/docs/answers/simulate-the-conversation#simulate-end-user-location-simulate-end-user-interaction)文档。
- **Video**
- **Message**：入站属性类型。当您不知道最终用户可能发送的文件类型时，请使用此属性类型接收文件。示例：最终用户可以发送图像或文档。请参阅[此示例](https://www.infobip.com/docs/answers/code-in-coding-element#use-message-attribute-type-to-receive-multiple-file-types)以了解如何使用 Message 类型的属性来接收文件以及如何使用 Code 元素来标识文件类型。
- **WhatsApp order response**（仅限 WhatsApp）：当您使用 **单一产品** 或多 **产品** 元素时，包含订单详细信息。

|笔记| |
|---|---|
|您不能使用[自定义属性类型](https://www.infobip.com/docs/answers/ai-chatbots/intents#type-named-entity-recognition-attributes)作为标准属性的数据类型。| |

下表显示了每个通道支持的入站消息。

| |Message|Video|Location|Image|Document|Contact|Audio|
|---|---|---|---|---|---|---|---|
|Apple 商务信息|**✓**|||||||
|谷歌的商业讯息|**✓**|||||||
|Instagram 消息|**✓**|||||||
|线|**✓**|||||||
|在线聊天|**✓**|||||||
|信使|**✓**|||||||
|无线电控制系统|**✓**|**✓**|**✓**|**✓**|**✓**||**✓**|
|短信|**✓**|||||||
|电报|**✓**|**✓**||**✓**|**✓**||**✓**|
|Twitter 直接消息||||||||
|Viber 商务消息|**✓**|||||||
|Viber 机器人|**✓**|||||||
|WhatsApp|**✓**|**✓**|**✓**|**✓**|**✓**|**✓**|**✓**|

有关创建和使用标准属性的信息，请参阅[操作方法](https://www.infobip.com/docs/answers/chatbot-structure/attributes#how-to)部分。

### 固定属性

固定属性是包含最终用户基本信息的数据对象。它们用于 Answers 中的后台进程。

您无法创建固定属性。这些属性未在[“属性”选项卡](https://www.infobip.com/docs/answers/chatbot-structure/attributes#attributes-tab)中列出。

所有聊天机器人都提供固定属性。在 Answers 元素中使用这些属性。

API 元素不支持固定属性。要在 API 元素中使用固定属性的值，请参阅[操作方法](https://www.infobip.com/docs/answers/chatbot-structure/attributes#use-a-fixed-attribute-in-an-api-element-how-to)部分。

Answers 具有以下固定属性。

- **authenticated**：一个布尔属性，根据最终用户的身份验证状态设置为 True 或 False。身份验证可以配置为过期。因此，即使此属性设置为 True，它也可以在身份验证过期后更改为 False。

- **conversationId**：用于标识从 Answers 中的聊天机器人传输到[Conversations](https://www.infobip.com/docs/conversations)或[外部联系中心解决方案](https://www.infobip.com/docs/answers/integrations/external-contact-center-solutions)中的座席的对话的 ID 。

- **displayName**:（仅限 WhatsApp 和 Google 的 Business Messages）：最终用户设置的显示名称，用于在频道的应用程序中标识自己。在 WhatsApp 中，这是向尚未保存发件人电话号码的人显示的名称。

- **endUserDestination**：存储最终用户通道使用的标识。每个渠道以不同的方式识别最终用户。示例：Messenger 通道使用 Facebook ID，实时聊天通道使用浏览器的会话 ID。

- **gbmEntryLocation**:（仅限 Google 的 Business Messages）：存储最终用户发起对话时的位置。对于每个会话，Answers 都会从 Google 获取此信息。使用此信息指导最终用户进行与位置相关的查询。示例：聊天机器人可以使用位置信息来识别距离最终用户最近的服务中心。

- **gbmNearPlaceID**:（仅限Google's Business Messages）：根据最终用户查询中的位置识别并存储最近的位置。  
    使用此属性可以根据位置设置特定的对话路由。示例：如果最终用户想要萨格勒布的服务中心位置，该属性将保存距离萨格勒布最近的服务中心的位置。

- **lastReceivedMessage**：捕获聊天机器人收到的最后一条入站消息。如果您希望最终用户使用预定义消息开始对话，请使用此属性。将属性值与关键字进行比较并相应地对对话框进行分支。示例：您提供折扣，并且希望最终用户的消息包含[促销代码](https://www.infobip.com/docs/answers/code-in-coding-element#branch-dialogs-according-to-incoming-promo-messages)。  
      
    您还可以将此属性值传递给 People 并在其他 Infobip 解决方案（例如 Broadcast）中使用它。  
      
    属性值以 JSON 格式保存。示例：如果最终用户发送的最后一条消息是“Thanks for the update.”，则属性值为 该属性`{``"type"``:``"TEXT"``,``"content"``:``"Thanks for the update"``}`  
      
    适用于收到的所有 MO 属性值，文本消息除外。对于短信，请使用**lastReceivedTextMessage**。

- **lastReceivedTextMessage**：捕获聊天机器人收到的最后一条入站文本消息。如果您希望最终用户使用预定义消息开始对话，请使用此属性。将属性值与关键字进行比较并相应地对对话框进行分支。示例：您提供折扣，并且希望最终用户的消息包含[促销代码](https://www.infobip.com/docs/answers/code-in-coding-element#branch-dialogs-according-to-incoming-promo-messages)。  
      
    您还可以将此属性值传递给 People 并在其他 Infobip 解决方案（例如 Broadcast）中使用它。  
      
    属性值是文本格式。示例：如果最终用户发送的最后一条消息是“Thanks for the update.”，则该属性值为消息的内容，即`"Thanks for the update".`
- **phoneNumber**：从最终用户的消息中捕获电话号码。该属性的类型为 Number，范围为 Global。  
    此属性仅适用于使用 MSISDN 作为目标的通道。它可用于以下渠道：
    - 无线电控制系统
    - 短信
    - WhatsApp
    - 维伯

- **sessionId**：最终用户的唯一标识符。示例：E32DCDD3056F21C9FBA26BBD84FC079F5FFD348207CC5136DE672FD091A77794

- **sourceId**（仅限 WhatsApp - 抢先体验）：存储 Facebook 广告或帖子的唯一 ID 的文本属性，用于将最终用户引导至聊天机器人。示例：615533673545722。如果此属性为空，则表明最终用户没有通过 Facebook 广告或帖子联系聊天机器人，而是使用了不同的入口点。[该属性是WhatsApp 推荐](https://www.infobip.com/docs/whatsapp/message-types#referral-inbound-messages)的参数之一。使用此属性以及**sourceType**和**sourceUrl**属性来进行路由、验证和个性化。如需了解更多信息，请参阅[获取 WhatsApp 推荐信息](https://www.infobip.com/docs/answers/chatbot-structure/attributes#get-whatsapp-referral-information-how-to)部分。

- **sourceType**（仅限 WhatsApp - 抢先体验）：指示聊天机器人入口点的文本属性。有效值如下：
    - POST：入口点是 Facebook 帖子。
    - AD：入口点是 Facebook 广告。
    - 未知：入口点不是 Facebook 帖子或 Facebook 广告。  
          
        此属性是 WhatsApp 推荐的参数之一。**将此属性与sourceId**和**sourceUrl**属性一起用于路由、验证和个性化。如需了解更多信息，请参阅[获取 WhatsApp 推荐信息](https://www.infobip.com/docs/answers/chatbot-structure/attributes#get-whatsapp-referral-information-how-to)部分。

- **sourceUrl**（仅限 WhatsApp - 抢先体验）：存储 Facebook 广告或帖子 URL 的文本属性，可将最终用户引导至聊天机器人。示例：https://fb.me/7OdfE45gk。如果此属性为空，则表示最终用户未通过 Facebook 广告或帖子联系聊天机器人，而是使用了不同的入口点。此属性是 WhatsApp 推荐的参数之一。使用此属性以及 **sourceId** 和 **sourceType** 属性来进行路由、验证和个性化。如需了解更多信息，请参阅[获取 WhatsApp 推荐信息](https://www.infobip.com/docs/answers/chatbot-structure/attributes#get-whatsapp-referral-information-how-to)部分。

- **uniqueSessionId**：会话的唯一标识符。[使用此标识符在 Answers分析](https://www.infobip.com/docs/answers/analytics)中查找会话。示例：6d554323-2e44-4c9d-8ff0-5ee62931165e

- **WhatsAppIdentityAcknowledged**（仅限 WhatsApp）：指示最终用户的身份是否正确。该值可以是 True 或 False。此属性用作 WhatsApp[身份更改](https://www.infobip.com/docs/whatsapp/additional-functionality#identity-change)功能的一部分。

- **WhatsAppIdentityHash**（仅限 WhatsApp）：包含特定最终用户身份检查的唯一代码。此属性用作 WhatsApp[身份更改](https://www.infobip.com/docs/whatsapp/additional-functionality#identity-change)功能的一部分。

- **whatsAppIdentityCreated**（仅限 WhatsApp）：提供身份更改数据创建时间的时间戳。此属性用作 WhatsApp[身份更改](https://www.infobip.com/docs/whatsapp/additional-functionality#identity-change)功能的一部分。

### 命名实体识别属性

[命名实体识别](https://www.infobip.com/glossary/ner) (NER) 是自然语言处理 (NLP) 的一部分。NER 的目标是识别和分类最终用户消息中的信息（实体）。

有关更多信息，请参阅[AI](https://www.infobip.com/docs/answers/ai-chatbots/intents#named-entity-recognition-attributes)部分。

## 属性选项卡

有关自定义属性类型和 NER 属性选项卡的信息，请转到[AI](https://www.infobip.com/docs/answers/ai-chatbots/intents#the-user-interface)部分。

要查看标准属性列表，请转至**属性**选项卡 >**属性**。每个属性都有以下字段：

- **名称**：属性的名称
- **类型**：标准属性类型
- **范围**：可以是以下之一：
    - 本地：该属性特定于对话框。
    - 全局：该属性可以在聊天机器人内的多个对话框中使用。
- **对座席可见**：指示当聊天从聊天机器人转移到座席时，属性值对座席是否可见。  
    仅当您使用 Infobip Conversations 解决方案时，此字段才适用。如果禁用此字段，客服人员将无法在对话的上下文卡中看到属性值，但可以在聊天记录中看到该值。
- **敏感值**：指示该属性是否包含敏感数据。示例：该属性用于身份验证，包含用户名或密码。如果选择此字段，则属性值在临时存储在答案数据库中时会被加密。

[![属性选项卡](https://www.infobip.com/docs/images/uploads/articles/answers-attributes-tab.png)](https://www.infobip.com/docs/images/uploads/articles/answers-attributes-tab.png)

## 属性验证

本节提供有关如何验证属性名称和值的详细信息。

### 属性名称的验证

以下验证应用于属性名称：

- 属性名称可以包含字母、数字、下划线和扬抑符。
- 属性名称不能包含空格、破折号或其他特殊字符。示例：客户编号、客户编号、客户编号
- 属性名称不能以数值开头。相反，请使用字母或下划线。示例：使用“_1daypromo”代替“1daypromo”。
- 如果属性名称包含多个单词，请使用下划线。示例：使用“customer_number”代替“客户编号”。

### 属性值的验证

**在Conditions**元素中使用属性时  ，它们将按照表中所示进行验证。有关 NER 属性验证的信息，请参阅[AI](https://www.infobip.com/docs/answers/ai-chatbots/intents#type-named-entity-recognition-attributes)部分。

|**Attribute type**|**Condition**|**Validation for the attribute value**|
|---|---|---|
|Text|is \| is not \| contains \| starts with \| ends with \| is null \| is not null|Can contain letters, numbers, or space|
|Phone Number|is \| is not \| contains \| starts with \| ends with \| is null \| is not null|Can contain only numbers|
|Boolean|is \| is not \| is null \| is not null|True, False|
|Date|is equal to \| is not equal to \| is before \| is before or equal to \| is after \| is after or equal to \| is null \| is not null|YYYY-MM-DD, only numbers|
|Location|is equal to \| is not equal to \| is null \| is not null||
|URL|is equal to \| is not equal to \| is null \| is not null|Validated using URL validator|
|Email|is \| is not \| contains \| starts with \| ends with \| is null \| is not null|Validated using email address validator|
|**Inbound attribute type**|**Condition**|**Validation for the attribute value**|
|MO Audio|is null \| is not null|Accepts audio files. Valid file format depends on the channel|
|MO Contacts|is null \| is not null|Accepts address, name, birthday, phone, organization, and URL|
|MO Document|is null \| is not null|Accepts documents. Valid file format depends on the channel|
|MO Image|is null \| is not null|Accepts image files. Valid file format depends on the channel|
|MO Location|is null \| is not null|Accepts latitude and longitude of a location|
|MO Video|is null \| is not null|Accepts video files. Valid file format depends on the channel|

## 指南和最佳实践

- 您可以在配置对话框之前或期间创建属性。
- 属性名称不能包含空格。相反，请使用连接符，例如下划线或连字符。
- 属性名称必须以字母或下划线开头。
- 您可以创建无限数量的属性。

## 如何

### 创建属性

要创建 NER 属性，请参阅[AI](https://www.infobip.com/docs/answers/ai-chatbots/intents#create-a-ner-attribute-and-add-to-an-intent-how-to)部分。

要创建标准属性，请执行以下操作：

1. 在 Infobip  [Web 界面](https://portal.infobip.com/)上，转到 **Answers**  >  **Chatbots** 并导航到您的聊天机器人。
2. 转到 **属性** 选项卡 > **属性** 部分。
3. 单击 **添加属性**。
4. 在 **名称** 字段中，输入属性的名称。请参阅[属性名称部分的](https://www.infobip.com/docs/answers/chatbot-structure/attributes#validation-of-attribute-names-validation-for-attributes)[准则](https://www.infobip.com/docs/answers/chatbot-structure/attributes#guidelines-and-best-practices)和 验证。[](https://www.infobip.com/docs/answers/chatbot-structure/attributes#validation-of-attribute-names-validation-for-attributes)
5. 在 **类型** 字段中，选择标准数据类型。如果要在条件元素中使用属性，请参阅[属性值验证](https://www.infobip.com/docs/answers/chatbot-structure/attributes#validation-of-attribute-values-validation-for-attributes) 部分以确保选择正确的类型。
6. 在 **范围** 字段中，选择以下选项之一：
    - 本地：该属性特定于对话框。
    - 全局：该属性可以在聊天机器人内的多个对话框中使用。
7. 在 **对座席可见** 字段中，选择当聊天从聊天机器人传输到座席时属性值是否必须对座席可见。  
    仅当您使用 Infobip Conversations 解决方案时，此字段才适用。如果禁用此字段，客服人员将无法在对话的上下文卡中看到属性值，但可以在聊天记录中看到该值。
8. 在 **敏感值** 字段中，选择属性是否包含敏感数据。示例：该属性用于身份验证并包含用户名或密码。如果选择此字段，则属性值在临时存储在答案数据库中时会被加密。
9. 使用复选标记**✓**保存属性或使用**x**标记删除属性。

### 编辑属性

执行以下操作之一：

- 单击属性名称以编辑字段。
- 单击属性对应的菜单并选择**Edit**。

[![编辑或删除属性](https://www.infobip.com/docs/images/uploads/articles/answers-attributes-edit-delete.png)](https://www.infobip.com/docs/images/uploads/articles/answers-attributes-edit-delete.png)

### 删除属性

单击属性对应的菜单并选择**删除**。

[![编辑或删除属性](https://www.infobip.com/docs/images/uploads/articles/answers-attributes-edit-delete.png)](https://www.infobip.com/docs/images/uploads/articles/answers-attributes-edit-delete.png)

您需要确认该操作。

[![删除答案中的属性](https://www.infobip.com/docs/images/uploads/articles/delete-attribute_1.png)](https://www.infobip.com/docs/images/uploads/articles/delete-attribute_1.png)

### 在 API 元素中使用固定属性

API 元素不支持固定属性。按照此解决方法可以使用 API 元素中固定属性的值，

1. 创建一个新属性。
2. 在代码元素中，将固定属性的值保存在新属性中。
3. 将新属性传递给 API 元素。

### 获取 WhatsApp 推荐信息

您可以在 Facebook 广告和帖子中使用 WhatsApp 号召性用语功能，这样当最终用户点击广告或帖子时，他们可以通过 WhatsApp 联系您的聊天机器人。

在您的聊天机器人中，您可以使用固定属性来接收和存储有关这些入口点的信息。

- 使用**sourceType**固定属性来识别最终用户是通过 Facebook 广告、Facebook 帖子还是其他入口点联系聊天机器人。 
- 使用**sourceId**固定属性获取 Facebook 广告或帖子的唯一 ID。
- 使用**sourceUrl**固定属性获取 Facebook 广告或帖子的 URL。

有关这些属性的更多信息，请参阅 [[#固定属性]] 部分。

然后，您可以使用这些属性中的信息，如以下示例所示：

- 根据入口点，您可以将最终用户路由到相关对话框。
- 如果入口点是 Facebook 广告或帖子，您可以从广告或帖子中识别最终用户的意图。您无需询问最终用户联系聊天机器人的目的。
- 使用 **[People](https://www.infobip.com/docs/answers/chatbot-elements/core-elements#people)** 元素将属性值保存在 [People](https://www.infobip.com/docs/people)中。
- 使用指标获取分析。示例：您可以确定通过特定 Facebook 广告联系您的聊天机器人的最终用户数量。

**示例**：您的 WhatsApp 聊天机器人有以下入口点：

- 您网站上的二维码
- 针对手机的 Facebook 帖子，页面帖子 ID 为 123
- 广告 ID 为 456 的笔记本电脑 Facebook 广告

最终用户单击Facebook 帖子中的**发送消息**WhatsApp 号召性用语按钮。WhatsApp 应用程序打开，开始与您的聊天机器人对话。自动添加一条预定义的消息，它指的是 Facebook 帖子。最终用户将此预定义消息发送到您的聊天机器人。

聊天机器人收到此消息和 WhatsApp 推荐信息。

WhatsApp 推荐信息保存在固定属性中。

- 来源ID：123
- 来源类型：POST
- 来源网址：https://fb.me/7OdfE45gk

聊天机器人配置为根据入口点路由对话。

- 如果最终用户通过手机的 Facebook 帖子联系您的聊天机器人，聊天机器人会将最终用户路由到“手机”对话框。
- 如果最终用户通过 Facebook 笔记本电脑广告联系您的聊天机器人，聊天机器人会将最终用户路由到笔记本电脑对话框。
- 如果最终用户使用不同的入口点，聊天机器人将不知道最终用户的意图。因此，聊天机器人将最终用户引导至主菜单，他们需要在其中选择一个选项来继续对话。

在这种情况下，最终用户单击了 Facebook 帖子。因此，聊天机器人将最终用户路由到“移动电话”对话框，而无需通过主菜单。最终用户不会丢失对话的上下文，因为他们可以直接从关于手机的帖子转到聊天机器人中关于手机的对话。

[![基于入口点的分支](https://www.infobip.com/docs/images/uploads/articles/answers-channels-whatsapp-referral-example-branch-entry-point.png)](https://www.infobip.com/docs/images/uploads/articles/answers-channels-whatsapp-referral-example-branch-entry-point.png)聊天机器人将属性值传递给人员。

[![将属性保存在人员中](https://www.infobip.com/docs/images/uploads/articles/answers-channels-whatsapp-referral-example-update-people.png)](https://www.infobip.com/docs/images/uploads/articles/answers-channels-whatsapp-referral-example-update-people.png)

然后，您可以获得有关点击 Facebook 帖子的最终用户数量的指标。