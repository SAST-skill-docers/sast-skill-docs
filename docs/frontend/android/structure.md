# Android 项目的构成

## 项目概览

在 Android Studio 中新建一个空项目，可以看到项目结构如下

<center>
    <img src="https://i.imgs.ovh/2023/11/24/MBny0.png" width="30%">
</center>

## 项目文件

项目中最关键的就是 app 目录下的项目文件，它包含几个重要的部分：

- `AndroidManifest.xml` 是应用清单文件，需要声明以下内容：

    - 应用的软件包名称，如 com.example.myapp。
    - 应用的组件，如应用中使用的 Activity 和 Service。
    - 应用为访问系统或其他应用的受保护部分所需的权限，如使用网络连接的权限、读取外部储存的权限等等，具体的权限清单可以查看 [权限 API 参考文档页面](https://developer.android.com/reference/android/Manifest.permission)。
    - 应用需要的硬件和软件功能，特别是 **minSdkversion** 和 **targetSdkversion**，前者定义了 APP 所需要的最低 API 级别，后者指定了应用的目标 API 级别，API 级别与 Android 的系统版本相对应，对应关系可查看 [什么是 API 级别](https://developer.android.com/guide/topics/manifest/uses-sdk-element?hl=zh-cn#ApiLevels)。

- `java` 目录下存放的是 Java 源代码文件。
- `res` 目录下包含所有非代码资源（例如 XML 布局、界面字符串和位图图像），我们将在下一节介绍 Android 的资源文件。
- `build.gradle` 是构建工具包 Gradle 执行应用构建过程的配置文件，除了指定使用的 Java 版本以及 API 级别外，应用所使用的依赖项也需要添加到 `build.gradle` 中，如
    ```text
    dependencies {
        implementation 'com.google.android.material:material:1.8.0'
        implementation 'com.squareup.okhttp3:okhttp:4.9.2'
    }
    ```

其他文件在创建项目时就已经由 Android Studio 帮助你完成了，几乎不需要更多的修改。