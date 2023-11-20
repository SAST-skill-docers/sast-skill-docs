# Android 开发入门

!!! info "说明"

    本文根据 2023 年学生科协暑培朱煜章同学主讲的 Android 课程的讲义进行一定修改而成。

Android 是一个开源的、基于 Linux 开发的移动设备操作系统，编写一个 Android 应用可以使用的编程语言有 Java, Kotlin, C++ 等，本文档将以 Java 为例进行讲解

## 前置知识

- Java 语言基础知识，可以参考 [Java 语言基础](../../languages/java/index.md)

## 开发准备

- Java 语言环境
- 我们选择 Android Studio 作为开发平台，可以在 [Download Android Studio & App Tools - Android Developers (google.cn)](https://developer.android.google.cn/studio/) 下载。Android Studio 是一个基于 IntelliJ IDEA 的 Android 开发工具，为开发人员提供了广泛的功能和工具，用于设计、编写、调试和测试 Android 应用程序。我们在本课程需要用到的功能有：
    - Java 编程窗口：Android 应用程序可以使用 Java 或 Kotlin 编写。我们在本课程选择 Java。
    - 布局编辑器：Android Studio 提供了一个可视化布局编辑器，用于设计应用程序的用户界面。开发人员可以直观地添加 UI 元素、调整布局和设置属性。
    - 调试工具：Android Studio 具有内置的调试工具，开发人员可以在代码中设置断点，跟踪变量的值，并执行逐行调试。
    - 虚拟设备管理器：Android Studio 包含一个虚拟设备管理器，用于创建和管理模拟的 Android 设备。这使开发人员能够在没有实际设备的情况下进行应用程序的测试和调试。
    - APK 构建工具：Android Studio 可以将应用程序打包为 Android 安装包（APK）。开发人员可以生成签名的 APK 文件，以便在设备上进行部署和分发。
    - SDK 管理器：Android Studio 包含一个 SDK（Software Development Kit）管理器，用于下载和管理 Android 平台的各种版本和附加组件。开发人员可以根据目标设备和最低支持版本来选择所需的 SDK 组件。

## 学习方法

单纯通过文档来学习 Android 开发是很痛苦的，因此我们强烈建议面向一些实际的代码，而不是文档中的样例代码，这样可以更容易了解 Android 的设计模式，而且在 IDE 中可以用你的 shift 键直接跳转到你需要浏览的地方，这比看文档看到不明白的地方再去百度/谷歌快太多了。本文末尾也会提供一个项目供大家参考。

## 后续拓展

本文档可以在以下课程帮助到你：

- 程序设计训练 Java 课堂
- 其他存在 Android 开发需求的课程

## 资源链接

- Android 开发者官方文档 <https://developer.android.google.cn/docs>
- GitHub 上的一个 Android 包含教程、一些开源项目和工具库的合集 <https://github.com/aritraroy/UltimateAndroidReference>