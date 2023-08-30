# 🏵 stARkit SDK 功能

### 3. stARkit SDK 功能

#### 3.1 device和sensor相关

#### RGB功能点介绍

关于控制camera及获取RGB相机图像的接口，预制体为SRFrameManager

控制接口：（Assets.cs)

```
//========================================================
//开启相机
//========================================================
public void OpenCamera(Action<string> cb)；

//========================================================
//关闭相机
//========================================================
public bool CloseCameraDevice()；

//========================================================
//相机数据获取回调
//========================================================
public event Action<SRCameraFrame> FrameReceived;

//========================================================
//主动获取相机数据
//========================================================
public bool TryGetLatestFrame(out SRCameraFrame cameraFrame)
```

关于相机数据获取回调的代码，可以参考（Assets.cs)

```
  SRFrameManager m_frameManager = FindObjectOfType<SRFrameManager>();
  m_frameManager.FrameReceived += UpdateSRFrame;

请注意，通过TryGetLatestFrame获取RGB数据，需要访问SRCameraFrame的imageData成员变量。SRCameraFrame.image为空。

    /// <summary>
    /// 相机图像数据
    /// </summary>
    [MarshalAs(UnmanagedType.Struct)]
    public SRImageData imageData;
```

相机数据类型为YUV 420(4:2:0)，数据格式为NV21 byte\[]数据。

可以参考Assets.cs 的M\_AddFrameData()了解如何使用imageData转出texture，或把眼镜RGB数据转换成byte\[]传输。

也可以参考Assets12.cs了解如何把眼镜RGB数据用Material在场景中显示图像。

另外，如果TryGetLatestFrame拿不到数据，请确保预制体正确设置如下：

