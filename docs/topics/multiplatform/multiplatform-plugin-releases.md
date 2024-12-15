[//]: # (title: Kotlin 跨平台插件发布)

确保你已安装最新版本的 [Kotlin 跨平台插件](https://plugins.jetbrains.com/plugin/14936-kotlin-multiplatform-mobile) 
以便继续在 Android Studio 中处理 Kotlin 跨平台项目。

## 更新到新版本 {id=update-to-the-new-release}

Android Studio 会在新的 Kotlin 跨平台插件发布时建议你进行更新。如果你接受建议，插件将自动更新到最新版本。
你需要重启 Android Studio 以完成插件安装。

你可以在 **设置/首选项** | **插件**（**Settings/Preferences** | **Plugins**） 中检查插件版本并手动更新。

你需要一个兼容的 Kotlin 版本才能使插件正常工作。兼容版本可以在 [版本详情](#release-details) 中找到。
你可以在 **设置/首选项** | **插件**（**Settings/Preferences** | **Plugins**） 或
**工具** | **Kotlin** | **配置 Kotlin 插件更新**（**Tools** | **Kotlin** | **Configure Kotlin Plugin Updates**） 中检查并更新你的 Kotlin 版本。

>如果没有安装兼容版本的 Kotlin，Kotlin 跨平台插件将被禁用。你需要更新你的 Kotlin
> 版本，然后在 **设置/首选项** | **插件**（**Settings/Preferences** | **Plugins**） 中启用该插件。
>
{style="note"}

## 版本详情 {id=release-details}

下表列出了最新的 Kotlin 跨平台插件版本的详细信息：

<table> 
<tr>  
<th>  
版本信息  
</th>  
<th>  
版本亮点  
</th>  
<th>  
兼容的 Kotlin 版本  
</th>  
</tr>  
<tr>  
<td>  

**0.8.4**

Released: 06 December, 2024

</td>
<td>

* Support for Kotlin’s [K2 mode](k2-compiler-migration-guide.md#support-in-ides) for improved stability and code analysis.

</td>
<td>

* [Any of Kotlin plugin versions](releases.md#release-details)

</td>
</tr>
<tr>
<td>

**0.8.3**

发布日期：2024年7月23日

</td>  
<td>  

* 修复了 Xcode 兼容性问题。

</td>  
<td>  

* [任意 Kotlin 插件版本](releases.md#release-details)

</td>  
</tr>  
<tr>  
<td>  

**0.8.2**

发布日期：2024年5月16日

</td>  
<td>  

* 支持 Android Studio Jellyfish 和新的 Canary 版本 Koala。
* 在共享模块中添加了 `sourceCompatibility` 和 `targetCompatibility` 的声明。

</td>  
<td>  

* [任意 Kotlin 插件版本](releases.md#release-details)

</td>  
</tr>  
<tr>  
<td>  

**0.8.1**

发布日期：2023年11月9日

</td>  
<td>  

* 将 Kotlin 更新至 1.9.20。
* 将 Jetpack Compose 更新至 1.5.4。
* 默认启用了 Gradle 构建和配置缓存。
* 为新版本的 Kotlin 重构了构建配置。
* iOS 框架现在默认是静态的。
* 修复了在 Xcode 15 的 iOS 设备上运行的问题。

</td>  
<td>  

* [任意 Kotlin 插件版本](releases.md#release-details)

</td>  
</tr>  
<tr>  
<td>  

**0.8.0**

发布日期：2023年10月5日

</td>  
<td>  

* [KT-60169](https://youtrack.jetbrains.com/issue/KT-60169) 迁移到 Gradle 版本目录。
* [KT-59269](https://youtrack.jetbrains.com/issue/KT-59269) 将 `android` 重命名为 `androidTarget`。
* [KT-59269](https://youtrack.jetbrains.com/issue/KT-59269) 更新了 Kotlin 和依赖项版本。
* [KTIJ-26773](https://youtrack.jetbrains.com/issue/KTIJ-26773) 重构为使用 `-destination` 参数替代 `-sdk` 和 `-arch`。
* [KTIJ-25839](https://youtrack.jetbrains.com/issue/KTIJ-25839) 重构了生成的文件名。
* [KTIJ-27058](https://youtrack.jetbrains.com/issue/KTIJ-27058) 添加了 JVM 目标配置。
* [KTIJ-27160](https://youtrack.jetbrains.com/issue/KTIJ-27160) 支持 Xcode 15.0。
* [KTIJ-27158](https://youtrack.jetbrains.com/issue/KTIJ-27158) 将新模块向导移至实验状态。

</td>  
<td>  

* [任意 Kotlin 插件版本](releases.md#release-details)

</td>  
</tr>  
<tr>  
<td>  

**0.6.0**

发布日期：2023年5月24日

</td>  
<td>  

* 支持新的 Canary Android Studio Hedgehog。
* 更新了跨平台项目中的 Kotlin、Gradle 和库版本。
* 在跨平台项目中应用新的 [`targetHierarchy.default()`](whatsnew1820.md#new-approach-to-source-set-hierarchy)。
* 在跨平台项目中为平台特定文件应用源集名称后缀。

</td>  
<td>  

* [任意 Kotlin 插件版本](releases.md#release-details)

</td>  
</tr>  
<tr>  
<td>  

**0.5.3**

发布日期：2023年4月12日

</td>  
<td>  

* 更新了 Kotlin 和 Compose 版本。
* 修复了 Xcode 项目方案解析问题。
* 添加了方案产品类型检查。
* 如果存在，`iosApp` 方案现在默认被选中。

</td>  
<td>  

* [任意 Kotlin 插件版本](releases.md#release-details)

</td>  
</tr>  
<tr>  
<td>  

**0.5.2**

发布日期：2023年1月30日

</td>  
<td>  

* [修复了 Kotlin/Native 调试器的问题（慢 Spotlight 索引）](https://youtrack.jetbrains.com/issue/KT-55988)。
* [修复了多模块项目中的 Kotlin/Native 调试器](https://youtrack.jetbrains.com/issue/KT-24450)。
* [为 Android Studio Giraffe 2022.3.1 Canary 添加了新构建](https://youtrack.jetbrains.com/issue/KT-55274)。
* [为 iOS 应用构建添加了配置标志](https://youtrack.jetbrains.com/issue/KT-55204)。
* [为生成的 iOS 项目中的 **框架搜索路径** 选项添加了继承路径](https://youtrack.jetbrains.com/issue/KT-55402)。

</td>  
<td>  

* [任意 Kotlin 插件版本](releases.md#release-details)

</td>  
</tr>  
<tr>  
<td>  

**0.5.1**

发布日期：2022年11月30日

</td>  
<td>  

* [修复了新项目生成：删除多余的 "app" 目录](https://youtrack.jetbrains.com/issue/KTIJ-23790)。

</td>  
<td>  

* [Kotlin 1.7.0—*](releases.md#release-details)

</td>  
</tr>  
<tr>  
<td>  

**0.5.0**

发布日期：2022年11月22日

</td>  
<td>  

* [更改了 iOS 框架分发的默认选项：现在为 **常规框架**](https://youtrack.jetbrains.com/issue/KT-54086)。
* [将 `MyApplicationTheme` 移至生成的 Android 项目中的单独文件](https://youtrack.jetbrains.com/issue/KT-53991)。
* [更新了生成的 Android 项目](https://youtrack.jetbrains.com/issue/KT-54658)。
* [修复了新项目目录意外被清除的问题](https://youtrack.jetbrains.com/issue/KTIJ-23707)。

</td>  
<td>  

* [Kotlin 1.7.0—*](releases.md#release-details)

</td>  
</tr>  
<tr>  
<td>  

**0.3.4**

发布日期：2022年9月12日

</td>  
<td>  

* [将 Android 应用迁移到 Jetpack Compose](https://youtrack.jetbrains.com/issue/KT-53162)。
* [移除了过时的 HMPP 标志](https://youtrack.jetbrains.com/issue/KT-52248)。
* [从 Android 清单中移除了包名](https://youtrack.jetbrains.com/issue/KTIJ-22633)。
* [更新了 Xcode 项目的 `.gitignore`](https://youtrack.jetbrains.com/issue/KT-53703)。
* [更新了向导项目以更好地展示预期/实际](https://youtrack.jetbrains.com/issue/KT-53928)。
* [更新了与 Android Studio Canary 版本的兼容性](https://youtrack.jetbrains.com/issue/KTIJ-22063)。
* [将 Android 应用的最低 Android SDK 更新至 21](https://youtrack.jetbrains.com/issue/KTIJ-22505)。
* [修复了安装 Xcode 后首次启动的问题](https://youtrack.jetbrains.com/issue/KTIJ-22645)。
* [修复了 M1 上 Apple 运行配置的问题](https://youtrack.jetbrains.com/issue/KTIJ-21781)。
* [修复了 Windows 操作系统上 `local.properties` 的问题](https://youtrack.jetbrains.com/issue/KTIJ-22037)。
* [修复了在 Android Studio Canary 版本上 Kotlin/Native 调试器的问题](https://youtrack.jetbrains.com/issue/KT-53976)。

</td>  
<td>  

* [Kotlin 1.7.0—1.7.*](releases.md#release-details)

</td>  
</tr>  
<tr>  
<td>  

**0.3.3**

发布日期：2022年6月9日

</td>  
<td>  

* 更新了对 Kotlin IDE 插件 1.7.0 的依赖。

</td>  
<td>  

* [Kotlin 1.7.0—1.7.*](releases.md#release-details)

</td>  
</tr>  
<tr>  
<td>  

**0.3.2**

发布日期：2022年4月4日

</td>  
<td>  

* 修复了 Android Studio 2021.2 和 2021.3 上 iOS 应用调试的性能问题。

</td>  
<td>  

* [Kotlin 1.5.0—1.6.*](releases.md#release-details)

</td>  
</tr>  
<tr>  
<td>  

**0.3.1**

发布日期：2022年2月15日

</td>  
<td>  

* [在 Kotlin Multiplatform Mobile 向导中启用了 M1 iOS 模拟器](https://youtrack.jetbrains.com/issue/KT-51105)。
* 改善了 XcProjects 索引的性能：[KT-49777](https://youtrack.jetbrains.com/issue/KT-49777)，[KT-50779](https://youtrack.jetbrains.com/issue/KT-50779)。
* 清理构建脚本：使用 `kotlin("test")` 代替 `kotlin("test-common")` 和 `kotlin("test-annotations-common")`。
* 增加了与 [Kotlin 插件版本](https://youtrack.jetbrains.com/issue/KTIJ-20167) 的兼容范围。
* [修复了 Windows 主机上的 JVM 调试问题](https://youtrack.jetbrains.com/issue/KT-50699)。
* [修复了禁用插件后版本无效的问题](https://youtrack.jetbrains.com/issue/KT-50966)。

</td>  
<td>  

* [Kotlin 1.5.0—1.6.*](releases.md#release-details)

</td>  
</tr>  
<tr>  
<td>  

**0.3.0**

发布日期：2021年11月16日

</td>  
<td>  

* [新建 Kotlin Multiplatform 库向导](https://youtrack.jetbrains.com/issue/KTIJ-19367)。
* 支持新类型的 Kotlin Multiplatform 库分发：[XCFramework](multiplatform-build-native-binaries.md#build-xcframeworks)。
* 为新的跨平台移动项目启用了 [层次项目结构](multiplatform-hierarchy.md#manual-configuration)。
* 支持 [显式声明 iOS 目标](https://youtrack.jetbrains.com/issue/KT-46861)。
* [在非 Mac 机器上启用了 Kotlin Multiplatform Mobile 插件向导](https://youtrack.jetbrains.com/issue/KT-48614)。
* [支持 Kotlin Multiplatform 模块向导中的子文件夹](https://youtrack.jetbrains.com/issue/KT-47923)。
* [支持 Xcode 的 `Assets.xcassets` 文件](https://youtrack.jetbrains.com/issue/KT-49571)。
* [修复了插件类加载器异常](https://youtrack.jetbrains.com/issue/KT-48103)。
* 更新了 CocoaPods Gradle 插件模板。
* 改进了 Kotlin/Native 调试器的类型评估。
* 修复了与 Xcode 13 一起启动 iOS 设备的问题。

</td>  
<td>  

* [Kotlin 1.6.0](releases.md#release-details)

</td>  
</tr>  
<tr>  
<td>  

**0.2.7**

发布日期：2021年8月2日

</td>  
<td>  

* [为 AppleRunConfiguration 添加了 Xcode 配置选项](https://youtrack.jetbrains.com/issue/KTIJ-19054)。
* [添加对 Apple M1 模拟器的支持](https://youtrack.jetbrains.com/issue/KT-47618)。
* [在项目向导中添加了有关 Xcode 集成选项的信息](https://youtrack.jetbrains.com/issue/KT-47466)。
* [生成带有 CocoaPods 的项目后，如果未安装 CocoaPods gem，添加错误通知](https://youtrack.jetbrains.com/issue/KT-47329)。
* [在使用 Kotlin 1.5.30 生成的共享模块中添加对 Apple M1 模拟器目标的支持](https://youtrack.jetbrains.com/issue/KT-47631)。
* [清理了使用 Kotlin 1.5.20 生成的 Xcode 项目](https://youtrack.jetbrains.com/issue/KT-47465)。
* 修复了在真实 iOS 设备上启动 Xcode Release 配置的问题。
* 修复了与 Xcode 12.5 一起启动模拟器的问题。

</td>  
<td>  

* [Kotlin 1.5.10](releases.md#release-details)

</td>  
</tr>  
<tr>  
<td>  

**0.2.6**

发布日期：2021年6月10日

</td>  
<td>  

* 与 Android Studio Bumblebee Canary 1 的兼容性。
* 支持 [Kotlin 1.5.20](whatsnew1520.md)：在项目向导中使用新的框架打包任务进行 Kotlin/Native。

</td>  
<td>  

* [Kotlin 1.5.10](releases.md#release-details)

</td>  
</tr>  
<tr>  
<td>  

**0.2.5**

发布日期：2021年5月25日

</td>  
<td>  

* [修复与 Android Studio Arctic Fox 2020.3.1 Beta 1 及更高版本的兼容性](https://youtrack.jetbrains.com/issue/KT-46834)。

</td>  
<td>  

* [Kotlin 1.5.10](releases.md#release-details)

</td>  
</tr>  
<tr>  
<td>  

**0.2.4**

发布日期：2021年5月5日

</td>  
<td>  

* 使用此版本的插件与 Android Studio 4.2 或 Android Studio 2020.3.1 Canary 8 或更高版本。
* 兼容性 [Kotlin 1.5.0](whatsnew15.md)。
* [能够在 Kotlin Multiplatform 模块中使用 CocoaPods 依赖管理器进行 iOS 集成](https://youtrack.jetbrains.com/issue/KT-45946)。

</td>  
<td>  

* [Kotlin 1.5.0](releases.md#release-details)

</td>  
</tr>  
<tr>
<td>

**0.2.3**

发布日期: 2021年4月5日

</td>
<td>

* [项目向导：模块命名改进](https://youtrack.jetbrains.com/issues?q=issue%20id:%20KT-43449,%20KT-44060,%20KT-41520,%20KT-45282)。
* [在项目向导中能够使用 CocoaPods 依赖管理器进行 iOS 集成](https://youtrack.jetbrains.com/issue/KT-45478)。
* [新项目中 gradle.properties 的可读性更好](https://youtrack.jetbrains.com/issue/KT-42908)。
* [如果未选中“为共享模块添加示例测试”，则不再生成示例测试](https://youtrack.jetbrains.com/issue/KT-43441)。
* [修复和其他改进](https://youtrack.jetbrains.com/issues?q=Subsystems:%20%7BKMM%20Plugin%7D%20Type:%20Feature,%20Bug%20State:%20-Obsolete,%20-%7BAs%20designed%7D,%20-Answered,%20-Incomplete%20resolved%20date:%202021-03-10%20..%202021-03-25)。

</td>
<td>

* [Kotlin 1.4.30](releases.md#release-details)

</td>
</tr>
<tr>
<td>

**0.2.2**

发布日期: 2021年3月3日

</td>
<td>

* [能够在 Xcode 中打开与 Xcode 相关的文件](https://youtrack.jetbrains.com/issue/KT-44970)。
* [能够在 iOS 运行配置中设置 Xcode 项目文件的位置](https://youtrack.jetbrains.com/issue/KT-44968)。
* [支持 Android Studio 2020.3.1 Canary 8](https://youtrack.jetbrains.com/issue/KT-45162)。
* [修复和其他改进](https://youtrack.jetbrains.com/issues?q=tag:%20KMM-0.2.2)。

</td>
<td>

* [Kotlin 1.4.30](releases.md#release-details)

</td>
</tr>
<tr>
<td>

**0.2.1**

发布日期: 2021年2月15日

</td>
<td>

与 Android Studio 4.2 一起使用此版本的插件。
* 基础设施改进。
* [修复和其他改进](https://youtrack.jetbrains.com/issues?q=tag:%20KMM-0.2.1%)。

</td>
<td>

* [Kotlin 1.4.30](releases.md#release-details)

</td>
</tr>
<tr>
<td>

**0.2.0**

发布日期: 2020年11月23日

</td>
<td>

* [支持 iPad 设备](https://youtrack.jetbrains.com/issue/KT-41932)。
* [支持在 Xcode 中配置的自定义方案名称](https://youtrack.jetbrains.com/issue/KT-41677)。
* [能够为 iOS 运行配置添加自定义构建步骤](https://youtrack.jetbrains.com/issue/KT-41678)。
* [能够调试自定义 Kotlin/Native 二进制文件](https://youtrack.jetbrains.com/issue/KT-40954)。
* [简化了 Kotlin Multiplatform Mobile Wizards 生成的代码](https://youtrack.jetbrains.com/issue/KT-41712)。
* [移除了对 Kotlin Android Extensions 插件的支持](https://youtrack.jetbrains.com/issue/KT-42121)，该插件在 Kotlin 1.4.20 中已被弃用。
* [修复在与主机断开连接后保存物理设备配置的问题](https://youtrack.jetbrains.com/issue/KT-42390)。
* 其他修复和改进。

</td>
<td>

* [Kotlin 1.4.20](releases.md#release-details)

</td>
</tr>
<tr>
<td>

**0.1.3**

发布日期: 2020年10月2日

</td>
<td>

* 添加了对 iOS 14 和 Xcode 12 的兼容性。
* 修复了由 Kotlin Multiplatform Mobile Wizard 创建的平台测试中的命名问题。

</td>
<td>

* [Kotlin 1.4.10](releases.md#release-details)
* [Kotlin 1.4.20](releases.md#release-details)

</td>
</tr>
<tr>
<td>

**0.1.2**

发布日期: 2020年9月29日

</td>
<td>

* 修复了与 [Kotlin 1.4.20-M1](eap.md#build-details) 的兼容性。
* 默认启用了对 JetBrains 的错误报告。

</td>
<td>

* [Kotlin 1.4.10](releases.md#release-details)
* [Kotlin 1.4.20](releases.md#release-details)

</td>
</tr>

<tr>
<td>

**0.1.1**

发布日期: 2020年9月10日

</td>
<td>

* 修复了与 Android Studio Canary 8 及更高版本的兼容性。

</td>
<td>

* [Kotlin 1.4.10](releases.md#release-details)
* [Kotlin 1.4.20](releases.md#release-details)

</td>
</tr>
<tr>
<td>

**0.1.0**

发布日期: 2020年8月31日

</td>
<td>

* Kotlin Multiplatform Mobile 插件的第一个版本。了解更多信息，请参见 [博客文章](https://blog.jetbrains.com/kotlin/2020/08/kotlin-multiplatform-mobile-goes-alpha/)。

</td>
<td>

* [Kotlin 1.4.0](releases.md#release-details)
* [Kotlin 1.4.10](releases.md#release-details)

</td>
</tr>

</table>
