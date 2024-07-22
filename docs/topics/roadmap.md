[//]: # (title: Kotlin 路线图)

<table>
    <tr>
        <td><strong>Last modified on</strong></td>
        <td><strong>December 2023</strong></td>
    </tr>
    <tr>
        <td><strong>Next update</strong></td>
        <td><strong>August 2024</strong></td>
    </tr>
</table>

Welcome to the Kotlin roadmap! Get a sneak peek into the priorities of the JetBrains Team.

## 主要优先事项

这个路线图的目标是给您一个大致的概念。以下是我们关注的最重要的项目列表：

* **K2 compiler**: a rewrite of the Kotlin compiler optimized for speed, parallelism, and unification. It will also allow us to introduce many highly-anticipated language features.
* **K2-based IntelliJ plugin**: faster code completion, highlighting, and search, together with more stable code analysis.
* **Kotlin Multiplatform**: streamline build setup and enhance the iOS development experience.
* **Experience of library authors**: a set of documentation and tools helping to set up, develop, and publish Kotlin libraries.

## Kotlin 子系统路线图

要查看我们正在进行的最大的项目，请访问 [YouTrack board](https://youtrack.jetbrains.com/agiles/153-1251/current) 或 [路线图详细信息](#roadmap-details) 表。

如果您对路线图或其中的项目有任何问题或反馈，请随时在 [YouTrack tickets](https://youtrack.jetbrains.com/issues?q=project:%20KT,%20KTIJ%20tag:%20%7BRoadmap%20Item%7D%20%23Unresolved%20) 或 Kotlin Slack 的 [#kotlin-roadmap](https://kotlinlang.slack.com/archives/C01AAJSG3V4) 频道中发布（[请求邀请](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up)）。

### YouTrack 面板

请访问我们的问题跟踪器 YouTrack 中的 [路线图面板](https://youtrack.jetbrains.com/agiles/153-1251/current) ![YouTrack](youtrack-logo.png){width=30}{type="joined"}

### Roadmap details {id=roadmap-details}

<table>
    <tr>
        <th>子系统</th>
        <th>目前关注的重点</th>
    </tr>
    <tr>
        <td><strong>语言</strong></td>
        <td>
            <p><tip><a href="https://youtrack.jetbrains.com/issue/KT-54620" target="_blank">所有即将推出的语言功能的列表</a></tip></p>
        </td>
    </tr>
    <tr>
        <td><strong>Compiler</strong></td>
        <td>
            <list>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-64568" target="_blank">Kotlin/Wasm: Switch wasm-wasi target of libraries to WASI Preview 2</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-64569" target="_blank">Kotlin/Wasm: Support Component Model</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-60255" target="_blank">Promote K2 compiler to Stable</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-60276" target="_blank">Support debugging inline functions on Android</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-60278" target="_blank">Make Kotlin/Wasm suitable for standalone Wasm VMs</a></li>
            </list>
        </td>
    </tr>
    <tr>
        <td><strong>多平台</strong></td>
        <td>
            <list>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-64570" target="_blank">Unify inline semantics between all Kotlin targets</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-64571" target="_blank">Support SwiftPM for Kotlin Multiplatform users</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-64572" target="_blank">Swift export: Design and implement support for Kotlin classes and interfaces</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-55512">Improve the new Kotlin/Native memory manager's robustness and performance, and deprecate the old one</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-52600" target="_blank">Stabilize klib: Make binary compatibility easier for library authors</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-42294" target="_blank">Improve Kotlin/Native compilation time</a></li>
            </list>
            <p><a href="https://blog.jetbrains.com/kotlin/2023/11/kotlin-multiplatform-development-roadmap-for-2024/" target="_blank">Kotlin Multiplatform development roadmap for 2024</a></p>
         </td>
    </tr>
    <tr>
        <td><strong>工具链</strong></td>
        <td>
            <list>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-64575" target="_blank">Support Gradle project isolation</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-64577" target="_blank">Improve integration of Kotlin/Native toolchain into Gradle</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KTNB-506" target="_blank">Kotlin Notebook: Light Notebooks and improved experience exploring data from HTTP endpoints</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-60279">Improve Kotlin build reports</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KTIJ-23988">First public release of K2-based IntelliJ plugin</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KTIJ-23989">Improve performance and code analysis stability of the current IDE plugin</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-55515">Expose stable compiler arguments in Gradle DSL</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-49511" target="_blank">Improve Kotlin scripting and experience with <code>.gradle.kts</code></a></li>
            </list>
         </td>
    </tr>
    <tr>
        <td><strong>库生态系统</strong></td>
        <td>
            <list>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-64578" target="_blank">Promote <code>kotlinx-datetime</code> to Beta</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-60280" target="_blank">Provide initial series of <code>kotlinx-io</code> releases</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-48011" target="_blank">Release <code>kotlinx-metadata-jvm</code> as Stable</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-49527" target="_blank">Promote <code>kotlinx-kover</code> to Beta</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-48998" target="_blank">Release Dokka as Stable</a></li>
            </list>
            <p>Ktor 和 Exposed 的路线图：</p>
            <list>
                <li><a href="https://blog.jetbrains.com/kotlin/2024/03/the-ktor-roadmap-for-2024/" target="_blank">Ktor framework roadmap</a></li>
                <li><a href="https://blog.jetbrains.com/kotlin/2023/08/exposed-moving-forward/" target="_blank">Exposed library roadmap</a></li>
            </list>
         </td>
    </tr>
</table>

> * 这个路线图不是团队正在处理的所有事项的详尽列表，仅列出了最重要的项目。
> * 对于在特定版本中交付特定功能或修复的承诺是不存在的。
> * 随着工作的推进，我们会调整优先事项，并大约每六个月更新一次路线图。
> 
{style="note"}

## 自 2023 年 7 月以来发生了什么变化

### 已完成的项目

我们已经**完成**了上一个路线图中的以下项目：

* ✅ Compiler: [Promote Kotlin/Wasm to Alpha](https://youtrack.jetbrains.com/issue/KT-60277)
* ✅ Multiplatform: [Promote Kotlin Multiplatform to Stable](https://youtrack.jetbrains.com/issue/KT-55513)

### 新项目

我们已经**添加**了以下项目到路线图中：

* 🆕 Compiler: [Kotlin/Wasm: Switch wasm-wasi target of libraries to WASI Preview 2](https://youtrack.jetbrains.com/issue/KT-64568)
* 🆕 Compiler: [Kotlin/Wasm: Support Component Model](https://youtrack.jetbrains.com/issue/KT-64569)
* 🆕 Multiplatform: [Unify inline semantics between all Kotlin targets](https://youtrack.jetbrains.com/issue/KT-64570)
* 🆕 Multiplatform: [Support SwiftPM for Kotlin Multiplatform users](https://youtrack.jetbrains.com/issue/KT-64571)
* 🆕 Multiplatform: [Swift export: Design and implement support for Kotlin classes and interfaces](https://youtrack.jetbrains.com/issue/KT-64572)
* 🆕 Tooling: [Support Gradle project isolation](https://youtrack.jetbrains.com/issue/KT-64575)
* 🆕 Tooling: [Improve integration of Kotlin/Native toolchain into Gradle](https://youtrack.jetbrains.com/issue/KT-64577)
* 🆕 Tooling: [Kotlin Notebook: Light Notebooks and improved experience exploring data from HTTP endpoints](https://youtrack.jetbrains.com/issue/KTNB-506)
* 🆕 Library ecosystem [Promote kotlinx-datetime to Beta](https://youtrack.jetbrains.com/issue/KT-64578)

### 移除的项目

We've **removed** the following item from the roadmap:

* ❌ Multiplatform: [Improve exporting Kotlin code to Objective-C](https://youtrack.jetbrains.com/issue/KT-42297)

> 有些项目从路线图中移除，但并未完全取消。在某些情况下，我们已将以前的路线图项目与当前的项目合并。
>
{style="note"}

### 进行中的项目

所有先前确定的路线图项目都在进行中。您可以查看它们的
[YouTrack tickets](https://youtrack.jetbrains.com/issues?q=project:%20KT,%20KTIJ%20tag:%20%7BRoadmap%20Item%7D%20%23Unresolved%20) 获取更新。