# 开始使用 Kotlin

Kotlin 是一种现代、已经成熟的编程语言，旨在让开发人员更加愉快地开发。
它简洁、安全，与 Java 和其他语言具有互操作性，为高效编程提供了许多在多个平台之间重用代码的方式。

要开始学习，为何不参加我们的 Kotlin 之旅呢？这个旅程涵盖了 Kotlin 编程语言的基础知识。

[ ![](start-kotlin-tour.svg){style=inline width=700 height=80} ](kotlin-tour-welcome.md)

## 安装 Kotlin {id="安装Kotlin"}

Kotlin 包含在每个 [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) 和 [Android Studio](https://developer.android.com/studio) 发布版中。
请下载并安装其中一个 IDE，开始使用 Kotlin。

## 使用 Kotlin 创建强大的应用程序 {id="使用Kotlin创建强大的应用程序"}
 
<tabs>

<tab id="backend" title="后端应用程序">

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

5. **关注 Kotlin** 在 ![Twitter](twitter.svg){width=25}{type="joined"} [Twitter](https://twitter.com/kotlin), ![Reddit](reddit.svg){width=25}{type="joined"} [Reddit](https://www.reddit.com/r/Kotlin/), 和 ![YouTube](youtube.svg){width=25}{type="joined"} [Youtube](https://www.youtube.com/channel/UCP7uiEZIqci43m22KDl0sNw)，并且不要错过任何重要的生态系统更新。

如果您遇到任何困难或问题，请向我们的 [问题跟踪器](https://youtrack.jetbrains.com/issues/KT) 报告问题。

</tab>

<tab id="cross-platform-mobile" title="跨平台移动应用程序">

在这里，您将学习如何使用 [Kotlin 多平台](https://kotlinlang.org/lp/multiplatform/) 开发和改进您的跨平台移动应用程序。

1. **[设置您的环境以进行跨平台开发](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-setup.html).**

2. **创建您的第一个 iOS 和 Android 应用程序：**

   * 要从头开始，请使用项目向导 [创建一个基本的跨平台移动应用程序](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-create-first-app.html)。
   * 如果您已有现有的 Android 应用程序，并希望使其跨平台，请完成 [使您的 Android 应用程序在 iOS 上运行](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-integrate-in-existing-app.html) 教程。
   * 如果您喜欢真实的例子，请克隆并玩弄现有项目，例如 [使用 Ktor 和 SQLdelight 创建多平台应用程序](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-ktor-sqldelight.html) 教程中的网络和数据存储项目或任何 [示例项目](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-samples.html)。

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

6. **关注 Kotlin** 在 ![Twitter](twitter.svg){width=25}{type="joined"} [Twitter](https://twitter.com/kotlin), ![Reddit](reddit.svg){width=25}{type="joined"} [Reddit](https://www.reddit.com/r/Kotlin/), 和 ![YouTube](youtube.svg){width=25}{type="joined"} [Youtube](https://www.youtube.com/channel/UCP7uiEZIqci43m22KDl0sNw)，并且不要错过任何重要的生态系统更新。

如果您遇到任何困难或问题，请向我们的 [问题跟踪器](https://youtrack.jetbrains.com/issues/KT) 报告问题。

</tab>

<tab id="android" title="安卓应用程序">

* 如果您想开始使用 Kotlin 进行 Android 开发，请阅读 [Google 的关于在 Android 上入门 Kotlin 的推荐](https://developer.android.com/kotlin/get-started)。

* 如果您是 Android 新手，并希望学习使用 Kotlin 创建应用程序，请查看 [这个 Udacity 课程](https://www.udacity.com/course/developing-android-apps-with-kotlin--ud9012)。

关注 Kotlin 在 ![Twitter](twitter.svg){width=25}{type="joined"} [Twitter](https://twitter.com/kotlin)、![Reddit](reddit.svg){width=25}{type="joined"} [Reddit](https://www.reddit.com/r/Kotlin/) 和 ![YouTube](youtube.svg){width=25}{type="joined"} [Youtube](https://www.youtube.com/channel/UCP7uiEZIqci43m22KDl0sNw)，不要错过任何重要的生态系统更新。

</tab>

<tab id="multiplatform-library" title="多平台库">

多平台编程支持是 Kotlin 的关键优势之一。它减少了在不同平台编写和维护相同代码所需的时间，同时保留了本地编程的灵活性和优势。

在这里，您将学习如何开发和发布一个多平台库：

1. **发布多平台库：**

   * 请参阅[发布多平台库](multiplatform-publish-lib.md) 了解更多信息。

2. **在您的应用程序中使用库：**

   * [Ktor](https://ktor.io/docs/)
   * [Serialization（序列化）](serialization.md)
   * [Coroutines（协程）](coroutines-overview.md)
   * [DateTime](https://github.com/Kotlin/kotlinx-datetime#readme)

   > 了解更多关于[添加库依赖项](multiplatform-add-dependencies.md)的信息。
   > 您还可以在[社区驱动的列表](https://libs.kmp.icerock.dev/)中找到多平台库。
   >
   {style="tip"}

3. **了解有关 Kotlin 多平台编程的更多信息：**

   * [Kotlin 多平台简介](multiplatform-get-started.md)。
   * [Kotlin 多平台支持的平台](multiplatform-dsl-reference.md#targets)。
   * [Kotlin 多平台编程的优势](multiplatform.md)。

4. **加入 Kotlin 多平台社区:**

   * ![Slack](slack.svg){width=25}{type="joined"} Slack：[获取邀请](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up)，加入 [#getting-started](https://kotlinlang.slack.com/archives/C0B8MA7FA) 和 [#multiplatform](https://kotlinlang.slack.com/archives/C3PQML5NU) 频道。
   * ![StackOverflow](stackoverflow.svg){width=25}{type="joined"} StackOverflow：订阅 ["kotlin-multiplatform" 标签](https://stackoverflow.com/questions/tagged/kotlin-multiplatform)。

5. **关注 Kotlin** 在 ![Twitter](twitter.svg){width=25}{type="joined"} [Twitter](https://twitter.com/kotlin)、![Reddit](reddit.svg){width=25}{type="joined"} [Reddit](https://www.reddit.com/r/Kotlin/) 和 ![YouTube](youtube.svg){width=25}{type="joined"} [Youtube](https://www.youtube.com/channel/UCP7uiEZIqci43m22KDl0sNw)，不要错过任何重要的生态系统更新。

如果您遇到任何困难或问题，请向我们的 [问题跟踪器](https://youtrack.jetbrains.com/issues/KT) 报告问题。

</tab>

</tabs>

## 是否有任何遗漏的内容吗？

如果此页面上有任何遗漏或看起来令人困惑的地方，请 [分享您的反馈](https://surveys.hotjar.com/d82e82b0-00d9-44a7-b793-0611bf6189df)。
