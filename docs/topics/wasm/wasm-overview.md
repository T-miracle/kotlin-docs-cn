[//]: # (title: Kotlin Wasm)

> Kotlin/Wasm 目前处于 [Alpha](components-stability.md) 阶段。
> 可能随时发生变化。你可以在生产前使用它。我们非常欢迎你在 [YouTrack](https://youtrack.jetbrains.com/issue/KT-56492) 上提供反馈。
> 
> [加入 Kotlin/Wasm 社区](https://slack-chats.kotlinlang.org/c/webassembly)。
>
{style="note"}

使用 Kotlin，你可以通过 Compose Multiplatform 和 Kotlin/Wasm 在你的
web 项目中构建应用程序并重用移动和桌面用户界面（UI）。

[Compose Multiplatform](https://www.jetbrains.com/lp/compose-multiplatform/) 是一个基于 Kotlin
和 [Jetpack Compose](https://developer.android.com/jetpack/compose) 的声明式框架，它允许你一次实现
UI，并在你目标的所有平台上共享它。对于 web 平台，Compose Multiplatform 使用
Kotlin/Wasm 作为其编译目标。

[探索我们基于 Compose Multiplatform 和 Kotlin/Wasm 构建的应用程序的在线演示](https://zal.im/wasm/jetsnack/)

![Kotlin/Wasm 示例](wasm-demo.png){width=700}

> 要在浏览器中运行使用 Kotlin/Wasm 构建的应用程序，你需要一个支持新的垃圾回收和异常处理提案的浏览器版本。
> 要检查浏览器的支持状态，请参见 [WebAssembly
> 路线图](https://webassembly.org/roadmap/)。
>
{style="tip"}

[WebAssembly (Wasm)](https://webassembly.org/) 是一种用于基于栈的虚拟机的二进制指令格式。  
这种格式是平台无关的，因为它在自己的虚拟机上运行。
Wasm 为 Kotlin 和其他语言提供了一个在 Web 上运行的编译目标。

Kotlin/Wasm 将你的 Kotlin 代码编译成 Wasm 格式。
使用 Kotlin/Wasm，你可以创建在支持 Wasm 和符合 Kotlin 要求的不同环境和设备上运行的应用程序。

此外，你可以轻松使用最流行的 Kotlin 库在 Kotlin/Wasm 中使用。
像其他 Kotlin 和跨平台项目一样，你可以在构建脚本中包含依赖声明。
更多信息，请参见 [在跨平台库上添加依赖](multiplatform-add-dependencies.md)。

你想自己尝试一下吗？

<a href="wasm-get-started.md"><img src="wasm-get-started-button.svg" width="700" style="block" alt="Kotlin/Wasm 入门"/></a>

## Kotlin/Wasm 性能

尽管 Kotlin/Wasm 仍处于 Alpha 阶段，运行在 Kotlin/Wasm 上的 Compose Multiplatform 已经展示出令人鼓舞的性能特征。
你可以看到它的执行速度优于 JavaScript，并接近 JVM 的性能：

![Kotlin/Wasm 性能](wasm-performance-compose.png){width=700}

我们定期在 Kotlin/Wasm 上运行基准测试，这些结果来自我们在最新版本的 Google Chrome 中的测试。

## 浏览器 API 支持

Kotlin/Wasm 标准库提供了浏览器 API 的声明，包括 DOM API。
通过这些声明，你可以直接使用 Kotlin API 访问和利用各种浏览器功能。
例如，在你的 Kotlin/Wasm 应用程序中，你可以操作 DOM 元素或使用 fetch API，而无需从头定义这些声明。
要了解更多信息，请参见我们的 [Kotlin/Wasm 浏览器示例](https://github.com/Kotlin/kotlin-wasm-examples/tree/main/browser-example)。

浏览器 API 支持的声明是通过 JavaScript [互操作能力](wasm-js-interop.md) 定义的。
你可以使用相同的能力来定义自己的声明。 此外，Kotlin/Wasm–JavaScript 互操作性还允许你从 JavaScript 使用 Kotlin 代码。
有关更多信息，请参见 [在 JavaScript 中使用 Kotlin 代码](wasm-js-interop.md#use-kotlin-code-in-javascript)。

## 留下反馈 {id=leave-feedback}

### Kotlin/Wasm 反馈 {id=kotlin-wasm-feedback}

* ![Slack](slack.svg){width=25}{type="joined"} Slack: [获取 Slack 邀请](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up) 并直接在我们的 [#webassembly](https://kotlinlang.slack.com/archives/CDFP59223) 频道中向开发人员提供反馈。
* 在 [YouTrack](https://youtrack.jetbrains.com/issue/KT-56492) 中报告任何问题。

### Compose Multiplatform 反馈 {id=compose-multiplatform-feedback}

* ![Slack](slack.svg){width=25}{type="joined"} Slack: 在 [#compose-web](https://slack-chats.kotlinlang.org/c/compose-web) 公开频道中提供反馈。
* [在 GitHub 上报告任何问题](https://github.com/JetBrains/compose-multiplatform/issues)。

## 了解更多 {id=learn-more}

* 你可以在这个 [YouTube 播放列表](https://kotl.in/wasm-pl) 中了解更多关于 Kotlin/Wasm 的信息。
* 在我们的 GitHub 仓库中探索 [Kotlin/Wasm 示例](https://github.com/Kotlin/kotlin-wasm-examples)。
