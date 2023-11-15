[//]: # (title: Kotlin 路线图)

<table>
    <tr>
        <td><strong>上次修改时间</strong></td>
        <td><strong>2023 年 7 月</strong></td>
    </tr>
    <tr>
        <td><strong>下次更新</strong></td>
        <td><strong>2023 年 12 月</strong></td>
    </tr>
</table>

欢迎来到 Kotlin 路线图！一窥 Kotlin 团队的优先事项。

## 主要优先事项

这个路线图的目标是给您一个大致的概念。以下是我们关注的最重要的项目列表：

* **K2 编译器**：优化为速度、并行性和统一性的 Kotlin 编译器的重写。这也将让我们引入许多期待已久的语言特性。
* **基于 K2 的 IntelliJ 插件**：更快的代码完成、高亮和搜索，以及更稳定的代码分析。
* **Kotlin Multiplatform**：通过提高工具链的稳定性和文档，确保兼容性保证，将这项技术推广到稳定状态。
* **库作者的体验**：一套文档和工具，帮助设置、开发和发布 Kotlin 库。

## Kotlin 子系统路线图

要查看我们正在进行的最大的项目，请访问 [YouTrack board](https://youtrack.jetbrains.com/agiles/153-1251/current) 或 [路线图详细信息](#路线图详细信息) 表。

如果您对路线图或其中的项目有任何问题或反馈，请随时在 [YouTrack tickets](https://youtrack.jetbrains.com/issues?q=project:%20KT,%20KTIJ%20tag:%20%7BRoadmap%20Item%7D%20%23Unresolved%20) 或 Kotlin Slack 的 [#kotlin-roadmap](https://kotlinlang.slack.com/archives/C01AAJSG3V4) 频道中发布（[请求邀请](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up)）。

### YouTrack 面板

请访问我们的问题跟踪器 YouTrack 中的 [路线图面板](https://youtrack.jetbrains.com/agiles/153-1251/current) ![YouTrack](youtrack-logo.png){width=30}{type="joined"}

![Roadmap board in YouTrack](roadmap-board.png){width=700}

### 路线图详细信息 {id=路线图详细信息}

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
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-60255" target="_blank">将 K2 编译器升级到稳定版</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-60276" target="_blank">支持在Android上调试内联函数</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-60277" target="_blank">将 Kotlin/Wasm 提升到 Alpha</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-60278" target="_blank">使 Kotlin/Wasm 适用于独立的 Wasm VM</a></li>
            </list>
        </td>
    </tr>
    <tr>
        <td><strong>多平台</strong></td>
        <td>
            <list>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-55513">将 Kotlin 多平台移动版提升至稳定版</a></li> 
                <li><a href="https://youtrack.jetbrains.com/issue/KT-55512">提高新的 Kotlin/Native 内存管理器的稳健性和性能，并弃用旧的</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-52600" target="_blank">稳定 klib：使库作者更容易实现二进制兼容性</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-42297" target="_blank">改进将 Kotlin 代码导出到 Objective-C</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-42294" target="_blank">改进 Kotlin/Native 编译时间</a></li>
            </list>
         </td>
    </tr>
    <tr>
        <td><strong>工具链</strong></td>
        <td>
            <list>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-60279">改进 Kotlin 构建报告</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KTIJ-23988">首次公开发布基于 K2 的 IntelliJ 插件</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KTIJ-23989">提高当前IDE插件的性能和代码分析稳定性</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-55515">在 Gradle DSL 中公开稳定的编译器参数</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-49511" target="_blank">使用 <code>.gradle.kts</code> 改进 Kotlin 脚本编写和体验</a></li>
            </list>
         </td>
    </tr>
    <tr>
        <td><strong>库生态系统</strong></td>
        <td>
            <list>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-60280" target="_blank">提供初始一系列的 `<code>kotlinx-io</code>` 发布。</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-48011" target="_blank">将 `<code>kotlinx-metadata-jvm</code>` 发布为稳定版。</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-49527" target="_blank">将 `<code>kotlinx-kover</code>` 升级为稳定版。</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-48998" target="_blank">将 Dokka 发布为稳定版。</a></li>
            </list>
            <p>Ktor 和 Exposed 的路线图：</p>
            <list>
                <li><a href="https://blog.jetbrains.com/ktor/2022/12/16/ktor-2023-roadmap/" target="_blank">Ktor 框架路线图</a></li>
                <li><a href="https://blog.jetbrains.com/kotlin/2023/08/exposed-moving-forward/" target="_blank">Exposed 库路线图</a></li>
            </list>
         </td>
    </tr>
</table>

> * 这个路线图不是团队正在处理的所有事项的详尽列表，仅列出了最重要的项目。
> * 对于在特定版本中交付特定功能或修复的承诺是不存在的。
> * 随着工作的推进，我们会调整优先事项，并大约每六个月更新一次路线图。
> 
{style="note"}

## 自 2022 年 12 月以来的变更

### 已完成的项目

我们已经**完成**了上一个路线图中的以下项目：

* ✅ 语言: [引入 `until` 操作符的特殊语法](https://youtrack.jetbrains.com/issue/KT-15613)
* ✅ 语言: [为 `Enum.values()` 提供现代且高性能的替代方案](https://youtrack.jetbrains.com/issue/KT-48872)
* ✅ 语言: [设计和实现对象上的 `toString`、`equals` 和 `hashCode` 的解决方案（数据对象）](https://youtrack.jetbrains.com/issue/KT-4107)
* ✅ 编译器: [发布 K2 Beta](https://youtrack.jetbrains.com/issue/KT-52604)
* ✅ 编译器: [解决与 JVM 上内联类相关的问题](https://youtrack.jetbrains.com/issue/KT-49514)
* ✅ 编译器: [实现 Kotlin/Wasm 编译器后端的实验版本](https://youtrack.jetbrains.com/issue/KT-46773)
* ✅ 工具: [提供更好的 Kotlin Daemon 使用体验](https://youtrack.jetbrains.com/issue/KT-49532)
* ✅ 工具: [改进 Gradle 增量编译的性能](https://youtrack.jetbrains.com/issue/KT-42309)
* ✅ 工具: [发布 Kotlin Notebooks IJ IDEA 插件的实验版本](https://youtrack.jetbrains.com/issue/KTIJ-23990)
* ✅ 库生态系统: [发布 `kotlinx-coroutines` 1.7](https://youtrack.jetbrains.com/issue/KT-49529)
* ✅ 库生态系统: [改进 `kotlinx-datetime` 库](https://youtrack.jetbrains.com/issue/KT-42315)
* ✅ 库生态系统: [继续开发和稳定化标准库](https://youtrack.jetbrains.com/issue/KT-52601)

### 新项目

我们已经**添加**了以下项目到路线图中：

* 🆕 编译器: [发布 Kotlin 2.0](https://youtrack.jetbrains.com/issue/KT-60255)
* 🆕 编译器: [支持在 Android 上调试内联函数](https://youtrack.jetbrains.com/issue/KT-60276)
* 🆕 编译器: [将 Kotlin/Wasm 升级为 Alpha 版](https://youtrack.jetbrains.com/issue/KT-60277)
* 🆕 编译器: [使 Kotlin/Wasm 适用于独立的 Wasm VM（不包含 JavaScript 支持）](https://youtrack.jetbrains.com/issue/KT-60278)
* 🆕 工具: [改进 Kotlin 构建报告](https://youtrack.jetbrains.com/issue/KT-60279)
* 🆕 库生态系统: [提供初始一系列的 `kotlinx-io` 发布](https://youtrack.jetbrains.com/issue/KT-60280)

### 移除的项目

我们已经**移除**了路线图中的以下项目：

* ❌ 语言: [支持非局部 `break` 和 `continue`](https://youtrack.jetbrains.com/issue/KT-1436)
* ❌ 编译器: [稳定 JVM 特定的实验性特性](https://youtrack.jetbrains.com/issue/KT-46770)
* ❌ 库生态系统: [稳定和文档化 `atomicfu`](https://youtrack.jetbrains.com/issue/KT-46786)
* ❌ 库生态系统: [改进 KDoc 体验](https://youtrack.jetbrains.com/issue/KT-55073)
* ❌ 库生态系统: [为库作者提供 Kotlin API 指南](https://youtrack.jetbrains.com/issue/KT-55077)

> 有些项目从路线图中移除，但并未完全取消。在某些情况下，我们已将以前的路线图项目与当前的项目合并。
>
{style="note"}

### 进行中的项目

所有先前确定的路线图项目都在进行中。您可以查看它们的
[YouTrack tickets](https://youtrack.jetbrains.com/issues?q=project:%20KT,%20KTIJ%20tag:%20%7BRoadmap%20Item%7D%20%23Unresolved%20) 获取更新。