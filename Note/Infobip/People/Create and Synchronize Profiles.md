人员配置文件的类型对于决定使用哪种同步方法至关重要： **潜在客户** 和 **客户** 是反映人员在客户旅程中的状态的配置文件，而 **公司** 是包含同一公司成员的组配置文件。匿名访问者不被定义为拥有人员配置文件，但是如果您在网站上部署了[Web SDK，](https://www.infobip.com/docs/people/synchronize-your-audience#web-sdk-create-and-update)您将能够在匿名访问者转化为潜在客户和客户之前跟踪他们的活动。

要开始创建任何配置文件类型，您需要一个 Infobip [帐户](https://www.infobip.com/docs/essentials/create-account)，并且您现有的客户信息应该准备好导入到 People 或使用 API、SDK 或外部平台进行集成。 

您当前的数据设置和业务目标将帮助您决定如何连接到我们的平台。例如，使用 JS SDK 将您的网站连接到 People：这将让您“记录”用户操作并发送相关消息或提供正确的服务。或者，您可以将 Salesforce 或 Microsoft Dynamics 365 帐户与 People 集成并发送有针对性的消息。 

本节将作为您的快速指南，了解如何添加配置文件并在 People 和其他 Infobip 解决方案以及外部系统之间进行调整。 

## 创建和更新

熟悉 People 的一个好地方是在 Infobip Web 界面上手动构建配置文件，然后我们将采用更实用的方法：通过 People API 和 SDK 添加配置文件，并将它们与不同的内部和外部解决方案一起使用。浏览所有选项并选择符合您业务需求的选项。  

请记住，从 API 创建新配置文件时，您将能够选择其类型：**Lead**或**Customer**。如果未指定类型，它将被记录为客户资料。 

请注意，JS Web SDK 可以跟踪事件并更新 [潜在客户](https://www.infobip.com/docs/people/synchronize-your-audience#lead-profiles-import)的个人资料属性；更新 [客户](https://www.infobip.com/docs/people/synchronize-your-audience#customer-profiles-import) 类型受到限制。 

此外，请勿在名字、姓氏、中间名和昵称字段中使用以下字符： & < > " / \ \n \r : ( ) { } = ; #  

此规则适用于配置文件的创建和编辑。

### 网页界面

登录 Web 界面，转到人员模块并决定是否要创建 **人员** 资料或 **公司** 资料。 

#### 创建潜在客户或客户

要手动添加人员的个人资料，请按照下列步骤操作： 

1. **联系信息** - 输入电子邮件或电话号码。
2. **基本信息** - 输入您的姓名、地址和其他可选信息。请记住选择类型：_Customer_或_Lead_。

[![网络界面上的新配置文件](https://www.infobip.com/docs/images/uploads/articles/people-create-lead-customer.png)](https://www.infobip.com/docs/images/uploads/articles/people-create-lead-customer.png)

3. 最后，添加 您想要链接到此配置文件的 任何[标签](https://www.infobip.com/docs/people/manage-audience#tags)或[自定义属性。](https://www.infobip.com/docs/people/manage-data#custom-attributes)

4. 单击 **创建人员**。 

5. 您将被重定向到新创建的[配置文件](https://www.infobip.com/docs/people/get-started#person-profile)。 

[![新客户资料](https://www.infobip.com/docs/images/uploads/articles/people-newly-created-customer-profile.png)](https://www.infobip.com/docs/images/uploads/articles/people-newly-created-customer-profile.png)

#### 创建公司简介

**在人员**模块主页上  ，单击 **创建公司** ，输入公司名称和域名。基本信息以及任何相关标签和自定义属性都是可选的，但最好添加尽可能多的信息。这将增加您与企业沟通的相关性。 

[![网页界面上的公司简介](https://www.infobip.com/docs/images/uploads/articles/people-create-company.png)](https://www.infobip.com/docs/images/uploads/articles/people-create-company.png)

每个公司页面都有两个部分：[概述](https://www.infobip.com/docs/people/get-started#overview-company-profile)和[人员](https://www.infobip.com/docs/people/get-started#people-company-profile)。

### 应用程序编程接口

Infobip People API 将您的网站、CRM 或应用程序连接到我们的数据平台。您可以对 People 中的以下对象执行基本的 CRUD（创建、读取、更新、删除）操作，在某些情况下还可以执行更高级的操作：

- Profiles
- Companies
- Tags
- Custom attributes
- Contact information: Push
- Events

有关这些操作的更多信息，请参阅 [People API 文档](https://www.infobip.com/docs/api#customer-engagement/people)。

[要开始使用这些对象，请按照 Infobip API 文档](https://www.infobip.com/docs/api)中的步骤操作，包括获取 API 密钥 。接下来，了解如何 [管理](https://www.infobip.com/docs/api#customer-engagement/people) 您的受众。。

### 网页版SDK

Web SDK 是 Infobip 提供的一个 JavaScript 库，用于将您的网站与 People 集成。它通过以下方式实现渐进式客户分析：

1. 跟踪您网站上的匿名和已知访问者的活动（会话、查看的页面、您定义的自定义网站操作）
2. 当匿名访问者填写网络表格或订阅时事通讯时，将其转换为新的潜在客户或与现有潜在客户资料进行匹配，并将其整个预先识别的网站活动合并到已识别的资料中。
3. 当匿名访问者登录其网站帐户时，将其与现有客户资料进行匹配，并将其登录前会话事件合并到客户资料时间线中。 
4. 使用从网站上已识别用户收集的信息更新潜在客户资料。

要开始使用 Web SDK，请查看[People Web SDK](http://github.com/infobip/people-web-sdk) Github 文档。

### 移动软件开发工具包

移动 SDK 涵盖 **潜在客户** 和**客户**。该方法适用于以下几种场景：

- 当用户安装应用程序并打开它时，将创建一个空的潜在客户资料。查看以下快速入门指南： 

- [安卓指南](https://github.com/infobip/mobile-messaging-sdk-android#quick-start-guide)
- [iOS 指南](https://github.com/infobip/mobile-messaging-sdk-ios#quick-start-guide)
- [华为指南](https://github.com/infobip/mobile-messaging-sdk-huawei#quick-start-guide)

**框架插件指南**

- [反应本机指南](https://github.com/infobip/mobile-messaging-react-native-plugin#quick-start-guide)
- [科尔多瓦指南](https://github.com/infobip/mobile-messaging-cordova-plugin#quick-start-guide)
- [颤振指南](https://github.com/infobip/mobile-messaging-flutter-plugin#quick-start-guide)

- 当用户登录并且 Infobip 使用来自第三个系统的数据确认这是真实的潜在客户资料时，会发生以下情况：

- 如果人员中不存在潜在客户，则会创建客户资料以及提供的联系信息。
- 如果潜在客户资料存在，将根据匹配的电子邮件地址或电话号码将其删除。将使用推送注册（PushReg ID）创建一个新客户，并且将包含所有提供的信息。
- 如果客户配置文件存在，PushReg ID 将从潜在客户配置文件移至客户配置文件。线索已删除。

用户 _和安装_ 文档将帮助您设置一切： 

- [Android 和华为指南](https://github.com/infobip/mobile-messaging-sdk-android/wiki/Users-and-installations#personalization-phone-numbers-emails-and-external-user-id)
- [iOS 指南](https://github.com/infobip/mobile-messaging-sdk-ios/wiki/Users-and-installations#personalization-phone-numbers-emails-and-external-user-id)

**框架插件指南**

- [反应本机指南](https://github.com/infobip/mobile-messaging-react-native-plugin/wiki/Users-and-installations#personalization-phone-numbers-emails-and-external-user-id)
- [科尔多瓦指南](https://github.com/infobip/mobile-messaging-cordova-plugin/wiki/Users-and-installations#personalization-phone-numbers-emails-and-external-user-id)
- [颤振指南](https://github.com/infobip/mobile-messaging-flutter-plugin/wiki/Users-and-installations#personalization-phone-numbers-emails-and-external-user-id) 

- 当用户注销时，应用程序将与现有客户配置文件断开连接，并创建一个新的空潜在客户。

## 内部整合

当连接到我们平台的外部系统（例如网页）的访问者与该系统交互时，会通过这些 Infobip 解决方案自动创建潜在客户：

_[瞬间](https://www.infobip.com/docs/moments)。_ 通过我们的双向沟通​​工具 Flow 收集潜在客户并将其培养给客户，或使用 Broadcast 进行单向沟通。通信期间获取的信息将在“人员”中自动更新。注意：当通过 Flow 收到入站 SMS 消息且此人的个人资料不存在时，大多数情况下都会创建潜在客户。如果潜在客户或客户资料确实存在，它将在 Flow 中使用。  
 

_[表格](https://www.infobip.com/docs/forms)。_当某人在您的网站上提交表单时，会在人员中创建潜在客户类型的个人资料。如果配置文件已存在，则将应用  [特定的处理规则。](https://www.infobip.com/docs/forms/data-processing)

## 进口

无需手动创建每个配置文件，您可以使用文件或 API 将潜在客户、客户和公司的现有数据传输给人员。这些是主要的导入规则： 

-  默认情况下，如果未指定类型，我们会导入 **潜在客户。**
- 用户可以指定导入期间将使用的类型。

### 潜在客户简介

检查 Lead 类型的配置文件是否已存在：

- 如果没有，则会创建新的潜在客户或客户。
- 如果存在重复的配置文件：选择不进行更改或重写当前配置文件。潜在客户将被更新；如果正在创建客户资料，则仅填充空白字段。 

### 客户资料

如果可以将 CRM 与人员模块集成，请检查是否可以使用我们的 Web 界面或 API 创建个人资料。如果可以，则会创建新的潜在客户或客户。如果没有，请查看人员平台上是否存在该个人资料： 

- 如果配置文件不存在，则会创建新的潜在客户或客户。
- 如果存在重复的配置文件，请选择不进行更改或重写当前的潜在客户或客户配置文件。潜在客户资料将升级为客户，并且客户资料将更新。 

### 使用文件

文件最大可为 25MB，我们支持 .csv、.xls、.xsls、.txt 或 .zip（25MB 大小限制适用于 zip 内的未压缩文件，而不是压缩文件本身）。要导入它，请按照下列步骤操作： 

1. 导航到 **人员** > **导入**。  
2. 从系统中选择一个文件并使用平台字段映射文件数据。   
3. 完成列映射后，单击 **“分析”**。

##### 重要的

您可以将国家/地区前缀添加到您的电话号码选择中，这是第四个选项列中的一个选项。如果您想跳过某些字段，请将它们保留为不匹配，系统将不会导入它们。

如果您需要导入具有多个电子邮件地址或电话号码的个人资料，则需要在导入文件中使用单独的行来指定主要人员 ID（外部 ID）、电话或电子邮件地址的其他联系人。您最多可以添加 **_20 个任意类型的附加联系人_**。超过此限制的记录将在文件分析期间被报告为无效并在导入时跳过。 

例如：

|ID|名|姓|电话号码|电子邮件|
|---|---|---|---|---|
|`5366`|`丹尼尔`|`史密斯`|`18004444444`|`j.doe@gmail.com`|
|`5366`|`丹尼尔`|`史密斯`|`12136210002`|`john_d@contoso.com`|

[![人员导入示例配置文件](https://www.infobip.com/docs/images/uploads/articles/people-example-import-profile.png)](https://www.infobip.com/docs/images/uploads/articles/people-example-import-profile.png)

4. 选择当人员中已存在匹配的人员配置文件（即相同的外部人员 ID、电话或电子邮件）时要执行的操作：

•    **丢弃** – 从文件中删除重复记录并且不导入它们  
•    **合并** – 将重复记录合并到一个人的个人资料中

##### 笔记

人脉中的单个个人资料最多可以有 100 个联系人（电话号码和/或电子邮件地址）。如果超过限制，导入将失败。

5. 分配标签来组织数据。系统将为每个导入添加标签，以便您可以跟踪导入的内容。添加标签时您有两种选择：

- **自动** - 默认系统标签应用于导入
- **手动 -** 您定义要应用于导入的标签。

[![人员导入后分析](https://www.infobip.com/docs/images/uploads/articles/people-import-post-analysis.png)](https://www.infobip.com/docs/images/uploads/articles/people-import-post-analysis.png)

6. 要开始导入过程，请单击 **“导入”**。导入时间取决于数据量。文件上传后，您将收到通知。

### 使用API

 如果您想完全消除将受众信息手动导入客户数据平台的需要，请选择使用 [People API 。](https://www.infobip.com/docs/api#customer-engagement/people)生成您的 [API 密钥，然后仔细阅读有关如何](https://www.infobip.com/docs/api)[管理受众的](https://www.infobip.com/docs/api#customer-engagement/people)说明 。

## 解决重复项

将文件导入 People 并出现重复数据时，选择是否要放弃或自动解决重复数据。如果丢弃，重复的数据将不会导入到系统中。

自动解决如何工作？假设您的文件中有两个条目共享相同的电话号码，并且您的人员数据库中有一个人具有相同的电话号码。第一个图像是文件数据，其中两行共享相同的电话号码，第二个图像显示具有相同电话号码的人员中的个人资料。   

![导入包含客户数据的文件](https://www.infobip.com/docs/images/uploads/articles/rows-in-file-import.png)

[![个人资料新](https://www.infobip.com/docs/images/uploads/articles/person-profile-new-f.png)](https://www.infobip.com/docs/images/uploads/articles/person-profile-new-f.png)

导入数据时，选择 **“自动解析”**，系统将执行以下操作： 

1. 查找文件中所有重复的记录。在上面的第一个示例中，第 1 行和第 2 行共享相同的电话号码，但具有不同的电子邮件地址和姓名。

2. 查找人脉平台上是否存在具有相同号码或电子邮件地址的其他个人资料。在我们使用的示例中，电话号码 385911234567 已属于 People 中的 Person ID 2649。 

3. 将文件内的重复记录合并为一行，并以电话号码 385991234567 作为此操作的基础。 

4. 从文件中合并。人员 `ID 2649` 和电话号码 `385911234567` 是此操作的基础。因此，您将拥有一个名为 `Chloe Omny `的人员 `ID 2649`、电话号码 `385911234567` 以及文件中的两个电子邮件地址。我们可以为每个人存储多个不同的电子邮件地址或号码。 

![客户联系信息](https://www.infobip.com/docs/images/uploads/articles/contact-info-example.png)

##### 笔记

当仅支持一个值（名字、姓氏、国家/地区、出生日期）的数据类型存在差异时，我们会保存最新的条目。但是，当支持多个值（目的地、电话号码、电子邮件）的数据类型存在差异时，我们会保存所有条目，因为一个人可以拥有多个电子邮件地址和电话号码。

## 合并配置文件

有时，客户会通过 2 个不同的渠道进入您的客户群，并拥有 2 种不同的个人资料：一种基于电话号码，另一种基于电子邮件地址或 Facebook ID。为了简化沟通，我们建议您找到这些“重复”配置文件并将其合并。

1. 在**人员**模块中，有一个显示所有个人资料的**列表视图**。选择要合并的内容并单击 **“合并”**。

[![客户信息合并](https://www.infobip.com/docs/images/uploads/articles/customer-information-merged.png)](https://www.infobip.com/docs/images/uploads/articles/customer-information-merged.png)

 您还可以选择一份个人资料，然后搜索属于同一个人的另一份个人资料（查找并合并）。

2. 决定您希望在最终合并的个人资料中保留哪些信息；必须至少选择一个联系信息字段。只有重复的字段才会出现在侧面板中。 

仅存在于其中一个配置文件中的字段将自动添加到新的合并配置文件中。要查看这些字段，请单击 **“显示更多”** 。 

[![合并配置文件选择信息](https://www.infobip.com/docs/images/uploads/articles/merge-profiles-select-info.png)](https://www.infobip.com/docs/images/uploads/articles/merge-profiles-select-info.png)

##### 重要的

在此功能的当前版本中，不支持[对话。](https://www.infobip.com/docs/conversations)这意味着在合并配置文件后，所有对话都将被删除。

## 同步

通过外部集成选项或 API 让您的平台和我们的平台之间的信息不断流动和更新。 

### 应用程序编程接口

我们的 API 文档将指导您完成设置。对于初学者，可以通过 Infobip API 页面获取 API 密钥，有关如​​何管理受众的进一步说明，请浏览  我们的客户参与文档中的 [“人员”部分。](https://www.infobip.com/docs/api#customer-engagement/people)

### 外部集成

外部集成支持 我们数据平台上**可用的所有类型的配置文件** 。映射数据时，检查字段是否匹配（潜在客户到潜在客户和客户到客户）。这将在我们当前提供的两种类型的集成的集成文章中进行额外解释： 

  
  
 

#### Salesforce 销售云

查看如何在[Salesforce Sales Cloud](https://www.infobip.com/docs/integrations/salesforce-integration)和我们的数据平台之间同步受众。 

#### 微软动态365

将人员与 [Microsoft Dynamics 365](https://www.infobip.com/docs/integrations/microsoft-dynamics-365-and-people-module)连接起来 ，以集成您存储在其中的客户数据。

## 出口

要将人员配置文件导出为 xlsx 格式，您可以从“人员”模块的“人员”视图中执行此操作。选择要导出的配置文件，然后单击**导出**以打开导出窗口。

[![人物档案导出](https://www.infobip.com/docs/images/uploads/articles/people-profile-export.png)](https://www.infobip.com/docs/images/uploads/articles/people-profile-export.png)

在导出窗口中，选择要包含在导出中的标准属性、联系信息和/或自定义属性。如果您的系统中有很多属性，请使用搜索栏来过滤可用的项目。

##### 笔记

日期和时间属性将以 ISO 8601 格式导出。 

- 日期：yyyy-MM-dd（例如 2023-02-23）

- 日期和时间（UTC 格式）：yyyy-MM-dd'T'HH:mm:ss.SSS（例如 2023-02-23T10:17:07.000） 

当您准备好导出数据时，单击**“导出”。** 导出将立即开始。完成后，单击“人员”屏幕左下角的**“下载”按钮来下载导出文件。**

[![人员导出资料下载](https://www.infobip.com/docs/images/uploads/articles/people-export-profile-download.png)](https://www.infobip.com/docs/images/uploads/articles/people-export-profile-download.png)

##### 笔记

您以前的导出首选项将被存储，因此当您运行新的导出时将选择相同的属性。但是，如果您从不同的设备或浏览器访问 Web 界面，则需要重新进行选择。