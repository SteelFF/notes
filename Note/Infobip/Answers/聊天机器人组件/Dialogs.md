对话是执行特定任务或一组任务的聊天机器人的组件。示例：银行聊天机器人需要执行诸如欢迎最终用户、提供有关抵押和储蓄的信息以及结束对话等任务。因此，聊天机器人可以为这些任务提供以下对话——欢迎、抵押、储蓄和再见。

对话将长对话分成易于管理的部分。聊天机器人和最终用户之间的整个通信都是通过对话进行的。

对话包含聊天机器人元素并遵循逻辑流程。

在人工智能聊天机器人中，每个对话都有一个意图。有关对话和意图如何在 AI 聊天机器人中工作的信息，请参阅[AI](https://www.infobip.com/docs/answers/ai-chatbots/intents)部分。

## 对话框如何工作

以下示例显示“开放时间”对话框。该对话框告知最终用户商店的营业时间。提供此信息后，流程将路由到下一个对话框，该对话框执行不同的任务。

“开放时间”对话框包含“文本”和“至对话框”元素。

[![对话](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-sample.png)](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-sample.png)

## 对话框类型

聊天机器人可以包含以下类型的对话框。

### 标准对话框

标准对话框是您为执行各种任务而创建的对话框。这些对话框包含聊天机器人的大部分操作。为了提高聊天机器人流程的清晰度，请为每个聊天机器人操作创建一个单独的对话框。示例：欢迎、创建帐户、管理约会和再见。

您可以创建的标准对话框的数量没有限制。

### 默认对话框

创建聊天机器人时默认添加此对话框。这始终是第一个对话。使用此对话框发送欢迎消息并确定最终用户的需求。

 聊天机器人中只能有一个 **默认对话框。**

此对话框还用作任何未定义场景的后备。

您无法移动或删除该对话框。您可以重命名该对话框。

默认对话框具有自动分配的默认[意图](https://www.infobip.com/docs/answers/ai-chatbots/intents)。您不能取消此意图与对话框的链接或将其他意图添加到此对话框。

### 验证对话框

聊天机器人中的某些对话可能包含敏感数据。示例：获取或更新个人信息的对话框。只有经过身份验证的最终用户才能访问此类对话框。

使用身份验证对话框验证最终用户的身份。当最终用户进入受保护的对话框时，将他们重定向到身份验证对话框。身份验证对话框根据 Infobip People 解决方案或第三方应用程序验证最终用户的数据。如果身份验证成功，只有最终用户才能访问受保护的对话框。对话框中的锁定图标表示该对话框受到保护。使用[To Dialog](https://www.infobip.com/docs/answers/chatbot-elements/core-elements#to-dialog)元素将最终用户路由到身份验证对话框。

有关创建身份验证对话框的分步说明，请参阅[创建聊天机器人](https://www.infobip.com/docs/answers/create-chatbot/create-secured-chatbot#authenticated-dialogs)部分。

[![验证对话框](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-authenticated-dialogs-process-flow.png)](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-authenticated-dialogs-process-flow.png)

当最终用户通过身份验证时，**经过身份验证的** [固定属性](https://www.infobip.com/docs/answers/chatbot-structure/attributes#fixed-attributes-attribute-types)设置为 True。

您可以配置身份验证，使其在特定时间段后过期。这段时间过后，最终用户需要再次对自己进行身份验证。有效期从最终用户发送的最后一条消息开始。当身份验证过期时，**经过身份验证的**固定属性设置为 False。示例：认证超时时间设置为 5 分钟。在对自己进行身份验证后，最终用户让聊天保持打开状态，并在 5 分钟后重新加入。由于身份验证已过期，最终用户需要再次对自己进行身份验证。

您不能通过身份验证限制默认和会话过期对话框。

您不能将意图添加到身份验证对话框。

### 会话过期对话框

当您希望聊天机器人通知最终用户他们的会话已完成时，请使用此对话框。您还可以让他们知道，如果他们决定继续对话，将开始新的会话。

您无法向此对话框添加意图。

## 对话组

如果聊天机器人有大量对话，按功能对它们进行分组可能会更容易。

示例：银行聊天机器人可能具有用于创建帐户、更新帐户和关闭帐户的对话框。您可以将这些对话框分组在对话框组帐户下。同样，您可以有一个用于 Mortgage 的对话组，等等。

**默认**对话框组会自动添加。您不能重命名、删除或移动此对话框组。

[![对话组](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-groups.png)](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-groups.png)

## 对话路由

对话框可以通过以下方式路由：

- 基于关键字：当聊天机器人识别出最终用户消息中的关键字时，聊天机器人会相应地引导流程。有关创建基于规则的聊天机器人的分步说明，请参阅 [创建聊天机器人](https://www.infobip.com/docs/answers/create-chatbot/create-keyword-chatbot) 部分。
- 基于意图（[人工智能聊天机器人](https://www.infobip.com/docs/answers/ai-chatbots)）：当最终用户发送消息时，聊天机器人会识别意图，并将最终用户引导至相关对话框。

## 对话框选项卡

此选项卡包含以下部分：

- 左窗格 - 对话框和对话框组列表
- 中间窗格 - 对话框生成器
- 右窗格 - 聊天机器人元素和集成列表。当您在中间窗格中选择一个元素或集成时，右侧窗格会显示聊天机器人元素和集成的配置

[![对话框选项卡](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-tab.png)](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-tab.png)

## 指南和最佳实践

- 对每个对话主题使用单独的对话框。示例：对于银行聊天机器人，使用
- 抵押贷款和储蓄的单独对话框。
- 对于 AI 聊天机器人，为每个意图创建一个单独的对话框。
- 如果许多对话框包含相同的内容和/或逻辑，请创建一个基本对话框。然后，[复制该对话框](https://www.infobip.com/docs/answers/chatbot-structure/dialogs#duplicate-a-dialog-how-to) 并对其进行修改，而不是创建新对话框。
- 添加关闭对话框。当最终用户完成查询时，将他们引导至此对话框。
- 您只能将意图添加到标准对话框。您无法将意图添加到“默认”、“身份验证”和“会话过期”对话框。
- 您无法取消默认意图与默认对话框的链接。

## 如何

### 创建一个对话框

在聊天机器人编辑器中，会自动添加**默认对话框。**

您可以添加以下类型的对话框：

- **标准对话**：在对话组中。单击**添加对话框**。新对话框被添加到对话框组中。  
    [![创建对话框](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-add.png)](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-add.png)
- **身份验证对话框**：单击 **“添加身份验证对话框”** 。默认情况下，此对话框添加到 **“默认”** 对话框组中。如果需要，您可以将此对话框移动到其他对话框组。  
    [![添加身份验证对话框](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-create-authentication.png)](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-create-authentication.png)
- **会话过期对话框**：单击 **“添加** **会话过期对话框”** 。
    - 此对话框仅支持某些**聊天机器人**元素。
    - 默认添加一个 **文本元素。** 如果需要，您可以删除此元素。
    - 默认情况下，此对话框添加到 **“默认”** 对话框组中。如果需要，您可以将此对话框移动到其他对话框组。  
        [![添加会话过期对话框](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-create-session-expire.png)](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-create-session-expire.png)

您现在可以添加[聊天机器人元素](https://www.infobip.com/docs/answers/chatbot-elements)并设计对话流程。

除“身份验证”和“会话过期”对话框外，所有对话框都包含一个显示对话框名称的块。您不能删除或移动此块。

### 向对话框添加元素

将一个[元素](https://www.infobip.com/docs/answers/chatbot-elements)从右侧窗格拖到聊天机器人编辑器中。

[![向对话框添加元素](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-add-elements.png)](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-add-elements.png)

### 重命名对话框

在“对话框”窗格中，单击对话框名称旁边的铅笔图标。添加新名称。

[![重命名对话框](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-rename.png)](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-rename.png)

### 复制对话框

单击对话框名称旁边的三个点以访问菜单。单击 **复制**。

[![复制对话框](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-duplicate.png)](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-duplicate.png)

##### 笔记

如果复制“默认”、“身份验证”或“会话过期”对话框，重复的对话框将创建为标准对话框。

### 删除对话框

单击对话框名称旁边的三个点以访问菜单。单击 **删除**。

[![删除对话框](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-delete.png)](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-delete.png)

##### 笔记

如果删除“身份验证”对话框，则所有已启用身份验证的对话框都将禁用身份验证。

### 将意图分配给对话框

请参阅[意图](https://www.infobip.com/docs/answers/ai-chatbots/intents#assign-an-intent-to-a-dialog-how-to)部分。

### 重新排序对话框

单击对话框名称前面的图标以获取手形图标。您可以根据需要将对话框拖放到对话框组内或其他对话框组。

您无法移动 **默认** 对话框。该对话框始终位于顶部。

[![重新排序对话框](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-reorder.png)](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-reorder.png)

### 搜索对话框

单击搜索图标并在搜索字段中输入对话框的名称。

[![搜索对话框](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-search.png)](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-search.png)

### 显示或隐藏对话框面板

单击箭头图标可显示或隐藏对话框窗格。

[![显示/隐藏对话框面板](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-show-hide.png)](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-show-hide.png)

### 为对话启用身份验证

#### 先决条件

创建[身份验证对话框](https://www.infobip.com/docs/answers/chatbot-structure/dialogs#authentication-dialog-dialog-types)以在其他对话框中启用身份验证。有关创建身份验证对话框的分步说明，请参阅 [创建聊天机器人](https://www.infobip.com/docs/answers/create-chatbot#authenticated-dialogs-secured) 部分。

#### 启用身份验证

要在 **“默认”** 对话框中启用身份验证，请使用 **“至”对话框** 元素将最终用户重定向到 **“身份验证”** 对话框。

要在其他对话框中启用身份验证，请执行以下操作：

1. 在中心窗格中，单击对话框的名称。
2. 在 **对话框设置** > **身份验证设置** 部分中，启用身份验证。

[![启用身份验证](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-settings-enable-authentication.png)](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-settings-enable-authentication.png)

该对话框现在有一个锁定符号，表示该对话框已启用身份验证。

[![锁定符号表示经过身份验证的对话框](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-authentication-lock-symbol.png)](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-authentication-lock-symbol.png)

当最终用户进入经过身份验证的对话框时，他们将被重定向到**身份验证**对话框，以执行身份验证或检查**经过身份验证**的固定属性的状态。

##### 笔记

如果删除“身份验证”对话框，则所有已启用身份验证的对话框都将禁用身份验证。

如果禁用对话框的身份验证，则当最终用户进入该对话框时，不会执行身份验证过程。

### 查看对话框设置

在中心窗格中，单击对话框的名称以查看其设置。

对于每个对话框，您可以执行以下操作：

- [](https://www.infobip.com/docs/answers/ai-chatbots/intents#assign-an-intent-to-a-dialog-how-to)为对话框[分配一个意图。](https://www.infobip.com/docs/answers/ai-chatbots/intents#assign-an-intent-to-a-dialog-how-to)
- [指定是否限制](https://www.infobip.com/docs/answers/chatbot-structure/dialogs#authentication-dialog-dialog-types)对对话框的访问。

[![配置对话框设置](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-settings.png)](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-settings.png)

### 创建对话组

单击 **“添加组”** 以创建新的对话框组。

[![创建对话组](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-dialog-groups-create.png)](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-dialog-groups-create.png)

### 重命名对话组

您无法重命名 **默认** 对话框组。 

按照以下步骤重命名其他对话框组。

1. 单击对话框组名称旁边的三个点以访问菜单。
2. 单击 **编辑**。
3. 输入姓名。

[![重命名对话框组](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-dialog-groups-rename.png)](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-dialog-groups-rename.png)

### 复制对话组

单击对话框组名称旁边的三个点以访问菜单。单击**复制**。

  
[![复制对话组](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-dialog-groups-duplicate.png)](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-dialog-groups-duplicate.png)

### 删除对话组

单击对话框组名称旁边的三个点以访问菜单。单击**删除**。

您无法删除**默认**对话框组。

[![删除对话组](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-dialog-groups-delete.png)](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-dialog-groups-delete.png)

### 重新排序对话框组

单击对话框组名称旁边的三个点以访问菜单。单击**“上移”**或**“下移”**以重新排序对话框组。

您无法移动**默认**对话框组。该对话框组始终位于列表顶部。

[![重新排序对话框组](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-dialog-groups-reorder.png)](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-dialog-groups-reorder.png)

### 显示或隐藏对话框组

单击对话框组名称旁边的箭头可展开或折叠该组。

[![显示/隐藏对话框组](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-dialog-groups-show-hide.png)](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-dialog-groups-show-hide.png)

## 对话创建示例

在以下示例中，聊天机器人帮助最终用户询问其航班的登机口。

1. 在聊天机器人编辑器中，单击**“添加对话框”**以创建新对话框。
2. 在与最终用户初次联系后，聊天机器人需要询问他们有关航班的信息。**因此，将属性**元素拖放到对话框块下。
    1. 在文本框中，输入聊天机器人发送给最终用户的文本。询问航班号。您可以合并以前创建的属性（例如名字），以使消息更加个性化。
    2. 添加一个属性来保存最终用户的响应。  
        [![添加属性](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-example-attribute.png)](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-example-attribute.png)
    3. 根据最终用户的响应，您需要对对话框进行分支以提供相关答案。**因此，将Conditions**元素拖放到**Attribute**元素下。
        1. 为每个航班号添加一个单独的分支。在右侧框架中，为每个航班号添加条件。  
            [![向条件元素添加分支](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-example-branch.png)](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-example-branch.png)
    4. 对于每个条件，添加一个后续元素，您可以在其中向最终用户提供答案。
        1. 对于特定航班号的每个分支，添加提供该航班登机口号码的**文本元素。**
        2. 对于**Else**分支，配置当最终用户的响应不满足任何条件时发生的情况。添加一个**文本**元素，说明聊天机器人无法获取所请求的信息。将最终用户发送到另一个对话框，该对话框用于处理未解决的查询。因此，添加**To agent**元素。  
            [![添加 Else 条件](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-example-branch-else.png)](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-example-branch-else.png)

[![对话流程](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-example-flow.png)](https://www.infobip.com/docs/images/uploads/articles/answers-dialogs-example-flow.png)