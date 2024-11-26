[//]: # (title: Kotlin/Wasm)

> Kotlin/Wasm 处于 [Alpha](components-stability.md) 阶段，可能随时发生变化。
> 在生产环境之前，您可以在其他场景中使用它。我们非常欢迎您在 [YouTrack](https://youtrack.jetbrains.com/issue/KT-56492) 提供反馈。
>
> [加入 Kotlin/Wasm 社区](https://slack-chats.kotlinlang.org/c/webassembly)。
>
{style="note"}

Kotlin/Wasm 具有将您的 Kotlin 代码编译为 [WebAssembly (Wasm)](https://webassembly.org/) 格式的能力。  
借助 Kotlin/Wasm，您可以创建在支持 Wasm 且满足 Kotlin 要求的不同环境和设备上运行的应用程序。

Wasm 是一种面向栈的虚拟机的二进制指令格式。该格式是平台无关的，因为它在自己的虚拟机上运行。
Wasm 为 Kotlin 和其他语言提供了一个编译目标。

您可以在不同的目标环境中使用 Kotlin/Wasm，例如浏览器中，用于开发使用
[Compose Multiplatform](https://www.jetbrains.com/lp/compose-multiplatform/) 构建的 Web 应用程序，或者在浏览器之外的独立
Wasm 虚拟机中。在浏览器外的使用场景中， [WebAssembly System Interface (WASI)](https://wasi.dev/)
提供了对平台 API 的访问，您也可以利用这些 API。


## Kotlin/Wasm 和 Compose 跨平台 {id=kotlin-wasm-and-compose-multiplatform}

借助 Kotlin，您可以通过 Compose 跨平台和 Kotlin/Wasm 在您的 Web 项目中构建应用程序并重用移动和桌面用户界面（UI）。

[Compose 跨平台](https://www.jetbrains.com/lp/compose-multiplatform/) 是一个基于 Kotlin 和
[Jetpack Compose](https://developer.android.com/jetpack/compose) 的声明式框架，允许您一次实现 UI 并在所有目标平台上共享它。

对于 Web 平台，Compose 跨平台使用 Kotlin/Wasm 作为其编译目标。
使用 Kotlin/Wasm 和 Compose 跨平台构建的应用程序使用 `wasm-js` 目标并在浏览器中运行。

[探索我们基于 Compose Multiplatform 和 Kotlin/Wasm 构建的应用程序的在线演示](https://zal.im/wasm/jetsnack/)

![Kotlin/Wasm 示例](wasm-demo.png){width=700}

> 要在浏览器中运行使用 Kotlin/Wasm 构建的应用程序，你需要一个支持新的垃圾回收和异常处理提案的浏览器版本。
> 要检查浏览器的支持状态，请参见 [WebAssembly
> 路线图](https://webassembly.org/roadmap/)。
>
{style="tip"}

此外，您可以在 Kotlin/Wasm 中开箱即用地使用最流行的 Kotlin 库。
与其他 Kotlin 和跨平台项目一样，您可以在构建脚本中包含依赖声明。
有关更多信息，请参见 [添加跨平台库的依赖](multiplatform-add-dependencies.md)。

你想自己尝试一下吗？

<a href="wasm-get-started.md"><img src="wasm-get-started-button.svg" width="700" alt="Kotlin/Wasm 入门" style="block"/></a>

## Kotlin/Wasm 和 WASI {id=kotlin-wasm-and-wasi}

Kotlin/Wasm 使用 [WebAssembly System Interface (WASI)](https://wasi.dev/) 来支持服务器端应用程序。  
使用 Kotlin/Wasm 和 WASI 构建的应用程序使用 Wasm-WASI 目标，允许您调用 WASI API 并在浏览器环境之外运行应用程序。

Kotlin/Wasm 利用 WASI 抽象平台特定的细节，使得相同的 Kotlin 代码能够在不同的平台上运行。
这使得 Kotlin/Wasm 的应用范围超越了 Web 应用程序，而无需为每个运行时环境进行自定义处理。

WASI 提供了一个安全的标准接口，用于在不同环境中运行编译为 WebAssembly 的 Kotlin 应用程序。

> 要查看 Kotlin/Wasm 和 WASI 的实际应用，请查看 [开始使用 Kotlin/Wasm 和 WASI 教程](wasm-wasi.md)。
>
{style="tip"}

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