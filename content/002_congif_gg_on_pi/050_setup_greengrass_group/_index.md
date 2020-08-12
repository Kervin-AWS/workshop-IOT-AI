---
title: "4. 配置Greengrass group"
date: 2018-10-03T10:17:52-07:00
draft: false
weight: 50
---
1.	回到 AWS IOT console，在刚才 greengrass 组的页面，选择“使用现有的Lambda”，选中刚才创建的 Lambda 函数（实验人数较多，请选中自己创建的那个函数）。点击”完成“。
    ![Image](/images/png/.png)
    ![Image](/images/png/.png)

2. 在出现的界面中，右侧有三个点，点击一下并选中”编辑配置“。
    ![Image](/images/png/.png)
    将内存限制修改为 512MB，超时修改为 10秒，
    
    并将 **Lambda 生命周期改为使此函数长时间生存，保持其无限期运行；对 /sys 目录的只读访问权限选中为”启用“。在环境变量中，输入 key-value 对的信息：MXNET_ENGINE_TYPE和NaiveEngine。**
    
    最后点击”更新“。
    
    配置界面如下图所示：
    ![Image](/images/png/.png)

3. 在出现的页面中选择”资源“，然后点击添加本地资源，分别创建 4 个本地资源，以使 Lambda 函数具备访问摄像头和文件系统的权限。
    ![Image](/images/png/.png)

4. 创建摄像头设备资源 webcam，选项如下图所示：
    ![Image](/images/png/.png)

5. 创建videoCoreInterface资源，选项如下图所示：
    ![Image](/images/png/.png)

6. 创建videoCoreSharedMemory资源，选项如下图所示： 
    ![Image](/images/png/.png)

7. 下载以下链接的机器学习模型（该模型由 Amazon Sagemaker训练出来的）到你的本地电脑：
[https://greengrass-ml-hands-on-lab.s3.cn-north-1.amazonaws.com.cn/new-human-action-model.tar.gz](https://greengrass-ml-hands-on-lab.s3.cn-north-1.amazonaws.com.cn/new-human-action-model.tar.gz)

8. 在IOT console 界面选中Machine Learning选项，并点击”添加机器学习资源“：
    ![Image](/images/png/.png)

    资源名称输入human-action-detect-model，并选择”**在 S3 中上传模型 (包括通过深度学习编译器优化的模型)**“，点击上传模型。
    ![Image](/images/png/.png)

    在新打开的 S3 界面创建一个存储桶（注：如果别的实验学员已经创建好了就无需创建了，直接用别人已经上传的模型就好了）
    
    并且把刚才下载的机器学习模型上传，上传的时候全部采用默认值即可。

    ![Image](/images/png/.png)

    在 IOT console 点击刷新按钮，选中刚才上传的模型文件

    ![Image](/images/png/.png)

    在本地路径中输入/models，意为将模型下载到 Greengrass 容器的/models 路径中，点击”保存“。
    ![Image](/images/png/.png)

9. 检查 lambda 的资源配置。如下图所示：
    ![Image](/images/png/.png)

10. 点击返回箭头，并选中”订阅“，点击”添加订阅“。在本步骤中将定义消息传递的逻辑关系。

    Lambda 函数通过推理产生的 IOT 消息可以被发送到 AWS IOT。

    ![Image](/images/png/.png)

11. 选择源为”Lambda“，并选中你创建的 Lambda 函数。选择目标为服务，并选中 IOT Cloud。点击”下一步“。
    ![Image](/images/png/.png)

12. 在主题筛选条件中，输入你在 Lambda 代码中定义的 topic，比如”xxxx/action“，点击下一步，并点击完成。
    ![Image](/images/png/.png)

13. 更新 service role：

    在 AWS Console 中选中 IAM ，在 roles 选项页中筛选出 Greengrass_ServiceRole ，并点击进入。
    
    在页面中找到 附加策略，点击后筛选出 AmazonS3ReadOnlyAccess，点击Attach policy。
    
    如下图所示
    ![Image](/images/png/.png)
    
    最后Greengrass_ServiceRole的权限如下图所示：
    ![Image](/images/png/.png)

14. 回到 Greengrass group 中选中我们刚才的 group ，在左侧选中 设置 ，点击右上角的添加角色，并选中Greengrass_ServiceRole，点击保存。

    这样，Greengrass 就有权限去 S3 下载你私有的机器学习模型了，如下图所示：

    ![Image](/images/png/.png)

```shell
git clone https://github.com/Kervin-AWS/aws-alexa-workshop-smarthome-lamp-cn.git 

cd aws-alexa-workshop-smarthome-lamp-cn
```

2.	解压之前下载的 ZIP 文件. 您将获得如下文件:

    - 一个 private key, `\<thing-name\>.private.key`

    - 一个 device certificate, `\<thing-name\>.cert.pem`

3.	把 private key 重命名为 **private.key** 并且拖拽上传到 **credentials** 文件夹(选择替换).

4.	将 device certificate 重命名为 **cert.pem** 并且拖拽上传到 **credentials** 文件夹(选择替换).

5.	修改 `index.js` 中的配置并保存

    - `iotEndpoint`. 在 **IoT Core** 控制台左侧 **Settings** 菜单中可以找到
    ![Image](/images/png/016.png)

    
    - `thingName`. 之前创建的事物名称

    - `deviceBindingUrl`. 这个请不要修改它，本实验已经帮您配置好Amplify

6.	安装依赖. 在 Cloud9 的 terminal 中运行 
```shell
npm install
```

7.	运行 
```shell
node index.js 
```
启动应用程序。运行成功后， terminal 将会输出一个二维码。 在下一步中，我们将使用这个二维码完成用户和设备之间的绑定关系。
 ![Image](/images/png/017.png)