将 Answers 连接到 Infobip 联络中心解决方案[Conversations](https://www.infobip.com/docs/conversations)。当存在聊天机器人无法处理的复杂问题或请求时，请将聊天交给人工代理。

您可以执行以下操作：

- **转移到代理作为后备**：如果聊天机器人无法理解最终用户想要什么或最终用户超过定义的无法识别的响应数量，您可以将聊天作为[后备选项](https://www.infobip.com/docs/answers/integrations/conversations#transfer-to-agent-as-fallback)转移到代理。
- **使用 To Agent 元素转移到代理**：可能存在需要人工干预的复杂情况，或者最终用户要求与代理通话的情况。在这种情况下，请使用**“至代理**”聊天机器人元素将[聊天转移至代理](https://www.infobip.com/docs/answers/integrations/conversations#use-the-to-agent-element-to-transfer-to-agent)。
- **对座席隐藏属性值**：当您设计聊天机器人时，您可以确定当聊天从聊天机器人转移到座席时属性值是否对[座席可见。](https://www.infobip.com/docs/answers/integrations/conversations#hide-attribute-values-from-agents)
- **切换后跟踪对话**：使用**conversationId** [固定属性](https://www.infobip.com/docs/answers/design-chatbot#fixed-attributes)来识别或跟踪从 Answers 中的聊天机器人转移到 Conversations 中的座席的对话。

在对话中，具有主管角色的用户可以[执行以下操作](https://www.infobip.com/docs/conversations/supervisor-guide#self-service-over-answers-self-service-automation)：

- **监控聊天机器人对话**：主管可以监控对话并识别聊天机器人设置中的问题。
- **接管聊天机器人对话**：如果聊天机器人无法解决最终用户的查询，主管可以将对话转移给代理。

聊天无法从代理重定向到聊天机器人。

有关在 Infobip 帐户中[启用对话以及](https://www.infobip.com/docs/conversations/get-started)[使用 Answers with Conversations 的](https://www.infobip.com/docs/conversations/supervisor-guide#self-service-over-answers-self-service-automation)信息，请参阅对话文档。

## 转移给代理作为后备

回退选项仅在某些聊天机器人元素中可用。[请参阅聊天机器人元素](https://www.infobip.com/docs/answers/chatbot-elements/channels)的文档以确定元素是否支持回退。

1. 在 Infobip [Web 界面](https://portal.infobip.com/)上，转到**Answers** > **Chatbots**并导航到您的聊天机器人。
2. 找到要将最终用户从中重定向到代理的元素。
3. 在**Fallback action**字段中，选择**Connect to agent**。
4. （可选）在**回退消息**字段中，通知最终用户您正在将聊天转移给代理。

在以下示例中，发送给最终用户的消息重复了 2 次。如果最终用户仍然没有提供相关响应，则应用后备操作“连接到代理”。

[![转移给代理作为后备选项](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-conversations-as-fallback.png)](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-conversations-as-fallback.png)

## 使用“至代理”元素转接至代理

使用[**“至代理**](http://www.infobip.com/docs/answers/channels#to-agent)”聊天机器人元素将聊天转移至对话中的代理。

1. 在 Infobip [Web 界面](https://portal.infobip.com/)上，转到**Answers** > **Chatbots**并导航到您的聊天机器人。
2. 确定要将最终用户从中重定向到代理的元素。
3. **将To agent**元素拖放到所需元素下。
4. 在 **To Agent** 元素中，添加[标签](https://www.infobip.com/docs/answers/chatbot-elements/core-elements#tags-to-agent)以将上下文添加到对话中。这有助于将支持请求或聊天路由到对话中的正确队列。

在以下示例中，如果最终用户的响应包含单词“agent”，则聊天将转移到 Conversations。

[![作为对话框的一部分重定向到代理](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-conversations-part-of-dialog.png)](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-conversations-part-of-dialog.png)

##### 重要的

To  **Agent** 元素立即结束 Answers 会话。

## 对代理隐藏属性值

您可以隐藏 [属性](http://www.infobip.com/docs/answers/design-chatbot#attributes) 值，以便代理无法在对话的上下文卡中查看这些值。但是，代理可以在聊天记录中看到属性值。 

1. 在 Infobip [Web 界面](https://portal.infobip.com/)上，转到**Answers** > **Chatbots**并导航到您的聊天机器人。
2. 转到**属性**选项卡 >**属性**部分。
3. 导航到您不希望客服人员看到其值的属性。
4. 取消选择**对座席可见**字段。 

[![对代理隐藏属性值](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-conversations-hide-attribute-value.png)](https://www.infobip.com/docs/images/uploads/articles/answers-integrations-conversations-hide-attribute-value.png)