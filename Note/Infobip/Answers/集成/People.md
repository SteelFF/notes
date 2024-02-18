连接到 Infobip 客户数据平台[People](https://www.infobip.com/docs/people)，以检索和更新有关最终用户的信息。在人员中，此信息存储在人员配置文件中。

您可以执行以下操作：

- 当最终用户与您的聊天机器人交互时，您可以从对话中捕获相关信息并将此信息存储在个人资料中。
- 您可以从最终用户的个人资料中检索有关最终用户的信息。

当您启用“答案”时，默认情况下会启用“人员”。

Answers 中可用的人员功能取决于您的 Answers[计划](https://www.infobip.com/docs/answers#plans)。

|   |   |   |   |   |
|---|---|---|---|---|
||**免费试用**|**开始**|**生长**|**规模**|
|**People 的基本功能，例如个人资料、文件上传、标签、细分和自定义字段**|支持的|支持的|支持的|支持的|
|**People 的高级功能，例如自定义事件、个人资料评分和计算字段**|支持的|不支持|支持的|支持的|

## 属性

使用属性从人员中添加或检索有关最终用户的信息。使用此信息来个性化您对最终用户的响应。

属性类型如下：

- [标准](https://www.infobip.com/docs/people/manage-data#standard-attributes)：标准字段中的信息。示例：联系信息
- [自定义](https://www.infobip.com/docs/people/manage-data#custom-attributes)：无法作为标准字段提供的自定义信息。示例：订阅的结束日期
- [计算](https://www.infobip.com/docs/people/manage-data#computed-attributes)：通过自定义公式、配置文件评分和事件聚合获得的信息。使用这些属性执行以下操作：
    
    - 自定义您发送给最终用户的消息。示例：如果最终用户在过去 7 天内购买了产品，您可以询问他们“您是否就最近的订单与我们联系？”
    - 如果最终用户的个人资料得分较高，您可以优先考虑他们的请求
    
    您只能从人员中检索计算属性，而无法更新它们。

### 属性映射

使用自定义映射将 Answers 中的属性与 People 中的属性进行映射。示例：在 Answers 中，您可以将最终用户的位置存储在 Location_City 属性中，并将其映射到 People 中的 City 属性。

确保答案中的属性类型与人员中的属性类型兼容。示例：如果 Answers 中的属性类型为布尔值，则无法将其与 People 中的文本属性映射。

当您使用 **“获取信息”** 操作（人员到答案）时，您可以按如下方式映射属性：

|Attribute type in People|Attribute type in Answers|
|---|---|
|Text<br><br>Gender|Text|
|Decimal number<br><br>Whole number|Number|
|True/False|Boolean|
|Date<br><br>Date Time|Date|
|List of objects|List|

当您使用**创建或更新人员配置文件**操作（对人员的回答）时，您可以按如下方式映射属性：

|Attribute type in Answers|Attribute type in People|
|---|---|
|Text<br><br>Phone number<br><br>Location<br><br>URL<br><br>Email|Text|
|NER<br><br>MO|Text|
|Boolean|True/False|
|List|List of objects|

## 自定义事件

当最终用户与您的聊天机器人交互并执行操作时，您可以将此事件传递给 People。该事件在“人员”中保存为[自定义事件](https://www.infobip.com/docs/people/events#custom-events)。然后，您可以在最终用户再次与您的聊天机器人交互时使用此信息，或在其他 Infobip 解决方案中使用它。

示例：最终用户在与您的聊天机器人的会话期间购买了产品。将此事件保存为人员中的自定义事件。然后您可以执行以下操作：

- 使用该事件作为答案和对话中的上下文：最终用户在 3 天后再次联系您的企业。聊天机器人或代理可以在初始消息中提及购买。“我能帮您什么忙？您是否就最近购买的商品与我们联系？”
- 通过朋友圈中的 Flow 活动跟进最终用户：“根据您最近的购买情况，这里有一些您可能喜欢的产品。”
- 使用事件属性来填充人员中的[计算属性](https://www.infobip.com/docs/people/manage-data#computed-attributes)。您可以将计算属性用于用例，例如填充总生命周期支出、平均支出、最常见的请求和最后的答案对话框。然后，您可以使用这些属性来个性化您在“答案”中的沟通或改进“朋友圈”中的细分。
- 将个人资料标记为高优先级：下次最终用户联系您的公司时，您可以优先考虑他们。

### 过程

过程如下：

1. 在 Answers 中，最终用户执行一个操作，例如将产品保存到购物车。
2. 将此事件的每个相关属性保存在 Answers 属性中。示例：成功将产品添加到购物车；产品的标识符、名称和价格。
3. 将这些属性传递给 People，您可以在其中将它们保存在最终用户的 Person 配置文件中的[自定义事件中。](https://www.infobip.com/docs/people/events#custom-events)

按照每个部分中的说明进行操作。

#### 在 People 中创建自定义事件

1. 登录到 Infobip [Web 界面](https://portal.infobip.com/)。
2. 转到**人员**>**事件**>**自定义事件**。
3. 单击**新建事件定义**。
4. 添加**事件名称**和**描述**。示例：要保存最终用户将产品添加到购物车的事件，请创建事件AddedToCart。  
    [![在人员中创建事件](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-event-definition.png)](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-event-definition.png)
5. 单击**新建事件属性**。  
    [![更新事件定义](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-event_property-1.png)](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-event_property-1.png)
6. 输入事件**属性名称**。根据需要创建尽可能多的属性。示例：要保存将产品添加到购物车的事件，您可以创建产品成功添加到购物车 (addedCart)、产品标识符 (productID)、产品名称 (productName) 和价格的属性产品（产品价格）。  
    [![为事件创建属性](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-event_property-2.png)](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-event_property-2.png)  
    [![事件属性列表](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-event_property-3.png)](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-event_property-3.png)
7. 单击**保存**。

#### 向人们发送自定义事件

1. 转到**答案**>**聊天机器人**并打开您的聊天机器人。
2. 在**“属性”**选项卡中，执行以下操作：
    1. 创建要在其中保存事件属性的属性。示例：保存商品加入购物车事件，可以创建商品加入购物车成功属性（addedCart）、商品标识（productID）、商品名称（productName）、商品价格产品（产品价格）。  
          
        对于每个属性，请确保您在 People 中创建等效的事件属性。此外，请确保属性类型与您为 People 中的事件创建的事件属性[兼容。](https://www.infobip.com/docs/answers/integrations/people#attribute-mapping-attributes)  
        [![创建新属性](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-new-attribute.png)](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-new-attribute.png)
    2. 创建一个属性来标识人员配置文件。示例：客户联系人。
3. 创建一个对话框以将事件从 Answers 发送给人们。示例：已添加到购物车。  
    [![创建新对话框](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-new-dialog.png)](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-new-dialog.png)
4. 拖放[**自定义事件**](https://www.infobip.com/docs/answers/chatbot-elements/core-elements#custom-event)元素。
5. 在**自定义事件**元素中，找到要在其中保存事件的人员配置文件。请执行下列操作：
    1. 在**联系信息属性**字段中，输入包含客户联系信息的 Answers 属性。示例：客户联系人。
    2. 在**联系人信息属性类型**字段中，选择个人资料的标识符。示例：如果您想使用最终用户的电话号码来标识他们的个人资料，请使用属性类型电话号码。**将答案属性与联系人信息属性类型**  
          
        字段进行匹配时，答案支持以下字段值：电话号码、电子邮件、内部 ID 或外部 ID。如果要在人员中使用不同的标识符来关联自定义事件，请首先使用[**人员**](https://www.infobip.com/docs/answers/chatbot-elements/core-elements#people)元素从人员配置文件中获取受支持的属性之一。People元素支持从其他 ID 格式进行检索**。**[](https://www.infobip.com/docs/answers/chatbot-elements/core-elements#people)  
        [![配置自定义事件元素](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-element.png)](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-element.png)
6. 在**Custom event configuration**部分，指定将针对 Person 配置文件保存的事件。为此，请使用保存事件的属性。
    1. 单击**添加自定义事件**。  
        [![配置自定义事件元素](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-configure-element.png)](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-configure-element.png)
    2. 选择您在人脉中创建的事件。示例：添加到购物车。  
        [![选择活动](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-map-event-1.png)](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-map-event-1.png)
7. （可选）向事件添加属性。
    1. 单击**添加属性映射**。  
        [![添加属性映射](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-map-event-2.png)](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-map-event-2.png)
    2. 选择一个或多个包含事件属性的 Answers 属性。示例：addedCart、productID、productName 和 productPrice。  
        [![选择答案属性](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-map-event-3.png)](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-map-event-3.png)
    3. 对于每个 Answers 属性，选择您为 People 中的事件创建的等效事件属性。示例：addedCart、productID、productName 和 ProductPrice。确保 Answers 属性类型和 People 事件属性[兼容](https://www.infobip.com/docs/answers/integrations/people#attribute-mapping-attributes)。  
          
        答案不支持**dateTime**属性。如果要将日期时间值传递到事件中，请将该值保存在答案中的文本属性中，并将该属性映射到文本属性类型。  
        [![选择人员属性](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-map-event-4.png)](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-map-event-4.png)
8. 单击**添加**。
9. （可选）检查事件是否已成功保存到人员配置文件中。添加[**条件**](https://www.infobip.com/docs/answers/chatbot-elements/core-elements#conditions)元素。配置元素如下：
    1. 在属性字段中，选择固定属性**lastPeopleStatusCode**。
    2. 将运算符设置为**is**。
    3. 指定状态 - **Success**、**Fail**或**Validation_Error**
    4. 根据需要分支聊天机器人流程。  
        [![验证事件是否已在 People 中更新](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-people-status-condition.png)](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-people-custom-events-people-status-condition.png)

该事件及其属性保存在“人员”中最终用户的个人资料中。

[![事件已添加到人员中的人员配置文件中](https://www.infobip.com/docs/images/uploads/articles/answers-elements-custom-event-in-people.png)](https://www.infobip.com/docs/images/uploads/articles/answers-elements-custom-event-in-people.png)

## 创建人员聊天机器人

本节展示如何构建一个与人联系的基本聊天机器人。按着这些次序：

1. [**在聊天机器人编辑器中，将People**](https://www.infobip.com/docs/answers/chatbot-elements/core-elements#people)元素拖放到对话框中。
2. 单击您添加的**People元素。**
3. 在**“选择操作”**字段中，指定要执行的所需操作。您可以从人员处获取信息，也可以向人员发布信息（创建/更新）。对于本示例，选择**创建或更新人员配置文件**。  
    [![选择操作类型 - 从“人员”中检索信息或更新“人员”中的信息](https://www.infobip.com/docs/images/uploads/articles/answers-people-build-chatbot-action-type.png)](https://www.infobip.com/docs/images/uploads/articles/answers-people-build-chatbot-action-type.png)
4. 在**联系人信息属性**字段中，指定要使用的属性。您可以使用存储在 People 中的标准频道特定属性，也可以使用 Answers 中的自定义属性。默认情况下，此值设置为`endUserDestination`。
5. 对于自定义属性，单击**添加属性映射**以映射人员和答案中的属性。[请参阅属性](https://www.infobip.com/docs/answers/integrations#attributes-people)> 属性映射部分中的指南。  
    [![映射属性](https://www.infobip.com/docs/images/uploads/articles/answers-people-attributes-computed.png)](https://www.infobip.com/docs/images/uploads/articles/answers-people-attributes-computed.png)
6. 添加**条件**元素。配置元素如下：

- 在属性字段中，选择固定属性 **lastPeopleStatusCode**。
- 根据需要设置运算符。
- 指定状态 - **Success**、**Fail**或**Validation_Error**。  
    [![添加条件元素](https://www.infobip.com/docs/images/uploads/articles/answers-people-condition.png)](https://www.infobip.com/docs/images/uploads/articles/answers-people-condition.png)

如果您使用列表属性，列表值将添加到列表中。如果要覆盖列表值而不是将值添加到列表中，请在映射属性时使用覆盖选项。您只能在创建或更新配置文件时覆盖该列表。