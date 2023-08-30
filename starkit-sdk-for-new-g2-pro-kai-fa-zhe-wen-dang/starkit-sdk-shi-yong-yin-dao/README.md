# 😎 stARkit SDK 使用引导

### stARkit SDK 使用引导

#### 2.1 设备版本说明

本SDK用于支持**联想晨星New G2 Pro眼镜**在**联想拯救者Y90手机**上的开发。

#### 系统信息

**支持设备** New G2 Pro + Y90

**计算能力** 高通骁龙8Gen1，GPU+DSP，12GB内存+256GB闪存

**光学参数** 双目透明显示，双1080P

**声学系统** 双麦克风、双喇叭

**CV** **系统** 摄像头自动对焦800万像素，双鱼眼摄像头，TOF相机

**空间定位** 高精度VSLAM，空间锚定

**交互方式** 头动、按键、遥控器

**无线连接** 内置5G/Bluetooth/Wifi/GPS/NFC

**可扩展性** 双USB Type-C接口

**安卓版本** Android 12

**系统版本** 3.6.4

#### 开发前设备连接方式

1.  将AR眼镜插入手机侧面的Type-C口中，会自动进入连接界面，启动眼镜端桌面ARLauncher。

    **注意：插入手机底部的Type-C口无效。**

    ![https://www.notion.soREADME\_Img/newg2pro\_connect.png](https://www.notion.soreadme\_img/newg2pro\_connect.png)
2.  ARLauncher启动后，在手机端会显示晨星遥控器界面，可支持与眼镜中的AR应用进行空间交互。另外点击遥控器右上角设置图标可以查看关于眼镜的版本信息，开关系统服务，获取使用帮助等。

    ![https://www.notion.soREADME\_Img/controller\_main.png](https://www.notion.soreadme\_img/controller\_main.png)

    ![https://www.notion.soREADME\_Img/controller\_settings.png](https://www.notion.soreadme\_img/controller\_settings.png)

    ![https://www.notion.soREADME\_Img/controller\_function.png](https://www.notion.soreadme\_img/controller\_function.png)

    在AR眼镜端会显示ARLauncher界面，用于呈现和管理预装应用以及基于SDK开发的AR应用，点击对应的应用图标既可启动；同时ARLauncher界面可支持调整眼镜的显示亮度。

    ![https://www.notion.soREADME\_Img/launcher.png](https://www.notion.soreadme\_img/launcher.png)
3. AR眼镜连接**手机侧面Type-C口**后，底部Type-C口可以同时支持扩展usb设备使用，但不支持充电或者外接usb线调试，如需要调试应用查看日志请使用wifi-adb进行调试, 使用详情请咨询售后。

#### 2.2 开发环境准备

#### 开发环境

基于stARkit Unity SDK开发应用，需要在本机安装Unity(SDK开发版本是**Unity2020.3.16**，在使用此SDK开发时建议使用**2020.3.16**或以上版本)。

安装unity过程中，可以根据需要选择相应的组件，若需要编译Android和iOS应用，**请参考下图勾选**。

**建议：请确保勾选Microsoft Visual Studio模块，或自行在本机安装其他版本Visual Studio。**

![https://www.notion.soREADME\_Img/unity\_install\_option.png](https://www.notion.soreadme\_img/unity\_install\_option.png)

#### SDK下载

请登录[晨星开发者平台](https://daystar.lenovo.com/dev)后，在顶部菜单“**下载**”—“**SDK下载**”页面中，下载stARkit SDK。**建议下载最新版本。**

![https://www.notion.soREADME\_Img/download\_sdk.png](https://www.notion.soreadme\_img/download\_sdk.png)

#### 开发账号注册

如果开发者需要使用SDK的识别定位功能，需要进行开发账号的注册。

stARkit开发者门户网站——[晨星视觉平台](https://daystar.lenovo.com/dev/#/home)提供项目管理、许可证管理等功能。登录晨星视觉平台,通过右上方按钮进行账户注册或登录。登录后在开发者中心—我的项目中新建项目或管理已有项目。

1.  注册新账号，然后登录使用自己的账号登录

    ![https://www.notion.soREADME\_Img/Net\_login.png](https://www.notion.soreadme\_img/Net\_login.png)
2.  账号登录成功后选择 **开发者中心** **我的项目**

    ![https://www.notion.soREADME\_Img/Net\_myproject.png](https://www.notion.soreadme\_img/Net\_myproject.png)
3.  进入我的项目后选择右上角的**新建项目**，根据提示完成操作

    ![https://www.notion.soREADME\_Img/Net\_newproject.png](https://www.notion.soreadme\_img/Net\_newproject.png)

    1. 新建完成后点击项目进入完成许可证的配置。

    ![https://www.notion.soREADME\_Img/Net\_projectid.png](https://www.notion.soreadme\_img/Net\_projectid.png)

    1. 将许可证的key和secret复制记录下来，在开发时填入场景中的SRApplication中

    ![https://www.notion.soREADME\_Img/Net\_key\_secret.png](https://www.notion.soreadme\_img/Net\_key\_secret.png)

    ![https://www.notion.soREADME\_Img/SRApplication.png](https://www.notion.soreadme\_img/SRApplication.png)

#### 2.3 导入Unity SDK

#### 创建工程

运行已安装的Unity软件，选择创建一个新工程。

![https://www.notion.soREADME\_Img/CreateProject\_new.png](https://www.notion.soreadme\_img/CreateProject\_new.png)

在①处设置你的工程名，在②处选择工程的存储路径，最好是一个空的文件夹，设置好后点击“创建”按钮，等待工程创建成功后打开。

![https://www.notion.soREADME\_Img/CreateProject\_name.png](https://www.notion.soreadme\_img/CreateProject\_name.png)

#### 导入SDK

从菜单栏“Asserts->Import Package->Custom Package..”导入[stARkit for daystAR SDK Unity Package](about:blank#SDK%E4%B8%8B%E8%BD%BD)为自定义包； 默认导入全部资源。

![https://www.notion.soREADME\_Img/Import\_package.png](https://www.notion.soreadme\_img/Import\_package.png)

**注意：** **如果导入SDK后Unity编辑器提示下图中错误信息时，需手动删除Assets/Common/Plugins/JsonNet路径下的Newtonsoft.Json.dll文件。**

![https://www.notion.soREADME\_Img/Newtonsoft.Json.dll.png](https://www.notion.soreadme\_img/Newtonsoft.Json.dll.png)

然后在Unity场景层级窗口中，删除Main Camera。

![https://www.notion.soREADME\_Img/Delete\_camera.png](https://www.notion.soreadme\_img/Delete\_camera.png)

#### 场景配置

在stARkit SDK中，提供了一些示例场景，其中**Assets/stARkit/Lenovo.stARkit.SDK/Tutorial/MixtureSeeThrough/Scene**为最基础的AR场景，可将此场景build出apk安装包，在晨星AR设备上面体验；若需要自己制作场景，请参考以下步骤：

在Unity下方的项目窗口中，找到预制体Assets/stARkit/Lenovo.stARkit.SDK/Prefab/StARkitMixtureGlassSeeThrough.Prefab。

![https://www.notion.soREADME\_Img/Path\_GST.png](https://www.notion.soreadme\_img/Path\_GST.png)

将预制体StARkitMixtureGlassSeeThrough拖入scene（Hierarchy）。或者选择StARkit Tool -> Add to scene with StARkitMixtureGlassSeeThrough快捷按钮。

![https://www.notion.soREADME\_Img/Button\_GST.png](https://www.notion.soreadme\_img/Button\_GST.png)

将许可证预制体**SRApplication**放入场景（通过StARkit Tool -> Add to scene with StARkitMixtureGlassSeeThrough 快捷键添加的会自动创建预制体，不需要再拖一遍），然后将开发者账号配置的项目许可证填写到预制体的对应参数上，SRApplication必须填写正确，3.5及以后的sdk进入应用必须联网验证。

![https://www.notion.soREADME\_Img/Net\_key\_secret.png](https://www.notion.soreadme\_img/Net\_key\_secret.png)

![https://www.notion.soREADME\_Img/SRApplication.png](https://www.notion.soreadme\_img/SRApplication.png)

将预制体SRFrameManager拖入scene (Hierarchy), 晨星设备上选择设备类型为D\_Starkit。

![https://www.notion.soREADME\_Img/SRFrameManager.png](https://www.notion.soreadme\_img/SRFrameManager.png)

在scene中创建一个方块。

![https://www.notion.soREADME\_Img/Create\_cube.png](https://www.notion.soreadme\_img/Create\_cube.png)

将方块放到初始显示的正前方1米处，并将方块的长宽高都设置为0.2米。

![https://www.notion.soREADME\_Img/Cube\_size.png](https://www.notion.soreadme\_img/Cube\_size.png)

按快捷键Ctrl+S 保存场景，选择保存的路径和场景名。

![https://www.notion.soREADME\_Img/Save\_scene.png](https://www.notion.soreadme\_img/Save\_scene.png)

#### 2.4 应用打包和部署

在按照教程构建好应用场景后，可以将其打包为apk应用。首先需要保证已安装好编译安卓应用必需的工具和组件，可以参考\*\*[开发环境](about:blank#%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83)\*\*部分的内容。 编译安卓应用的具体步骤如下:

#### 切换平台

在Unity窗口点击 “File->Build settings”中选择安卓平台，先点击switch platform， 待完成后点击player settings，如下图所示：

![https://www.notion.soREADME\_Img/switch\_to\_android.png](https://www.notion.soreadme\_img/switch\_to\_android.png)

#### 填写应用名称

![https://www.notion.soREADME\_Img/Build\_name1.png](https://www.notion.soreadme\_img/Build\_name1.png)

![https://www.notion.soREADME\_Img/Build\_name2.png](https://www.notion.soreadme\_img/Build\_name2.png)

设置Player setting -> Resolution and Presentation ->Default Orientation ->Landscape Left，取消勾选Optimized Frame Pacing。

![https://www.notion.soREADME\_Img/Def\_Ori\_LandScapeLeft.png](https://www.notion.soreadme\_img/Def\_Ori\_LandScapeLeft.png)

#### 编译配置

unity2021.3以下勾选Player setting -> Other Setting -> Auto Graphics API，unity2021.3及以上取消勾选Auto Graphics API。Color space一般配置为Gamma。

![https://www.notion.soREADME\_Img/Auto\_Grap\_API.png](https://www.notion.soreadme\_img/Auto\_Grap\_API.png)

设置Player setting -> Other Setting -> Multithreaded Rendering -> false，关闭多线程渲染。Lightmap Encoding选->Low Quality。

眼镜端开发StARkitMixtureGlassSeeThrough中的抓图功能与勾选 Graphics Jobs 选项有冲突，会导致glCreateProgram Error，所以在**眼镜开发时建议不勾选Graphics Jobs**。

![https://www.notion.soREADME\_Img/Mult\_Rend.png](https://www.notion.soreadme\_img/Mult\_Rend.png)

Target API Level不做特定版本限制，SDK验证过没有问题的API Level为(25、27、29)。

**注意：**

**1.    如果编译过程中出现下图错误提示，请下载对应的Android SDK版本并在Unity环境中正确配置。**

![https://www.notion.soREADME\_Img/android\_sdk\_level\_error\_hint.png](https://www.notion.soreadme\_img/android\_sdk\_level\_error\_hint.png)

**2.    API Level低于29的时候需要删除AndroidManifest.xml中application标签下的 android:requestLegacyExternalStorage=“true”属性。**

**3.    API level 25时需设置Player setting -> Resolution and Presentation ->Supported Aspect Ratio->Aspect Ratio Mode:Custom，Up To:3.6。**

![https://www.notion.soREADME\_Img/AspectRatioMode.png](https://www.notion.soreadme\_img/AspectRatioMode.png)

Scripting Backend可选IL2CPP也可选Mono，Target Architectures建议选择 ARM64，算法运行速度在ARMv7模式下会比ARM64更慢，请根据实际情况选择。

![https://www.notion.soREADME\_Img/Lib64.png](https://www.notion.soreadme\_img/Lib64.png)

设置Player setting -> Configuration -> Write Permission : External（SDCard）。

![https://www.notion.soREADME\_Img/Writ\_Perm.png](https://www.notion.soreadme\_img/Writ\_Perm.png)

在Porject Settings面板选择Quality找到Rendering模块：设置Anisortropic Texture -> Per Texture，设置Anti Aliasing -> Disabled 。Other模块：设置VSync Count -> Don‘t Sync。

![https://www.notion.soREADME\_Img/quality.png](https://www.notion.soreadme\_img/quality.png)

#### 编译应用

退出“Player Settings“窗口，回到”Build Setting“窗口，依次点击”Add Open Scenes" 和“Build”，获取安卓平台应用。

![https://www.notion.soREADME\_Img/Start\_building.png](https://www.notion.soreadme\_img/Start\_building.png)
