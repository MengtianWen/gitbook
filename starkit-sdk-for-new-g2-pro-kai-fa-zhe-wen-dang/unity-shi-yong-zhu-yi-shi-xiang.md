# 🎲 Unity使用注意事项

### Unity使用注意事项

1.  如果导入**SDK**后Unity编辑器提示下图中错误信息时，需手动删除Assets/Common/Plugins/JsonNet路径下的Newtonsoft.Json.dll文件

    ![https://www.notion.soREADME\_Img/Newtonsoft.Json.dll.png](https://www.notion.soreadme\_img/Newtonsoft.Json.dll.png)
2.  **SDK**的打包版本是**Unity2020.3.16**，由于Unity低版本对Unity高版本兼容的不是很好，故**使用Unity低版本导入SDK会导致编译Android平台库错误，如出现此情况请自行检查所有库的设置是否正确**： **64位：**

    ![https://www.notion.soREADME\_Img/lib\_win64.png](https://www.notion.soreadme\_img/lib\_win64.png)

    **32位：**

    ![https://www.notion.soREADME\_Img/lib\_win32.png](https://www.notion.soreadme\_img/lib\_win32.png)
3. **New G2 Pro**的动态申请权限弹框不会显示，普通权限弹框都会**默认允许**，但依然会**OnPause**和**OnResume**。
4.  如遇到应用在ARLauncher(眼镜端)里看不到图标，请检查AndroidManifest文件中是否缺少**category android:name=“android.intent.category.ARLAUNCHER”**。

    ![https://www.notion.soREADME\_Img/AndroidManifest.png](https://www.notion.soreadme\_img/AndroidManifest.png)
5.  **授权提示如下表：**

    | 颜色提示 | 参数类型                                                | 说明                           |
    | ---- | --------------------------------------------------- | ---------------------------- |
    | 红色   | Auth\_Fail ｜ Auth\_Unknow                           | 授权失败，请检查授权码是否填写。             |
    | 黄色   | Auth\_AppKey\_Overdue ｜ Auth\_AppKey\_Misspelling   | 授权码已过期或拼写错误，请检查。             |
    | 蓝色   | Auth\_Internet\_Fail ｜ Auth\_Native\_Token\_Overdue | 设备未联网，请联网授权。联网后，重启应用或插拔头端生效！ |

    红色：Auth\_Fail ｜ Auth\_Unknow 授权失败，请检查授权码是否填写
6. 开发者如果是通过编辑器按钮添加的预制体，替换新的sdk后需要重新创建。
7. 在**Android**上使用6DOF功能，切换场景过程中会删除**StARkitMixtureGlassSeeThrough**，必须在切换前先调用**StARkitMixtureGlassSeeThrough**中的**CloseBeforeLoadScene**。
8. 在**Android**上，眼镜上的确认键的键值在**Unity**中是**JoystickButton0**（**23**）。
9. **UILAYER** 是用显示一些异常 **UI** 的，会在需要时修改相机的 **CullingMask** ，只渲染这一层，请不要加正常的物体或者 **UI** 设置为这个 **Layer** 。
10. 有开发者在SDK升级到最新后出的**APK**启动黑屏，但是无任何错误的**log**，这时可以新建一个**Unity**工程，将原工程的全部内容导出为**Package**然后导入新工程。
11. 有开发者在SDK升级后有报错个别脚本找不到，这时可以再导入一次。
12. 打包Android的时候**BuildSystem** 要选择 **Gradle**。
