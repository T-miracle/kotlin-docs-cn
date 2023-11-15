[//]: # (title: Kotlin 多平台)
[//]: # (描述：Kotlin Multiplatform 允许创建适用于桌面、Web 和移动设备的跨平台应用程序。在保持本地用户体验的同时共享应用逻辑。)

Kotlin 多平台 技术旨在简化跨平台项目的开发。
它减少了在[不同平台](#Kotlin多平台用例)编写和维护相同代码所需的时间，同时保持了本地编程的灵活性和优势。

![Kotlin 多平台](kotlin-multiplatform.svg){width=700}

## Kotlin 多平台用例 {id="Kotlin多平台用例"}

### Android 和 iOS 应用程序

在移动平台之间共享代码是 Kotlin 多平台 的一个主要用例。
通过 Kotlin 多平台，你可以构建跨平台移动应用程序，实现在 Android 和 iOS 项目之间共享网络、数据存储和数据验证、分析、计算以及其他应用逻辑。

查看 [开始使用 Kotlin Multiplatform](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-getting-started.html)
和 [使用 Ktor 和 SQLDelight 创建多平台应用程序](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-ktor-sqldelight.html)
教程，在这些教程中，你将为 Android 和 iOS 创建包含两个平台都共享的代码模块的应用程序。

借助由 JetBrains 开发的基于 Kotlin 的声明式 UI 框架 [Compose Multiplatform](https://www.jetbrains.com/lp/compose-multiplatform/)，
你还可以在 Android 和 iOS 之间共享 UI，创建完全跨平台的应用程序：

![Sharing different levels and UI](multiplatform-compose.svg){width=600}

查看 [开始使用 Compose Multiplatform](https://github.com/JetBrains/compose-multiplatform-ios-android-template/#readme) 教程，
创建一个自己的移动应用程序，其中的 UI 在两个平台之间共享。

### 多平台库

Kotlin 多平台 对于库的作者也是有帮助的。
你可以创建一个多平台库，包含通用代码以及针对 JVM、Web 和本地平台的特定平台实现。
一旦发布，多平台库可以作为其他跨平台项目中的依赖使用。

查看 [发布多平台库](multiplatform-publish-lib.md) 获取更多详细信息。

### 桌面应用程序

Compose Multiplatform 可以帮助在桌面平台（如 Windows、macOS 和 Linux）之间共享 UI。
许多应用程序，包括 [JetBrains Toolbox 应用](https://blog.jetbrains.com/kotlin/2021/12/compose-multiplatform-toolbox-case-study/)，已经采用了这种方法。

尝试使用 [Compose Multiplatform 桌面应用程序](https://github.com/JetBrains/compose-multiplatform-desktop-template#readme) 模板创建自己的项目，其中的 UI 在桌面平台之间共享。

## 跨平台代码共享

Kotlin 多平台 允许你维护一个应用程序逻辑的单一代码库，用于[不同平台](multiplatform-dsl-reference.md#targets)。
你还能够获得本地编程的优势，包括出色的性能和对平台 SDK 的完全访问。

Kotlin 提供以下代码共享机制：

* 在[所有平台](multiplatform-share-on-platforms.md#share-code-on-all-platforms)中共享通用代码，该代码用于你的项目。
* 在[一些平台](multiplatform-share-on-platforms.md#share-code-on-similar-platforms)中共享代码，以在相似的平台上重用大部分代码：

  ![在不同平台之间共享的代码](kotlin-multiplatform-hierarchical-structure.svg){width=700}

* 如果需要从共享代码中访问特定于平台的 API，请使用 Kotlin 的 [expected 和 actual 声明机制](multiplatform-expect-actual.md)。

## 开始使用

* 如果你想创建共享代码的 iOS 和 Android 应用程序，请从 [开始使用 Kotlin 多平台](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-getting-started.html) 开始
* 如果你想创建针对其他平台的应用程序或库，请探索 [代码共享原则和示例](multiplatform-share-on-platforms.md)

> 刚接触 Kotlin 吗？查看 [Kotlin 入门](getting-started.md)。
>
{type="tip"}

### 示例项目

查看[跨平台应用程序示例](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-samples.html)，了解 Kotlin Multiplatform 的工作原理。
