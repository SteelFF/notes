关键字是使聊天机器人能够确定流程中下一步的单词和短语。当聊天机器人识别出最终用户消息中的预定义关键字时，聊天机器人会相应地引导流程。

关键字对于关键字驱动的聊天机器人尤为重要。

## 同义词

当最终用户回复您的聊天机器人时，他们不一定会使用关键字。相反，他们可能会使用关键字的变体或同义词。示例：最终用户可能会说“reservation”，而不是使用预定义关键字“booking”。

因此，您可以将它们作为同义词添加到现有关键字，而不是将这些变体和含义添加为单独的关键字。示例：对于关键字“reservation”，您可以添加以下同义词：reserve、book、booking 和reservation。

无论最终用户的响应包含关键字还是同义词，聊天机器人都会采取相同的操作。因此，您只需为关键字定义下一步，而不是为每个同义词定义下一步。

同义词可以包括单词、短语和表情符号。

同义词不区分大小写。

## 关键词如何运作

确定您希望最终用户发送到聊天机器人的单词，并将这些单词配置为关键字。示例：如果您希望最终用户从选项列表中进行选择，请将每个选项配置为关键字。当聊天机器人识别出最终用户消息中的这些关键字时，聊天机器人会相应地引导流程。

仅当最终用户的消息包含关键字或其[同义词](https://www.infobip.com/docs/answers/chatbot-structure/keywords#synonyms) 且没有任何拼写错误或附加词时，关键字匹配才有效。

以下示例展示了如何使用关键字来引导聊天机器人流程。

定义了关键字及其同义词。

[![定义关键字和同义词](https://www.infobip.com/docs/images/uploads/articles/answers-keywords-example-define.png)](https://www.infobip.com/docs/images/uploads/articles/answers-keywords-example-define.png)

在用户输入元素中，聊天机器人检查最终用户的消息是否与这些关键字匹配，并相应地引导流程。

[![如何使用关键词](https://www.infobip.com/docs/images/uploads/articles/answers-keywords-example.png)](https://www.infobip.com/docs/images/uploads/articles/answers-keywords-example.png)

最终用户的消息是“答案”。在 User Input 元素中，此消息与关键字“Answers”相匹配。聊天机器人将流程定向到“答案”对话框。

[![如何使用关键字 - 在模拟器中测试](https://www.infobip.com/docs/images/uploads/articles/answers-keywords-example-simulator.png)](https://www.infobip.com/docs/images/uploads/articles/answers-keywords-example-simulator.png)

最终用户的消息是“聊天机器人”。聊天机器人将“聊天机器人”识别为“答案”的同义词。因此，最终用户的消息与关键字“答案”匹配。聊天机器人将流程引导至“答案”对话框。

[![同义词匹配](https://www.infobip.com/docs/images/uploads/articles/answers-keywords-example-simulator-synonym.png)](https://www.infobip.com/docs/images/uploads/articles/answers-keywords-example-simulator-synonym.png)

最终用户的消息是“我想了解答案”。关键字匹配失败。聊天机器人不会将消息与关键字“答案”进行匹配，因为该消息包含其他单词。

[![关键词匹配失败](https://www.infobip.com/docs/images/uploads/articles/answers-keywords-example-simulator-fail.png)](https://www.infobip.com/docs/images/uploads/articles/answers-keywords-example-simulator-fail.png)

最终用户的消息是“Answres”。关键字匹配失败。聊天机器人不会将消息与关键字“Answers”进行匹配，因为最终用户的消息包含拼写错误。

[![拼写错误 - 关键字匹配失败](https://www.infobip.com/docs/images/uploads/articles/answers-keywords-example-simulator-fail-spelling.png)](https://www.infobip.com/docs/images/uploads/articles/answers-keywords-example-simulator-fail-spelling.png)

## 关键字选项卡

关键字选项卡包含关键字及其同义词的列表。

[![关键字选项卡](https://www.infobip.com/docs/images/uploads/articles/answers-keywords-tab.png)](https://www.infobip.com/docs/images/uploads/articles/answers-keywords-tab.png)

## 指南和最佳实践

- 您可以创建无限数量的关键字。
- 您必须至少添加 1 个同义词。
- 为关键字添加尽可能多的同义词。同义词数量越多，聊天机器人采取正确操作的概率就越高。

## 如何

### 创建一个关键字

您可以从关键字选项卡或通过聊天机器人元素创建关键字。

#### 从关键字选项卡

1. 在 Infobip [Web 界面](https://portal.infobip.com/)上，转到**Answers** > **Chatbots**并导航到您的聊天机器人。
2. 转到**关键字**选项卡。
3. 单击**添加关键字**。
4. 在**关键字**字段中，输入关键字。示例：预订
5. 在**同义词**字段中，添加关键字的变体和同义词。示例：reservation、reserve、booking 和 book。

[![创建关键字](https://www.infobip.com/docs/images/uploads/articles/answers-keywords-create.png)](https://www.infobip.com/docs/images/uploads/articles/answers-keywords-create.png)

#### 来自聊天机器人元素

某些聊天机器人元素允许您在对话框中创建关键字。示例：[用户输入](https://www.infobip.com/docs/answers/chatbot-elements/core-elements#user-input)。

[![从聊天机器人元素创建关键字](https://www.infobip.com/docs/images/uploads/articles/answers-keywords-create-from-chatbot-element.png)](https://www.infobip.com/docs/images/uploads/articles/answers-keywords-create-from-chatbot-element.png)

创建关键字后，执行以下操作：

1. 转到**关键字**选项卡。
2. 在关键字的**同义词**字段中，添加关键字的变体和同义词。示例：reservation、reserve、booking 和 book。

### 编辑或删除关键字

单击关键字旁边的菜单并选择相关操作。

[![编辑或删除关键字](https://www.infobip.com/docs/images/uploads/articles/answers-keywords-edit-delete.png)](https://www.infobip.com/docs/images/uploads/articles/answers-keywords-edit-delete.png)

## 故障排除

### 应定义至少 1 个同义词

您尚未向关键字添加任何同义词。您必须为每个关键字添加至少 1 个同义词。

[![缺少同义词](https://www.infobip.com/docs/images/uploads/articles/answers-keywords-troubleshoot-synonym.png)](https://www.infobip.com/docs/images/uploads/articles/answers-keywords-troubleshoot-synonym.png)