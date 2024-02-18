Amazon Elastic Compute Cloud（Amazon EC2）在 Amazon Web Services（AWS）云中按需提供可扩展的计算容量。使用 Amazon EC2 可以降低硬件成本，因此您可以更快地开发和部署应用程序。您可以使用 Amazon EC2 启动所需数量的虚拟服务器，配置安全性和联网以及管理存储。您可以添加容量（纵向扩展）来处理计算密集型任务，例如月度或年度进程或网站流量峰值。如果使用量减少，您可以再次减少容量（缩减）。

下图显示了部署在 Amazon Virtual Private Cloud（VPC）中 Amazon EC2 实例的基本架构。在此示例中，EC2 实例位于该区域的可用区内。EC2 实例由安全组提供保护，而安全组是控制传入和传出流量的虚拟防火墙。私有密钥将存储在本地计算机上，公有密钥则会存储在实例上。这两个密钥将指定为密钥对，用以证明用户身份。在这种情况下，实例由 Amazon EBS 卷提供支持。VPC 通过互联网网关与互联网进行通信。有关 Amazon VPC 的更多信息，请参阅《[Amazon VPC 用户指南](https://docs.aws.amazon.com/vpc/latest/userguide/)》。
![VPC 中 EC2 实例的基本架构图。](https://docs.aws.amazon.com/zh_cn/AWSEC2/latest/UserGuide/images/ec2-basic-arch.png)

Amazon EC2 提供以下高级功能：

**Instance**

虚拟服务器。

**Amazon Machine Image (AMI)**

实例的预置模板，其中包含服务器所需的组件（包括操作系统和其他软件）。

**Instance Types**

实例 CPU、内存、存储、网络容量和图形硬件的多种配置。

**Key pairs**

实例的安全登录信息。AWS 会存储公有密钥，而您可以在安全位置存储私有密钥。

**Instance Store Volumes**

停止、休眠或终止实例时会删除的临时数据的存储卷。

**Amazon EBS Volumes**

使用 Amazon Elastic Block Store（Amazon EBS）的数据的持久性存储卷。

**Regions, Availability Zones, Local Zones, AWS Outposts, and Wavelength Zone**

用于存储资源的多个物理位置，例如实例和 Amazon EBS 卷。

**Security Group**

虚拟防火墙，允许您指定可以访问实例的协议、端口和源 IP 范围，以及实例可以连接的目标 IP 范围。

**Elastic IP Addresses**

用于动态云计算的静态 IPv4 地址。

**Tag**

您可以创建并分配给 Amazon EC2 资源的元数据。

**Virtual Private  Clouds (VPC)**

您可以创建的虚拟网络，在逻辑上与 AWS 云中的其余部分隔绝。您可以选择将这些虚拟网络连接到您自己的网络。