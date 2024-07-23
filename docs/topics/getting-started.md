# 开始使用 Kotlin

Kotlin 是一种现代、已经成熟的编程语言，旨在让开发人员更加愉快地开发。
它简洁、安全，与 Java 和其他语言具有互操作性，为高效编程提供了许多在多个平台之间重用代码的方式。

要开始学习，为何不参加我们的 Kotlin 之旅呢？这个旅程涵盖了 Kotlin 编程语言的基础知识。

<a href="kotlin-tour-welcome.md"><img src="start-kotlin-tour.svg" width="700" height="80" style="block" alt=""/></a>

## 安装 Kotlin {id="安装Kotlin"}

Kotlin 包含在每个 [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) 和 [Android Studio](https://developer.android.com/studio) 发布版中。
请下载并安装其中一个 IDE，开始使用 Kotlin。

## 选择您的 Kotlin 用例 {id=choose-your-kotlin-use-case}
 
<tabs>

<tab id="backend" title="后端">

以下是您可以在开发 Kotlin 服务器端应用程序时迈出的第一步。

1. **创建您的第一个后端应用程序：**

   * 要从头开始，请使用 IntelliJ IDEA 项目向导 [创建一个基本的 JVM 应用程序](jvm-get-started.md)。
   * 如果你更喜欢更强大的例子，请选择以下框架之一并创建一个项目：

   <table width="100%" >
   <tr>
      <th>Spring</th>
      <th>Ktor</th>
   </tr>
   <tr>
   <td width="50%">
     一组成熟的框架家族，拥有一个已建立的生态系统，被全球数百万开发者使用。
   <br/>
   <list>
      <li><a href="jvm-get-started-spring-boot.md">使用 Spring Boot 创建 RESTful Web 服务</a>.</li>
      <li><a href="https://spring.io/guides/tutorials/spring-boot-kotlin/">使用 Spring Boot 和 Kotlin 构建 Web 应用程序</a>.</li>
      <li><a href="https://spring.io/guides/tutorials/spring-webflux-kotlin-rsocket/">将 Spring Boot 与 Kotlin 和 RSocket 结合使用</a>.</li>
   </list>
   </td>
   <td width="50%">
      一个轻量级框架，适合那些重视架构决策自由度的人。
   <list>
      <li><a href="https://ktor.io/docs/creating-http-apis.html">使用 Ktor 创建 HTTP APIs</a>.</li>
      <li><a href="https://ktor.io/docs/creating-web-socket-chat.html">使用 Ktor 创建一个 WebSocket 聊天应用</a>.</li>
      <li><a href="https://ktor.io/docs/creating-interactive-website.html">使用 Ktor 创建一个交互式网站</a>.</li>
      <li><a href="https://ktor.io/docs/heroku.html">发布服务器端 Kotlin 应用程序：在 Heroku 上使用 Ktor</a>.</li>
   </list>
   
   </td>
   </tr>
   </table>

2. **在你的应用程序中使用 Kotlin 和第三方库**。了解有关[向项目添加库和工具依赖项](gradle-configure-project.md#configure-dependencies)的更多信息。
   * [Kotlin 标准库](https://kotlinlang.org/api/latest/jvm/stdlib/)提供了许多有用的功能，如[集合](collections-overview.md)或[协程](coroutines-guide.md)。
   * 查看以下关于[Kotlin 的第三方框架、库和工具](https://blog.jetbrains.com/kotlin/2020/11/server-side-development-with-kotlin-frameworks-and-libraries/)的信息。

3. **了解有关服务器端 Kotlin 的更多信息：**
   * [如何编写你的第一个单元测试](jvm-test-using-junit.md).
   * [如何在应用程序中混合 Kotlin 和 Java 代码](mixing-java-kotlin-intellij.md).

4. **加入 Kotlin 服务器端社区：**
   * ![Slack](slack.svg){width=25}{type="joined"} Slack：[获取邀请](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up)，加入 [#getting-started](https://kotlinlang.slack.com/archives/C0B8MA7FA)、[#server](https://kotlinlang.slack.com/archives/C0B8RC352)、[#spring](https://kotlinlang.slack.com/archives/C0B8ZTWE4) 或 [#ktor](https://kotlinlang.slack.com/archives/C0A974TJ9) 频道。
   * ![StackOverflow](stackoverflow.svg){width=25}{type="joined"} StackOverflow：订阅 ["kotlin"](https://stackoverflow.com/questions/tagged/kotlin)、["spring-kotlin"](https://stackoverflow.com/questions/tagged/spring-kotlin) 或 ["ktor"](https://stackoverflow.com/questions/tagged/ktor) 标签。

5. **关注 Kotlin** 在 ![Twitter](twitter.svg){width=18}{type="joined"} [Twitter](https://twitter.com/kotlin)、![Reddit](reddit.svg){width=25}{type="joined"} [Reddit](https://www.reddit.com/r/Kotlin/) 和 ![YouTube](youtube.svg){width=25}{type="joined"} [YouTube](https://www.youtube.com/channel/UCP7uiEZIqci43m22KDl0sNw)，不要错过任何重要的生态系统更新。

如果您遇到任何困难或问题，请向我们的 [问题跟踪器](https://youtrack.jetbrains.com/issues/KT) 报告问题。

</tab>

<tab id="cross-platform-mobile" title="跨平台">

在这里，你将学习如何使用 [Kotlin Multiplatform](https://kotlinlang.org/lp/multiplatform/) 开发和改进你的跨平台应用程序。

1. **[设置您的环境以进行跨平台开发](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-setup.html).**

2. **创建您的第一个 iOS 和 Android 应用程序：**

  * 要从头开始，[使用项目向导创建一个基本的跨平台应用程序](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-create-first-app.html)。
  * 如果你有一个现有的 Android 应用程序并希望将其变成跨平台应用程序，请完成 [使你的 Android 应用程序在 iOS 上运行](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-integrate-in-existing-app.html) 教程。
  * 如果你更喜欢实际的例子，可以克隆并玩转一个现有项目，例如 [使用 Ktor 和 SQLdelight 创建跨平台应用](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-ktor-sqldelight.html) 教程中的网络和数据存储项目或任何 [示例项目](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-samples.html)。

3. **使用广泛的多平台库**，只需在共享模块中实现所需的业务逻辑一次。了解更多关于[添加依赖项](multiplatform-add-dependencies.md)的信息。

   | 库             | 详情                                                                                                                                                         |
   |---------------|------------------------------------------------------------------------------------------------------------------------------------------------------------| 
   | Ktor          | [文档](https://ktor.io/docs/client.html)                                                                                                                     | 
   | Serialization | [文档](serialization.md) 和 [示例](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-ktor-sqldelight.html#create-an-application-data-model) |
   | Coroutines    | [文档](coroutines-guide.md) 和 [示例](coroutines-and-channels.md)                                                                                               |
   | DateTime      | [文档](https://github.com/Kotlin/kotlinx-datetime#readme)                                                                                                    |
   | SQLDelight    | 第三方库。 [文档](https://cashapp.github.io/sqldelight/)                                                                                                          |
   
   > 您还可以在[社区驱动的列表](https://libs.kmp.icerock.dev/)中找到多平台库。
   > 
   {style="tip"}

4. **了解有关 Kotlin 多平台的更多信息：**
   * 了解更多关于 [Kotlin Multiplatform](multiplatform-get-started.md)。
   * 浏览 [示例项目](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-samples.html)。
   * [发布一个多平台库](multiplatform-publish-lib.md)。
   * 了解 Kotlin Multiplatform 在 [Netflix](https://netflixtechblog.com/netflix-android-and-ios-studio-apps-kotlin-multiplatform-d6d4d8d25d23)、[VMware](https://kotlinlang.org/lp/multiplatform/case-studies/vmware/)、[Yandex](https://kotlinlang.org/lp/multiplatform/case-studies/yandex/) 和 [许多其他公司](https://kotlinlang.org/lp/multiplatform/case-studies/) 中的使用情况。

5. **加入 Kotlin 多平台社区：**

   * ![Slack](slack.svg){width=25}{type="joined"} Slack：[获取邀请](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up)，加入 [#getting-started](https://kotlinlang.slack.com/archives/C0B8MA7FA) 和 [#multiplatform](https://kotlinlang.slack.com/archives/C3PQML5NU) 频道。
   * ![StackOverflow](stackoverflow.svg){width=25}{type="joined"} StackOverflow：订阅 ["kotlin-multiplatform" 标签](https://stackoverflow.com/questions/tagged/kotlin-multiplatform)。

6. **关注 Kotlin** 在 ![Twitter](twitter.svg){width=18}{type="joined"} [Twitter](https://twitter.com/kotlin)、![Reddit](reddit.svg){width=25}{type="joined"} [Reddit](https://www.reddit.com/r/Kotlin/) 和 ![YouTube](youtube.svg){width=25}{type="joined"} [YouTube](https://www.youtube.com/channel/UCP7uiEZIqci43m22KDl0sNw)，不要错过任何重要的生态系统更新。

如果您遇到任何困难或问题，请向我们的 [问题跟踪器](https://youtrack.jetbrains.com/issues/KT) 报告问题。

</tab>

<tab id="android" title="Android">

要开始在 Android 开发中使用 Kotlin，请阅读 [Google 关于在 Android 上开始使用 Kotlin 的推荐](https://developer.android.com/kotlin/get-started)。

关注 Kotlin 在 ![Twitter](twitter.svg){width=18}{type="joined"} [Twitter](https://twitter.com/kotlin)、![Reddit](reddit.svg){width=25}{type="joined"} [Reddit](https://www.reddit.com/r/Kotlin/) 和 ![YouTube](youtube.svg){width=25}{type="joined"} [YouTube](https://www.youtube.com/channel/UCP7uiEZIqci43m22KDl0sNw)，不要错过任何重要的生态系统更新。

</tab>

<tab id="data-analysis" title="数据分析">

从构建数据管道到将机器学习模型投入生产，Kotlin 是处理数据并充分利用数据的绝佳选择。

1. **在 IDE 中无缝创建和编辑笔记本：**

  * [开始使用 Kotlin Notebook](get-started-with-kotlin-notebooks.md)。

2. **探索和实验您的数据：**

  * [DataFrame](https://kotlin.github.io/dataframe/overview.html) – 一个用于数据分析和操作的库。
  * [Kandy](https://kotlin.github.io/kandy/welcome.html) – 一个用于数据可视化的绘图工具。

3. **获取 Kotlin 数据分析的最新更新：**

  * ![Slack](slack.svg){width=25}{type="joined"} Slack： [获取邀请](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up) 并加入 [#datascience](https://kotlinlang.slack.com/archives/C4W52CFEZ) 频道。
  * ![Twitter](twitter.svg){width=25}{type="joined"} Twitter：关注 [KotlinForData](http://twitter.com/KotlinForData)。

4. **关注 Kotlin** on ![Twitter](twitter.svg){width=25}{type="joined"} [Twitter](https://twitter.com/kotlin), ![Reddit](reddit.svg){width=25}{type="joined"} [Reddit](https://www.reddit.com/r/Kotlin/), 和 ![YouTube](youtube.svg){width=25}{type="joined"} [YouTube](https://www.youtube.com/channel/UCP7uiEZIqci43m22KDl0sNw)，不要错过任何重要的生态系统更新。

</tab>

</tabs>

## 是否有任何遗漏的内容吗？

如果此页面上有任何遗漏或看起来令人困惑的地方，请 [分享您的反馈](https://surveys.hotjar.com/d82e82b0-00d9-44a7-b793-0611bf6189df)。
