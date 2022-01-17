---
title: SwiftUI 与 iOS 开发入门
---

SwiftUI 是用于 iOS/macOS/watchOS/tvOS 等平台的通用原生 UI 框架，使用（且仅适用于）Swift 语言，于 [WWDC19](https://developer.apple.com/videos/play/wwdc2019/216/)（2019 年）推出，支持 iOS 13.0+、macOS 10.15+ 等。

SwiftUI 基于各平台原有的 UI 框架开发，如 iOS 的 UIKit，macOS 的 AppKit 等。相对于这些框架，SwiftUI 的概念更加简单，且构建方式更加符合直觉，逐渐成为入门 iOS 开发的首选。

SwiftUI 在经历几年的发展后已经逐渐完善，基本上可以胜任小型应用的开发。令人遗憾的是，目前（2022 年 1 月）由于 SwiftUI 仍在发展初期，iOS 开发中某些复杂的功能需要依赖 UIKit 实现，因此只学习 SwiftUI 可能并不能够支持某些较为复杂应用的开发。本文仅介绍纯 SwiftUI 框架，与 UIKit 相关的内容可以参考 [Stanford CS193p 2020 年之前的课程](https://www.bilibili.com/video/BV1Mx411L7dS)（2020 年开始该课程改为 SwiftUI）。

## 前置知识

Swift 语言基本知识，建议先阅读 [Swift 语言基础](../../languages/swift/index.md)。

## 环境配置

目前支持 iOS 开发的平台有 macOS 和 iPadOS。本教程基于 iOS 15 SDK，需要：

- macOS：[Xcode](https://apps.apple.com/cn/app/xcode/id497799835) 13.0 以上版本
- iPadOS：[Swift Playgrounds](https://apps.apple.com/app/id908519492) 4.0 以上版本

本教程基于 iPadOS 上 4.0 版本的 Swift Playgrounds，macOS 上 13.2 以上版本的 Xcode 拥有类似的环境。部分截屏在 Xcode 下获得。

## 学习资源

可以参考如下资源：

- [Swift 官方文档](https://swift.org/documentation/)：权威的语言和标准库参考
- [WWDC Sessions](https://developer.apple.com/videos/)：WWDC 上官方的 API、语言等介绍
- [Stanford CS193p](https://cs193p.sites.stanford.edu/)：斯坦福 SwiftUI 课程
- [Stack Overflow](https://stackoverflow.com/)：搜索时加上 `iOS`、`Swift`、`SwiftUI` 等关键词
- [Hacking with Swift](https://www.hackingwithswift.com/)：比较全面的 Swift 和 iOS 各种 API 的教程
- [The SwiftUI Lab](https://swiftui-lab.com/)：对 SwiftUI 的一些深度探索
