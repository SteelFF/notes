## 配置扩展（.zip 文件存档）

您可以将扩展作为 [Lambda 层](https://docs.amazonaws.cn/lambda/latest/dg/configuration-layers.html)添加到函数中。使用层可让您在整个组织或整个 Lambda 开发人员社区中共享扩展。您可以向层添加一个或多个扩展。您最多可以为一个函数注册 10 个扩展。

您可以使用与任何层相同的方法将扩展添加到函数中。有关更多信息，请参阅[将层与 Lambda 函数结合使用](https://docs.amazonaws.cn/lambda/latest/dg/invocation-layers.html)。

###### 将扩展添加到您的函数（控制台）

1. 打开 Lamba 控制台的 [Functions](https://console.amazonaws.cn/lambda/home#/functions)（函数）页面。
    
2. 选择函数。
    
3. 选择 **Code（代码）**（如果尚未选择）。
    
4. 在 **Layers（层）** 下，选择 **Edit（编辑）**。
    
5. 对于**选择层**，选择**指定 ARN**。
    
6. 对于**指定 ARN**，输入扩展层的 Amazon Resource Name (ARN)。
    
7. 选择 **Add**（添加）。
    

## 在容器映像中使用扩展

您可以向[容器映像](https://docs.amazonaws.cn/lambda/latest/dg/images-create.html)添加扩展。ENTRYPOINT 容器映像设置指定函数的主进程。在 Dockerfile 中配置 ENTRYPOINT 设置，或者覆盖函数配置。

您可以在一个容器内运行多个进程。Lambda 管理主进程和任何其他进程的生命周期。Lambda 使用[扩展 API](https://docs.amazonaws.cn/lambda/latest/dg/runtimes-extensions-api.html) 管理扩展生命周期。

### 示例：添加外部扩展

外部扩展在 Lambda 函数之外的单独进程中运行。Lambda 为 `/opt/extensions/` 目录中的每个扩展启动一个进程。Lambda 使用扩展 API 管理扩展生命周期。函数运行完成后，Lambda 向每个外部扩展发送一个 `Shutdown` 事件。

###### 例 向 Python 基本映像添加外部扩展
``` shell
FROM public.ecr.aws/lambda/python:3.8

# Copy and install the app
COPY /app /app
WORKDIR /app
RUN pip install -r requirements.txt

# Add an extension from the local directory into /opt
ADD my-extension.zip /opt
CMD python ./my-function.py    
```
