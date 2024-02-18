Azure Active Directory B2C（Azure AD B2C）是一种客户身份访问管理（CIAM）解决方案，包含在Microsoft Azure服务目录中。它的价值主张是从客户（可口可乐）那里抽象出所有的消费者身份验证和授权过程。正如他们所声称的，除了上述功能之外，他们不打算提供其他功能，因此消费者数据存储、隐私合规规则引擎和其他一些功能并不是现成的。然而，它们确实提供了一种策略/工作流定义机制，可以帮助我们通过所需的脚本/编码来满足其中的一些需求。

AAD B2C的基本工作如下
![[Pasted image 20230711101235.png]]

该项目中，使用AAD B2C收集用户的身份识别信息，也就是I AM数据，属于用户profile数据。应用通过Lambda上的REST API获取用户的profile数据，但是这些数据处理过程未被一些隐私合规规则覆盖。因此需要项目中间层实现DATA Privacy策略。


## B2C custom Policies
我们可以使用user flow来快速创建注册、登录和策略编辑体验流程。User flow是微软提供的预定义的、内置的、可配置的策略。
而复杂的流程策略可以采用custom policy 来创建，具体流程需要重点学习
  
![用户流设置 UI 与自定义策略配置文件的屏幕截图。](https://learn.microsoft.com/en-us/azure/active-directory-b2c/media/user-flow-overview/user-flow-vs-custom-policy.png)