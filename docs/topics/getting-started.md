# 开始使用 Kotlin

<tldr>
<p>最新的 Kotlin 版本:<b> <a href="%kotlinLatestWhatsnew%">%kotlinVersion%</a></b></p>
</tldr>

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

</tab>

<tab id="backend" title="后端">

在这里，您将学习如何使用 Kotlin 开发后端应用程序。

* **将 Kotlin 引入你的 Java 项目：**

  * [配置 Java 项目以使用 Kotlin](mixing-java-kotlin-intellij.md)
  * [为你的 Java Maven 项目添加 Kotlin 测试](jvm-test-using-junit.md)

* **使用 Kotlin 从零创建后端应用：**

  * [使用 Spring Boot 创建 RESTful Web 服务](jvm-get-started-spring-boot.md)
  * [使用 Ktor 创建 HTTP API](https://ktor.io/docs/creating-http-apis.html)

</tab>

<tab id="cross-platform-mobile" title="跨平台">

在这里，你将学习如何使用 [Kotlin 跨平台](https://kotlinlang.org/docs/multiplatform/get-started.html) 开发跨平台应用。

1. **[设置您的环境以进行跨平台开发](https://kotlinlang.org/docs/multiplatform/quickstart.html).**

2. **创建您的第一个 iOS 和 Android 应用程序：**

   * 从零创建跨平台应用，并且：
     * [在保持原生 UI 的同时共享业务逻辑](https://kotlinlang.org/docs/multiplatform/multiplatform-create-first-app.html)
     * [共享业务逻辑和 UI](https://kotlinlang.org/docs/multiplatform/compose-multiplatform-create-first-app.html)
   * [让你现有的 Android 应用在 iOS 上运行](https://kotlinlang.org/docs/multiplatform/multiplatform-integrate-in-existing-app.html)
   * [使用 Ktor 和 SQLDelight 创建跨平台应用](https://kotlinlang.org/docs/multiplatform/multiplatform-ktor-sqldelight.html)

3. **探索 [示例项目](https://kotlinlang.org/docs/multiplatform/multiplatform-samples.html)**。


</tab>

<tab id="android" title="Android">

要开始在 Android 开发中使用 Kotlin，请阅读 [Google 关于在 Android 上开始使用 Kotlin 的推荐](https://developer.android.com/kotlin/get-started)。

</tab>

<tab id="data-analysis" title="数据分析">

从构建数据管道到将机器学习模型投入生产，Kotlin 是处理数据并充分利用数据的绝佳选择。

1. **在 IDE 中无缝创建和编辑笔记本：**

   * [开始使用 Kotlin Notebook](get-started-with-kotlin-notebooks.md)

2. **探索和实验您的数据：**

   * [DataFrame](https://kotlin.github.io/dataframe/overview.html) – 一个用于数据分析和操作的库。
   * [Kandy](https://kotlin.github.io/kandy/welcome.html) – 一个用于数据可视化的绘图工具。

3. **在 Twitter 上关注 Kotlin 数据分析：** [KotlinForData](http://twitter.com/KotlinForData).

</tab>

</tabs>

## 加入 Kotlin 社区 {id=join-the-kotlin-community}

关注 Kotlin 生态的最新动态，并分享你的经验。

* 加入我们：
  * ![Slack](slack.svg){width=25}{type="joined"} Slack： [获取邀请](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up)。
  * ![StackOverflow](stackoverflow.svg){width=25}{type="joined"} StackOverflow：订阅 ["kotlin"](https://stackoverflow.com/questions/tagged/kotlin) 标签。
* 在以下平台关注 Kotlin：
  ![YouTube](youtube.svg){width=25}{type="joined"} [Youtube](https://www.youtube.com/channel/UCP7uiEZIqci43m22KDl0sNw)、
  ![Twitter](twitter.svg){width=18}{type="joined"} [Twitter](https://twitter.com/kotlin)、
  ![Bluesky](bsky.svg){width=18}{type="joined"} [Bluesky](https://bsky.app/profile/kotlinlang.org)、
  ![Reddit](reddit.svg){width=25}{type="joined"} [Reddit](https://www.reddit.com/r/Kotlin/)。
* 订阅 [Kotlin 新闻](https://info.jetbrains.com/kotlin-communication-center.html)。

如果你遇到任何困难或问题，请在我们的 [问题追踪器](https://youtrack.jetbrains.com/issues/KT) 中提交 Issue。

## 是否有遗漏？ {id=is-anything-missing}

如果此页面上有任何遗漏或看起来令人困惑的地方，请 [分享您的反馈](https://surveys.hotjar.com/d82e82b0-00d9-44a7-b793-0611bf6189df)。
