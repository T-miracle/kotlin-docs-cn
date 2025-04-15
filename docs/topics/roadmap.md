[//]: # (title: Kotlin 路线图)

<table>
    <tr>
        <td><strong>最后修改时间</strong></td>
        <td><strong>2025 年 2 月</strong></td>
    </tr>
    <tr>
        <td><strong>下次更新</strong></td>
        <td><strong>2025 年 8 月</strong></td>
    </tr>
</table>

欢迎来到 Kotlin 路线图！来看看 JetBrains 团队的优先事项。

## 主要优先事项

本路线图的目标是为你提供整体概览。  
以下是我们的关键关注领域列表 —— 这些是我们当前最重要的发展方向：

- **语言演进**：更高效的数据处理、更强的抽象能力、更清晰代码下的性能提升。
- **Kotlin 跨平台**：发布 Kotlin 直接导出为 Swift 的功能，简化构建配置流程，以及简化跨平台库的创建。
- **第三方生态作者体验**：简化 Kotlin 库、工具与框架的开发与发布流程。

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
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-75371">Finalize JSpecify support</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-75372">Deprecate K1 compiler</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-75370">Promote Kotlin/Wasm (<code>wasm-js</code> target) to Beta</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-64568" target="_blank">Kotlin/Wasm: Switch <code>wasm-wasi</code> target of libraries to WASI Preview 2</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-64569" target="_blank">Kotlin/Wasm: Support Component Model</a></li>
            </list>
        </td>
    </tr>
    <tr id="multiplatform">
        <td><strong>跨平台</strong></td>
        <td>
            <list>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-64572">The first public release of Swift Export</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-71278">Enable Concurrent Mark and Sweep (CMS) GC by default</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-71290">Stabilize klib cross-compilation on different platforms</a></li> 
                <li><a href="https://youtrack.jetbrains.com/issue/KT-71281">Implement the next generation distribution format of multiplatform libraries</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-71289">Support declaring Kotlin Multiplatform dependencies at project-level</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-64570" target="_blank">Unify inline semantics between all Kotlin targets</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-71279" target="_blank">Enable incremental compilation of klib artifacts by default</a></li>
            </list>
            <tip><p><a href="https://www.jetbrains.com/help/kotlin-multiplatform-dev/kotlin-multiplatform-roadmap.html" target="_blank">Kotlin Multiplatform development roadmap</a></p></tip>
         </td>
    </tr>
    <tr id="tooling">
        <td><strong>工具链</strong></td>
        <td>
            <list>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-75374" target="_blank">Improve development experience for Kotlin/Wasm projects in IntelliJ IDEA</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-75376" target="_blank">Improve performance of imports</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-75377" target="_blank">Support resources in XCFrameworks</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KTNB-898" target="_blank">Kotlin Notebook: Smoother access and improved experience</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KTIJ-31316" target="_blank">IntelliJ IDEA K2 mode complete release</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-71286" target="_blank">Design the Build Tools API</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-71292" target="_blank">Kotlin Ecosystem Plugin supporting Declarative Gradle</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-54105" target="_blank">Support Gradle project isolation</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-64577" target="_blank">Improve integration of Kotlin/Native toolchain into Gradle</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-60279" target="_blank">Improve Kotlin build reports</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-55515" target="_blank">Expose stable compiler arguments in Gradle DSL</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-49511" target="_blank">Improve Kotlin scripting and experience with <code>.gradle.kts</code></a></li>
            </list>
         </td>
    </tr>
    <tr id="library-ecosystem">
        <td><strong>库生态系统</strong></td>
        <td>
            <p><b>Library ecosystem roadmap items:</b></p>
            <list>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-71295" target="_blank">Refine the Dokka HTML output UI</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-12719" target="_blank">Introduce default warnings/errors for Kotlin functions that return non-unit values that are unused</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-71298" target="_blank">New multiplatform API for the standard library: Support for Unicode and codepoints</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-71300" target="_blank">Stabilize the <code>kotlinx-io</code> library</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-71297" target="_blank">Improve Kotlin distribution UX: add code coverage and binary compatibility validation</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-64578" target="_blank">Promote <code>kotlinx-datetime</code> to Beta</a></li>
            </list>
            <p><b>Ktor:</b></p>
            <list>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KTOR-1501">Add gRPC support to Ktor with a generator plugin and tutorial</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KTOR-7158">Make project structuring for the backend applications simple</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KTOR-3937">Publish CLI generator to SNAP</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KTOR-6026">Create Kubernetes Generator Plugin</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KTOR-6621">Make Dependency Injection Usage Simple</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KTOR-7938">HTTP/3 Support</a></li>
            </list>
            <p><b>Exposed:</b></p>
            <list>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/EXPOSED-444">Release 1.0.0</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/EXPOSED-74">Add R2DBC Support</a></li>
            </list>
         </td>
    </tr>
</table>

> * 这个路线图不是团队正在处理的所有事项的详尽列表，仅列出了最重要的项目。
> * 对于在特定版本中交付特定功能或修复的承诺是不存在的。
> * 随着工作的推进，我们会调整优先事项，并大约每六个月更新一次路线图。
> 
{style="note"}

## What's changed since September 2024

### 已完成的项目

我们已经**完成**了上一个路线图中的以下项目：

* ✅ Compiler: [Support debugging inline functions on Android](https://youtrack.jetbrains.com/issue/KT-60276)
* ✅ Compiler: [Improve the quality of compiler diagnostics](https://youtrack.jetbrains.com/issue/KT-71275)
* ✅ Multiplatform: [Support Xcode 16 in Kotlin](https://youtrack.jetbrains.com/issue/KT-71287)
* ✅ Multiplatform: [Publish publicly available API reference for Kotlin Gradle Plugin](https://youtrack.jetbrains.com/issue/KT-71288)
* ✅ Tooling: [Provide out-of-the-box debugging experience for Kotlin/Wasm targets](https://youtrack.jetbrains.com/issue/KT-71276)
* ✅ Library ecosystem: [Implement new Dokka Gradle plugin based on Dokkatoo](https://youtrack.jetbrains.com/issue/KT-71293)
* ✅ Library ecosystem: [New multiplatform API for the standard library: Atomics](https://youtrack.jetbrains.com/issue/KT-62423)
* ✅ Library ecosystem: [Expand Library authors’ guidelines](https://youtrack.jetbrains.com/issue/KT-71299)

### 新项目

我们已经**添加**了以下项目到路线图中：

* 🆕 Compiler: [Finalize JSpecify support](https://youtrack.jetbrains.com/issue/KT-75371)
* 🆕 Compiler: [Deprecate K1 compiler](https://youtrack.jetbrains.com/issue/KT-75372)
* 🆕 Compiler: [Promote Kotlin/Wasm (`wasm-js` target) to Beta](https://youtrack.jetbrains.com/issue/KT-75370)
* 🆕 Tooling: [Improve development experience for Kotlin/Wasm projects in IntelliJ IDEA](https://youtrack.jetbrains.com/issue/KT-75374)
* 🆕 Tooling: [Improve performance of imports](https://youtrack.jetbrains.com/issue/KT-75376)
* 🆕 Tooling: [Support resources in XCFrameworks](https://youtrack.jetbrains.com/issue/KT-75377)
* 🆕 Tooling: [Smoother access and improved experience in Kotlin Notebook](https://youtrack.jetbrains.com/issue/KTNB-898)
* 🆕 Ktor: [Add gRPC support to Ktor with a generator plugin and tutorial](https://youtrack.jetbrains.com/issue/KTOR-1501)
* 🆕 Ktor: [Make project structuring for the backend applications simple](https://youtrack.jetbrains.com/issue/KTOR-7158)
* 🆕 Ktor: [Publish CLI generator to SNAP](https://youtrack.jetbrains.com/issue/KTOR-3937)
* 🆕 Ktor: [Create Kubernetes Generator Plugin](https://youtrack.jetbrains.com/issue/KTOR-6026)
* 🆕 Ktor: [Make Dependency Injection Usage Simple](https://youtrack.jetbrains.com/issue/KTOR-6621)
* 🆕 Ktor: [HTTP/3 Support](https://youtrack.jetbrains.com/issue/KTOR-7938)
* 🆕 Exposed: [Release 1.0.0](https://youtrack.jetbrains.com/issue/EXPOSED-444)
* 🆕 Exposed: [Add R2DBC Support](https://youtrack.jetbrains.com/issue/EXPOSED-74)

<!--
### Removed items

我们已**移除**以下项目：

* ❌ Compiler: [Improve the quality of compiler diagnostics](https://youtrack.jetbrains.com/issue/KT-71275)

> 有些项目从路线图中移除，但并未完全取消。在某些情况下，我们已将以前的路线图项目与当前的项目合并。
>
{style="note"}
-->

### 进行中的项目

All other previously identified roadmap items are in progress. You can check their [YouTrack tickets](https://youtrack.jetbrains.com/issues?q=project:%20KT,%20KTIJ%20tag:%20%7BRoadmap%20Item%7D%20%23Unresolved%20)
for updates.
