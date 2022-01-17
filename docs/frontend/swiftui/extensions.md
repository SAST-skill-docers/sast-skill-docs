本文仅对 SwiftUI 中一些最基本的知识作了介绍，而这远远不够。要想开发一个完整的应用，你还需要学习更多内容。

这是一些仅供参考的建议：

- 经常探索[官方文档](https://developer.apple.com/documentation/swiftui)，看看你能使用的工具有什么
- 看看历年 [WWDC](https://developer.apple.com/videos/topics/) 有什么视频讲解的内容是你需要的，关注每年的 WWDC
- 不要惧怕英文内容，阅读英文文档是开发的必经之路
- 学会用英文搜索，**尽量不用中文搜索**

## 概念性知识

这些概念性知识能够帮助你理解 SwiftUI 是如何运行的，进而帮助你写出效率更高、更加正确的程序。SwiftUI 本身并不复杂，但其原理需要深入的学习才能理解，而对原理的理解往往会在很大程度上影响程序的质量。

这些话题非常重要：

- [SwiftUI 生命周期](https://developer.apple.com/videos/play/wwdc2020/10037/)：`App`、`Scene` 和 `View` 之间的关系
- [SwiftUI 中的数据流](https://developer.apple.com/videos/play/wwdc2020/10040/)：SwiftUI 中的数据流及其使用原则
- [MVVM 架构](https://www.bilibili.com/video/BV1q64y1d7x5?p=3)：虽然我们已经简单介绍过，但你仍应听一听 CS193p 的这节课
- [View Identity](https://developer.apple.com/videos/play/wwdc2021/10022/)：在底层，不同的 View 之间是如何区分的？

## 技术性知识

这些技术性的知识是一些“SwiftUI 能做什么”的集合，基本上是[官方文档](https://developer.apple.com/documentation/swiftui)中你能用的所有 SwiftUI 现成工具，可以需要时再查阅，也可以无聊时随便翻翻。遇到问题时，[Google](https://google.com)[、Stackoverflow](https://stackoverflow.com) 和 [WWDC 视频](https://developer.apple.com/videos/swiftui-ui-frameworks)是你可靠的助手。

- [原生组件](https://developer.apple.com/documentation/swiftui/views-and-controls)：文字、图片、按钮等组件
- [排布](https://developer.apple.com/documentation/swiftui/view-layout-and-presentation)：Stack、Grid、ScrollView、NavigationView 等
- [数据流与状态](https://developer.apple.com/documentation/swiftui/state-and-data-flow)：State、Binding、ObservedObject 等
- [绘画与动画](https://developer.apple.com/documentation/swiftui/drawing-and-animation)：绘制形状、添加动画
- [触控识别](https://developer.apple.com/documentation/swiftui/gestures)：识别用户触摸输入
- View modifier
    - [外观](https://developer.apple.com/documentation/swiftui/view-appearance)
    - [文字相关](https://developer.apple.com/documentation/swiftui/view-text-and-symbols)
    - [额外的 UI 部件](https://developer.apple.com/documentation/swiftui/view-auxiliary-views)
    - [布局](https://developer.apple.com/documentation/swiftui/view-layout)
    - [图像与渲染](https://developer.apple.com/documentation/swiftui/view-graphics-and-rendering)
    - [用户输入与事件](https://developer.apple.com/documentation/swiftui/view-input-and-events)
    - [视图的展示](https://developer.apple.com/documentation/swiftui/view-presentation)
- 在 SwiftUI 中使用 UIKit
    - [CS193p](https://www.bilibili.com/video/BV1q64y1d7x5?p=15)
    - [官方教程](https://developer.apple.com/tutorials/SwiftUI/interfacing-with-uikit)
    - [官方文档](https://developer.apple.com/documentation/swiftui/framework-integration)

## 其它相关知识

- [Combine 框架](https://developer.apple.com/videos/play/wwdc2019/722/)：为 SwiftUI 设计的异步事件管理框架
- [UIKit 课程](https://www.bilibili.com/video/BV1Mx411L7dS)：CS193p 的 UIKit 完整课程
