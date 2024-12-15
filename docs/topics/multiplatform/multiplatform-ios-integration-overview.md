[//]: # (title: iOS 集成方法)

你可以将 Kotlin Multiplatform 共享模块集成到 iOS 应用中。为此，你需要从共享模块生成一个
[iOS 框架](https://developer.apple.com/library/archive/documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WhatAreFrameworks.html)
，然后将其作为依赖项添加到 iOS 项目中：

![iOS 集成示意图](ios-integration-scheme.svg)

可以将此框架作为本地或远程依赖项进行使用。
如果你希望完全控制整个代码库，并且在公共代码发生变化时能够即时更新最终应用程序，则选择本地集成。

如果你想显式地将最终应用程序的代码库与公共代码库分离，则设置远程集成。
在这种情况下，共享代码将像常规的第三方依赖项一样集成到最终应用程序中。

## 本地集成 {id=local-integration}

在本地集成设置中，有两种主要的集成选项。
你可以通过一个特殊的脚本使用直接集成，这会将 Kotlin 构建作为 iOS 构建的一部分。
如果你的 Kotlin Multiplatform 项目中有 Pod 依赖项，可以选择 CocoaPods 集成方式。

### 直接集成 {id=direct-integration}

你可以通过向 Xcode 项目添加一个特殊的脚本，直接将 iOS 框架从 Kotlin Multiplatform 项目中连接到 iOS。
该脚本会集成到项目构建设置的构建阶段。

如果你的 Kotlin Multiplatform 项目中**没有**导入 CocoaPods 依赖项，这种集成方式适合你。

如果你在 Android Studio 中创建项目，选择 **Regular framework** 选项将自动生成此设置。
如果你使用 [Kotlin Multiplatform web 向导](https://kmp.jetbrains.com/)，则默认应用直接集成。

有关更多信息，请参见 [直接集成](multiplatform-direct-integration.md)。

### 使用本地 podspec 的 CocoaPods 集成 {id=cocoapods-integration-with-a-local-podspec}

你可以通过 [CocoaPods](https://cocoapods.org/)，一个流行的
Swift 和 Objective-C 项目的依赖管理工具，从 Kotlin Multiplatform 项目连接 iOS 框架。

这种集成方式适合以下情况：

* 你有一个使用 CocoaPods 的 iOS 项目，并且在一个 mono 仓库中设置了该项目
* 你在 Kotlin Multiplatform 项目中导入了 CocoaPods 依赖项

要设置包含本地 CocoaPods 依赖项的工作流程，你可以手动编辑脚本或通过 Android Studio 中的向导生成项目。

有关更多信息，请参见 [CocoaPods 概述和设置](native-cocoapods.md)。

## 远程集成 {id=remote-integration}

对于远程集成，您的项目可能会使用 Swift 包管理器（SPM）或 CocoaPods 作为依赖管理工具，将
Kotlin Multiplatform 项目与 iOS 框架连接起来。

### 使用 XCFrameworks 的 Swift 包管理器 {id=swift-package-manager-with-xcframeworks}

你可以使用 XCFrameworks 设置一个 Swift 包管理器（SPM）依赖项，以连接来自
Kotlin Multiplatform 项目的 iOS 框架。

有关更多信息，请参见 [Swift 包导出设置](native-spm.md)。

### 使用 XCFrameworks 的 CocoaPods 集成 {id=cocoapods-integration-with-xcframeworks}

你可以使用 Kotlin CocoaPods Gradle 插件构建 XCFrameworks，然后通过
CocoaPods 将项目的共享部分与移动应用分开进行分发。

有关更多信息，请参见 [构建最终的原生二进制文件](multiplatform-build-native-binaries.md#build-frameworks)。