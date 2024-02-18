人员平台上的统一个人资料数据可帮助您了解您正在与谁通信以及此人的需求是什么。每一张个人资料卡，无论是 **个人** 还是 **公司**，都显示了客户旅程的阶段、用户活动、他们的特征和标签，以及每个个人资料所属的受众群体。

大多数这些功能（例如标签或受众群体）可以直接从个人资料页面添加或访问。利用它们来管理客户参与和支持，但请记住保持个人资料的重点。

本文将帮助您了解主要的配置文件类型及其详细信息。 

## 人物简介

有两种类型的个人资料： **潜在客户** 和 **客户**。类型根据客户旅程的阶段确定： 

- 潜在客户是包含未经验证的人员姓名和联系信息的个人资料。潜在客户信息可以随时从多个来源进行更改。  
     
- 客户是经过最值得信赖的验证渠道验证的个人资料。任何修改都需要做更多的工作来防止不需要的更新。 

人员模块中存储的数据有助于跟踪潜在客户的活动并将其转变为客户。您还可以根据现有客户的个人资料信息为他们安排活动。 

您的所有潜在客户和客户资料的列表在人员主页上可见。

### 匿名访客

匿名访问者是指未被网站识别为已知人员（潜在客户或客户）的网站用户，因此在人员中没有个人资料。[通过Web SDK](https://www.infobip.com/docs/people/synchronize-your-audience#web-sdk-create-and-update)在网站上跟踪访问者浏览活动和自定义站点事件。一旦网站识别出访问者，例如在他们创建帐户或以表单提供某些用户详细信息后，网站就可以调用一种方法将访问者的事件历史记录合并到**潜在客户**（新的或现有的） 或 **客户**（现有）人物中的个人资料。 

### 档案信息

在个人资料卡上，您将看到最后一次联系此人的时间以及可以使用哪些渠道与他们联系。所有其他配置文件数据都分为几个信息块：[[#概述]]、[[#联系信息]]以及[[#属性、标签和事件]]。


### 概述

概述是配置文件数据的摘要，它显示以下内容：

- 您输入的必需和可选的**基本信息。**  
     
- Infobip 平台和用于同步客户数据的连接系统（Salesforce Sales Cloud 或 Microsoft Dynamics 365）上的**人员 ID 。**  
     
- **系统字段**让您知道哪个用户创建了客户档案、何时以及如何创建。还有有关客户正在使用的浏览器和操作系统的信息。    
     
- **活动源** 可让您快速了解最新的个人资料更改和活动更新。如果您需要关注特定事件，请使用过滤。

[![人员资料馈送过滤器](https://www.infobip.com/docs/images/uploads/articles/person-profile-feed-filter.png)](https://www.infobip.com/docs/images/uploads/articles/person-profile-feed-filter.png)

### 联系信息

每个个人资料的必填联系字段是电子邮件地址和电话号码，并且您可以添加多个。**如果客户允许您使用其他一些渠道，这些渠道也将在“联系信息”** 选项卡下可见。 

打开 _“显示渠道活动”_ 或 _“显示附加信息_”（取决于渠道）开关，以查看哪个用户添加了信息、上次联系客户的时间以及他们如何通过此渠道与您的企业互动。

[![](https://www.infobip.com/docs/images/uploads/articles/person-profile-contact-information.png)](https://www.infobip.com/docs/images/uploads/articles/person-profile-contact-information.png)

### 属性、标签和事件

个人资料中的最后一个选项卡提供了有关此人的兴趣和行为的更完整的信息。此信息是可选的，可以添加以更好地细分和分组受众。查看解释并决定是否需要使用其中的部分或全部： 

__Tags__ 每个人的标签概述，您可以在其中添加现有标签或创建新标签。例如，如果此人对您的产品表现出兴趣，您可以添加“感兴趣”标签。   
 

__Segments__ 此人所属的受众群体，按人口统计数据或行为或两者组织。这可能是来自特定城市区域的一群人，他们在前几周从您的网上商店订购了商品或多次访问过您的网站。  
 

__Custom Attributes__ 此人的订阅计划、收入组、购买总额或类似信息。选择一个现有属性添加到此配置文件中，或者转到“配置”页面创建一个新属性，然后您就可以添加它。   
 

__Lists__ 添加任何类型的现有列表，或在“配置”页面上创建一个新列表并将其添加到配置文件中。列表可以包含购买的商品、订阅计划、同一提供商的不同帐户等。    
 

__Events__ 在活动源中可见，它们包括以下内容：来自另一个系统的外部事件、我们平台内记录的产品事件、通过特定渠道进行客户响应的渠道事件以及涉及现有个人资料信息更改的人员事件。您可以过滤事件组并搜索该组内的特定事件。

_表达式。_  所选配置文件的表达式属性及其相应值的视图。

|笔记| |
|---|---|
|只有您之前定义的外部事件才会显示在那里。必须启用通道事件。| | 

[![人员-属性-细分-标签-事件](https://www.infobip.com/docs/images/uploads/articles/people-attributes-segments-tags-events.png)](https://www.infobip.com/docs/images/uploads/articles/people-attributes-segments-tags-events.png)

### 创建并同步

创建和更新个人资料时，您可以使用多种方法。但是，根据配置文件类型，有些规则和限制适用。例如，为了不断更新客户档案的信息，该过程更加严格，以保护客户群免受任何不需要的更新。

对于 **Leads**，请使用以下方法将其添加到 People 平台，然后持续更新：

- [在 Web 界面上手动](https://www.infobip.com/docs/people/synchronize-your-audience#web-interface-create-and-update)
- [文件导入](https://www.infobip.com/docs/people/synchronize-your-audience#use-files-import)
- [网页版SDK](https://www.infobip.com/docs/people/synchronize-your-audience#web-sdk-create-and-update)
- [移动软件开发工具包](https://www.infobip.com/docs/people/synchronize-your-audience#mobile-sdk-create-and-update)
- [外部集成](https://www.infobip.com/docs/people/synchronize-your-audience#external-integrations-synchronize) 
- [精彩瞬间](https://www.infobip.com/docs/moments)
- [形式](https://www.infobip.com/docs/forms)

对于 **客户**来说，方法如下： 

- [在 Web 界面上手动](https://www.infobip.com/docs/people/synchronize-your-audience#web-interface-create-and-update)
- [应用程序编程接口](https://www.infobip.com/docs/people/synchronize-your-audience#api-create-and-update)
- [移动软件开发工具包](https://www.infobip.com/docs/people/synchronize-your-audience#mobile-sdk-create-and-update)

## 公司简介

如果您想对属于同一企业的人员进行分组，请参阅公司资料。您将能够向整个公司发送通信、使用个人资料信息进行行为[分析](https://www.infobip.com/docs/people/analytics)等等。

返回并单击任何公司卡即可查看与其相关的详细信息。您还可以按名称或域名搜索现有公司。

每个公司页面都有两个部分： **概述** 和 **人员**。

### 概述

公司简介中的“概览”页面选项卡包括以下内容：基本信息、公司 ID、分配的标签、系统字段、网站、自定义属性。 

[![人们新公司概述](https://www.infobip.com/docs/images/uploads/articles/people-new-company.png)](https://www.infobip.com/docs/images/uploads/articles/people-new-company.png)

#### 基本信息

除了基本数据外，该块还涵盖公司在客户旅程中所处的阶段以及描述该阶段的受众群体等信息。这些是基本信息中可用的类别：

_部分。_ 它显示了公司所属的受众群体。例如，公司每月或每年支付的金额或公司成员的数量。阅读有关如何创建 [分段的](https://www.infobip.com/docs/people/manage-audience#segments)更多信息。

__Country__ 公司所在地。

__Region__ 描述公司所在地的更广泛区域。

__Account Manager__ 该公司的客户经理。

__Stage__ 客户旅程中的一个阶段以及您要添加的任何类似信息。  

__Industry__ 公司经营的任何行业：电子商务、电子银行、旅游等。

__VAT Number__公司识别税号。 

__Notes__ 您希望在此部分中存储的其他信息。   
  
 

#### 其他信息

概述页面上提供的其他数据将帮助您管理公司简介并使沟通更轻松：

_公司 ID_ 与外部系统同步时使用。无法改变。

_分配的标签。_ 按兴趣标记公司。每个公司成员都会被分配这个标签。例如，如果一家公司正在使用特定服务，您可以添加带有该服务名称的标签。**标签是在公司**主页 或 **概览**页面上创建、添加或删除的  。阅读[标签](https://www.infobip.com/docs/people/manage-audience#tags) 以熟悉此功能。 

_系统字段_。它们显示谁创建了配置文件、何时创建，以及最后修改它的人。 

_网站_。添加一个或多个公司网站。但是，您不能为不同的公司包含相同的域。如果某人拥有公司资料中不存在的另一个域，您仍然可以将他们添加到该公司。 

_自定义属性。_ 创建自定义属性以与公司进行相关沟通。转至 **“人员”**  >  **“配置”**  >  **“公司自定义属性”** 以定义要用于特定业务的属性。[在详细介绍属性的文章](https://www.infobip.com/docs/people/manage-data#custom-attributes)中了解有关配置的更多信息。 

### People

**“ People”** 选项卡显示属于一家公司的人员数量。单击该选项卡可添加新成员或仅查看成员列表及其 [信息](https://www.infobip.com/docs/people/get-started#person-profile)。

[![人员添加成员](https://www.infobip.com/docs/images/uploads/articles/people-add-member.png)](https://www.infobip.com/docs/images/uploads/articles/people-add-member.png)

如果您想删除现有成员，请将光标悬停在该人员的卡片上，然后单击“x”按钮。

要删除整个公司，请返回 **“公司”** 页面，选中出现的复选框，然后单击 **“删除”** 选项卡。 

### 创建并同步

可用于创建公司或同步个人资料数据的方法： 

- [在 Infobip Web 界面上手动](https://www.infobip.com/docs/people/synchronize-your-audience#web-interface-create-and-update)
- [外部集成](https://www.infobip.com/docs/people/synchronize-your-audience#external-integrations-synchronize)