[//]: # (title: Kotlin 路线图)

<table>
    <tr>
        <td><strong>Last modified on</strong></td>
        <td><strong>August 2025</strong></td>
    </tr>
    <tr>
        <td><strong>Next update</strong></td>
        <td><strong>February 2026</strong></td>
    </tr>
</table>

欢迎来到 Kotlin 路线图！来看看 JetBrains 团队的优先事项。

## 主要优先事项

本路线图的目标是为你提供整体概览。  
以下是我们的关键关注领域列表 —— 这些是我们当前最重要的发展方向：

* **Language evolution**: keep Kotlin both pragmatic and expressive with meaningful language improvements
  that emphasize semantics over syntax changes.
* **Multiplatform**: build the foundation for modern multiplatform apps with solid iOS support,
  mature web targets, and reliable IDE tooling.
* **Staying agnostic**: support developers no matter their tools or targets.
* **Ecosystem support**: simplify the development and publication process for Kotlin libraries, tools, and frameworks.

## Kotlin 子系统路线图

<!-- To view the biggest projects we're working on, see the [Roadmap details](#roadmap-details) table. -->

如果您对路线图或其中的项目有任何问题或反馈，请随时在 [YouTrack tickets](https://youtrack.jetbrains.com/issues?q=project:%20KT,%20KTIJ%20tag:%20%7BRoadmap%20Item%7D%20%23Unresolved%20) 或 Kotlin Slack 的 [#kotlin-roadmap](https://kotlinlang.slack.com/archives/C01AAJSG3V4) 频道中发布（[请求邀请](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up)）。

<!-- ### YouTrack board
Visit the [roadmap board in our issue tracker YouTrack](https://youtrack.jetbrains.com/agiles/153-1251/current) ![YouTrack](youtrack-logo.png){width=30}{type="joined"}
-->

<table>
    <tr>
        <th>子系统</th>
        <th>目前关注的重点</th>
    </tr>
    <tr id="language">
        <td><strong>语言</strong></td>
        <td>
            <p><a href="kotlin-language-features-and-proposals.md">查看完整的 Kotlin 语言特性和提案列表</a> ，或关注 <a href="https://youtrack.jetbrains.com/issue/KT-54620">YouTrack 问题追踪，了解即将发布的语言特性</a></p>
        </td>
    </tr>
    <tr id="compiler">
        <td><strong>编译器</strong></td>
        <td>
            <list>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-80304">Kotlin/Wasm: prototype multithreading support using a new threads proposal</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-75371">Finalize JSpecify support</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-75372">Deprecate K1 compiler</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-75370">Promote Kotlin/Wasm (<code>wasm-js</code> target) to Beta</a></li>
            </list>
        </td>
    </tr>
    <tr id="multiplatform">
        <td><strong>跨平台</strong></td>
        <td>
            <list>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-80305">Support coroutines in Swift Export</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-80308">Kotlin/JS: Compile to the modern JavaScript</a></li> 
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-80310">Kotlin/JS: Extend possibilities for exporting Kotlin declarations to JavaScript</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-80307">Kotlin/JS: Improve onboarding materials for Kotlin/JS</a></li> 
                <li><a href="https://youtrack.jetbrains.com/issue/KT-71278">Enable Concurrent Mark and Sweep (CMS) GC by default</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-68323">Implement the next generation distribution format of multiplatform libraries</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-64570" target="_blank">Unify inline semantics between all Kotlin targets</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-71279" target="_blank">Enable incremental compilation of klib artifacts by default</a></li>
            </list>
            <tip><p><a href="https://jb.gg/kmp-roadmap-2025" target="_blank">Kotlin Multiplatform development roadmap</a></p></tip>
         </td>
    </tr>
    <tr id="tooling">
        <td><strong>工具链</strong></td>
        <td>
            <list>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-80322" target="_blank">Support Kotlin LSP and VS Code</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KTIJ-35208" target="_blank">Improve Kotlin + JPA experience</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-80311" target="_blank">Support Kotlin JS\WASM in Gradle project isolation</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KTNB-1133" target="_blank">Kotlin Notebooks: support new use cases</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-75374" target="_blank">Improve development experience for Kotlin/Wasm projects in IntelliJ IDEA</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-75376" target="_blank">Improve performance of imports</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KTIJ-31316" target="_blank">IntelliJ IDEA K2 mode complete release</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-76255" target="_blank">Design the Build Tools API</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-71292" target="_blank">Release Kotlin Ecosystem Plugin supporting Declarative Gradle</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-49511" target="_blank">Improve Kotlin scripting and experience with <code>.gradle.kts</code></a></li>
            </list>
         </td>
    </tr>
    <tr id="ecosystem">
        <td><strong>Ecosystem</strong></td>
        <td>
            <list>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-80323">Implement KDoc machine-readable representation</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-80324">Stabilize Kotlin Notebooks</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-80327">Release Kotlin DataFrame 1.0</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-80328">Release Kandy 0.9</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-12719" target="_blank">Introduce default warnings/errors for Kotlin functions that return non-unit values that are unused</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-71298" target="_blank">New multiplatform API for the standard library: Support for Unicode and codepoints</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-71300" target="_blank">Stabilize the <code>kotlinx-io</code> library</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-71297" target="_blank">Improve Kotlin distribution UX: add code coverage and binary compatibility validation</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-64578" target="_blank">Promote <code>kotlinx-datetime</code> to Beta</a></li>
            </list>
            <p><b>Ktor:</b></p>
            <list>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KTOR-8316">Support OpenAPI specification for the Ktor Client and Server Application</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KTOR-6622">Improve Ktor Administration & Observability</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KTOR-7958">WebRTC Client</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KTOR-1501">Add gRPC support to Ktor with a generator plugin and tutorial</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KTOR-6026">Create Kubernetes Generator Plugin</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KTOR-6621">Make Dependency Injection Usage Simple</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KTOR-7938">HTTP/3 Support</a></li>
            </list>
            <p><b>Exposed:</b></p>
            <list>
                <li><a href="https://youtrack.jetbrains.com/issue/EXPOSED-444">Release 1.0.0</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/EXPOSED-74">Add R2DBC Support</a></li>
            </list>
         </td>
    </tr>
</table>

> * 这个路线图不是团队正在处理的所有事项的详尽列表，仅列出了最重要的项目。
> * 对于在特定版本中交付特定功能或修复的承诺是不存在的。
> * 随着工作的推进，我们会调整优先事项，并大约每六个月更新一次路线图。
> 
{style="note"}

## What's changed since February 2025

### 已完成的项目

我们已经**完成**了上一个路线图中的以下项目：

* ✅ Multiplatform: [The first public release of Swift Export](https://youtrack.jetbrains.com/issue/KT-64572)
* ✅ Multiplatform: [Support declaring Kotlin Multiplatform dependencies at project-level](https://youtrack.jetbrains.com/issue/KT-71289)
* ✅ Multiplatform: [Stabilize klib cross-compilation on different platforms](https://youtrack.jetbrains.com/issue/KT-71290)
* ✅ Multiplatform: [Kotlin/JS: Support common sources between WasmJS and JS for Compose fallback Mode](https://youtrack.jetbrains.com/issue/KT-79394)
* ✅ Tooling: [Improve Kotlin build reports](https://youtrack.jetbrains.com/issue/KT-60279)
* ✅ Tooling: [Expose stable compiler arguments in Gradle DSL](https://youtrack.jetbrains.com/issue/KT-55515)
* ✅ Tooling: [Support Gradle project isolation](https://youtrack.jetbrains.com/issue/KT-54105)
* ✅ Tooling: [Improve integration of Kotlin/Native toolchain into Gradle](https://youtrack.jetbrains.com/issue/KT-64577)
* ✅ Tooling: [Kotlin Notebook: Smoother access and improved experience](https://youtrack.jetbrains.com/issue/KTNB-898)
* ✅ Tooling: [Support resources in XCFrameworks](https://youtrack.jetbrains.com/issue/KT-75377)
* ✅ Ecosystem: [Refine the Dokka HTML output UI](https://youtrack.jetbrains.com/issue/KT-71295)
* ✅ Ecosystem: [Make project structuring for the backend applications simple](https://youtrack.jetbrains.com/issue/KTOR-7158)
* ✅ Ecosystem: [Publish CLI generator to SNAP](https://youtrack.jetbrains.com/issue/KTOR-3937)
* ✅ Ecosystem: [Make Dependency Injection Usage Simple](https://youtrack.jetbrains.com/issue/KTOR-6621)

### 新项目

我们已经**添加**了以下项目到路线图中：

* 🆕 Compiler: [Kotlin/Wasm: prototype multithreading support using a new threads proposal](https://youtrack.jetbrains.com/issue/KT-80304)
* 🆕 Multiplatform: [Support coroutines in Swift Export](https://youtrack.jetbrains.com/issue/KT-80305)
* 🆕 Multiplatform: [Kotlin/JS: Compile to the modern JavaScript](https://youtrack.jetbrains.com/issue/KT-80308)
* 🆕 Multiplatform: [Kotlin/JS: Extend possibilities for exporting Kotlin declarations to JavaScript](https://youtrack.jetbrains.com/issue/KT-80310)
* 🆕 Multiplatform: [Kotlin/JS: Improve onboarding materials for Kotlin/JS](https://youtrack.jetbrains.com/issue/KT-80307)
* 🆕 Tooling: [Support Kotlin LSP and VS Code](https://youtrack.jetbrains.com/issue/KT-80322)
* 🆕 Tooling: [Improve Kotlin + JPA experience](https://youtrack.jetbrains.com/issue/KTIJ-35208)
* 🆕 Tooling: [Support Kotlin JS\WASM in Gradle project isolation](https://youtrack.jetbrains.com/issue/KT-80311)
* 🆕 Tooling: [Kotlin Notebooks: support new use cases](https://youtrack.jetbrains.com/issue/KTNB-1133)
* 🆕 Ecosystem: [Implement KDoc machine-readable representation](https://youtrack.jetbrains.com/issue/KT-80323)
* 🆕 Ecosystem: [Stabilize Kotlin Notebooks](https://youtrack.jetbrains.com/issue/KT-80324)
* 🆕 Ecosystem: [Release Kotlin DataFrame 1.0](https://youtrack.jetbrains.com/issue/KT-80327)
* 🆕 Ecosystem: [Release Kandy 0.9](https://youtrack.jetbrains.com/issue/KT-80328)
* 🆕 Ecosystem: [Support OpenAPI specification for the Ktor Client and Server Application](https://youtrack.jetbrains.com/issue/KTOR-8316)
* 🆕 Ecosystem: [Improve Ktor Administration & Observability](https://youtrack.jetbrains.com/issue/KTOR-6622)
* 🆕 Ecosystem: [WebRTC Client](https://youtrack.jetbrains.com/issue/KTOR-7958)

### Removed items

我们已**移除**以下项目：

* ❌ Compiler: [Kotlin/Wasm: Switch `wasm-wasi` target of libraries to WASI Preview 2](https://youtrack.jetbrains.com/issue/KT-64568)
* ❌ Compiler: [Kotlin/Wasm: Support Component Model](https://youtrack.jetbrains.com/issue/KT-64569)
* ❌ Ecosystem: [Publish to Snap](https://youtrack.jetbrains.com/issue/KTOR-3937)

> 有些项目从路线图中移除，但并未完全取消。在某些情况下，我们已将以前的路线图项目与当前的项目合并。
>
{style="note"}
