# 🏸 附录1：晨星扫描仪使用指南

### 附录1：晨星扫描仪使用指南

#### 设备介绍

晨星扫描仪是由联想研究院开发的一款三维物体扫描应用。开发者通过其扫描设备，获取相关数据，并将数据上传至云端进行训练，生成用于联想3D物体识别——stARkit for Cross-platform SDK的目标特征文件。该应用需要硬件支持获取深度图像，因此目前适配的硬件设备为包含 LiDAR 模组的iOS设备 。主要包括iPad Pro 2020 / 2021、iPhone 12 Pro以及iPhone 12 Pro Max。

#### 应用安装与账号登录

打开App Store，搜索“**晨星扫描仪**”，选择正确应用后安装即可。

在设备中找到并打开“晨星扫描仪”应用（如图红色提示框所示），输入登录账号和密码，点击“登录”按钮，验证成功后即可登录。若登录失败，请检查账号和密码是否输入正确，以及网络是否连接。登录账号注册官网地址：[https://daystar.lenovo.com/dev](https://daystar.lenovo.com/dev)。如扫描场景无网络连接，也可选择“暂不登录”跳过登录流程直接进行扫描，待有网络环境时再将扫描结果上传至服务端进行训练。

![https://www.notion.soREADME\_Img/Scanner2.png](https://www.notion.soreadme\_img/Scanner2.png)

![https://www.notion.soREADME\_Img/Scanner3.png](https://www.notion.soreadme\_img/Scanner3.png)

使用地图重定位功能，设置页面如下配置：

![https://www.notion.soREADME\_Img/scanner\_setting.jpg](https://www.notion.soreadme\_img/scanner\_setting.jpg)

#### 扫描物体/环境

登录成功后，进入扫描界面，支持三种模式：物体扫描模式、环境扫描模式、图片模式，右上角按钮可切换不同模式。

1、默认为“物体“扫描模式，请按左下角提示操作，应用会自动框选出物体位置；环境扫描模式则是扫描整个3D空间，图片模式是对场景中某些对象进行拍照上传进行处理。下面以物体扫描为示例。

选中物体扫描模式后，观察画面中蓝色部分将物体覆盖完整后，点击“开始扫描”按钮，应用会实时重建出物体的3D网格模型，围绕物体扫描一圈后，可以点击”完成扫描“按钮结束扫描过程；同时，扫描过程中如发现扫描效果不好，可点击”重新扫描“按钮。

![https://www.notion.soREADME\_Img/Scanner4.png](https://www.notion.soreadme\_img/Scanner4.png)

![https://www.notion.soREADME\_Img/Scanner5.png](https://www.notion.soreadme\_img/Scanner5.png)

#### 上传数据

在扫描完成后，会生成模型，如图所示，可以旋转、缩放观察模型。 点击图中左上角“返回”按钮，可选择“保存”和“不保存”本次的扫描模型，选择“保存“，模型保存在设备本地文件中，选择”不保存“，会删除本次的扫描模型。 点击图中右上角上传图标，可以将扫描的模型保存并上传至云端进行识别训练。

![https://www.notion.soREADME\_Img/Scanner6.png](https://www.notion.soreadme\_img/Scanner6.png)

![https://www.notion.soREADME\_Img/Scanner7.png](https://www.notion.soreadme\_img/Scanner7.png)

上传完成后，可以在开发者门户网站的项目管理页面进行浏览、删除、编辑目标等操作，亦可下载目标后用于物体识别。
