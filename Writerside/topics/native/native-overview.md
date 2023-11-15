[//]: # (title: Kotlin Native（Kotlin 原生）)

Kotlin/Native 是一种将 Kotlin 代码编译为本地二进制文件的技术，可以在没有虚拟机的情况下运行。
Kotlin/Native 包括 Kotlin 编译器的基于 [LLVM](https://llvm.org/) 的后端和 Kotlin 标准库的本地实现。

## 为什么使用 Kotlin/Native？

Kotlin/Native 主要设计用于允许在不适合或不可能使用 _虚拟机_ 的平台上进行编译，例如嵌入式设备或 iOS。
在需要生成一个不需要额外运行时或虚拟机的独立程序的情况下，它非常理想。

## 目标平台 {id=目标平台}

Kotlin/Native 支持以下平台：
* macOS
* iOS, tvOS, watchOS
* Linux
* Windows (MinGW)
* Android NDK

> 要编译苹果平台的目标，包括 macOS、iOS、tvOS 和 watchOS，
> 您需要安装 [Xcode](https://apps.apple.com/us/app/xcode/id497799835) 及其命令行工具。
> 
{style="note"}

[查看支持目标的完整列表](native-target-support.md).

## 互通性

Kotlin/Native 支持与不同操作系统的本地编程语言进行双向互操作性。
编译器创建：
* 适用于多个 [平台](#目标平台) 的可执行文件
* 针对 C/C++ 项目的带有 C 标头的静态库或 [动态库](native-dynamic-libraries.md)
* 针对 Swift 和 Objective-C 项目的 [Apple 框架](apple-framework.md)

Kotlin/Native 支持互操作性，可以直接从 Kotlin/Native 中使用现有库：
* 静态或动态的 [C 库](native-c-interop.md)
* C、[Swift 和 Objective-C](native-objc-interop.md) 框架

可以轻松将已编译的 Kotlin 代码包含在使用 C、C++、Swift、Objective-C 等语言编写的现有项目中。
也可以轻松使用现有的本地代码、静态或动态的 [C 库](native-c-interop.md)、
Swift/Objective-C [框架](native-objc-interop.md)、图形引擎以及其他任何内容直接从 Kotlin/Native 中使用。

Kotlin/Native [库](native-platform-libs.md) 有助于在项目之间共享 Kotlin 代码。
POSIX、gzip、OpenGL、Metal、Foundation 等许多热门库和 Apple 框架都已作为 Kotlin/Native 库预导入并包含在编译器包中。

## 在平台之间共享代码

[Kotlin Multiplatform](multiplatform.md) 有助于在多个平台之间共享通用代码，包括 Android、iOS、JVM、
Web 和本地。多平台库提供了通用 Kotlin 代码所需的 API，并允许在一个地方编写项目的共享部分。

您可以使用 [开始使用 Kotlin 多平台](multiplatform-get-started.md) 教程
创建应用程序并在 iOS 和 Android 之间共享业务逻辑。要在 iOS、Android、桌面和 Web 之间共享 UI，
可以尝试使用 [Compose Multiplatform](https://www.jetbrains.com/lp/compose-multiplatform/)，
这是基于 Kotlin 和 [Jetpack Compose](https://developer.android.com/jetpack/compose) 的 JetBrains 声明式 UI 框架。

## 如何开始

对 Kotlin 还不熟悉吗？查看 [Kotlin 入门](getting-started.md)。

推荐的文档：

* [开始使用 Kotlin 多平台](multiplatform-get-started.md)
* [与 C 互操作性](native-c-interop.md)
* [与 Swift/Objective-C 互操作性](native-objc-interop.md)

推荐的教程：

* [开始使用 Kotlin/Native](native-get-started.md)
* [开始使用 Kotlin Multiplatform](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-getting-started.html)
* [从 C 映射原始数据类型](mapping-primitive-data-types-from-c.md)
* [Kotlin/Native 作为动态库](native-dynamic-libraries.md)
* [Kotlin/Native 作为 Apple 框架](apple-framework.md)
