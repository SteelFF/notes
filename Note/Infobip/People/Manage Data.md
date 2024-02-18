在 People 中安全存储和更新客户信息后，您可以开始使用可根据人们的需求、兴趣和活动设计各种通信流的功能。

## 属性

每个人的个人资料都包含在您针对特定受众时派上用场的信息。这些“属性”分为三组：标准、自定义和系统生成。  

### 标准

标准字段是预定义的，可由 Infobip API 和 Web 界面用户访问。它们包括一些基本的个人资料信息，这些信息根据个人资料属于[个人](https://www.infobip.com/docs/people/get-started#person-profile)还是 [公司](https://www.infobip.com/docs/people/get-started#company-profile)而有所不同。  

### 自定义

这些类型的属性按帐户进行配置并指定自定义名称，提供与您的客户相关的更具体的信息。 

使用自定义字段，您可以分配标准字段中不可用的任何信息。它可能是合同结束日期、订阅、到期付款或创建所需通信场景所需的其他一些数据。  
 

|笔记| |
|---|---|
|自定义属性名称必须是唯一的。| |

要创建新的自定义属性，请转至 Web 界面上的“人员”模块，然后选择  **“配置”** > **“自定义属性”** > **“创建自定义属性”**。  
选择一种数据类型：文本、小数、整数、真/假、日期、日期时间。类型一旦选定就无法更改。

[![人员新自定义属性](https://www.infobip.com/docs/images/uploads/articles/people-new-custom-attributes.png)](https://www.infobip.com/docs/images/uploads/articles/people-new-custom-attributes.png)

此属性现在在个人资料页面上可见，并且可用于沟通活动。    
 

|笔记| |
|---|---|
|将数据导入到人员时，可以“动态”添加新的自定义字段。在映射屏幕的列表中，选择 “添加新字段”。| |

您还可以通过 People API 创建和管理自定义属性。使用以下方法之一：

- [创建自定义属性](https://dev.infobip.com/#customer-engagement/people/create-a-custom-attribute)  
     
- [获取单个自定义属性](https://dev.infobip.com/#customer-engagement/people/get-a-single-custom-attribute)  
     
- [获取自定义属性列表](https://dev.infobip.com/#customer-engagement/people/get-a-list-of-custom-attributes)  
     
- 更新自定义属性：  
          •  [更新人员](https://dev.infobip.com/#customer-engagement/people/update-a-person) - 更新新字段，删除其他字段（如果您未指定）。  
          •  [部分更新](https://dev.infobip.com/#customer-engagement/people/partial-person-update) - 仅更新个人资料中的一个特定字段。   
     
- [删除自定义属性](https://dev.infobip.com/#customer-engagement/people/delete-a-custom-attribute)  
     

## Channel Attributes 

这些在不同的情况下都会派上用场，包括当您想要根据之前在特定时间发生的交互来联系一组客户时。

### 最后联系

[此系统生成的属性是指上次在任何渠道](https://www.infobip.com/docs/product-documentation#communication-channels)中联系您的客户的时间。此信息也可以在人员主页上看到。

要根据客户上次收到您的通信的时间来创建细分受众群，请转至**人员**>**细分**并选择_上次联系_属性。另外，请根据所需的通信场景考虑您希望与此属性一起使用的随附运算符。  

[![](https://www.infobip.com/docs/images/uploads/articles/broadcast-segment-new.png)](https://www.infobip.com/docs/images/uploads/articles/broadcast-segment-new.png)  
 通过将[细分受众元素添加到您的通信中，可以直接在](https://www.infobip.com/docs/moments/flow-elements#functions)[Flow](https://www.infobip.com/docs/moments/manage-flow) 中指定该属性 。在元素的侧面板中，选择_“上次联系”_属性、运算符（在本例中，运算符为“之前”）以及日期和时间。[](https://www.infobip.com/docs/moments/flow-elements#functions)

[![](https://www.infobip.com/docs/images/uploads/articles/last-contacted-in-flow-feb.png)](https://www.infobip.com/docs/images/uploads/articles/last-contacted-in-flow-feb.png)  
 

#### 运算符是如何计算的？

对于“最后联系”操作员来说，1 天就是 24 小时。因此，如果您选择“上次联系时间正好是 0 天前”，您将联系不到 24 小时前收到消息的用户。一天从当前时刻开始计算为 24 小时，24 到 48 小时之间的任何时间都将被视为“1 天前”。

可用的运算符：

**More than** – 假设您只想向过去一周未联系过的客户发送消息。选择“超过 7 天前”过滤器。 

**Less than** – 如果您想向过去一周内收到您的消息但未回复的客户发送提醒，请选择少于 7 天。  

**Exactly** - 在目标是非常具体地了解日期的情况下使用此运算符；例如，您想联系在 2 天前收到您最后一条消息的人。 

**Before** – 仅联系那些在特定日期和时间之前最后联系的人。   

**After** – 向特定日期之后最后一次联系的所有人员发送消息。您可以选择新产品发布的日期，并在“之后”添加运算符，以确保该受众群体收到包含更多详细信息的消息，因为产品基础知识已经传达给他们。 

**On** – 指定确切的日期，以便仅将在该特定日期最后联系的人员包含在通信中。这些可能是您向其宣布新的忠诚度计划升级已于当天生效的客户。

**Unknown** – 用于无法确定最后联系数据的情况。   
 

#### 每个频道的最后联系方式

每个频道还提供最后联系信息。这意味着您可以使用通过特定通道的最近通信以及与其相关的数据作为基础来决定何时发送下一条消息，并且可能为其选择不同的通道。可用渠道包括电子邮件、Messenger、线路、短信、推送或电报。  
[![](https://www.infobip.com/docs/images/uploads/articles/last-contacted-showing-channel.png)](https://www.infobip.com/docs/images/uploads/articles/last-contacted-showing-channel.png)  
 

使用“人员”主页上的过滤器来查找在特定时间段内通过特定渠道最后联系的人员组。  
[![](https://www.infobip.com/docs/images/uploads/articles/last-contacted-in-filtering.png)](https://www.infobip.com/docs/images/uploads/articles/last-contacted-in-filtering.png)

## 计算属性

计算属性使您能够使数据走得更远。计算属性看起来像常规的个人资料属性，并且也以类似的方式工作，为朋友圈活动和通信[个性化中的受众定位和](https://www.infobip.com/docs/moments/flow-elements#personalization-with-placeholders)[细分](https://www.infobip.com/docs/moments/flow-elements#segment-audience-functions)提供更丰富的选项。[](https://www.infobip.com/docs/moments/flow-elements#personalization-with-placeholders)

有 3 种类型的计算属性。使用自定义公式根据不同的人员档案数据进行计算，[事件聚合](https://www.infobip.com/docs/people/manage-data#event-aggregates-computed-attributes)根据事件数据进行计算，并使用[档案评分](https://www.infobip.com/docs/people/manage-data#profile-scoring-computed-attributes) 根据适合度和行为标准来评估档案。 

计算属性将随着 People 实例中的个人资料数据的更改而自动更新。这样您就可以始终相信数据是最新且准确的。

您可以从人员中的“配置”选项卡管理计算的属性。选择计算属性选项卡以访问计算属性仪表板，您可以在其中进行更改。

[![](https://www.infobip.com/docs/images/uploads/articles/people-computed-attributes-dashboard.png)](https://www.infobip.com/docs/images/uploads/articles/people-computed-attributes-dashboard.png)

#### 计算属性生命周期

虽然在大多数情况下计算属性的外观和工作方式与常规配置文件属性类似，但它们是复杂的数据类型，涉及管理其生命周期的特定步骤。在草稿期间，您可以根据需要多次构建、优化和测试计算属性，以确保数据输出符合您的要求。 

当您对草稿感到满意时，激活计算属性以开始处理阶段。计算出的属性将被计算并应用于 People 中的每个配置文件。如果您的实例中有许多配置文件，这可能需要一些时间。处理完成后，计算属性将处于活动状态。

一旦激活，计算属性将随着您的 People 实例中的个人资料数据的变化而自动更新。

|笔记| |
|---|---|
| - 每次计算属性时，都会将新的时间戳应用于人员简档以进行更新。如果您根据配置文件的上次更新时间（修改时间）触发流程，请记住这一点。| |
| - 活动的计算属性不可编辑。如果您希望进行编辑，您可以复制计算属性并对新创建的草稿副本进行编辑。| |
| - 您可以毫无问题地删除草稿属性。但是，为了确保数据稳定性，无法删除其他计算属性或活动流中正在使用的计算属性。| |
| - 您可以检查计算属性区域以查看每个计算属性的使用位置。可以删除不活动的计算属性，但请记住在删除之前始终进行检查，因为删除过程是永久性的且无法撤消。| |

### Custom Functions

自定义公式（以前称为表达式）是一种计算属性，您可以使用它来使配置文件属性更加强大。您可以根据标准和自定义配置文件属性自动计算值，以便提供另一种吸引受众的方式并以更加个性化的方式提供服务。

[使用表达式语言](https://www.infobip.com/docs/expression-language)可以实现这一点。其基本形式的表达式语言包括输出表达式值的公式中的一个或多个常量、属性、函数和运算符。然后可以将该值作为自定义属性存储在人员配置文件中。 

例如，如果您要捕获发送的电子邮件和点击的电子邮件数据，则可以使用自定义公式来计算电子邮件营销活动的转化率。公式看起来像这样：

```
turn(100*(email_link_clicks/emails_sent),0) 
```

##### 笔记

如果自定义公式中任何属性的值为空（即未定义），则表达式将为配置文件返回空值。

要创建自定义公式，请导航至“人员”>“配置”，然后在“计算属性”选项卡中单击**“新建”** >“自定义公式”。

您可以通过在公式生成器中输入属性名称、函数和运算符来构建公式。当您键入时，公式生成器将自动提供可用函数和属性的建议。您还可以使用 **“添加功能”** 和 **“添加属性”** 选择器。将打开可用函数和属性的下拉列表，您可以在其中进行选择并构建公式。从标准、自定义和活动计算属性（配置文件评分和事件聚合）中进行选择。

如果您犯了错误，您将看到一条错误消息并能够进行修复。

另外，请查看[表达式语言参考](https://www.infobip.com/docs/expression-language/expression-language-reference)部分，以获取所有可用函数和运算符的更详细描述和示例。 

[![](https://www.infobip.com/docs/images/uploads/articles/people-create-new-expression.png)](https://www.infobip.com/docs/images/uploads/articles/people-create-new-expression.png)

您可以在创建过程中随时测试您的公式。选择您想要测试表达式的配置文件，然后在构建和测试自定义公式时立即获得结果。

首次创建自定义公式时，单击 **“创建并退出”** 以将其另存为草稿。在草稿中，自定义公式不处于活动状态，并且在个人资料中不可见。您可以在草稿中根据需要多次编辑自定义公式。当您想要保存草稿编辑时，请单击 **“保存并退出”**。

当您对公式感到满意时，您可以切换激活自定义公式以启动激活过程，将自定义属性添加到人员中的所有配置文件中。在处理过程中，该公式是针对整个受众进行计算的。% 进度条可帮助您在仪表板中跟踪处理进度。完成后，自定义公式将在所有配置文件的属性中可见。

|笔记| |
|---|---|
|最佳做法是等到自定义公式完全激活后再使用。如果您在处理时尝试使用计算属性，您可能会发现使用自定义公式的段和过滤器可能缺少配置文件。| |  
|您无法编辑活动或正在处理的自定义公式。这意味着您需要复制要编辑的公式，然后对新版本进行更改。一旦您测试并验证您的新更改按预期工作，公式将被再次处理。当它处于活动状态时，您可以放弃旧的自定义公式。| |
|请记住在删除计算属性之前始终进行检查，因为删除的计算属性无法恢复并将被永久删除。| |

### 个人资料评分

个人资料评分可以帮助您从潜在客户和客户数据中获得更多可行的见解。配置文件分数是分配给配置文件的数字，以便您可以使用单个分数按多个条件对配置文件进行比较和排序。对个人资料进行评分的用例有很多，最流行的是 [线索评分](https://www.infobip.com/docs/tutorials/lead-scoring-in-people)、信用评分和行为评分等。

#### 它是如何工作的？ 

我们实施了基于规则的评分，以便您可以将您的领域知识转化为围绕您应用于受众的标准的一组简单规则。每个规则都有分配给它的得分点。适用于单个配置文件的所有规则的运行总和称为配置文件分数。输出始终是整数值。

可以获得什么分数：

- 您可以为 [标准](https://www.infobip.com/docs/people/manage-data#standard-attributes)、 [自定义](https://www.infobip.com/docs/people/manage-data#custom-attributes)和活动[计算属性](https://www.infobip.com/docs/people/manage-data#computed-attributes)创建规则，以确定配置文件如何适合您的“理想客户”。
- 为了涵盖行为部分，您可以创建有关客户交互的规则，无论是 [标准事件](https://www.infobip.com/docs/people/events#standard-events) （渠道、产品事件或配置文件更改）还是 [自定义事件](https://www.infobip.com/docs/people/events#custom-events)。

任何规则都可以分配正数和负数的分数，为您提供更大的灵活性。

让我们从使用单个标准“区域”的简单示例开始。假设 Region1 的观众比其他地区的观众花费更多。仅根据这些信息，您就可以使用单个规则创建一个简单的模型：

**If Region is Region1, assign 50 score points**

如果我们现在停止构建规则，则 Region1 中的所有配置文件的配置文件分数将为 50，其余的配置文件分数将为 0。 

然而，对于这个例子，让我们继续扩展规则。假设还有另一个区域，称为 Region2。Region2 花费很多，但比 Region1 花费少。有了这些信息，您就可以根据这些信息制定第二条规则。

**If Region is Region1, assign 50 score points**

**If Region is Region2, assign 25 score points**

现在，您有两条规则可以根据区域条件将受众群体分为 3 个部分。Region1 的配置文件得分为 50，Region2 得分为 25，其余得分为 0。相同的逻辑适用于其他标准。

行为评分使您可以选择创建有关个人资料参与度的规则 - 个人资料参与度越高，其进入漏斗的机会就越大。

按影响/价值区分您的资源也可以提高模型质量。例如，访问您网站主页的用户不如访问定价页面的用户有价值。可以对事件计数和时间范围以及事件的特定属性应用附加条件。

再次，让我们从简单开始，看看我们可以使用 [People Events](https://www.infobip.com/docs/people/events#people-events-standard-events)的 PageView 事件围绕此创建一些规则： 

**If `PageView` happened within the last 90 days, and URL is 'https://www.example.com, assign 5 score points**

**If `PageView` happened within the last 90 days, and URL is 'https://www.example.com/pricing, assign 10 score points**

准备好后，只需激活模型并查看其应用于人员数据库中的所有配置文件。当模型中的属性发生变化或新事件发生时，分数将自动更新。

此外，对于使用滑动时间窗口（例如“过去 X 天内”）的事件规则，我们每天都会重新计算。这涵盖了在指定时间范围内没有发生任何事件并且分数应降低的情况。

**配置步骤**

在创建模型之前，请确保您已配置和/或启用  要在评分模型中使用的所有[事件。](https://www.infobip.com/docs/people/events)

1. 创建模型， **人员** > **配置** 并选择 _计算属性_，单击新建，然后选择配置文件评分。新创建的模型处于草稿状态，这意味着它当前未应用于配置文件，因此尚未创建相应的属性。  
    [![人员领导评分仪表板](https://www.infobip.com/docs/images/uploads/articles/people-lead-scoring-dashboard.png)](https://www.infobip.com/docs/images/uploads/articles/people-lead-scoring-dashboard.png)

2. 单击“添加规则”可为您的所有条件创建规则。您可以为标准、自定义和活动计算属性（自定义公式和事件聚合）和事件添加规则。  
      
    对于事件规则，支持以下操作数： 

- Occurrence - 'happened'
- Frequency - 'equal to X times', less than X times', 'less or equal X times', 'more or equal X times', 'more than X times', 
- Recency - 'any time', 'within the last X minutes/hours/days', 'before the last X minutes/hours/days' , 'between the last X and Y minutes/hours/days'
- Event properties (such as payload) use the same operands as event properties in the segment editor 
      
    [![人员领导评分添加属性](https://www.infobip.com/docs/images/uploads/articles/people-lead-scoring-add-attributes.png)](https://www.infobip.com/docs/images/uploads/articles/people-lead-scoring-add-attributes.png)

3. 在用户身上测试模型。该模型将立即应用于所选的配置文件。如果模型应用于用户后用户配置文件发生任何更改，您应该单击刷新图标刷新配置文件数据或通过再次选择配置文件来重新应用模型。  
      
    [![人员领导评分测试用户](https://www.infobip.com/docs/images/uploads/articles/people-lead-scoring-test-user.png)](https://www.infobip.com/docs/images/uploads/articles/people-lead-scoring-test-user.png)

4. 当您的模型准备就绪时，切换以激活它。一旦激活，模型就会进入处理状态，对所有现有配置文件进行计算。在计算时，会为与评分模型同名的模型创建相应的计算属性。新配置文件在创建后会自动评分，配置文件分数将在配置文件的配置文件卡上的计算属性内可见。

|笔记| |
|---|---|
|模型激活后无法修改或删除，除非您先禁用它们。当模型处于活动或处理状态时，您仍然可以在仅查看模式下查看模型，并根据需要根据配置文件进行测试。| |

5. 查看结果并开始在人员[细分](https://www.infobip.com/docs/people/manage-audience#segments)和[自定义公式](https://www.infobip.com/docs/people/manage-data#custom-formulas-computed-attributes)中使用它们，并在[时刻](https://www.infobip.com/docs/moments/flow-elements#segment-audience-functions)和[答案](https://www.infobip.com/docs/answers/integrations#attributes-people)中找到它们的集成。 

要修改或删除模型，首先应禁用该模型，使模型进入禁用状态。禁用模型后，已包含分数的配置文件将看到分数不会因新事件或更改而更新。禁用模型后创建的配置文件将不具有包含分数的相应计算属性。

删除模型后，相应的计算属性将从所有配置文件中删除。删除是永久性的，因此为了确保数据稳定性，如果配置文件评分计算属性正在流中使用，或者作为另一个自定义公式或事件聚合计算属性的一部分，则您无法删除配置文件评分模型。请参阅[计算属性](https://www.infobip.com/docs/people/manage-data#computed-attributes)以了解更多信息。

#### 个人资料评分最佳实践

以下是您在定义个人资料评分规则时需要考虑的一些最佳实践建议。

此外，请查看[人员中的潜在客户评分](https://www.infobip.com/docs/tutorials/lead-scoring-in-people)教程，了解实际的个人资料评分示例。

- 对于是否将个人资料适合度分数和行为分数分开，还是使用包含个人资料 _和_ 行为数据的单一分数，存在不同的意见。这完全取决于您的特定用例，我们只是为您提供同时执行这两项操作的可能性，并让您选择最适合您的。
- 我们建议您按顺序为每个条件一一添加规则。
- 配置文件拟合的典型建议是 4-6 个标准，规则的数量取决于您希望实现和维护的粒度。
- 行为标准也根据您使用的互动而有所不同，但通常包括对活动中链接的点击、表单提交和网络活动（例如页面浏览量、门控内容下载和购物车交互等）
- 当您决定更换模型时，最好禁用而不是立即删除旧模型。这样您就可以比较和分析结果。

### 事件聚合

事件聚合是一种根据配置文件[事件](https://www.infobip.com/docs/people/events)数据 计算得出的 [计算属性](https://www.infobip.com/docs/people/manage-data#computed-attributes) 。它们有助于计算汇总信息，例如：[](https://www.infobip.com/docs/people/events)

- 为了建立客户终身价值 (LTV)，客户所下订单的总价值是多少？ 
- 客户总共购买了多少次？
- 客户在过去 30 天内访问特定页面有多少次？
- 使用忠诚度积分进行购买时，客户的平均购买量是多少？
- 过去 14 天内客户收到了多少封我们发来的电子邮件？

[![人员计算属性事件聚合仪表板](https://www.infobip.com/docs/images/uploads/articles/people-computed-attribute-event-aggregate-dashboard.png)](https://www.infobip.com/docs/images/uploads/articles/people-computed-attribute-event-aggregate-dashboard.png)

要创建事件聚合，请转到“人员”中的“配置”选项卡。选择 **计算属性** 并单击 **新建 > 事件聚合。**

#### 定义您的事件聚合

事件聚合由以下组成部分组成：

1. 用于进行计算的聚合函数 
2. 事件和事件属性。例如，如果您要计算“结账已完成”事件中“订单价值”属性的平均值。Count 函数不采用任何属性，因为它根据事件实例的数量进行计算。
3. 可选的事件属性过滤器。例如，如果您只想计算使用信用卡支付的购买金额（付款方式为卡）。
4. 用于从聚合中排除第一个或最后一个匹配事件的可选选择器。例如，您可以选择首先从计算重复购买次数的聚合中排除。
5. 用于过滤所选时间段内事件的时间段。 

[![人创造事件聚合](https://www.infobip.com/docs/images/uploads/articles/people-create-event-aggregate.png)](https://www.infobip.com/docs/images/uploads/articles/people-create-event-aggregate.png)

通过选择聚合函数并选择要计算的事件和事件属性来创建事件聚合，然后定义事件发生的时间段。系统将针对所选时间段内的所有事件计算您的事件总计。

为属性命名，然后在人员配置文件上测试您的计算。

#### 聚合函数

从以下聚合函数中选择：

|范围|描述|
|---|---|
|Count|使用 Count 来跟踪事件发生的次数。|
|And|使用 Sum 获取事件属性值的总和。例如，多个_“商品购买”_事件的_总价_|
|Min|使用 Min 存储事件属性的最小值。例如，_“已购买项目_”事件中的最小价格值<br><br>Min 还可以存储标准事件时间事件属性或自定义日期时间属性事件的最早时间值。|
|Max|使用 Max 来存储事件属性的最大值。例如，应用程序评分 事件中的最大评分值<br><br>Max 还存储标准事件时间事件属性或自定义日期时间属性事件的最新时间值。|
|Average|使用 Average 存储事件属性值的平均值。|
|Count distinct|使用“Count Distinct”来计算选定事件属性中具有唯一值的事件数。例如，  “已购买项目” 事件中的唯一购买|
|First|使用 First 查找第一个发生的事件的属性值。例如，第一个“购买的商品”事件中的“商品”。|
|Last|使用 Last 查找最后发生的事件的属性值。例如，上次 “查看项目”事件中的“项目”。|
|Exists|使用 Exists 作为标志来跟踪事件是否发生。如果配置文件包含所选事件，结果返回 true。|
|Most Common|使用最常见来跟踪事件最常见（最频繁）的属性值。|

#### 设置时间段

|时间段|描述|
|---|---|
|Lifetime|所有事件发生|
|Last 7 days|过去 7 天的所有事件，但不包括今天发生的事件|
|Last 14 days|过去 14 天的所有事件，但不包括今天发生的事件|
|Last 30 days|过去 30 天的所有事件，但不包括今天发生的事件|
|Last 60 days|过去 60 天的所有事件，但不包括今天发生的事件|
|Last 90 days|过去 90 天的所有事件，但不包括今天发生的事件|
|24 hours|当前时间前 24 小时|
|Custom Period|自选定日期以来发生的所有事件，或从今天之前 n 天开始发生的所有事件。|

#### 测试您的事件聚合

您可以在创建过程中随时对用户测试事件聚合。选择您想要测试事件聚合的用户，然后在构建和测试公式时立即获得结果。

[![人员测试事件聚合](https://www.infobip.com/docs/images/uploads/articles/people-test-event-aggregates.png)](https://www.infobip.com/docs/images/uploads/articles/people-test-event-aggregates.png)

首次创建事件聚合时，单击 **“创建并退出”** 以将其另存为草稿。在草稿中，事件聚合不处于活动状态，并且在人员简档中不可见。当处于草稿中时，您可以根据需要多次编辑此属性。当您想要保存草稿编辑时，请单击 **“保存并退出”**。 

当您对计算感到满意时，切换 **激活事件聚合 属性。**它将开始根据您的 People 实例中的配置文件进行处理。

您的事件聚合仅在针对所有配置文件进行处理后才会处于活动状态。如果您希望调整活动事件聚合，您可以将其停用，进行更改，然后再次重新激活它。

事件聚合的生命周期与其他计算属性相同。前往[计算属性](https://www.infobip.com/docs/people/manage-data#computed-attributes)部分了解更多信息。

## 列表

有时，当您向客户发送消息时，您希望包含“一组信息”，例如包含保险单、即将付款或他们留在购物车中的商品的列表。那么，你该怎么办？_使用称为列表的_客户属性的自动活动 将是最好的前进方式。

[Moments](https://www.infobip.com/docs/moments)用户使用的列表是按客户存储的。根据您要创建的通信，您可以向同一客户发送有关每个存储项目的单独消息，并提及存储列表中的所有对象或仅提及前几个对象。

例如，如果一个人拥有多笔贷款，而您需要通知他们其中一笔贷款的付款日期即将到来，那么列表就会派上用场。请记住，[个人profile](https://www.infobip.com/docs/people/get-started#person-profile)代表一个真实的人，与此人相关的所有数据都存储在同一个人资料中 - 不同的贷款、有关此人作为主要订户的家庭手机计划中不同线路的信息等。

前往[使用列表](https://www.infobip.com/docs/moments/using-lists)， 了解如何在通信中使用列表，并使用它们个性化您的内容。