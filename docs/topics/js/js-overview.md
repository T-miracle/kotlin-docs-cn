[//]: # (title: 用于 JavaScript 的 Kotlin)

Kotlin/JS 提供将您的 Kotlin 代码、Kotlin 标准库和任何兼容的依赖项转译为 JavaScript 的能力。
目前的 Kotlin/JS 实现面向 [ES5](https://www.ecma-international.org/ecma-262/5.1/)。

使用 Kotlin/JS 的推荐方式是通过 `kotlin.multiplatform` Gradle 插件。
它允许您在一个地方轻松设置和控制面向 JavaScript 的 Kotlin 项目。
这包括关键功能，比如控制应用程序的打包、直接从 npm 添加 JavaScript 依赖项等。
要了解可用选项的概述，请查看 [设置 Kotlin/JS 项目](js-project-setup.md)。

## Kotlin/JS IR 编译器

[Kotlin/JS IR 编译器](js-ir-compiler.md) 相比旧的默认编译器，带来了许多改进。
例如，通过消除死代码（冗余代码、从没被调用过的代码），它减小了生成可执行文件的大小，并提供了与 JavaScript 生态系统及其工具更加流畅的互操作性。

> 自 Kotlin 1.8.0 版本以来，旧编译器已被弃用。
> 
{style="note"}

通过从 Kotlin 代码生成 TypeScript 声明文件 (`d.ts`)，IR 编译器使得创建“混合”应用程序更加容易，这些应用程序将 TypeScript 和 Kotlin 代码结合在一起，并利用 Kotlin 跨平台 实现代码共享功能。

要了解有关 Kotlin/JS IR 编译器中可用功能的更多信息，以及如何在项目中尝试它，请访问 [Kotlin/JS IR 编译器文档页面](js-ir-compiler.md) 和 [迁移指南](js-ir-migration.md)。

## Kotlin/JS 框架

现代 Web 开发在很大程度上受益于简化构建 Web 应用程序的框架。以下是一些由不同作者编写的 Kotlin/JS 的热门 Web 框架示例：

### Kobweb

_Kobweb_ is an opinionated Kotlin framework for creating websites and web apps. It leverages [Compose HTML](https://github.com/JetBrains/compose-multiplatform?tab=readme-ov-file#compose-html) and
live-reloading for fast development. Inspired by [Next.js](https://nextjs.org/), Kobweb promotes a standard structure for adding widgets, layouts,
and pages.

Out of the box, Kobweb provides page routing, light/dark mode, CSS styling, Markdown support, backend APIs, and more features.
It also includes a UI library called Silk, a set of versatile widgets for modern UIs. 

Kobweb also supports site export, generating page snapshots
for SEO and automatic search indexing. Additionally, Kobweb makes it easy to create DOM-based UIs that efficiently update in response to state changes.

Visit the [Kobweb](https://kobweb.varabyte.com/) site for documentation and examples.

For updates and discussions about the framework, join the [#kobweb](https://kotlinlang.slack.com/archives/C04RTD72RQ8) and
[#compose-web](https://kotlinlang.slack.com/archives/C01F2HV7868) channels in the Kotlin Slack.

### KVision

_KVision_ 是一款面向对象的 Web 框架，可以在 Kotlin/JS 中编写具有即用型组件的应用程序，这些组件可以作为应用程序用户界面的构建块使用。
您可以使用响应式和命令式编程模型来构建前端，使用连接器与 Ktor、Spring Boot 和其他框架集成到您的服务器端应用程序，并使用 [Kotlin 跨平台](multiplatform-intro.md) 共享代码。

[访问 KVision 网站](https://kvision.io) 获取文档、教程和示例。

要获取关于该框架的更新和讨论，请加入 [Kotlin Slack](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up) 中的
[#kvision](https://kotlinlang.slack.com/messages/kvision) 和 [#javascript](https://kotlinlang.slack.com/archives/C0B8L3U69) 频道。

### fritz2

_fritz2_ 是一个用于构建响应式 Web 用户界面的独立框架。
它提供了自己的类型安全 DSL 用于构建和呈现 HTML 元素，并利用 Kotlin 的协程和流来表达组件及其数据绑定。
还提供开箱即用的状态管理、验证、路由等功能，并与 Kotlin 跨平台 项目集成。

[访问 fritz2 网站](https://www.fritz2.dev) 获取文档、教程和示例。

要获取有关该框架的更新和讨论，请加入 [Kotlin Slack](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up) 中的
[#fritz2](https://kotlinlang.slack.com/messages/fritz2) 和 [#javascript](https://kotlinlang.slack.com/archives/C0B8L3U69) 频道。

### Doodle

_Doodle_ 是一个基于矢量的 Kotlin/JS UI 框架。
Doodle 应用程序利用浏览器的图形能力来绘制用户界面，而不是依赖于 DOM、CSS 或 JavaScript。
通过使用这种方法，Doodle 使您能够精确控制任意 UI 元素、矢量形状、渐变和自定义可视化的渲染。

[访问 Doodle 网站](https://nacular.github.io/doodle/) 获取文档、教程和示例。

要获取有关该框架的更新和讨论，请加入 [Kotlin Slack](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up) 中的
[#doodle](https://kotlinlang.slack.com/messages/doodle) 和 [#javascript](https://kotlinlang.slack.com/archives/C0B8L3U69) 频道。

## 加入 Kotlin/JS 社区 {id="加入Kotlin-JS社区"}

您可以加入官方 [Kotlin Slack](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up) 中的
[#javascript](https://kotlinlang.slack.com/archives/C0B8L3U69) 频道，与社区和团队进行交流。