![https://www.notion.soREADME\_Img/SRFrameManagerAutoOpenCamera.png](https://www.notion.soreadme\_img/SRFrameManagerAutoOpenCamera.png)

正确设置后，打开SRBackground组件，能在眼镜里看到RGB图像以背景显示。

![https://www.notion.soREADME\_Img/SRBackground.png](https://www.notion.soreadme\_img/SRBackground.png)

**Tips: 不支持获取IMU/鱼眼，深度相机数据。**

#### 3.2 交互

#### Gaze

gaze位置始终在屏幕中心点，也就是（0，0）。它是一个二维物体，上面有碰撞体检测。会根据碰撞的深度，通过shader改变显示图标。

配合手机按键，可以实现选中功能，进行交互。

GazeEvent Demo主要介绍Gaze和Controller交互系统。Gaze通常是在平面的正中间，通过头的转动控制交互，当连接对应的手柄后，手柄控制Gaze在平面内的位置。

1.  选择Assets/stARkit/Lenovo.stARkit.SDK/Tutorial/GazeEvent/GazeEventDemo,依照上面的步骤出一个应用。

    ![https://www.notion.soREADME\_Img/Build\_GazeEvent.png](https://www.notion.soreadme\_img/Build\_GazeEvent.png)
2.  默认是事件系统是使用手柄的，可以在Scene中的EventCamera上挂的StARkitEventSystem上修改。

    ![https://www.notion.soREADME\_Img/HandCon\_setting.png](https://www.notion.soreadme\_img/HandCon\_setting.png)
3. 将Build的应用安装到指定设备上，并运行。
4.  程序运行起来后，会看到正前方有3列UI。

    **Interactive（可交互） ：** 这一列组件是可以Gaze交互的，最下方的方块也是可交互的，可以和最后一列进行对比找到其中的规律。和UGUI交互的条件是Canvas必须设置为WorldSpace，EventCamera设置为挂有StARkitEventSystem脚本的相机。3D物体的交互必须有碰撞体，且有挂有继承了Ipointer事件系统的组件或脚本。

    **Focus\&Disable（全局监听Focus和关闭Gaze） ：** 这一列是监听了Gaze的全局事件，注册事件之后，当Gaze的交互物体发生改变时或者在交互物上移动时都会收到消息，继承IstARkitFocusChangedHandler。按钮DisableGaze是动态关闭Gaze交互功能。中间的Controller按钮是当有手柄连接时切换方向键控制和3dof控制的。

    **NotInteractive（不可交互） ：** 这一栏是不可交互的，主要是用来和可交互对比的。

    ![https://www.notion.soREADME\_Img/UI\_GazeEvent.png](https://www.notion.soreadme\_img/UI\_GazeEvent.png)

Tips：和UGUI交互的条件是Canvas必须设置为WorldSpace，EventCamera设置为挂有StARkitEventSystem脚本的相机。 和3D物体交互则物体必须有碰撞体且挂有实现了Ipointer事件的脚本。

#### Controller

controller模式支持手机屏的点击事件，包括手机屏虚拟按键点击事件以及手机屏空白处点击事件。

controller交互和gaze交互的模式切换，需要从手机端controller发送控制信息，SDK不支持主动切换模式。

交互模式信息可以从以下接口获取：

status value： 0 gaze mode

status value： 1 controller mode

```
    /// <summary>
    /// 手柄连接状态回调
    /// </summary>
    /// <param name="status"></param>
    [AOT.MonoPInvokeCallback(typeof(System.Action<EControllerDataType>))]
    private static void ControllerStatusCB(EControllerStatus status)
```

(Assets.stARkit.Toolkit.cs)

#### 手机按键

参考starkitKeyInput.cs实现现有按键的回调。按键支持头端按键（ok键、back键、power键）和手机触摸屏虚拟按键 （recenter键）。以下为判断ok键是否按下的接口代码。

```
    /// <summary>
    /// 确认键按下
    /// </summary>
    /// <returns></returns>
    public static bool OkDown()
```

#### 3.3 空间定位

此功能适用于纹理较为丰富的室内场景，不适用场景：

* 室内大/空旷场景：可看到的视野纵深比较大的场景
* 室外场景：场景处于室外（光照不可控、空旷等原因）
* 弱纹理场景：场景里没有明显的参照物或图案、例如大面积白墙场景
* 重复纹理场景：场景中存在多个相同重复的目标物或图案
* 强光或暗光场景：场景中的光线过于强烈或过暗，导致相机曝光不足或过曝
* 动态场景：场景中有过多的移动目标
* 运动空间内：电梯、汽车、高铁、飞机内
* 其他：玻璃反光场景，非50HZ日光灯场景，长时间AR设备不移动的情况

Tips：若无法超出以上范围的情况难以判断，可使用设备预装“空间集锦”应用测试虚拟画面是否能稳定的在空间中显示。

#### 功能点介绍

1）主要功能函数：

**StartHeadTracking** 打开6dof/3dof功能。

| 返回值          | 参数类型 | 参数                    |
| ------------ | ---- | --------------------- |
| EStARkitCode | int  | mode：0表示6dof，1表示3dof。 |

**StopHeadTracking** 关闭 6dof/3dof功能。

| 返回值          | 参数类型 | 参数                    |
| ------------ | ---- | --------------------- |
| EStARkitCode | int  | mode：0表示6dof，1表示3dof。 |

**GetHeadPose** 根据传入的参数获取对应的slam数据。

| 返回值          | 参数类型          | 参数                                                                                                                                                          |
| ------------ | ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| EStARkitCode | STrackingData | pose :用来接收slam数据的结构体                                                                                                                                        |
|              | double        | timeStamp:slam的时间戳，应设置为绝对时间或增量时间（单位-毫秒）。option = 1时，时间戳是绝对时间，并返回其附近的姿势；option = 0时，时间戳是增量时间，并获取最新姿势，如果时间戳> 0，则应用运动预测，如果时间戳= 0，则直接返回最新姿势， 如果时间<0, 则返回历史时刻位姿。 |
|              | Int           | option:指示时间戳记是绝对时间还是相对时间,详情见参数timeStamp的介绍。                                                                                                                 |
|              | string        | node:指示目标坐标选择,“camera:rgb”:slam坐标转到RGB相机；“camera:tof”：slam坐标转到TOF相机；“head”：slam坐标转到开发者两眼中间；“imu” ：slam坐标转到IMU。                                              |
|              | Int           | coordinate\_left：指定数据的左右手系。0为右手系，1为左手系。                                                                                                                     |

**GetSlamDataNote** 获取slam数据的节点名转换。

| 返回值    | 参数类型   | 参数                                                                        |
| ------ | ------ | ------------------------------------------------------------------------- |
| string | string | note:获取数据的节点名。，按照关键字匹配，关键字分别是： “rgb”,“tof”,“head”,“imu”，如果都不匹配则默认为“head”。 |

**TransfromPose** 将fromNote节点下的Unity位姿转换到显示的Head节点。

| 返回值          | 参数类型          | 参数                                                                                                         |
| ------------ | ------------- | ---------------------------------------------------------------------------------------------------------- |
| EStARkitCode | STrackingData | fromPose：需要转换的Unity坐标系的位姿。                                                                                 |
|              | STrackingData | toPose：转化为Unity世界坐标系的位姿                                                                                    |
|              | string        | fromNode：需要转换的节点（“rgb”和“tof”选一个）                                                                           |
|              | double        | predictTime：当前显示模式下的预测时间。（GlassSeeThrough 下是“BaseDisplayAPI.Instance.DisplayGetPredoctedTime” 这个接口获取的预测时间） |
|              | string        | toNode ：需要转换的目标节点，可以不指定，默认为world节点                                                                         |

2）recenter功能

3DOF模式下，开发者可使用脚本StARkitHeadTrackingService.cs的SceneRecenter()函数，让眼镜显示的画面回到眼前。（不建议6DOF模式使用，此功能会改变虚拟物体的位姿） .

3）状态获取函数：

**StARkitGetModuleStatus** 获取各模块的当前状态。

| 返回值          | 参数类型           | 参数                  |
| ------------ | -------------- | ------------------- |
| EStARkitCode | EStARkitModule | module：需要获得哪个模式的状态。 |
|              | EStARkitStatus | status：查询的模式当前状态。   |

**RegisterModuleStatusCB** 注册状态变化的回调函数。主要是slam的状态，但是也包括其他模块。建议使用CommonStatusCallBack中的封装模块。

| 返回值          | 参数类型                           | 参数                                                                                    |
| ------------ | ------------------------------ | ------------------------------------------------------------------------------------- |
| EStARkitCode | Action\<int, int, int, IntPtr> | statusCB:注册状态回调函数，当状态发生改变时会主动回调。回调是另外的线程回调的，使用必须注意不能直接在回调中处理Unity组件。回调的参数：见此表后面的一张表格。 |

**UnRegisterModuleStatusCB** 取消状态回调注册，需和注册成对调用。

| 返回值          | 参数类型                           | 参数                                |
| ------------ | ------------------------------ | --------------------------------- |
| EStARkitCode | Action\<int, int, int, IntPtr> | statusCB:取消注册回调接口，这个一定要是注册时传入的那个。 |

**Action \<int,int,int,IntPtr>状态回调函数:**

(i)st\_MODULE\_6DOF

| Event                      | Description\_length                | Description                                                                                     |
| -------------------------- | ---------------------------------- | ----------------------------------------------------------------------------------------------- |
| 0 （PLEASE\_MOVE\_SLIGHTLY） | Description 的字节长度，用来读取IntPtr中的数据的。 | 说明字符串                                                                                           |
| 1 （TEXTURE\_NOT\_ENOUGH）   |                                    | 说明字符串                                                                                           |
| 2 （MOVE\_TOO\_FAST）        |                                    | 说明字符串                                                                                           |
| 3 （INITIAL\_SUCCESS）       |                                    | 说明字符串                                                                                           |
| 4 （TRACK\_LOST）            |                                    | 说明字符串                                                                                           |
| 5 （KEYFRAME\_COUNTER）      |                                    | 关键帧编号计数器（int）                                                                                   |
| 6 （RELOCALIZED）            |                                    | 字符串：“lost:relocalized（丢失：重新定位成功）”，“map:relocalized（地图：重新定位成功）”                                  |
| 7 （TRACKING\_QOS）          |                                    | 浮点数的跟踪质量值\[0-100]                                                                               |
| 8（MAP\_EXPORT）             |                                    | 字符串：“success”表示保存map文件成功；“fail:bad path”表示文件路径有问题；“fail:unknow”表示未知错误导致失败。                      |
| 9（MAP\_LOAD）               |                                    | 字符串“success”表示保存map文件成功；“fail:bad path”表示文件路径有问题；“fail:bad file”表示文件错误；“fail:unknow”表示未知错误导致失败。 |
| 10（ANCHOR\_FRAMES）         |                                    | 保存的锚点环境框架的数量。                                                                                   |

(ii)st\_MODULE\_3DOF

| Event                      | Description\_length | Description |
| -------------------------- | ------------------- | ----------- |
| 11（3DOF\_INITIAL\_SUCCESS） |                     | 说明字符串       |

4）状态回调函数：

| 属性名      | 类型                                                | 说明                                                   |
| -------- | ------------------------------------------------- | ---------------------------------------------------- |
| Current  | CommonStatusCallBack                              | 这是一个静态访问单例                                           |
| StatusCB | Action\<EStARkitModule, EStARkitEventID, string>; | 这是一个委托事件变量，通过+=（注册）和-=（取消注册），会在收到底层回调后的下一帧调用注册的委托事件。 |

5）主要数据结构介绍

EStARkitModule

| 属性名             | 值 | 说明            |
| --------------- | - | ------------- |
| MODULE\_STARKIT | 0 | 整个设备          |
| MODULE\_6DOF    | 1 | 6dof模块        |
| MODULE\_3DOF    | 2 | 3dof模块        |
| MODULE\_MESH    | 3 | SpatialMesh模块 |
| MODULE\_PLANE   | 4 | FindPlane模块   |

EStARkitStatus

表示各个模块当前状态。

| 属性名             | 值 | 说明       |
| --------------- | - | -------- |
| STATUS\_UNINIT  | 0 | 模块没有初始化  |
| STATUS\_INITED  | 1 | 模块已经初始化  |
| STATUS\_RUNNING | 2 | 模块正在正常工作 |
| STATUS\_STOPPED | 3 | 模块已经停止   |

ETrackingMode

| 属性名                   | 值 | 说明      |
| --------------------- | - | ------- |
| TRACKING\_POSITION    | 0 | 表示6dof。 |
| TRACKING\_ORIENTATION | 1 | 表示3dof。 |

ETrackingNote

| 属性名  | 值 | 说明     |
| ---- | - | ------ |
| None | 0 | 未定义    |
| HEAD | 1 | 人的两眼中间 |
| RGB  | 2 | RGB相机  |
| TOF  | 3 | TOF相机  |
| IMU  | 4 | IMU坐标系 |

STrackingData

| 属性名         | 类型        | 说明                                |
| ----------- | --------- | --------------------------------- |
| dTime       | double    | 时间戳，单位是秒。                         |
| dVector     | double\[] | slam的平移数据，，是x，y，z三个轴坐标的数组，所以长度为3。 |
| dQuaternion | double\[] | slam的旋转四元素数据，分别是x，y，z，w，长度为4。     |
| tag         | char\[]   | 数据标签信息，长度为10。                     |

#### 功能点样例

MixtureSeeThrough Demo主要展示双目合像显示和Slam的3dof/6dof锚定效果。支持投屏功能，详情咨询售后。

1. 选择Assets/stARkit/Lenovo.stARkit.SDK/Tutorial/MixtureSeeThrough/ MixtureSeeThrough\_6dof,按照Android Build的步骤出一个APK。
2. 将Build好的apk在设备中安装好后进入。
3. 进入应用后，会看到有一个星宝在正前方，3dof模式下，你只能转动头，无法走进或者走远观察，方块可以随着转动固定在头的相对位置，并且没有明显的形变表示功能正常。6dof模式下，不仅可以转头，还能前后上下左右的移动，从各个角度观察方块的位置，方块在真实世界的位置没有明显的移动变化表示功能正常。

#### 3.4 识别定位

本功能目前支持物体、场景、图片识别定位能力，提供对相对目标物的3D位姿估计能力（非缺陷检测等2D检测识别能力）。

本功能需要关注应用场景，识别的对象要避免大片玻璃等反光材质、避免重复场景、避免弱纹理、避免空旷场景（环境识别）。具体使用要求如下：

1）.扫描&识别要求：

（1）扫描&识别环境：非强光或暗光场景，扫描时和定位时光照，形状，相对位置相对较为一致

（2）扫描&识别速度：扫描速度不可以过快产生运动模糊，建议扫描过程中速度<0.05m/s & 10°/s，识别过程中建议静止状态识别

（3）扫描&识别方式：需要将后续识别定位视角比较均匀的覆盖扫描到，识别时的位置应与扫描过的位置距离小于1米、角度小于20度内(确保识别时的视角与扫描时的视角有大于50%的重合度)。

2）.目标物要求：

（1）物体：

* 尺寸： 长宽高最小15cm，长宽高最大200cm
* 表面纹理：物体表面有明显纹理、图案，无重复纹理/图案，非黑色吸光材料
* 物体形状：形状固定，非对称物体、非反光物体

（2）场景：

* 场景大小：建议使用场景大小 < 200平方米
* 场景纹理：场景有明显纹理、图案，无重复纹理/图案，非反光
* 场景变化：扫描场景内物体/图案在场景内无明显位置变化

（3）图片：

* 尺寸：建议识别画面中图片区域占比25%\~90%
* 图案：非对称、非重复纹理图案
* 图片印刷后保持平面贴放

3）.使用建议和注意事项：

（1）按虚实叠加精度由高到低，建议依次采用图片，物体，场景的识别定位方式。（2）识别定位算法提供一次识别定位能力，其中物体和场景的识别定位一次定位耗时较长，如需要持续虚实叠加建议使用具备6DoF tracking能力的设备进行持续6DoF tracking。

（2）虚实叠加场景下，因为引入人眼光学误差会导致相对更大的视觉误差。

（3）识别定位过程中，需要保持相机距离目标物的远近和角度在扫描范围内进行识别定位。

（4）识别功能需要提前对目标进行扫描、上传。方式一：具备激光雷达LiDAR的IPAD或Iphone运行晨星扫描仪对目标进行扫描、上传。方式二：通过晨星平台官网上传目标的图片文件，支持png, jpg格式。（仅支持图片识别定位）

**Tips：若无法判断是否支持，建议联系售前人员采用stARxxx工具进行简单应用开发尝试。**

4）.使用步骤：

基于本SDK的地图定位功能包含两个步骤：

1. 地图生成。针对待识别定位对象进行数据采集（上传或扫描），然后在后台服务器进行离线训练，生成目标特征文件。
2.  实时识别。将上个步骤生成的特征文件导入设备，基于相机实时视频流识别目标，估计其空间位姿，进而实现虚实叠加。

    以上步骤涉及三部分——**晨星扫描仪、开发者网站和SDK应用**。

    **晨星扫描仪**负责采集待识别三维物体的信息并上传到平台云端，通过训练生成识别所需的特征数据；

    **开发者网站**负责开发者管理目标，以及开发者上传数据制作目标；

    **SDK应用**则运行在设备上，负责从云端加载特征数据，并进行识别与定位跟踪，实时输出相机与目标在三维空间中的相对姿态（位移和旋转）。

![https://www.notion.soREADME\_Img/arch.png](https://www.notion.soreadme\_img/arch.png)

#### 物体识别定位

1）. 地图生成

该功能需要首先使用**晨星扫描仪**扫描采集待识别目标的数据后，上传到云端进行训练处理，进而生成目标识别信息，然后即可在平台网页端进行管理操作。 目标管理操作见[这里](about:blank#%E7%9B%AE%E6%A0%87%E7%AE%A1%E7%90%86%E5%92%8C%E4%B8%8B%E8%BD%BD)。**晨星扫描仪**的使用方法参考[此处](about:blank#%E9%99%84%E5%BD%951%EF%BC%9A%E6%99%A8%E6%98%9F%E6%89%AB%E6%8F%8F%E4%BB%AA%E4%BD%BF%E7%94%A8%E6%8C%87%E5%8D%97)。

![https://www.notion.soREADME\_Img/scan\_obj.gif](https://www.notion.soreadme\_img/scan\_obj.gif)

在项目页面点击“物体识别目标物”，进入目标管理页面后，可以看到当前项目中的所有目标及其信息。上述各类目标在经过平台处理后，显示为可用状态时，可以通过目标列表右侧的“下载”按钮，将制作好的目标下载到本地电脑中，用于后续的应用开发。 **下载的目标为tag\[id]\[rand\_string].zip的形式，请不要更改文件名和内容。**

![https://www.notion.soREADME\_Img/download\_target.png](https://www.notion.soreadme\_img/download\_target.png)

2）. 应用开发

在Assets/Recognition/TargetReco中，提供了4个目标识别与跟踪的示例场景（见**Assets/Recognition/TargetReco/Tutorial/Scenes**），双击打开后既可在unity editor中修改配置运行或编译。

![https://www.notion.soREADME\_Img/recoTutorialScenes.png](https://www.notion.soreadme\_img/recoTutorialScenes.png)

**RecognitionTutorial For Smart Core**：支持通用Android和iOS设备的示例场景。

**RecognitionTutorialForDaystar**：支持晨星眼镜设备的示例场景。

请注意**SRApplication** 里key和secret的填写。一个开发者存在多个项目时，上传地图到哪个项目，就填写对应项目的key和secret。

![https://www.notion.soREADME\_Img/multi-project.png](https://www.notion.soreadme\_img/multi-project.png)

3\). 自定义开发

若需要自己制作场景，请参考以下步骤，下列步骤以Unity2020.3.8f1c1为示例，不同版本unity的界面或选项存在一定差异。

1. 开发场景配置

在Unity下方的项目窗口中，找到相关的预制件，将其拖拽添加到Unity场景中，注意各个预制件的层级，添加后状态见下图红框。需要添加的预制件包含：

/Assets/Common/Prefabs/SRApplication.prefab

/Assets/stARFrame/Prefabs/SRFrameManager.prefab

/Assets/stARFrame/Prefabs/SRBackground.prefab(D\_ARFoundation和无SLAM的D\_WebCamera设备必选)

/Assets/Recognition/TargetReco/Prefabs/SRTargetReco.prefab

/Assets/Recognition/TargetReco/Prefabs/SRTargetManager.prefab

/Assets/Recognition/TargetReco/Prefabs/SRNotification.prefab (可选)

![https://www.notion.soREADME\_Img/aidor\_prefabs\_new.png](https://www.notion.soreadme\_img/aidor\_prefabs\_new.png)

具体配置：

点击场景中的SRApplication，并在右侧属性列表输入服务器网址以及应用的APP Key和Secret，**请务必填入自己APP对应的Key和Secret**,获取途径参考[此处](about:blank#%E5%BC%80%E5%8F%91%E8%B4%A6%E5%8F%B7%E6%B3%A8%E5%86%8C)。

点击SRFrameManager，选择相应的设备类型。SRTargetReco建议使用如图配置。

![https://www.notion.soREADME\_Img/prefab\_config\_new.png](https://www.notion.soreadme\_img/prefab\_config\_new.png)

1. 添加目标到场景中

点击场景中的SRTargetManager，右侧属性面板点击“添加目标”按钮，在弹出的对话框中，找到从开发者网站下载的目标文件zip包，点击“打开”即可将该目标添加到场景中，加载一个SRTargetModel\_tag\[xxx]预制件。同时在SRTargetManager面板的目标列表会显示当前场景中的目标，通过点击“删除”按钮可将对应目标移除。如需要显示场景zip包自带的模型，场景zip包解压的文件夹，需要保留在工程中。

![https://www.notion.soREADME\_Img/add\_target.png](https://www.notion.soreadme\_img/add\_target.png)

1. 叠加虚拟模型

如上述操作添加目标后，相应SRTargetModel预制件中会附着一个目标3D模型作为子物体，此处可以根据具体业务需求，在该3D模型下挂着各种虚拟资源（模型、视频、图片等），这样在识别到该物体时便会自动显示加入的虚拟资源。通过“添加目标”按钮添加时，Model默认链接到zip包解压的文件夹下资源。

![https://www.notion.soREADME\_Img/add\_virtual\_model.png](https://www.notion.soreadme\_img/add\_virtual\_model.png)

1. 运行程序

应用前需在Project Settings->Graphics->Always Included Shaders中添加Unlit/Texture。

![https://www.notion.soREADME\_Img/pc\_shader.png](https://www.notion.soreadme\_img/pc\_shader.png)

在“Assets.stARkit.SDK”下找到“StARkitMixtureGlassSeeThrough.prefab” 预制件，将其拖拽添加到场景层级窗口。

![https://www.notion.soREADME\_Img/target\_reco\_daystar.png](https://www.notion.soreadme\_img/target\_reco\_daystar.png)

更改SRFrameManager与SRTargetReco的配置，设备类型一定要与实际设备对应，否则可能会导致虚实叠加不准，详细场景配置可参照RecognitionTutorialForDaystar场景。

![https://www.notion.soREADME\_Img/daystar\_reco.png](https://www.notion.soreadme\_img/daystar\_reco.png)

以上配置完成将其打包为安卓应用，即可安装到联想晨星设备上运行，方法同[打包和部署安卓应用](about:blank#%E5%9F%BA%E4%BA%8EUnity%E6%89%93%E5%8C%85%E5%92%8C%E9%83%A8%E7%BD%B2%E5%AE%89%E5%8D%93%E5%BA%94%E7%94%A8)。

4\). 预制件配置说明

| SRApplication | SDK 通用预制件                                                     |
| ------------- | ------------------------------------------------------------- |
| Domain Host   | 设置平台网站域名，通常为https://daystar.lenovo.com, 私有云开发者的具体修改值需联系服务器管理员 |
| App Key       | 需要识别的APP对应的Key                                                |
| App Secret    | 需要识别的APP对应的Secret                                             |

| SRFrameManager   | SDK 通用预制件                                                                   |
| ---------------- | --------------------------------------------------------------------------- |
| Device Type      | 设备类型 D\_ARFoundation:Android设备和iOS设备 D\_Starkit:晨星AR眼镜连接拯救者手机和基于光学透视的晨星AR眼镜 |
| Resolution       | 相机分辨率                                                                       |
| Auto Open Camera | 是否自动开关相机                                                                    |

| SRRecognition    | TargetReco预制件                                                                   |
| ---------------- | ------------------------------------------------------------------------------- |
| AutoGetFrame     | True：自动获取图像，无需开发者传图像 False：开发者需自主传入图像帧                                          |
| AutoStartReco    | True：应用启动时自动开始识别 False：开发者手动调用接口开启识别                                            |
| SRTrackingMode   | TARGET\_TRACKING:物体跟踪，建议在手机、PC等设备上使用 ENV\_TRACKING:环境跟踪，建议在晨星AR眼镜上使用，适用于物体静止的场景 |
| Roi Width Ratio  | 识别感兴趣区的宽度与图像宽度比例                                                                |
| Roi Height Ratio | 识别感兴趣区的高度与图像高度比例                                                                |

| SRTargetModel      | TargetReco预制件                      |
| ------------------ | ---------------------------------- |
| Tag                | 目标唯一标识，由云端系统生成                     |
| Auto Add Target    | 自动加载目标，取消勾选后需开发者自行调用接口加载，适合高级开发者使用 |
| Auto Enable Target | 自动激活目标，取消候选后需开发者自行调用接口激活           |

5\). TargetReco接口介绍

**stARkit SDK中TargetReco**提供的接口类为SRTargetRecoBehaviour，该类提供了实例获取方法public static SRTargetRecoBehaviour Instance()，通过该方法获取类实例后即可调用其中的接口控制算法行为。其中控制识别算法的接口主要有：

1. 目标操作接口

```
//========================================================
//action有4种类型：
//    TARGET_ACTION_ADD     —— 加载目标
//    TARGET_ACTION_REMOVE  —— 删除目标
//    TARGET_ACTION_ENABLE  —— 激活目标
//    TARGET_ACTION_DISABLE —— 冻结目标
//========================================================
public void TargetDoAction(string tag, SRTargetAction action);
```

一个目标需要先被加载，然后激活后才可以被识别和跟踪。

但一个目标暂时不需要识别时，可以将其冻结，若后续都不再需要使用，则建议将其删除以节约系统资源。一个目标被加载后默认处于冻结状态。

在RecognitionTutorial的SRTargetBehaviour.cs脚本文件中有调用示例。

1. 识别流程控制接口

```
//========================================================
//暂停识别，调用接口后，识别回调事件将不会被触发
//========================================================
public void TargetRecoPause();

//========================================================
//恢复识别
//========================================================
public void TargetRecoResume();
```

1. TargetReco自定义回调事件

stARkit识别在每个主要接口调用完成后，会触发相应的回调事件。开发者可以通过注册回调的方式触发自定义行为。

1. SDK初始化完成

在SDK初始化完成后会触发，通常仅在应用启动后触发一次。该事件在SRTargetBehaviour.cs脚本有调用示例，用于在初始化完成后自动加载目标。

```
回调事件：SRTargetRecoBehaviour.InitializeEvent
回调函数：void SRInitializeFinishedCallback(int errCode);
参数说明：errCode —— 初始化错误码，0：成功， 非0：失败
```

1. 目标操作完成

每次目标操作完成后触发。该事件在SRTargetBehaviour.cs脚本有调用示例，用于加载目标后自动激活目标。

```
回调事件：SRTargetRecoBehaviour.DoActionEvent
回调函数：void SRDoActionFinishedCallback(int errCode, string tag, SRTargetAction action);
参数说明：errCode —— 初始化错误码，0：成功， 非0：失败
         tag —— 操作目标的tag
         action —— 操作类型
```

1. SDK销毁完成

在SDK销毁完成后会触发，通常仅在应用退出前触发一次

```
回调事件：SRTargetRecoBehaviour.DestroyEvent
回调函数：void SRDestroyFinishedCallback(int errCode);
参数说明：errCode —— 初始化错误码，0：成功， 非0：失败
```

1. 单帧识别完成

每帧识别完成后都会触发一次，可根据回调的识别目标信息来判断该帧图像的识别情况。该事件在SRTargetBehaviour.cs脚本有调用示例，在每帧识别完后，实时更新虚拟模型的在场景中的位姿信息。

```
回调事件：SRTargetRecoBehaviour.TargetPoseChangedEvent
回调函数：void SRTargetPoseChangedCallback(int errCode, SRAugTargets augTargets, IntPtr usrData);
参数说明：errCode —— 初始化错误码，0：成功， 非0：失败
         augTargets —— 识别出的目标以及其位姿信息
         usrData —— 开发者自定义信息，默认情况为识别图像帧的一些附加信息
```

1. TargetReco自定义相机数据

默认情况下，stARkit SDK会自动打开相机获取画面进行识别，在某些情况下，开发者需要多个模块共用一个相机，因此需要其自行控制相机的开关，并将图像数据传到SDK中。 SDK中提供了一个接口供外部传入图像数据：

```
void SubmitFrame(ref SRRecoFrame frame)；
frame —— 图像帧数据
```

同时，若要主动调用该接口，需要在SRTargetReco prefab中把“自动获取图像”的复选框去除勾选。

1. TargetReco其他接口说明

TargetReco接口中还提供了2个接口，用于获取SDK相关的状态：

**SDK版本获取接口**

```
//用于获取算法SDK的版本信息
public string GetVersion();
```

**错误描述信息获取接口：** 每个回调事件中都会附带一个错误码，用于标识本次接口调用是否成功，当错误码非0时，可以调用GetErrorDescription函数获取错误信息，辅助定位失败原因。

```
//获取错误码对应的描述
public string GetErrorDescription(int errCode);
```

#### 场景识别定位

1\). 地图生成

若需要识别的对象为一个空间环境区域，其制作目标的方式和物体目标类似，仅需在扫描仪中右上角切换为环境模式即可。需要**注意**的是环境扫描时，扫图的人扫描的范围（包含垂直高度，远近）要涵盖住识别的人使用的范围， 同一个面尽量避免不同角度反复扫描，这样重定位的位姿精度会高些。(仅支持**晨星扫描仪**扫描上传）

![https://www.notion.soREADME\_Img/scan\_area.gif](https://www.notion.soreadme\_img/scan\_area.gif)

![https://www.notion.soREADME\_Img/download\_scene.png](https://www.notion.soreadme\_img/download\_scene.png)

2）. 应用开发

在Assets/Recognition/SceneReco中，提供了2个环境识别与跟踪的示例场景（见**Assets/Recognition/SceneReco/Tutorial**），双击打开后既可在unity editor中修改配置 运行或编译。

![https://www.notion.soREADME\_Img/scene\_reco\_tutorial.png](https://www.notion.soreadme\_img/scene\_reco\_tutorial.png)

**ARFoundationExample**：支持通用Android和iOS设备的环境识别示例场景。

**StARkitMapExample**：支持晨星眼镜设备的环境识别示例场景。

若需要做虚实叠加，可以通过晨星视觉智能平台网页下载所需的3D场景模型，将其置于unity场景中用于参考，确保3D模型的position和rotation在(0，0，0)，再将此model拖至\[StARkitMapSession]下SR Scene Session中的场景模型。

![https://www.notion.soREADME\_Img/scene\_reco\_model.png](https://www.notion.soreadme\_img/scene\_reco\_model.png)

3D模型的材质需要绑定自带的：

![https://www.notion.soREADME\_Img/scene\_material.png](https://www.notion.soreadme\_img/scene\_material.png)

请注意**SRApplication** 里key和secret的填写。一个开发者存在多个项目时，上传地图到哪个项目，就填写对应项目的key和secret。

![https://www.notion.soREADME\_Img/multi-project.png](https://www.notion.soreadme\_img/multi-project.png)

2.1）**本地重定位**

此功能需要将云端下载zip包中的xxxx\_ld.dat文件放到。

![https://www.notion.soREADME\_Img/dat.png](https://www.notion.soreadme\_img/dat.png)

运行示例场景apk可看到如下界面，选择“Loc”，取消勾选“UseCloud”，在“请输入map id”输入框中输入id；

![https://www.notion.soREADME\_Img/scene\_reco5.png](https://www.notion.soreadme\_img/scene\_reco5.png)

点击”开始识别“界面会有提示->(id)重定位中；

![https://www.notion.soREADME\_Img/scene\_reco7.png](https://www.notion.soreadme\_img/scene\_reco7.png)

移动camera进行环境重定位，成功后界面会有提示->(id)重定位成功，同时放在上述unity开发场景中的对应model会贴附到真实环境中，也可观察屏幕右下方重定位前后的坐标信息。

![https://www.notion.soREADME\_Img/scene\_reco4.png](https://www.notion.soreadme\_img/scene\_reco4.png)

2.2）**云端识别+本地重定位**

此功能请选择“RecoOnly”，其他选项默认。”获取云环境列表“可查看或删除晨星扫描仪上传到云端的环境目标id；

![https://www.notion.soREADME\_Img/scene\_reco1.png](https://www.notion.soreadme\_img/scene\_reco1.png)

点击”开始识别“界面会有提示->正在识别中；

![https://www.notion.soREADME\_Img/scene\_reco2.png](https://www.notion.soreadme\_img/scene\_reco2.png)

移动camera进行环境识别，识别到界面会有提示->识别成功，随后会提示->(id)重定位中；

![https://www.notion.soREADME\_Img/scene\_reco3.png](https://www.notion.soreadme\_img/scene\_reco3.png)

继续移动camera进行环境重定位，成功后界面会有提示->(id)重定位成功。

3）. unity预制件配置说明

| SRApplication | SDK 通用预制件                                                     |
| ------------- | ------------------------------------------------------------- |
| Domain Host   | 设置平台网站域名，通常为https://daystar.lenovo.com, 私有云开发者的具体修改值需联系服务器管理员 |
| App Key       | 需要识别的APP对应的Key                                                |
| App Secret    | 需要识别的APP对应的Secret                                             |

| SRFrameManager   | SDK 通用预制件                                                                   |
| ---------------- | --------------------------------------------------------------------------- |
| Device Type      | 设备类型 D\_ARFoundation:Android设备和iOS设备 D\_Starkit:晨星AR眼镜连接拯救者手机和基于光学透视的晨星AR眼镜 |
| Resolution       | 相机分辨率                                                                       |
| Auto Open Camera | 是否自动开关相机                                                                    |

| \[StARkitMapSession]           | SceneReco预制件                |
| ------------------------------ | --------------------------- |
| 场景模型(Scene Model)              | 环境识别id对应的模型                 |
| 云服务功能(Cloud service function)  | Enabled：启用云服务 Disable：禁用云服务 |
| Map云端域名(Map cloud domain name) | 同SRApplication平台网站域名        |

4）. SceneReco接口调用

**stARkit SDK中SceneReco**提供的接口类为SRSceneRecoBehaviour，该类提供了实例获取方法public static SRTargetRecoBehaviour Instance(bool cloud)，参数为是否使用云服务，通过该方法获取类实例后即可调用其中的接口控制算法行为。其中控制识别算法的接口主要有：

1. 流程控制接口

```
//========================================================
//开启识别或重定位
//"relocTimeoutMS":识别或重定位的时长
//"mapid":-2 识别加重定位，-1 仅识别；>=0 仅重定位
//"localMapPathDirectory"：本地存放id特征文件的路径
//========================================================
public virtual SRRestultCode SceneRecoStart(float relocTimeoutMS,int mapid=-2,string localMapPathDirectory = null)

//========================================================
//停止识别或重定位
//========================================================
 public virtual SRRestultCode SceneRecoStop()
```

```
//========================================================
//获取环境id列表
//========================================================
  public virtual SRRestultCode GetSceneList(int pageNo, int pageSize)

//========================================================
//根据环境id删除对应特征文件
//========================================================
  public virtual SRRestultCode DeleteSceneData(int mapId)
```

```
//========================================================
//传数据
//========================================================
public virtual SRRestultCode SubmitFrame(SRFrameData[] frameDatas,List<byte[]> datas)
```

1. SceneReco自定义回调事件

stARkit环境识别提供了一组注册接收回调和取消回调注册。开发者可以通过注册回调的方式触发自定义行为。

```
//========================================================
//注册接收回调
//"SRBaseSceneRecivedData":识别或重定位过程中的状态信息
//========================================================
 public void RegisterRecived (Action<SRBaseSceneRecivedData> recived)

//========================================================
//取消回调注册
//========================================================
 public void UnRegisterRecived(Action<SRBaseSceneRecivedData> recived)
```

#### 图片识别定位

图片目标是指开发者在平台上传一张图片，经云端自动处理后生成可用于SDK识别的目标。然后开发者将该图片打印出来后，SDK可直接识别打印的图片，进行叠加各种虚拟的信息。图片目标的制作流程与模型目标基本一样，仅在于上传时选择图片即可。 **注意：若该识别应用是运行在AR眼镜，请务必填写正确的打印图片宽度**

![https://www.notion.soREADME\_Img/upload\_image.gif](https://www.notion.soreadme\_img/upload\_image.gif)

在项目页面点击“物体识别目标物”，进入目标管理页面后，可以看到当前项目中的所有目标及其信息。上述各类目标在经过平台处理后，显示为可用状态时，可以通过目标列表右侧的“下载”按钮，将制作好的目标下载到本地电脑中，用于后续的应用开发。 **下载的目标为tag\[id]\[rand\_string].zip的形式，请不要更改文件名和内容。**

![https://www.notion.soREADME\_Img/download\_target.png](https://www.notion.soreadme\_img/download\_target.png)

**往后的开发流程、接口定义等，与物体地图定位一致，请参考物体地图定位章节。**

#### 3.5 3D mesh

#### 功能点介绍

1）主要功能函数：

| 函数名                                                                  | 返回值          | 参数类型                | 参数                                            |
| -------------------------------------------------------------------- | ------------ | ------------------- | --------------------------------------------- |
| StARkitStartSpatialMesh开启扫描空间Mesh功能                                  | EStARkitCode | 无                   | 无                                             |
| StARkitStopSpatialMesh关闭扫描空间Mesh功能                                   | EStARkitCode | 无                   | 无                                             |
| StARkitClearSpatialMesh清除扫描缓存的数据                                     | EStARkitCode | 无                   | 无                                             |
| StARkitSaveFinalSpatialMesh根据传入的文件路径将扫描缓存的数据保存为完整版和精简版，传入的文件必须是.raw。 | EStARKitCode | string              | meshPath：保存文件路径。                              |
|                                                                      |              | string              | simplifyMeshPath:精简版mesh数据保存路径，主要用于生成碰撞体的mesh |
| StARkitGetFinalSpatialMesh这是把StARkitSaveFinalSpatialMesh存储的数据读取出来。   | EStARkitCode | string              | meshPath:存储的数据文件路径。                           |
|                                                                      |              | SStARkitMeshData    | meshData:读取的文件数据。                             |
| StARkitGetBlockMesh获取mesh数据                                          | EStARkitCode | SStARkitMeshData\[] | meshData:拿到的可以直接用于画mesh的数据。                   |

#### SStARkitMeshData

1）命名空间：Lenovo.stARkit.Toolkit.Utilites.Definitions。

2）简介：这是用来画Mesh的数据的集合，FindPlane和SpatialMesh的数据最后会整合后为SStARkitMeshData，这里面的顶点数组和三角数组可以用来直接画Mesh。

3）属性：

| 属性名         | 类型                | 说明                                 |
| ----------- | ----------------- | ---------------------------------- |
| MeshID      | uint              | 空间Mesh的编号，根据这个ID来判断哪个部分的Mesh数据更新了。 |
| MeshVerSion | uint              | Mesh数据的更新号。                        |
| Vertices    | Vector3\[]        | Mesh的顶点数组。                         |
| Triangles   | int\[]            | Mesh的三角面排列数组。                      |
| plane       | SStARkitPlaneData | 当Mesh是FindPlane的数据时包含一个中心点和法线信息。   |

#### SBlockMeshInfo

1）命名空间：Lenovo.stARkit.Toolkit.Utilites.Definitions。

2）简介：这是SpatialMesh的mesh的数据信息的结构体，这个结构体包含了顶点个数和三角面个数，和SMeshFaceTriangleIndex，SMeshVertex配合一起使用。

3）属性：

| 属性名             | 类型   | 说明                                 |
| --------------- | ---- | ---------------------------------- |
| MeshID          | uint | 空间Mesh的编号，根据这个ID来边帮那个部分的Mesh数据更新了。 |
| MeshVersion     | uint | Mesh数据的更新号，当此编号发生变化，表示数据发生了变化。     |
| MeshVertexCount | uint | 此次mesh数据的顶点个数。                     |
| MeshFaceCount   | uint | 此次mesh数据的三角面个数。                    |

#### SMeshFaceTriangleIndex

1）命名空间：Lenovo.stARkit.Toolkit.Utilites.Definitions。

2）简介：这是SpatialMesh的三角面信息的结构体，表示哪3个顶点组成一个三角面，和SBlockMeshInfo，SMeshVertex配合使用。

3）属性：

| 属性名 | 类型   | 说明         |
| --- | ---- | ---------- |
| v0  | uint | 三角面的第一个顶点。 |
| v1  | uint | 三角面的第二个顶点。 |
| v2  | uint | 三角面的第三个顶点。 |

#### SMeshVertex

1）命名空间：Lenovo.stARkit.Toolkit.Utilites.Definitions。

2）简介：这是SpatialMesh的顶点信息结构体，表示一个顶点在世界坐标系下的位置，和SBlockMeshInfo，SMeshFaceTriangleIndex配合使用。

3） 属性：

| 属性名 | 类型    | 说明      |
| --- | ----- | ------- |
| x   | float | 顶点X轴的坐标 |
| y   | float | 顶点Y轴的坐标 |
| z   | float | 顶点Z轴的坐标 |

#### 功能点样例

3D mesh Demo是空间重建功能展示，需要设备具有TOF模组。

1. 选择Assets/stARkit/Lenovo.stARkit.SDK/Tutorial/SpatialMesh/Scene/SpatialBlockMeshExample,依照上面的Android Build的步骤出一个APK。
2. 将Build好的apk在设备中安装好后运行应用。
3.  运行起来后将看到有4个选项按钮在视野前方并会跟随头动，使用gaze对这些按钮进行操作。

    **Start Scan Mesh ：** 开启空间扫描功能，函数的调用结果会在4个按钮下方的第一个显示栏显示，根据显示内容查看函数调用情况。如果函数调用成功了，那么每帧都会去获取数据，但不是每次都有数据更新的，数据函数的结果会在最下方的信息栏显示，最后的数字表示拿到Mesh碎片的个数。

    **Stop Scan Mesh :** 关闭扫描功能，函数的调用结果会在4个按钮下方的第一个显示栏显示，根据显示内容查看函数调用情况。

    **Save Final Mesh ：** 将扫描到的结果全部保存下来，现在保存的文件格式raw,当需要时可以调用加载FinalMesh的函数读取显示。函数的调用结果会在4个按钮下方的第一个显示栏显示，根据显示内容查看函数调用情况。

    **Clear Cache Mesh ：** 清除当前缓存的数据。函数的调用结果会在4个按钮下方的第一个显示栏显示，根据显示内容查看函数调用情况。

Save Final Mesh 后光标点击Mesh，检测到Mesh的碰撞体会生成Cube。

![https://www.notion.soREADME\_Img/UI\_BlockMesh.png](https://www.notion.soreadme\_img/UI\_BlockMesh.png)
