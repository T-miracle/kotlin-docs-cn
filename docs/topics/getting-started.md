# 开始使用 Kotlin

Kotlin 是一种现代、已经成熟的编程语言，旨在让开发人员更加愉快地开发。
它简洁、安全，与 Java 和其他语言具有互操作性，为高效编程提供了许多在多个平台之间重用代码的方式。

为了开始，为什么不来参加我们的 Kotlin 之旅呢？这个之旅涵盖了 Kotlin 编程语言的基础知识，可以完全在您的浏览器中完成。

<a href="kotlin-tour-welcome.md"><img src="start-kotlin-tour.svg" width="700" height="80" style="block" alt=""/></a>

## 安装 Kotlin {id="安装Kotlin"}

Kotlin 包含在每个 [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) 和 [Android Studio](https://developer.android.com/studio) 发布版中。
请下载并安装其中一个 IDE，开始使用 Kotlin。

## 选择您的 Kotlin 用例 {id=choose-your-kotlin-use-case}
 
<tabs>

<tab id="console" title="控制台">

在这里，您将学习如何使用 Kotlin 开发控制台应用程序并创建单元测试。

1. **[使用 IntelliJ IDEA 项目向导创建基本的 JVM 应用程序](jvm-get-started.md)。**

2. **[编写您的第一个单元测试](jvm-test-using-junit.md)。**

3. **加入 Kotlin 社区：**

   * ![Slack](slack.svg){width=25}{type="joined"} Slack: [获取邀请](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up)。
   * ![StackOverflow](stackoverflow.svg){width=25}{type="joined"} StackOverflow: 订阅 ["kotlin"](https://stackoverflow.com/questions/tagged/kotlin) 标签。

4. **在以下平台关注 Kotlin：**

   * ![Twitter](twitter.svg){width=18}{type="joined"} [Twitter](https://twitter.com/kotlin)
   * ![Reddit](reddit.svg){width=25}{type="joined"} [Reddit](https://www.reddit.com/r/Kotlin/)
   * ![YouTube](youtube.svg){width=25}{type="joined"} [YouTube](https://www.youtube.com/channel/UCP7uiEZIqci43m22KDl0sNw)

如果遇到任何困难或问题，请在我们的 [问题追踪器](https://youtrack.jetbrains.com/issues/KT) 中报告问题。

</tab>

<tab id="backend" title="后端">

在这里，您将学习如何使用 Kotlin 开发后端应用程序。

1. **创建您的第一个后端应用程序：**

   * [使用 Spring Boot 创建一个 RESTful web 服务](jvm-get-started-spring-boot.md)。
   * [使用 Ktor 创建 HTTP APIs](https://ktor.io/docs/creating-http-apis.html)。

2. **[学习如何在应用程序中混合使用 Kotlin 和 Java 代码](mixing-java-kotlin-intellij.md)。**

3. **加入 Kotlin 后端社区：**

   * ![Slack](slack.svg){width=25}{type="joined"} Slack: [获取邀请](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up)。
   * ![StackOverflow](stackoverflow.svg){width=25}{type="joined"} StackOverflow: 订阅 ["kotlin"](https://stackoverflow.com/questions/tagged/kotlin) 标签。

4. **在以下平台关注 Kotlin：**

   * ![Twitter](twitter.svg){width=18}{type="joined"} [Twitter](https://twitter.com/kotlin)
   * ![Reddit](reddit.svg){width=25}{type="joined"} [Reddit](https://www.reddit.com/r/Kotlin/)
   * ![YouTube](youtube.svg){width=25}{type="joined"} [YouTube](https://www.youtube.com/channel/UCP7uiEZIqci43m22KDl0sNw)

如果遇到任何困难或问题，请在我们的 [问题追踪器](https://youtrack.jetbrains.com/issues/KT) 中报告问题。

</tab>

<tab id="cross-platform-mobile" title="跨平台">

在这里，你将学习如何使用 [Kotlin Multiplatform](https://kotlinlang.org/lp/multiplatform/) 开发和改进你的跨平台应用程序。

1. **[设置您的环境以进行跨平台开发](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-setup.html).**

2. **创建您的第一个 iOS 和 Android 应用程序：**

  * 要从头开始，[使用项目向导创建一个基本的跨平台应用程序](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-create-first-app.html)。
  * 如果你有一个现有的 Android 应用程序并希望将其变成跨平台应用程序，请完成 [使你的 Android 应用程序在 iOS 上运行](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-integrate-in-existing-app.html) 教程。
  * 如果你更喜欢实际的例子，可以克隆并玩转一个现有项目，例如 [使用 Ktor 和 SQLdelight 创建跨平台应用](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-ktor-sqldelight.html) 教程中的网络和数据存储项目或任何 [示例项目](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-samples.html)。

3. **使用广泛的跨平台库**，只需在共享模块中实现所需的业务逻辑一次。了解更多关于[添加依赖项](multiplatform-add-dependencies.md)的信息。

   | 库             | 详细信息                                                                                                                                                       |
   |---------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
   | Ktor          | [文档](https://ktor.io/docs/client.html)                                                                                                                     |
   | Serialization | [文档](serialization.md) 和 [示例](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-ktor-sqldelight.html#create-an-application-data-model) |
   | Coroutines    | [文档](coroutines-guide.md) 和 [示例](coroutines-and-channels.md)                                                                                               |
   | DateTime      | [文档](https://github.com/Kotlin/kotlinx-datetime#readme)                                                                                                    |
   | SQLDelight    | 第三方库。 [文档](https://cashapp.github.io/sqldelight/)                                                                                                          |

   > 您还可以在 [社区驱动的列表](https://libs.kmp.icerock.dev/) 中找到跨平台库。
   >
   {style="tip"}

4. **了解有关 Kotlin 跨平台的更多信息：**
   * 了解更多关于 [Kotlin Multiplatform](multiplatform-intro.md)。
   * 浏览 [示例项目](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-samples.html)。
   * [发布一个跨平台库](multiplatform-publish-lib.md)。
   * 了解 Kotlin Multiplatform 在 [Netflix](https://netflixtechblog.com/netflix-android-and-ios-studio-apps-kotlin-multiplatform-d6d4d8d25d23)、[VMware](https://kotlinlang.org/lp/multiplatform/case-studies/vmware/)、[Yandex](https://kotlinlang.org/lp/multiplatform/case-studies/yandex/) 和 [许多其他公司](https://kotlinlang.org/lp/multiplatform/case-studies/) 中的使用情况。

5. **加入 Kotlin 跨平台社区：**

   * ![Slack](slack.svg){width=25}{type="joined"} Slack：[获取邀请](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up)，加入 [#getting-started](https://kotlinlang.slack.com/archives/C0B8MA7FA) 和 [#multiplatform](https://kotlinlang.slack.com/archives/C3PQML5NU) 频道。
   * ![StackOverflow](stackoverflow.svg){width=25}{type="joined"} StackOverflow：订阅 ["kotlin-multiplatform" 标签](https://stackoverflow.com/questions/tagged/kotlin-multiplatform)。

6. **在以下平台关注 Kotlin：**

   * ![Twitter](twitter.svg){width=18}{type="joined"} [Twitter](https://twitter.com/kotlin)
   * ![Reddit](reddit.svg){width=25}{type="joined"} [Reddit](https://www.reddit.com/r/Kotlin/)
   * ![YouTube](youtube.svg){width=25}{type="joined"} [YouTube](https://www.youtube.com/channel/UCP7uiEZIqci43m22KDl0sNw)

如果遇到任何困难或问题，请在我们的 [问题追踪器](https://youtrack.jetbrains.com/issues/KT) 中报告问题。

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
  * ![Twitter](twitter.svg){width=18}{type="joined"} Twitter：关注 [KotlinForData](http://twitter.com/KotlinForData)。

4. **在以下平台关注 Kotlin：**
   * ![Twitter](twitter.svg){width=18}{type="joined"} [Twitter](https://twitter.com/kotlin)
   * ![Reddit](reddit.svg){width=25}{type="joined"} [Reddit](https://www.reddit.com/r/Kotlin/)
   * ![YouTube](youtube.svg){width=25}{type="joined"} [YouTube](https://www.youtube.com/channel/UCP7uiEZIqci43m22KDl0sNw)

</tab>

</tabs>

## 是否有任何遗漏的内容吗？

如果此页面上有任何遗漏或看起来令人困惑的地方，请 [分享您的反馈](https://surveys.hotjar.com/d82e82b0-00d9-44a7-b793-0611bf6189df)。
