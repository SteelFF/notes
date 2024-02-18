本页是一个快速教程，介绍如何设计受众群体、向单个客户和组添加标签，或者如何过滤受众以及如何在“请勿联系”列表中添加或删除人员。  

## Segments

根据与客户进行相关交互的任何信息动态组织您的受众。使用人员中可用的任何[事件](https://www.infobip.com/docs/people/events)和[属性](https://www.infobip.com/docs/people/manage-data#attributes)配置分段。例如，您可能想要向在您的产品或服务上花费了一定金额但在过去几个月内未进行购买的客户发送一条消息。

转到 **“人员”** >  **“细分”** 并设计此细分。在此场景中，添加 _“总支出”_ 属性、金额，然后使用可用的运算符进行更精细的定位。  
[![新段示例](https://www.infobip.com/docs/images/uploads/articles/new-segment-example.png)](https://www.infobip.com/docs/images/uploads/articles/new-segment-example.png)

我们为许多场景提供了一些现成的片段，您可以立即使用并根据您的需求进行调整：

- 活跃移动用户 - 使用过去 30 天内发生超过或等于 1 次的应用程序启动事件
- 非活跃移动用户 - 使用过去 30 天内**未** 发生超过或等于 1 次的应用程序启动事件 。

### 属性运算符

这些运算符（上面屏幕截图中的第一个块）通过一些非常具体的说明帮助定义受众群体： 

|操作|描述|例子|
|---|---|---|
|**Is**|准确值|当联系已消费特定金额的客户时，请使用此接线员。|
|**Is Not**|除了特定值之外的所有内容|使用它向消费多于或少于指定金额的客户发送消息。|
|**Less Than**|任何低于特定值的值|对于距离消费达到一定金额还有很长一段路要走的顾客。|
|**Less or equal**|该值低于或等于您定义的值|用于向花费相同金额或更少金额的客户发送消息。|
|**Greater or equal**|该值高于或等于您定义的值|您希望定位购买量达到或超过特定阈值的客户。|
|**Greater than**|该值高于定义值|当针对消费超过一定阈值的客户时。|
|**Is empty**|属性为空|包括“城市”属性为空的配置文件。|
|**Is not empty**|该属性不为空|包括“最爱颜色”属性不为空的配置文件。|

根据您选择的属性类型，属性运算符可能会有所不同。以下是您在过滤和调整分段规则时可能会遇到的更多字符串和时间运算符：

**字符串运算符**

|字符串运算符|描述|例子|
|---|---|---|
|**Starts with**|以文本开头的过滤属性|筛选以“Lo”开头的城市属性以显示城市以“Lo”开头的个人资料。返回示例，伦敦和洛杉矶。|
|**Ends with**|过滤以文本结尾的属性|筛选以“on”结尾的城市属性以显示城市以“on”结尾的个人资料。回报示例：里斯本和伦敦。|
|**Contains**|过滤包含文本的属性|筛选包含字母“da”的名字属性，以显示名字包含“da”的个人资料。返回示例，亚当、阿伊达或丹妮尔。|

**日期/时间运算符**

|日期/时间运算符|描述|例子|
|---|---|---|
|**Will be in**|日期/时间属性将以 x 天、周或月为单位|过滤出生日期在 5 天后的个人资料。|
|**Will be within**|日期/时间属性将在从今天起的 x 天、几周或几个月内|筛选从今天起 7 天内的出生日期。这将包括从今天到指定日期/时间之间的所有日期。|
|**Will be more than**|日期/时间属性将超过 x 天、周或月|过滤出生日期距今天超过 7 天的日期。|
|**Was**|日期/时间属性是 x 天、几周或几个月前|过滤出生日期为 5 天前的个人资料。|
|**Was within**|日期/时间属性在今天之前的 x 天、几周或几个月内|过滤出生日期在过去 5 天内（包括今天）的个人资料。|
|**Was more than**|日期/时间属性距今天已超过 x 天、几周或几个月|过滤出生日期早于今天 1 周以上的个人资料。|
|**Before**|对于出现在日期之前的日期属性|过滤购买日期早于且不包括 2021 年 10 月 10 日的配置文件。|
|**Until**|对于日期之后出现的日期属性|过滤购买日期早于 2021 年 10 月 10 日（含 10/10/2021）的配置文件。|
|**Since**|对于自指定日期以来发生的日期属性|筛选购买日期自 2021 年 10 月 10 日起（包括 10/10/2021）的配置文件。|
|**After**|当日期属性发生在指定日期之后时。|过滤购买日期晚于且不包括 10/10/2021 的配置文件。|

### 事件运营商

您用于创建细分的事件将包括发生次数和时间范围：在特定时间段内发生购买的次数。连接发生和时间运算符，您将拥有一个非常精致的活动。  
 

#### 发生：

|操作|例子|
|---|---|
|**Equals**|客户购买的确切次数。|
|**Less**|对于小于您定义的特定数量的购买数量。例如，购买次数少于 3 次。|
|**Less or equal**|当定位购买次数小于或等于您设置的值的客户时。|
|**More or equal**|联系购买的确切数量等于或超过定义值的客户。|
|**More than**|适用于购买次数超过特定数量的顾客。例如，当您想奖励那些购买超过 5 种产品的人时。|

#### 时间：

|操作|例子|
|---|---|
|Any time|购买发生的具体时间并不重要。|
|Within the last|它发生在特定的时间范围内，在过去的“x”天、小时、分钟内。|
|Before the last|购买发生在过去“x”天、小时、分钟之前。|
|Between the last|该事件可能发生在过去“x”和“y”天、小时或分钟之间。|

对于更复杂的场景，请向一条分段规则添加更多属性和事件。创建完规则后，保存它，每个符合这些条件的人都将自动包含在细分中。

## 标签

可以快速应用（和删除）标签，以帮助您组织受众以进行更相关的沟通。标签存在于帐户级别并应用于任何人的个人资料。可以为一个人添加多个标签，每个标签可以“附加”到多个人。 

当您想要根据人员的特定兴趣或特征对人员进行分组时，标签会变得很方便。假设您想要为所有带有_电子愿望清单_标签的人添加标签（因为他们将电子产品类别中的商品添加到他们的愿望清单中），以便您稍后可以向他们发送包含特别优惠的活动。

![如何根据兴趣和属性标记人员](https://www.infobip.com/docs/images/uploads/articles/N__TARGET_Create_Tags.png)  
 

标签是通过 People API 或在我们的网络界面上创建的。 

### 应用程序编程接口

要通过 People API 创建和管理标签，请使用以下方法之一：

- [创建新标签](https://dev.infobip.com/people/tags#create-new-tag)
- [获取单个标签](https://dev.infobip.com/people/tags#get-single-tag)
- [获取标签列表](https://dev.infobip.com/people/tags#get-list-of-tags)
- [删除标签](https://dev.infobip.com/people/tags#delete-tag)

### 网页界面

如果要使用 Infobip Web 界面添加标签，请选择**人员**>**标签**>**创建标签**并输入标签名称。根据需要创建并分配任意数量的标签。

[![人员管理标签](https://www.infobip.com/docs/images/uploads/articles/people_manage_tags.png)](https://www.infobip.com/docs/images/uploads/articles/people_manage_tags.png)

您的所有标签都列在“标签”页面上，包括属于每个标签的人数。有关每个人的标签的更多详细信息，请单击人员个人资料卡。 

要删除标签，请单击“x”按钮。   
 

#### 为导入分配标签

当您将联系人导入到人员模块时，每个人都会获得一个包含导入时间和日期的默认标签。

可以直接从导入屏幕管理标签。导入数据并选择**分析后，** 您可以选择删除默认标签并分配新标签。  
 

#### ![客户数据平台上的标签](https://www.infobip.com/docs/images/uploads/articles/tags-without-tesla.png)  
  
  
管理人员中的标签

可以按标签搜索个人资料，并在搜索中包含一个或多个标签。结果还可以让您很好地了解特定“标签组”中有多少人。  
[![标签仪表板](https://www.infobip.com/docs/images/uploads/articles/tags.png)](https://www.infobip.com/docs/images/uploads/articles/tags.png)

您可以将标签分配给一个或多个个人资料。选中人员姓名旁边的复选框，单击**“管理标签”**，然后搜索现有标签以添加或创建新标签。如果您选择更大的受众群体，您将看到一条通知，表明您正在尝试将标签应用到更大的受众群体。  
[![管理标签警告消息](https://www.infobip.com/docs/images/uploads/articles/Manage_Tags_Warning.png)](https://www.infobip.com/docs/images/uploads/articles/Manage_Tags_Warning.png)

##### 笔记

一个人可以拥有任意多个标签。如果您想一次性标记所有人员，则可以在导入文件时为人员分配标签，也可以稍后在“人员”页面上按特定条件过滤数据后执行此操作。两种方法产生相同的结果。

### 流动

如果您的帐户启用了我们的客户互动解决方案“[时刻”](https://www.infobip.com/docs/moments)，则可以使用标签与客户进行双向通信。例如，您可以向参加同一会议的人员发送消息。 [另一种可能性是稍后在通信流](https://www.infobip.com/docs/moments/get-started#create-your-first-flow) 中添加标签，以根据人们对您的消息的响应来标记他们，并将该标签用于未来的活动。   
[![流中标签](https://www.infobip.com/docs/images/uploads/articles/tags-in-flow.png)](https://www.infobip.com/docs/images/uploads/articles/tags-in-flow.png)  
  


## 请勿联系名单

所有不想再联系的电话号码和电子邮件地址都存储在“**请勿联系”**列表中。使用此功能可确保不会向选择退出您的通信活动的人员发送任何通信。始终遵守 GDPR 等重要的个人数据法规，并始终能够跟踪想要停止接收来自您公司的通信的人是否确实在您的“请勿联系”列表中。  
   
使用**人员**模块中的**配置**页面将数据导入到列表中。数据也可以从现有的通信流中自动添加。

如果您管理多个帐户，您将能够为黑名单记录选择特定帐户。从那时起，当您尝试向黑名单记录发送消息时，我们的平台将拒绝该消息并阻止发送任何不需要的通信。

### 手动添加人员 

1. 登录[Web界面](https://portal.infobip.com/)。
    
2. 在**人员** 平台上，转到 **配置选项**卡。
    
3. 选择 **“Do Not Contact” list** 以查看取消订阅的用户。
    
4. **添加新**联系人或**导入**不想再接收您的通信的人员列表。
    

[![黑名单不联系](https://www.infobip.com/docs/images/uploads/articles/blocklist-do-not-contact-list.png)](https://www.infobip.com/docs/images/uploads/articles/blocklist-do-not-contact-list.png)

### 自动添加人员 

1. 前往[Moments](https://www.infobip.com/docs/moments) 并创建您的交流。  
     
2. **接下来，选择名为“添加到不联系列表”** 的元素 。 验证并启动。如果客户回复特定文本或执行特定活动，此元素将开始工作，并且客户的电话号码或电子邮件将被列入黑名单。

[![不在流程中联系列表](https://www.infobip.com/docs/images/uploads/articles/do-not-contact-list-in-flow.png)](https://www.infobip.com/docs/images/uploads/articles/do-not-contact-list-in-flow.png)  
 

## 目标人群过滤器

当您想要查看在特定时间段内联系了多少人以及通过特定渠道联系了多少人时，过滤器会很有帮助。您还可以使用它们按标签、性别、国家/地区、城市、地址搜索人员，或组合这些类别来执行更高级的搜索。 

## 快速过滤器

要使用快速过滤器，请打开“人员”页面并进行选择。应用过滤器后，摘要卡将显示符合您的条件的人数。添加更多过滤器以优化搜索。

### 高级过滤器

按任何标准细分您的客户。如果您想使用系统中的 任何[属性进行过滤，请选择此高级选项。](https://www.infobip.com/docs/people/manage-data#attributes)

假设您想要过滤居住在纽约或墨西哥城的所有联系人。对于此场景，我们将选择“city”并添加运算符“is”。该值为“伦敦”。   
   
下一步是添加另一组规则：选择“or+”并重复该过程。完成后，单击 **“应用过滤器”** 按钮即可获取结果。  
  
 [![人们高级过滤器](https://www.infobip.com/docs/images/uploads/articles/people-advanced-filters.png)](https://www.infobip.com/docs/images/uploads/articles/people-advanced-filters.png)