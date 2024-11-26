[//]: # (title: Kotlin 路线图)

<table>
    <tr>
        <td><strong>最后修改时间</strong></td>
        <td><strong>2024年9月</strong></td>
    </tr>
    <tr>
        <td><strong>下次更新</strong></td>
        <td><strong>2025年2月</strong></td>
    </tr>
</table>

欢迎来到 Kotlin 路线图！来看看 JetBrains 团队的优先事项。

## 主要优先事项

这个路线图的目标是给您一个大致的概念。以下是我们关注的最重要的项目列表：

* **语言演化**：更高效的数据处理，增加的抽象层次，提升的性能和清晰的代码。
* **基于 K2 的 IntelliJ IDEA 插件**：更快的代码补全、突出显示和搜索，以及更稳定的代码分析。
* **Kotlin 跨平台**：发布直接将 Kotlin 导出为 Swift，简化的构建设置，以及简化的跨平台库创建。
* **第三方生态系统作者的体验**：简化 Kotlin 库、工具和框架的开发和发布过程。

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
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-71275">提高编译器诊断的质量</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-71283">生成 JVM 默认方法</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-64568" target="_blank">Kotlin/Wasm：将库的 wasm-wasi 目标切换为 WASI Preview 2</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-64569" target="_blank">Kotlin/Wasm：支持组件模型</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-60276" target="_blank">支持在 Android 上调试内联函数</a></li>
            </list>
        </td>
    </tr>
    <tr id="multiplatform">
        <td><strong>跨平台</strong></td>
        <td>
            <list>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-64572">首次公开发布 Swift 导出</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-71278">默认启用并发标记清扫（CMS）GC</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-71279">默认启用 klib 构件的增量编译</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-71290">稳定 klib 在不同平台上的交叉编译</a></li> 
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-71281">实现下一代跨平台库的分发格式</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-71289">支持在项目级别声明 Kotlin 跨平台依赖</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-71287">支持 Kotlin 中的 Xcode 16</a></li> 
                <li><a href="https://youtrack.jetbrains.com/issue/KT-64570" target="_blank">统一所有 Kotlin 目标的内联语义</a></li>
            </list>
            <tip><p><a href="https://blog.jetbrains.com/kotlin/2023/11/kotlin-multiplatform-development-roadmap-for-2024/" target="_blank">2024 年 Kotlin 跨平台开发路线图</a></p></tip>
         </td>
    </tr>
    <tr id="tooling">
        <td><strong>工具链</strong></td>
        <td>
            <list>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KTIJ-31316">IntelliJ IDEA K2 模式完整发布</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-71286">设计构建工具 API</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-71288">发布公开可用的 Kotlin Gradle 插件 API 参考</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-71292">支持声明式 Gradle 的 Kotlin 生态系统插件</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-71276">为 Kotlin/Wasm 目标提供开箱即用的调试体验</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-64575" target="_blank">支持 Gradle 项目隔离</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-64577" target="_blank">改善 Kotlin/Native 工具链与 Gradle 的集成</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KTNB-506" target="_blank">Kotlin Notebook：轻量级 Notebooks 和改进的 HTTP 端点数据探索体验</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-60279">改进 Kotlin 构建报告</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-55515">在 Gradle DSL 中公开稳定的编译器参数</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-49511" target="_blank">改进 Kotlin 脚本和 <code>.gradle.kts</code> 的体验</a></li>
            </list>
         </td>
    </tr>
    <tr id="library-ecosystem">
        <td><strong>库生态系统</strong></td>
        <td>
            <list>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-71293">基于 Dokkatoo 实现新的 Dokka Gradle 插件</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-71295">精细化 Dokka HTML 输出的 UI</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-62423">标准库的新 multiplatform API：原子操作</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-12719">为 Kotlin 函数引入默认警告/错误，针对未使用的非 Unit 返回值</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-71298">标准库的新 multiplatform API：支持 Unicode 和字符集</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-71299">扩展库作者指南</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-71300">稳定 <code>kotlinx-io</code> 库</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-71297">改进 Kotlin 分发 UX：增加代码覆盖率和二进制兼容性验证</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-64578" target="_blank">推广 <code>kotlinx-datetime</code> 到 Beta 版本</a></li>
            </list>
            <tip><p>Ktor 和 Exposed 的路线图：</p>
            <list>
                <li><a href="https://blog.jetbrains.com/kotlin/2024/03/the-ktor-roadmap-for-2024/" target="_blank">Ktor 框架路线图</a></li>
                <li><a href="https://blog.jetbrains.com/kotlin/2023/08/exposed-moving-forward/" target="_blank">Exposed 库路线图</a></li>
            </list>
            </tip>
         </td>
    </tr>
</table>

> * 这个路线图不是团队正在处理的所有事项的详尽列表，仅列出了最重要的项目。
> * 对于在特定版本中交付特定功能或修复的承诺是不存在的。
> * 随着工作的推进，我们会调整优先事项，并大约每六个月更新一次路线图。
> 
{style="note"}

## 自 2023 年 12 月以来发生的变化

### 已完成的项目

我们已经**完成**了上一个路线图中的以下项目：

* ✅ 编译器: [将 K2 编译器升级为稳定版](https://youtrack.jetbrains.com/issue/KT-60255)
* ✅ 编译器: [使 Kotlin/Wasm 适用于独立的 Wasm 虚拟机](https://youtrack.jetbrains.com/issue/KT-60278)
* ✅ 跨平台: [提高新的 Kotlin/Native 内存管理器的稳健性和性能，并废弃旧的内存管理器](https://youtrack.jetbrains.com/issue/KT-55512)
* ✅ 跨平台: [稳定 klib：使库作者更容易进行二进制兼容性处理](https://youtrack.jetbrains.com/issue/KT-52600)
* ✅ 跨平台: [提高 Kotlin/Native 编译时间](https://youtrack.jetbrains.com/issue/KT-42294)
* ✅ 工具: [K2 基于的 IntelliJ IDEA 插件的首次公开发布](https://youtrack.jetbrains.com/issue/KTIJ-23988)
* ✅ 工具: [提高当前 IDE 插件的性能和代码分析稳定性](https://youtrack.jetbrains.com/issue/KTIJ-23989)
* ✅ 库生态系统: [提供初始版本的 kotlinx-io 发布](https://youtrack.jetbrains.com/issue/KT-60280)
* ✅ 库生态系统: [将 kotlinx-metadata-jvm 发布为稳定版](https://youtrack.jetbrains.com/issue/KT-48011)
* ✅ 库生态系统: [将 kotlinx-kover 升级为 Beta 版](https://youtrack.jetbrains.com/issue/KT-49527)

### 新项目

我们已经**添加**了以下项目到路线图中：

* 🆕 编译器: [提高编译器诊断质量](https://youtrack.jetbrains.com/issue/KT-71275)
* 🆕 编译器: [生成 JVM 默认方法](https://youtrack.jetbrains.com/issue/KT-71283)
* 🆕 跨平台: [首次公开发布 Swift 导出](https://youtrack.jetbrains.com/issue/KT-64572)
* 🆕 跨平台: [默认启用并发标记清理（CMS）垃圾回收](https://youtrack.jetbrains.com/issue/KT-71278)
* 🆕 跨平台: [默认启用 klib 工件的增量编译](https://youtrack.jetbrains.com/issue/KT-71279)
* 🆕 跨平台: [稳定不同平台上的 klib 跨平台编译](https://youtrack.jetbrains.com/issue/KT-71290)
* 🆕 跨平台: [实现下一代跨平台库分发格式](https://youtrack.jetbrains.com/issue/KT-71281)
* 🆕 跨平台: [支持在项目级别声明 Kotlin 跨平台依赖](https://youtrack.jetbrains.com/issue/KT-71289)
* 🆕 跨平台: [支持在 Kotlin 中使用 Xcode 16](https://youtrack.jetbrains.com/issue/KT-71287)
* 🆕 工具: [IntelliJ IDEA K2 模式完整发布](https://youtrack.jetbrains.com/issue/KTIJ-31316)
* 🆕 工具: [设计构建工具 API](https://youtrack.jetbrains.com/issue/KT-71286)
* 🆕 工具: [发布 Kotlin Gradle 插件的公开 API 参考](https://youtrack.jetbrains.com/issue/KT-71288)
* 🆕 工具: [为 Kotlin/Wasm 目标提供开箱即用的调试体验](https://youtrack.jetbrains.com/issue/KT-71276)
* 🆕 工具: [支持声明性 Gradle 的 Kotlin 生态系统插件](https://youtrack.jetbrains.com/issue/KT-71292)
* 🆕 库生态系统: [基于 Dokkatoo 实现新的 Dokka Gradle 插件](https://youtrack.jetbrains.com/issue/KT-71293)
* 🆕 库生态系统: [完善 Dokka HTML 输出 UI](https://youtrack.jetbrains.com/issue/KT-71295)
* 🆕 库生态系统: [标准库的新跨平台 API: Atomics](https://youtrack.jetbrains.com/issue/KT-62423)
* 🆕 库生态系统: [为返回非 unit 值且未使用的 Kotlin 函数引入默认警告/错误](https://youtrack.jetbrains.com/issue/KT-12719)
* 🆕 库生态系统: [标准库的新跨平台 API: 支持 Unicode 和代码点](https://youtrack.jetbrains.com/issue/KT-71298)
* 🆕 库生态系统: [扩展库作者指南](https://youtrack.jetbrains.com/issue/KT-71299)
* 🆕 库生态系统: [稳定 kotlinx-io 库](https://youtrack.jetbrains.com/issue/KT-71300)
* 🆕 库生态系统: [改善 Kotlin 分发的用户体验: 添加代码覆盖率和二进制兼容性验证](https://youtrack.jetbrains.com/issue/KT-71297)

### 移除的项目

我们已**移除**以下项目：

* ❌ 跨平台: [Swift 导出: 设计并实现对 Kotlin 类和接口的支持](https://youtrack.jetbrains.com/issue/KT-64572)
* ❌ 工具: [支持 Kotlin 跨平台用户的 SwiftPM](https://youtrack.jetbrains.com/issue/KT-64571)
* ❌ 库生态系统: [将 Dokka 发布为稳定版](https://youtrack.jetbrains.com/issue/KT-48998)

> 有些项目从路线图中移除，但并未完全取消。在某些情况下，我们已将以前的路线图项目与当前的项目合并。
>
{style="note"}

### 进行中的项目

所有先前确定的路线图项目都在进行中。您可以查看它们的
[YouTrack tickets](https://youtrack.jetbrains.com/issues?q=project:%20KT,%20KTIJ%20tag:%20%7BRoadmap%20Item%7D%20%23Unresolved%20) 获取更新。