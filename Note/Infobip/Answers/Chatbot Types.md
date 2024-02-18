根据用例和所需功能，聊天机器人可分为以下主要类别：

- 基于规则的聊天机器人
- 人工智能 (AI) 聊天机器人

## 基于规则的聊天机器人

[基于规则的](https://www.infobip.com/glossary/keyword-chatbot) 聊天机器人基于预定义的规则和关键字识别工作。这些聊天机器人用于帮助最终用户进行简单的客户服务查询和常见问题 (FAQ)。

基于规则的聊天机器人也称为关键字驱动、基于流或决策树的聊天机器人。

聊天机器人功能包括以下内容：

- 定义和识别关键词
- 为最终用户提供一系列选项
- 将用户响应存储在属性中
- 定义规则或条件

基于规则的聊天机器人不使用人工智能 (AI)，但是，设计良好的基于​​规则的聊天机器人可以像 AI 聊天机器人一样强大。

### 例子

有关基于规则的聊天机器人的示例，请参阅以下部分：

- [创建基于规则的聊天机器人](https://www.infobip.com/docs/answers/create-chatbot/create-keyword-chatbot)
- [教程](https://www.infobip.com/docs/use-cases?child:use_case_related_products=answers)

## 人工智能聊天机器人

人工智能 [(AI) 聊天机器人](https://www.infobip.com/glossary/ai-chatbot) 使用机器学习和自然语言处理来模拟类人对话。有关 AI 聊天机器人的更多信息，请参阅 [AI 聊天机器人](https://www.infobip.com/docs/answers/ai-chatbots) 部分。

## 基于规则的聊天机器人和人工智能聊天机器人之间的区别

|基于规则的聊天机器人|人工智能聊天机器人|
|---|---|
|基于关键字的作品|基于机器学习和自然语言处理的作品|
|为最终用户查询提供预定义的响应|从最终用户消息（NER 属性）中提取实体并使用这些实体继续对话。|
|最终用户获得与聊天机器人交谈的体验。|因为 AI 聊天机器人理解自然语言，它可以模拟人类交互并提供更好的对话体验。|
|当聊天机器人结构不复杂时使用。  <br>基于规则的聊天机器人遵循决策树结构。示例：如果最终用户需要转到特定对话框，他们需要从主菜单开始，然后浏览导致该对话框的所有子菜单。|当聊天机器人结构复杂时使用。  <br>最终用户无需遵循特定路径即可执行所需操作。示例：最终用户键入他们的查询。如果聊天机器人训练有素，用户可以直接进入相关对话框。|

## 基于功能的聊天机器人类型

聊天机器人还可以根据其功能进行分类，如下所示：

- Secured
- Integrated
- Multi-lingual

### 安全

如果您的聊天机器人处理敏感信息，您可以保护聊天机器人、其内容以及最终用户在与聊天机器人对话期间捕获的信息。

使用以下一个或多个选项为特定属性、对话甚至整个聊天机器人添加安全层。

#### 已验证的对话

聊天机器人中的某些对话可能包含敏感数据。示例：获取或更新个人信息的对话框。只有经过身份验证的最终用户才能访问此类对话框。 

使用身份验证对话框来验证最终用户的身份。当最终用户进入受保护的对话框时，将他们重定向到身份验证对话框。身份验证对话框根据 Infobip People 解决方案或第三方应用程序验证最终用户的数据。如果身份验证成功，最终用户才可以访问受保护的对话框。

对话框中的锁图标表示该对话框受到保护。

![认证流程](https://www.infobip.com/docs/images/uploads/articles/Untitled_Diagram.png)

当最终用户通过身份验证时， **经过身份验证的** [固定属性](https://www.infobip.com/docs/answers/design-chatbot#fixed-attributes-attributes) 设置为 True。

您可以配置身份验证，使其在特定时间段后过期。这段时间过后，最终用户需要再次对自己进行身份验证。当身份验证过期时， **经过身份验证的** 固定属性设置为 False。示例：认证超时时间设置为 5 分钟。在对自己进行身份验证后，最终用户让聊天保持打开状态，并在 5 分钟后重新加入。由于身份验证已过期，最终用户需要再次对自己进行身份验证。

您无法通过身份验证限制“默认”和“会话过期”对话框。

您只能  向聊天机器人添加一个**身份验证对话框。**您不能向此对话框添加意向。

使用 [To Dialog](https://www.infobip.com/docs/answers/chatbot-elements/channels) 元素将最终用户路由到身份验证对话框。有关创建身份验证对话框的分步说明，请参阅 [创建安全聊天机器人](https://www.infobip.com/docs/answers/create-chatbot/create-secured-chatbot) 部分。

#### 暂停

使用会话过期对话框，以便聊天机器人会话在定义的时间段后过期。

#### 苹果认证

如果您的聊天机器人使用[Apple Messages for Business](https://www.infobip.com/docs/apple-messages-for-business)渠道，请使用[Apple 身份验证](https://www.infobip.com/docs/answers/chatbot-elements/channels?ch=apple-messages-for-business#apple-authentication)。这使您能够在 Messages for Business 应用程序中针对 Infobip People 解决方案或第三方应用程序对最终用户进行身份验证。

#### 通过API认证

使用[API](https://www.infobip.com/docs/answers/chatbot-elements/core-elements#api)元素配置授权标头和响应设置，以从 Infobip People 解决方案或第三方应用程序获取用户信息。

#### 敏感属性

使用属性存储潜在敏感数据时，请使用[敏感值](https://www.infobip.com/docs/answers/chatbot-structure/attributes#attributes-tab)字段来保护属性。如果选择此字段，则属性值在临时存储在答案数据库中时会被加密。

示例：该属性用于身份验证，包含用户名或密码。

#### 定制代码

使用[代码](https://www.infobip.com/docs/answers/chatbot-elements/core-elements#code)元素自定义聊天机器人和聊天内容的安全性。

### 多种语言

使用 Answers 支持的任何语言创建基于规则的聊天机器人。人工智能聊天机器人支持的语言数量有限。请参阅[聊天机器人设置中的](https://www.infobip.com/docs/answers/chatbot-settings)**语言**部分，了解支持的语言列表。[](https://www.infobip.com/docs/answers/chatbot-settings)

### 融合的

使用 Answers 渠道元素可以与其他 Infobip 产品和外部解决方案[集成。](https://www.infobip.com/docs/answers/integrations)

例子：

- [代码](https://www.infobip.com/docs/answers/chatbot-elements/core-elements#code)
- [应用程序编程接口](https://www.infobip.com/docs/answers/chatbot-elements/core-elements#api)
- 网络钩子
- Infobip[人员](https://www.infobip.com/docs/answers/integrations/people)解决方案
- Infobip[对话](https://www.infobip.com/docs/answers/integrations/conversations)解决方案
- [苹果认证](https://www.infobip.com/docs/answers/chatbot-elements/channels?ch=apple-messages-for-business#apple-authentication)
- 支付系统