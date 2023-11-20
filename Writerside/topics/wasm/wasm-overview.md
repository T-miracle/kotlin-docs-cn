[//]: # (title: Kotlin Wasm)

> Kotlin Wasm 是 [实验性的](components-stability.md)。
> 它可能随时更改。仅用于评估目的。
>
> 我们希望您在 [YouTrack](https://kotl.in/issue) 上提供反馈。
>
{style="warning"}

[WebAssembly (Wasm)](https://webassembly.org) 是一种基于堆栈的虚拟机的二进制指令格式。
这种格式是独立于平台的，因为它在自己的虚拟机上运行。
Wasm 旨在快速和安全，并且可以编译来自各种编程语言的代码，包括 Kotlin。

Kotlin/Wasm 是 Kotlin 的一个新的编译目标。您可以在 Kotlin Multiplatform 项目中使用它。
通过 Kotlin/Wasm，您可以创建在支持 WebAssembly 并符合 Kotlin 要求的不同环境和设备上运行的应用程序。

> 通过这个 [YouTube 播放列表](https://kotl.in/wasm-pl) 了解更多关于 Kotlin/Wasm 的信息。
>
{style="note"}

## 浏览器支持

要在浏览器中运行使用 Kotlin/Wasm 构建的应用程序，您需要一个支持新的 [垃圾回收功能](https://github.com/WebAssembly/gc) 的版本。

[在《开始使用 Kotlin/Wasm》中了解更多](wasm-get-started.md#troubleshooting)。

## 互操作性

Kotlin/Wasm 允许您在 Kotlin 中同时使用 JavaScript 代码和浏览器 API，以及在 JavaScript 中使用 Kotlin 代码。

[了解有关 Kotlin Wasm 与 JavaScript 互操作性的更多信息](wasm-js-interop.md)。

## 用于 Web 的 Compose Multiplatform

> Web 支持是 [实验性的](components-stability.md) 并且可能随时更改。仅用于评估目的。
> 我们将感谢您在公共 Slack 频道 [#compose-web](https://slack-chats.kotlinlang.org/c/compose-web) 中提供反馈。
> 如果遇到任何问题，请在 [GitHub](https://github.com/JetBrains/compose-multiplatform/issues) 上报告。
>
{style="warning"}

用于 Web 的 Compose Multiplatform 基于新的 Kotlin/Wasm 目标。
您可以创建一个 Kotlin 多平台 项目，并尝试在 Web 上共享您的移动或桌面 UI。
使用 用于 Web 的 Compose Multiplatform，您可以在浏览器中运行您的代码，享受 WebAssembly 的所有优势。

## 如何开始

* [在 IntelliJ IDEA 中开始使用 Kotlin/Wasm](wasm-get-started.md)
* 查看 [带有 Kotlin/Wasm 示例的 GitHub 存储库](https://github.com/Kotlin/kotlin-wasm-examples)

## 库支持

您可以直接在 Kotlin/Wasm 中使用 Kotlin 标准库（`stdlib`）和测试库（[`kotlin.test`](https://kotlinlang.org/api/latest/kotlin.test/)）。
这些库的版本与 `kotlin-multiplatform` 插件的版本相同。

Kotlin/Wasm 对其他 Kotlin 库有实验性的支持。[阅读更多关于如何在您的项目中启用它们](wasm-libraries.md)。

## 反馈

* 直接在 Kotlin Slack 中向开发者提供反馈 – [获取邀请](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up)
  并加入 [#webassembly](https://kotlinlang.slack.com/archives/CDFP59223) 频道。
* 如果您在使用 Kotlin/Wasm 中遇到任何问题，请在 [此 YouTrack 问题](https://youtrack.jetbrains.com/issue/KT-56492) 中报告。
