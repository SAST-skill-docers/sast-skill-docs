# Android 项目的构成

## Manifests

在 Android 应用中，`AndroidManifest.xml` 文件是必不可少的。它是 Android 应用的核心配置文件，它包含了应用的元数据和配置信息，用于描述应用的结构和行为。这个文件为系统提供了关于应用的各种关键信息，如应用的**包名**、**应用图标**、**版本信息**、**支持的 Android 版本**、**对外暴露的组件**（如 Activity、Service、BroadcastReceiver 等），以及其他系统需要知道的设置和权限。

以下是一个 `AndroidManifest.xml` 文件的示例：

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.Wordle"
        tools:targetApi="31">
        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```

## Fragments/Activities/Services/Adapters/Intents

这些组件是 Android 应用的基本组成部分，位于 `app/src/main/java/` 文件夹里。

- **Fragments**：可以看作是活动的一部分，用于管理用户界面的一部分。
- **Activities**：是应用中的一个单独的屏幕，用户可以与之交互。
- **Services**：用于在后台执行长时间运行的操作，而不会干扰用户界面。
- **Adapters**：用于将数据绑定到视图组件，如列表视图或网格视图。
- **Intents**：用于启动活动、服务或广播，还可以在应用之间传递数据。

其中，`MainActivity.java` 通常作为应用的主入口点，它是一个继承自 `Activity` 的类，用于定义应用的主界面和用户交互逻辑。

## Tests

测试是确保应用质量和稳定性的重要部分。测试文件通常位于以下两个目录：

- `app/src/test/`：包含单元测试，这些测试在开发人员的机器上运行。
- `app/src/androidTest/`：包含仪器测试，这些测试在设备或模拟器上运行。

## Resources

资源文件是应用的非代码部分，包括图像、字符串、颜色定义等。资源文件位于 `app/src/main/res/` 文件夹中，主要包括：

- `drawable/`：存放图像资源。
- `values/`：存放字符串、颜色定义、尺寸定义等。
- `layout/`：存放应用的布局文件，如 XML 文件。

## Gradle Scripts

Gradle 是 Android 应用的构建系统。Gradle Scripts 用于定义项目的构建过程和依赖关系。主要文件包括：

- `gradle.properties`：定义全局配置，如最小 SDK 版本。
- `gradle-wrapper.properties`：定义 Gradle 包装器的配置。
- `settings.gradle.kts`：定义项目设置，如模块名称。
- `build.gradle.kts`：定义模块的构建逻辑，包括依赖项和插件。
