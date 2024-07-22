[//]: # (title: 使用 React 和 Kotlin/JS 构建 Web 应用程序 — 教程)

这个教程将教你如何使用 Kotlin/JS 和 [React](https://reactjs.org/) 框架构建一个浏览器应用程序。你将会：

* 完成与构建典型 React 应用程序相关的常见任务。
* 了解如何利用 [Kotlin 的 DSL](type-safe-builders.md) 来简洁、一致地表达概念，同时保持可读性，从而实现全面采用 Kotlin
  编写成熟应用程序的目标。
* 学习如何使用现成的 npm 组件，使用外部库，并发布最终应用程序。

这个输出将是一个专门用于 [KotlinConf](https://kotlinconf.com/) 活动的 **KotlinConf Explorer** web 应用程序，其中包含会议演讲的链接。
用户将能够在一个页面上观看所有演讲，并将其标记为已观看或未观看。

本教程假设您具有 Kotlin 的先前知识和基本的 HTML 和 CSS 知识。了解 React 背后的基本概念可能有助于理解一些示例代码，但并不是严格要求。

> 您可以在[这里](https://github.com/kotlin-hands-on/web-app-react-kotlin-js-gradle/tree/finished)获取最终的应用程序。
>
{style="note"}

## 开始之前

1. 下载并安装最新版本的 [IntelliJ IDEA](https://www.jetbrains.com/idea/download/index.html)。
2. 克隆 [项目模板](https://github.com/kotlin-hands-on/web-app-react-kotlin-js-gradle) 并在 IntelliJ IDEA 中打开。
   模板包括一个基本的 Kotlin 多平台 Gradle 项目，具有所有必需的配置和依赖项。

    * `build.gradle.kts` 文件中的依赖项和任务：

   ```kotlin
   dependencies {
       // React、React DOM + 包装器
       implementation(enforcedPlatform("org.jetbrains.kotlin-wrappers:kotlin-wrappers-bom:1.0.0-pre.430"))
       implementation("org.jetbrains.kotlin-wrappers:kotlin-react")
       implementation("org.jetbrains.kotlin-wrappers:kotlin-react-dom")
   
       // Kotlin React Emotion (CSS)
       implementation("org.jetbrains.kotlin-wrappers:kotlin-emotion")
   
       // 视频播放器
       implementation(npm("react-player", "2.12.0"))
   
       // 分享按钮
       implementation(npm("react-share", "4.4.1"))
   
       // 协程和序列化
       implementation("org.jetbrains.kotlinx:kotlinx-coroutines-core:1.6.4")
       implementation("org.jetbrains.kotlinx:kotlinx-serialization-json:1.5.0")
   }
   ```

    * `src/jsMain/resources/index.html` 中的 HTML 模板页面，用于插入此教程中将要使用的 JavaScript 代码：

   ```html
   <!doctype html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <title>Hello, Kotlin/JS!</title>
   </head>
   <body>
       <div id="root"></div>
       <script src="confexplorer.js"></script>
   </body>
   </html>
   ```
   {validate="false"}

   当您构建 Kotlin/JS 项目时，您的所有代码及其依赖项将自动捆绑到一个名为 `confexplorer.js` 的单个 JavaScript 文件中。
   作为典型的 [JavaScript 约定](https://faqs.skillcrush.com/article/176-where-should-js-script-tags-be-linked-in-html-documents)
   ，body 的内容（包括 `root` div）首先加载，以确保浏览器在加载所有页面元素之前加载脚本。

* `src/jsMain/kotlin/Main.kt` 中的代码片段：

   ```kotlin
   import kotlinx.browser.document
   
   fun main() {
       document.bgColor = "red"
   }
   ```

### 运行开发服务器

默认情况下，Kotlin 多平台 Gradle 插件带有对内嵌的 `webpack-dev-server` 的支持，允许您在 IDE 中运行应用程序，而无需手动设置任何服务器。

为了测试程序在浏览器中成功运行，通过在 IntelliJ IDEA 内的 Gradle 工具窗口中调用
`run` 或 `browserDevelopmentRun` 任务（在 `other` 或 `kotlin browser` 目录中可用）来启动开发服务器：

![Gradle 任务列表](browser-development-run.png){width=700}

要从终端运行程序，使用 `./gradlew run`。

当项目编译并捆绑完成时，在浏览器窗口中将会显示一个空白的红色页面：

![空白的红色页面](red-page.png){width=700}

### 启用热重载 / 连续模式

配置 _[连续编译](dev-server-continuous-compilation.md)_ 模式，这样您就不必每次更改后手动编译和执行项目。
在继续之前，请确保停止所有正在运行的开发服务器实例。

1. 编辑 IntelliJ IDEA 在第一次运行 Gradle `run` 任务后自动生成的运行配置：

   ![编辑运行配置](edit-configurations-continuous.png){width=700}

2. 在 **Run/Debug Configurations** 对话框中，将 `--continuous` 选项添加到运行配置的参数中：

   ![启用连续模式](continuous-mode.png){width=700}

   应用更改后，您可以使用 IntelliJ IDEA 中的 **Run** 按钮重新启动开发服务器。
   要从终端运行连续 Gradle 构建，请改用 `./gradlew run --continuous`。

3. 要测试此功能，请在 Gradle 任务正在运行时将页面颜色更改为蓝色，位于 `Main.kt` 文件中：

   ```kotlin
   document.bgColor = "blue"
   ```

   项目然后重新编译，在重新加载页面后，浏览器页面将显示新颜色。

您可以在开发过程中保持开发服务器以连续模式运行。当您进行更改时，它将自动重新构建和重新加载页面。

> 您可以在 `master` 分支 [这里](https://github.com/kotlin-hands-on/web-app-react-kotlin-js-gradle/tree/master)找到项目的此状态。
>
{style="note"}

## 创建一个 web 应用草稿

### 添加第一个使用 React 的静态页面

为了让您的应用程序显示一个简单的消息，将 `Main.kt` 文件中的代码替换为以下内容：

```kotlin
import kotlinx.browser.document
import react.*
import emotion.react.css
import csstype.Position
import csstype.px
import react.dom.html.ReactHTML.h1
import react.dom.html.ReactHTML.h3
import react.dom.html.ReactHTML.div
import react.dom.html.ReactHTML.p
import react.dom.html.ReactHTML.img
import react.dom.client.createRoot
import kotlinx.serialization.Serializable

fun main() {
    val container = document.getElementById("root") ?: error("Couldn't find root container!")
    createRoot(container).render(Fragment.create {
        h1 {
            +"Hello, React+Kotlin/JS!"
        }
    })
}
```

{validate="false"}

* `render()` 函数指示 [kotlin-react-dom](https://github.com/JetBrains/kotlin-wrappers/tree/master/kotlin-react-dom)
  在 [fragment](https://reactjs.org/docs/fragments.html) 内部将第一个 HTML 元素渲染到 `root`
  元素上。这个元素是在模板中包含的 `src/jsMain/resources/index.html` 中定义的容器。
* 内容是一个 `<h1>` 标题，并使用类型安全的 DSL 渲染 HTML。
* `h1` 是一个接受 lambda 参数的函数。当您在字符串字面量前面添加 `+` 符号时，实际上调用了 `unaryPlus()`
  函数，这是使用 [运算符重载](operator-overloading.md) 实现的。它将字符串附加到封闭的 HTML 元素上。

项目重新编译后，浏览器将显示这个 HTML 页面：

![HTML 页面示例](hello-react-js.png){width=700}

### 将 HTML 转换为 Kotlin 的类型安全 HTML DSL

Kotlin 对 React 的 [wrappers](https://github.com/JetBrains/kotlin-wrappers/blob/master/kotlin-react/README.md)
包含了一种 [领域特定语言 (DSL)](type-safe-builders.md)，可以让您在纯 Kotlin 代码中编写 HTML。
这种方式类似于 JavaScript 中的 [JSX](https://reactjs.org/docs/introducing-jsx.html)。
但是，由于这个标记是 Kotlin，您将获得静态类型语言的所有好处，比如自动完成或类型检查。

比较一下经典的 HTML 代码和其在 Kotlin 中类型安全的变体：

<tabs>
<tab title="HTML">

```html
<h1>KotlinConf Explorer</h1>
<div>
    <h3>Videos to watch</h3>
    <p>John Doe: Building and breaking things</p>
    <p>Jane Smith: The development process</p>
    <p>Matt Miller: The Web 7.0</p>
    <h3>Videos watched</h3>
    <p>Tom Jerry: Mouseless development</p>
</div>
<div>
    <h3>John Doe: Building and breaking things</h3>
    <img src="https://via.placeholder.com/640x360.png?text=Video+Player+Placeholder">
</div>
```

</tab>
<tab title="Kotlin">

```kotlin
h1 {
    +"KotlinConf Explorer"
}
div {
    h3 {
        +"Videos to watch"
    }
    p {
        + "John Doe: Building and breaking things"
    }
    p {
        +"Jane Smith: The development process"
    }
    p {
        +"Matt Miller: The Web 7.0"
    }
    h3 {
        +"Videos watched"
    }
    p {
        +"Tom Jerry: Mouseless development"
    }
}
div {
    h3 {
        +"John Doe: Building and breaking things"
    }
    img {
       src = "https://via.placeholder.com/640x360.png?text=Video+Player+Placeholder"
    }
}
```

</tab>
</tabs>

复制 Kotlin 代码，并更新 `main()` 函数中的 `Fragment.create()` 函数调用，替换之前的 `h1` 标签。

等待浏览器重新加载。页面现在应该是这样的：

![web 应用草稿](website-draft.png){width=700}

### 使用 Kotlin 构造在标记中添加视频

使用这种 DSL 在 Kotlin 中编写 HTML 有一些优势。您可以使用常规的 Kotlin 构造来操作您的应用程序，比如循环、条件、集合和字符串插值。

现在，您可以用 Kotlin 对象的列表来替换硬编码的视频列表：

1. 在 `Main.kt` 中，创建一个 `Video` [数据类](data-classes.md)，将所有视频属性集中在一个地方：

   ```kotlin
   data class Video(
       val id: Int,
       val title: String,
       val speaker: String,
       val videoUrl: String
   )
   ```

2. 填充两个列表，分别用于未观看的视频和已观看的视频。在 `Main.kt` 中的文件级别添加以下声明：

   ```kotlin
   val unwatchedVideos = listOf(
       Video(1, "Opening Keynote", "Andrey Breslav", "https://youtu.be/PsaFVLr8t4E"),
       Video(2, "Dissecting the stdlib", "Huyen Tue Dao", "https://youtu.be/Fzt_9I733Yg"),
       Video(3, "Kotlin and Spring Boot", "Nicolas Frankel", "https://youtu.be/pSiZVAeReeg")
   )
   
   val watchedVideos = listOf(
       Video(4, "Creating Internal DSLs in Kotlin", "Venkat Subramaniam", "https://youtu.be/JzTeAM8N1-o")
   )
   ```

3. 要在页面上使用这些视频，编写一个 Kotlin 的 `for` 循环来遍历未观看的 `Video` 对象的集合。
   将 "Videos to watch" 下面的三个 `p` 标签替换为以下代码片段：

   ```kotlin
   for (video in unwatchedVideos) {
       p {
           +"${video.speaker}: ${video.title}"
       }
   }
   ```

4. 对 "Videos watched" 下的单个标签应用相同的流程：

   ```kotlin
   for (video in watchedVideos) {
       p {
           +"${video.speaker}: ${video.title}"
       }
   }
   ```

等待浏览器重新加载。布局应该与之前保持一致。您可以向列表中添加更多视频以确保循环正常工作。

### 使用类型安全的 CSS 添加样式

[kotlin-emotion](https://github.com/JetBrains/kotlin-wrappers/blob/master/kotlin-emotion/) 对
[Emotion](https://emotion.sh/docs/introduction) 库的包装使得可以直接在 HTML 中指定 CSS 属性，甚至是动态属性。
从概念上讲，这使得它类似于 [CSS-in-JS](https://reactjs.org/docs/faq-styling.html#what-is-css-in-js) —— 但是针对的是 Kotlin。
使用 DSL 的好处是您可以使用 Kotlin 代码构造来表达格式规则。

此教程的模板项目已经包含了使用 `kotlin-emotion` 所需的依赖关系：

```kotlin
dependencies {
    // ...
    // Kotlin React Emotion (CSS) (chapter 3)
    implementation("org.jetbrains.kotlin-wrappers:kotlin-emotion")
    // ...
}
```

使用 `kotlin-emotion`，您可以在 HTML 元素 `div` 和 `h3` 内指定一个 `css` 块，其中您可以定义样式。

要将视频播放器移动到页面的右上角，请使用 CSS 并调整视频播放器的代码（片段中的最后一个 `div`）：

```kotlin
div {
    css {
        position = Position.absolute
        top = 10.px
        right = 10.px
    }
    h3 {
        +"John Doe: Building and breaking things"
    }
    img {
        src = "https://via.placeholder.com/640x360.png?text=Video+Player+Placeholder"
    }
}
```

随意尝试一些其他样式。例如，您可以更改 `fontFamily` 或为 UI 添加一些 `color`。

## 设计应用程序组件

在 React 中，基本构建块被称为 **[组件](https://reactjs.org/docs/components-and-props.html)**。
组件本身也可以由其他更小的组件组成。
通过组合组件，您构建您的应用程序。
如果您将组件结构化为通用且可重用的形式，您将能够在应用程序的多个部分中使用它们，而不会重复代码或逻辑。

`render()` 函数的内容通常描述一个基本组件。您当前应用程序的布局如下所示：

![当前布局](current-layout.png){width=700}

如果您将应用程序分解为单独的组件，您将得到一个更结构化的布局，其中每个组件都处理其职责：

![带有组件的结构化布局](structured-layout.png){width=700}

组件封装了特定的功能。使用组件可以缩短源代码并使其更易于阅读和理解。

### 添加主要组件

为了开始创建应用程序的结构，首先明确指定 `App`，作为渲染到 `root` 元素的主要组件：

1. 在 `src/jsMain/kotlin` 文件夹中创建一个新的 `App.kt` 文件。
2. 在此文件中，添加以下代码片段，并将类型安全的 HTML 从 `Main.kt` 移动到其中：

   ```kotlin
   import kotlinx.coroutines.async
   import react.*
   import react.dom.*
   import kotlinx.browser.window
   import kotlinx.coroutines.*
   import kotlinx.serialization.decodeFromString
   import kotlinx.serialization.json.Json
   import emotion.react.css
   import csstype.Position
   import csstype.px
   import react.dom.html.ReactHTML.h1
   import react.dom.html.ReactHTML.h3
   import react.dom.html.ReactHTML.div
   import react.dom.html.ReactHTML.p
   import react.dom.html.ReactHTML.img
   
   val App = FC<Props> {
       // typesafe HTML goes here, starting with the first h1 tag!
   }
   ```

   `FC` 函数创建了一个 [函数组件](https://reactjs.org/docs/components-and-props.html#function-and-class-components)。

3. 在 `Main.kt` 文件中，更新 `main()` 函数如下：

   ```kotlin
   fun main() {
       val container = document.getElementById("root") ?: error("Couldn't find root container!")
       createRoot(container).render(App.create())
   }
   ```

   现在程序创建了一个 `App` 组件的实例，并将其渲染到指定的容器中。

要了解更多关于 React 概念的信息，请参阅[文档和指南](https://reactjs.org/docs/hello-world.html#how-to-read-this-guide)。

### 提取列表组件

由于 `watchedVideos` 和 `unwatchedVideos` 列表都包含一系列视频，因此创建一个单一可重用的组件，并仅调整列表中显示的内容是有意义的。

`VideoList` 组件遵循与 `App` 组件相同的模式。它使用了 `FC` 构建器函数，并包含来自 `unwatchedVideos` 列表的代码。

1. 在 `src/jsMain/kotlin` 文件夹中创建一个新的 `VideoList.kt` 文件，并添加以下代码：

   ```kotlin
   import kotlinx.browser.window
   import react.*
   import react.dom.*
   import react.dom.html.ReactHTML.p
   
   val VideoList = FC<Props> {
       for (video in unwatchedVideos) {
           p {
               +"${video.speaker}: ${video.title}"
           }
       }
   }
   ```

2. 在 `App.kt` 中，通过不带参数调用 `VideoList` 组件来使用它：

   ```kotlin
   // . . .

   div {
       h3 {
           +"Videos to watch"
       }
       VideoList()
   
       h3 {
           +"Videos watched"
       }
       VideoList()
   }

   // . . .
   ```

   目前，`App` 组件无法控制由 `VideoList` 组件显示的内容。它是硬编码的，所以您会看到两个相同的列表。

### 添加属性以在组件之间传递数据

由于您要重用 `VideoList` 组件，您需要能够填充不同的内容到其中。您可以添加将项目列表作为属性传递给组件的功能。在 React 中，这些属性称为 _props_。当在 React 中更改组件的 props 时，框架会自动重新渲染组件。

对于 `VideoList`，您需要一个包含要显示的视频列表的属性。定义一个接口，其中包含可以传递给 `VideoList` 组件的所有 props：

1. 将以下定义添加到 `VideoList.kt` 文件中：

   ```kotlin
   external interface VideoListProps : Props {
       var videos: List<Video>
   }
   ```
   [external](js-interop.md#external-modifier) 修饰符告诉编译器接口的实现是外部提供的，因此它不会尝试从声明生成 JavaScript 代码。

2. 调整 `VideoList` 的类定义，以使用传递到 `FC` 块中的 props 作为参数：

   ```kotlin
   val VideoList = FC<VideoListProps> { props ->
       for (video in props.videos) {
           p {
               key = video.id.toString()
               +"${video.speaker}: ${video.title}"
           }
       }
   }
   ```

   `key` 属性帮助 React 渲染器确定在 `props.videos` 的值更改时应该执行什么操作。它使用键来确定列表的哪些部分需要刷新，哪些部分保持不变。您可以在 [React 指南](https://reactjs.org/docs/lists-and-keys.html) 中找到有关列表和键的更多信息。

3. 在 `App` 组件中，确保使用正确的属性实例化子组件。在 `App.kt` 中，将位于 `h3` 元素下方的两个循环替换为对 `VideoList` 的调用，以及 `unwatchedVideos` 和 `watchedVideos` 的属性。
   在 Kotlin DSL 中，您将它们分配到属于 `VideoList` 组件的块中：

   ```kotlin
   h3 {
       +"Videos to watch"
   }
   VideoList {
       videos = unwatchedVideos
   }
   h3 {
       +"Videos watched"
   }
   VideoList {
       videos = watchedVideos
   }
   ```

重新加载后，浏览器将显示列表现在正确地呈现。

### 使列表可交互

首先，在用户点击列表条目时添加一个弹出警告消息。在 `VideoList.kt` 中，添加一个 `onClick` 处理函数，用于触发一个包含当前视频信息的警告：

```kotlin
// . . .

p {
    key = video.id.toString()
    onClick = {
        window.alert("Clicked $video!")
    }
    +"${video.speaker}: ${video.title}"
}

// . . .
```

如果您在浏览器窗口中点击列表项之一，您将会在弹出的警告窗口中看到有关视频的信息，如下所示：

![浏览器警告窗口](alert-window.png){width=700}

> 直接将 `onClick` 函数定义为 lambda 是简洁的，对于原型设计非常有用。
> 但是，由于目前 Kotlin/JS 中相等性的处理方式，从性能角度来看，这并不是传递点击处理程序的最优方式。
> 如果您想要优化渲染性能，请考虑将函数存储在变量中并传递它们。
>
{style="tip"}

### 添加状态以保存数值

您可以添加一些功能来突出显示所选视频，并使用 ▶ 三角形标记它，而不仅仅是向用户发出警告。为此，引入此组件特定的 _状态_。

状态是 React 中的核心概念之一。在现代 React（使用所谓的 **Hooks API**）中，状态使用
[`useState` hook](https://reactjs.org/docs/hooks-state.html) 表示。

1. 将以下代码添加到 `VideoList` 声明的顶部：

   ```kotlin
   val VideoList = FC<VideoListProps> { props ->
       var selectedVideo: Video? by useState(null)

   // . . .
   ```
   {validate="false"}

   * `VideoList` 函数组件保留状态（一个与当前函数调用无关的值）。状态是可空的，并且具有 `Video?` 类型。其默认值为 `null`。
   * `React` 中的 `useState()` 函数指示框架跟踪状态以跨多个函数调用。例如，即使您指定了默认值，React
     也会确保默认值仅在开始时分配。当状态更改时，组件将基于新状态进行重新渲染。
   * `by` 关键字表示 `useState()` 作为[委托属性](delegated-properties.md)。
     与任何其他变量一样，您可以读取和写入值。`useState()` 背后的实现负责处理使状态工作所需的机制。

   要了解有关 State Hook 的更多信息，请查看[React 文档](https://reactjs.org/docs/hooks-state.html)。

2. 更改 `VideoList` 组件中的 `onClick` 处理程序和文本，如下所示：

   ```kotlin
   val VideoList = FC<VideoListProps> { props ->
       var selectedVideo: Video? by useState(null)
       for (video in props.videos) {
           p {
               key = video.id.toString()
               onClick = {
                   selectedVideo = video
               }
               if (video == selectedVideo) {
                   +"▶ "
               }
               +"${video.speaker}: ${video.title}"
           }
       }
   }
   ```

   * 当用户单击视频时，其值将分配给 `selectedVideo` 变量。
   * 当渲染所选列表条目时，三角形将被前置。

请检查浏览器并点击列表中的项目，以确保一切正常工作。

## 组合组件

当前，两个视频列表各自工作，这意味着每个列表都跟踪所选视频。即使只有一个播放器，用户也可以在未观看列表和观看列表中分别选择两个视频：

![两个视频同时在两个列表中被选择](two-videos-select.png){width=700}

一个列表无法同时跟踪其自身以及兄弟列表中的所选视频。
原因是所选视频不是列表状态的一部分，而是应用程序状态的一部分。
这意味着您需要将状态从各个组件中 **提升** 出来。

### 提升状态

React 确保 props 只能从父组件传递给其子组件。这样可以防止组件之间相互耦合。

如果一个组件想要改变一个兄弟组件的状态，它需要通过它们的父组件来实现。
在这种情况下，状态也不再属于任何子组件，而是属于上级父组件。

将状态从组件迁移到它们的父组件的过程称为 _提升状态_。对于您的应用程序，将 `currentVideo` 作为状态添加到 `App` 组件中：

1. 在 `App.kt` 中，在 `App` 组件的定义顶部添加以下内容：

   ```kotlin
   val App = FC<Props> {
       var currentVideo: Video? by useState(null)
   
       // . . .
   }
   ```

   `VideoList` 组件不再需要跟踪状态。它将作为属性接收当前视频。

2. 在 `VideoList.kt` 中删除 `useState()` 调用。
3. 准备 `VideoList` 组件接收所选视频作为属性。为此，扩展 `VideoListProps` 接口以包含 `selectedVideo`：

   ```kotlin
   external interface VideoListProps : Props {
       var videos: List<Video>
       var selectedVideo: Video?
   }
   ```

4. 更改三角形的条件，使其使用 `props` 而不是 `state`：

   ```kotlin
   if (video == props.selectedVideo) {
       +"▶ "
   }
   ```

### 传递处理程序

目前，没有办法给属性赋值，因此 `onClick` 函数无法按照当前设置正常工作。要从子组件更改父组件的状态，您需要再次提升状态。

在 React 中，状态始终从父级流向子级。
因此，要从一个子组件更改 _应用程序_ 状态，您需要将处理用户交互的逻辑移到父组件中，然后将逻辑作为属性传递进去。
请记住，在 Kotlin 中，变量可以具有[函数类型](lambdas.md#function-types)。

1. 再次扩展 `VideoListProps` 接口，使其包含一个名为 `onSelectVideo` 的变量，它是一个接受 `Video` 并返回 `Unit` 的函数：

   ```kotlin
   external interface VideoListProps : Props {
       // ...
       var onSelectVideo: (Video) -> Unit
   }
   ```

2. 在 `VideoList` 组件中，在 `onClick` 处理程序中使用新属性：

   ```kotlin
   onClick = {
       props.onSelectVideo(video)
   }
   ```

   您现在可以删除 `VideoList` 组件中的 `selectedVideo` 变量。

3. 回到 `App` 组件，并为两个视频列表中的每一个传递 `selectedVideo` 和 `onSelectVideo` 的处理程序：

   ```kotlin
   VideoList {
       videos = unwatchedVideos // and watchedVideos respectively
       selectedVideo = currentVideo
       onSelectVideo = { video ->
           currentVideo = video
       }
   }
   ```

4. 对于已观看的视频列表，重复上一步。

切换回浏览器，确保在选择视频时，选择在两个列表之间跳转而不重复。

### 提取视频播放器组件

您现在可以创建另一个独立的组件，视频播放器，它目前是一个占位图像。
您的视频播放器需要知道演讲标题、演讲者和视频链接。
这些信息已经包含在每个 `Video` 对象中，因此您可以将其作为属性传递并访问其属性。

1. 创建一个名为 `VideoPlayer.kt` 的新文件，并添加以下 `VideoPlayer` 组件的实现：

   ```kotlin
   import csstype.*
   import react.*
   import emotion.react.css
   import react.dom.html.ReactHTML.button
   import react.dom.html.ReactHTML.div
   import react.dom.html.ReactHTML.h3
   import react.dom.html.ReactHTML.img
   
   external interface VideoPlayerProps : Props {
       var video: Video
   }
   
   val VideoPlayer = FC<VideoPlayerProps> { props ->
       div {
           css {
               position = Position.absolute
               top = 10.px
               right = 10.px
           }
           h3 {
               +"${props.video.speaker}: ${props.video.title}"
           }
           img {
               src = "https://via.placeholder.com/640x360.png?text=Video+Player+Placeholder"              
           }
       }
   }
   ```

2. 因为 `VideoPlayerProps` 接口指定了 `VideoPlayer` 组件接受一个非空的 `Video`，请确保在 `App` 组件中相应地处理。

   在 `App.kt` 中，将视频播放器的先前 `div` 代码片段替换为以下内容：

   ```kotlin
   currentVideo?.let { curr ->
       VideoPlayer {
           video = curr
       }
   }
   ```

   [`let` 作用域函数](scope-functions.md#let)确保只有在 `state.currentVideo` 不为 null 时才添加 `VideoPlayer` 组件。

现在，单击列表中的条目将会显示视频播放器，并使用单击条目的信息填充它。

### 添加按钮并连接

为了让用户能够将视频标记为已观看或未观看，并在两个列表之间移动，将按钮添加到 `VideoPlayer` 组件中。

由于此按钮将在两个不同的列表之间移动视频，处理状态更改的逻辑需要从 `VideoPlayer` 中提取出来，并作为一个属性从父组件传递进来。
该按钮的外观应基于视频是否已观看而有所不同。这也是您需要作为属性传递的信息。

1. 在 `VideoPlayer.kt` 中扩展 `VideoPlayerProps` 接口，包括这两种情况的属性：

   ```kotlin
   external interface VideoPlayerProps : Props {
       var video: Video
       var onWatchedButtonPressed: (Video) -> Unit
       var unwatchedVideo: Boolean
   }
   ```

2. 您现在可以将按钮添加到实际组件中了。将以下代码片段复制到 `VideoPlayer` 组件的主体中，放在 `h3` 和 `img` 标签之间：

   ```kotlin
   button {
       css {
           display = Display.block
           backgroundColor = if (props.unwatchedVideo) NamedColor.lightgreen else NamedColor.red
       }
       onClick = {
           props.onWatchedButtonPressed(props.video)
       }
       if (props.unwatchedVideo) {
           +"标记为已观看"
       } else {
           +"标记为未观看"
       }
   }
   ```

   通过 Kotlin CSS DSL 可以动态地更改按钮的颜色，使用基本的 Kotlin `if` 表达式。

### 将视频列表移到应用程序状态中

现在是时候调整 `App` 组件中的 `VideoPlayer` 使用位置了。
当点击按钮时，视频应该从未观看列表移动到已观看列表，反之亦然。
由于这些列表现在实际上可以改变，将它们移到应用程序状态中：

1. 在 `App.kt` 中，在 `App` 组件的顶部添加以下属性和 `useState()` 调用：

   ```kotlin
   val App = FC<Props> {
       var currentVideo: Video? by useState(null)
       var unwatchedVideos: List<Video> by useState(listOf(
           Video(1, "Opening Keynote", "Andrey Breslav", "https://youtu.be/PsaFVLr8t4E"),
           Video(2, "Dissecting the stdlib", "Huyen Tue Dao", "https://youtu.be/Fzt_9I733Yg"),
           Video(3, "Kotlin and Spring Boot", "Nicolas Frankel", "https://youtu.be/pSiZVAeReeg")
       ))
       var watchedVideos: List<Video> by useState(listOf(
           Video(4, "Creating Internal DSLs in Kotlin", "Venkat Subramaniam", "https://youtu.be/JzTeAM8N1-o")
       ))

       // . . .
   }
   ```

2. 由于所有的演示数据都直接包含在 `watchedVideos` 和 `unwatchedVideos` 的默认值中，您不再需要文件级别的声明。
   在 `Main.kt` 中，删除对 `watchedVideos` 和 `unwatchedVideos` 的声明。
3. 更改属于视频播放器的 `App` 组件中 `VideoPlayer` 的调用位置，使其如下所示：

   ```kotlin
   VideoPlayer {
       video = curr
       unwatchedVideo = curr in unwatchedVideos
       onWatchedButtonPressed = {
           if (video in unwatchedVideos) {
               unwatchedVideos = unwatchedVideos - video
               watchedVideos = watchedVideos + video
           } else {
               watchedVideos = watchedVideos - video
               unwatchedVideos = unwatchedVideos + video
           }
       }
   }
   ```

返回浏览器，选择一个视频，然后多次点击按钮。视频将在两个列表之间跳转。

## 使用 npm 包

要使应用程序可用，您仍然需要一个实际播放视频的视频播放器以及一些按钮来帮助用户分享内容。

React 拥有丰富的生态系统，提供了许多预制的组件，您可以使用它们来替代自己构建这些功能。

### 添加视频播放器组件

要将占位符视频组件替换为实际的 YouTube 播放器，请使用 npm 中的 `react-player` 包。它可以播放视频并允许您控制播放器的外观。

有关组件文档和 API 描述，请参阅其在 GitHub 上的 [README](https://www.npmjs.com/package/react-player)。

1. 检查 `build.gradle.kts` 文件。`react-player` 包应该已经包含在内：

   ```kotlin
   dependencies {
       // ...
       // 视频播放器
       implementation(npm("react-player", "2.12.0"))
       // ...
   }
   ```

   正如您所见，npm 依赖项可以通过在构建文件的 `dependencies` 块中使用 `npm()` 函数添加到 Kotlin/JS 项目中。
   然后，Gradle 插件会为你下载和安装这些依赖项。
   为此，它使用自己的捆绑安装的 [`yarn`](https://yarnpkg.com/) 包管理器。

2. 要在 React 应用程序内部使用 JavaScript 包，必须告诉 Kotlin 编译器要期望什么，为此需要提供[外部声明](js-interop.md)。

   创建一个新的 `ReactYouTube.kt` 文件，并添加以下内容：

   ```kotlin
   @file:JsModule("react-player")
   @file:JsNonModule
   
   import react.*
   
   @JsName("default")
   external val ReactPlayer: ComponentClass<dynamic>
   ```

   当编译器看到像 `ReactPlayer` 这样的外部声明时，它会假定对应类的实现由依赖项提供，并不为其生成代码。

   最后两行等同于 JavaScript 的 `require("react-player").default;` 导入。
   它们告诉编译器，在运行时，一个组件将符合 `ComponentClass<dynamic>`。

但是，在这种配置中，`ReactPlayer` 接受的 props 的泛型类型设置为 `dynamic`。
这意味着编译器将接受任何代码，存在在运行时破坏代码的风险。

更好的选择是创建一个外部接口，指定属于此外部组件 props 的属性的类型。
您可以在组件的 [README](https://www.npmjs.com/package/react-player) 中了解有关 props 接口的信息。
在这种情况下，使用 `url` 和 `controls` props：

1. 通过将 `dynamic` 替换为外部接口来调整 `ReactYouTube.kt` 的内容：

   ```kotlin
   @file:JsModule("react-player")
   @file:JsNonModule
   
   import react.*
   
   @JsName("default")
   external val ReactPlayer: ComponentClass<ReactPlayerProps>
   
   external interface ReactPlayerProps : Props {
       var url: String
       var controls: Boolean
   }
   ```

2. 您现在可以使用新的 `ReactPlayer` 来替换 `VideoPlayer` 组件中的灰色占位符矩形。
   在 `VideoPlayer.kt` 中，用以下代码段替换 `img` 标签：

   ```kotlin
   ReactPlayer {
       url = props.video.videoUrl
       controls = true
   }
   ```

### 添加社交分享按钮

分享应用程序内容的简便方法是为 Messenger 和电子邮件添加社交分享按钮。
您也可以使用现成的 React 组件，例如 [react-share](https://github.com/nygardk/react-share/blob/master/README.md)：

1. 检查 `build.gradle.kts` 文件。这个 npm 库应该已经包含在内：

   ```kotlin
   dependencies {
       // ...
       // 分享按钮
       implementation(npm("react-share", "4.4.1"))
       // ...
   }
   ```

2. 要从 Kotlin 中使用 `react-share`，您需要编写更基本的外部声明。
   GitHub 上的 [示例](https://github.com/nygardk/react-share/blob/master/demo/Demo.tsx#L61) 显示，一个分享按钮由两个
   React 组件组成：`EmailShareButton` 和 `EmailIcon`，例如：
   不同类型的分享按钮和图标都具有相同的接口。您将为每个组件创建与您已经为视频播放器创建的相同方式的外部声明。

   将以下代码添加到一个新的 `ReactShare.kt` 文件中：

   ```kotlin
   @file:JsModule("react-share")
   @file:JsNonModule
   
   import react.ComponentClass
   import react.Props
   
   @JsName("EmailIcon")
   external val EmailIcon: ComponentClass<IconProps>
   
   @JsName("EmailShareButton")
   external val EmailShareButton: ComponentClass<ShareButtonProps>
   
   @JsName("TelegramIcon")
   external val TelegramIcon: ComponentClass<IconProps>
   
   @JsName("TelegramShareButton")
   external val TelegramShareButton: ComponentClass<ShareButtonProps>
   
   external interface ShareButtonProps : Props {
       var url: String
   }
   
   external interface IconProps : Props {
       var size: Int
       var round: Boolean
   }
   ```

3. 将新组件添加到应用程序的用户界面中。在 `VideoPlayer.kt` 中，在 `ReactPlayer` 的使用之前，添加一个 `div`，其中包含两个分享按钮：

   ```kotlin
   // . . .

   div {
       css {
            position = Position.absolute
            top = 10.px
            right = 10.px
        }
       EmailShareButton {
           url = props.video.videoUrl
           EmailIcon {
               size = 32
               round = true
           }
       }
       TelegramShareButton {
           url = props.video.videoUrl
           TelegramIcon {
               size = 32
               round = true
           }
       }
   }

   // . . .
   ```

现在您可以检查您的浏览器，查看按钮是否有效。
单击按钮时，应该会出现一个带有视频 URL 的“分享窗口”。
如果按钮不显示或不起作用，您可能需要禁用广告和社交媒体阻止器。

![分享窗口](social-buttons.png){width=700}

随意根据 [react-share](https://github.com/nygardk/react-share/blob/master/README.md#features)
中提供的其他社交网络的分享按钮重复此步骤。

## 使用外部 REST API

您现在可以在应用程序中使用来自 REST API 的真实数据来替换硬编码的演示数据。

对于本教程，有一个 [小型 API](https://my-json-server.typicode.com/kotlin-hands-on/kotlinconf-json/videos/1)。
它只提供一个端点，`videos`，并接受一个数字参数以从列表中获取元素。
如果您访问该 API，您会发现从 API 返回的对象具有与 `Video` 对象相同的结构。

### 使用 Kotlin 调用 JS 功能

浏览器已经内置了大量的 [Web API](https://developer.mozilla.org/en-US/docs/Web/API)。
您也可以从 Kotlin/JS 中使用它们，因为它已经包含了对这些 API 的包装器。
一个例子是 [fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)，用于进行 HTTP 请求。

第一个潜在问题是，像 `fetch()` 这样的浏览器 API 使用
[回调](https://developer.mozilla.org/en-US/docs/Glossary/Callback_function) 执行非阻塞操作。
当多个回调需要依次运行时，它们需要被嵌套。
自然地，代码会被大量缩进，越来越多的功能被堆叠在一起，这使得代码变得更难阅读。

为了解决这个问题，您可以使用 Kotlin 的协程，这是更好的处理此类功能的方法。

第二个问题源于 JavaScript 动态类型的特性。从外部 API 返回的数据类型没有任何保证。
为了解决这个问题，您可以使用 `kotlinx.serialization` 库。

检查 `build.gradle.kts` 文件。相关的代码段应该已经存在：

```kotlin
dependencies {
    // . . .

    // 协程 & 序列化
    implementation("org.jetbrains.kotlinx:kotlinx-coroutines-core:1.6.4")
}
```

### 添加序列化

当调用外部 API 时，您会得到 JSON 格式的文本，它仍然需要转换为 Kotlin 对象，以便进行处理。

[`kotlinx.serialization`](https://github.com/Kotlin/kotlinx.serialization) 是一个库，可以实现从 JSON 字符串到 Kotlin 对象的这种转换。

1. 检查 `build.gradle.kts` 文件。相关的代码段应该已经存在：

   ```kotlin
   plugins {
       // . . .
       kotlin("plugin.serialization") version "%kotlinVersion%"
   }
   
   dependencies {
       // . . .

       // 序列化
       implementation("org.jetbrains.kotlinx:kotlinx-serialization-json:1.5.0")
   }
   ```

2. 在获取第一个视频之前，需要告诉序列化库关于 `Video` 类。在 `Main.kt` 中，给其定义添加 `@Serializable` 注解：

   ```kotlin
   @Serializable
   data class Video(
       val id: Int,
       val title: String,
       val speaker: String,
       val videoUrl: String
   )
   ```

### 获取视频

要从 API 中获取视频，请在 `App.kt`（或一个新文件）中添加以下函数：

```kotlin
suspend fun fetchVideo(id: Int): Video {
    val response = window
        .fetch("https://my-json-server.typicode.com/kotlin-hands-on/kotlinconf-json/videos/$id")
        .await()
        .text()
        .await()
    return Json.decodeFromString(response)
}
```

* **挂起函数** `fetch()` 从 API 中获取具有给定 `id` 的视频。
  这个响应可能需要一段时间，因此您需要 `await()` 结果。
  接下来，`text()` 使用回调，从响应中读取主体。然后，您等待其完成。
* 在函数返回值之前，将其传递给 `Json.decodeFromString`，这是来自 `kotlinx.coroutines` 的函数。
  它将您从请求中收到的 JSON 文本转换为具有适当字段的 Kotlin 对象。
* `window.fetch` 函数调用返回一个 `Promise` 对象。
  通常情况下，您需要定义一个回调处理程序，一旦 `Promise` 解决并且结果可用，就会调用该处理程序。
  但是，通过协程，您可以 `await()` 这些 promises。每当调用类似 `await()` 这样的函数时，方法都会停止（挂起）执行。
  一旦 `Promise` 可以解决，它的执行就会继续。

为了给用户提供视频的选择，定义 `fetchVideos()` 函数，它将从上述相同的 API 中获取 25 个视频。
要并行运行所有请求，请使用 Kotlin 协程提供的 [`async`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/async.html) 功能：

1. 将以下实现添加到您的 `App.kt`：

   ```kotlin
   suspend fun fetchVideos(): List<Video> = coroutineScope {
       (1..25).map { id ->
           async {
               fetchVideo(id)
           }
       }.awaitAll()
   }
   ```

   遵循 [结构化并发原则](https://kotlinlang.org/docs/coroutines-basics.html#structured-concurrency)，该实现包装在 `coroutineScope` 中。
   然后，您可以启动 25 个异步任务（每个请求一个），并等待它们全部完成。

2. 您现在可以向应用程序添加数据了。
   添加 `mainScope` 的定义，并更改您的 `App` 组件，使其以以下片段开头。不要忘记将演示值替换为 `emptyLists` 实例：

   ```kotlin
   val mainScope = MainScope()
   
   val App = FC<Props> {
       var currentVideo: Video? by useState(null)
       var unwatchedVideos: List<Video> by useState(emptyList())
       var watchedVideos: List<Video> by useState(emptyList())
   
       useEffectOnce {
           mainScope.launch {
               unwatchedVideos = fetchVideos()
           }
       }

   // . . .
   ```
   {validate="false"}

   * [`MainScope()`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-main-scope.html)
     是 Kotlin 结构化并发模型的一部分，它创建了异步任务运行的范围。
   * `useEffectOnce` 是另一个 React **hook**（具体来说是 [useEffect](https://reactjs.org/docs/hooks-effect.html) hook 的简化版本）。
      它表示组件执行了 _副作用_。它不仅仅渲染自身，还通过网络进行通信。

检查您的浏览器。应用程序应该显示实际数据：

![从 API 获取的数据](website-api-data.png){width=700}

当您加载页面时：

* `App` 组件的代码将被调用。这启动了 `useEffectOnce` 块中的代码。
* `App` 组件以空列表作为已观看和未观看视频的参数进行渲染。
* 当 API 请求完成时，`useEffectOnce` 块将其分配给 `App` 组件的状态。这会触发重新渲染。
* `App` 组件的代码将再次被调用，但 `useEffectOnce` 块 _不会_ 第二次运行。

如果您想深入了解协程的工作原理，请查看此 [协程教程](coroutines-and-channels.md)。

## 部署到生产环境和云端

是时候将应用程序发布到云端，并使其对其他人可访问了。

### 打包生产版本

要打包所有资产以生产模式运行，请通过 IntelliJ IDEA 的工具窗口运行 Gradle 中的 `build` 任务，或者运行 `./gradlew build`。
这将生成一个经过优化的项目构建，应用各种改进，如 DCE（无用代码消除）。

构建完成后，您可以在 `/build/dist` 中找到部署所需的所有文件。它们包括 JavaScript 文件、HTML 文件和运行应用程序所需的其他资源。
您可以将它们放在静态 HTTP 服务器上，使用 GitHub Pages 提供服务，或将它们托管在您选择的云提供商上。

### 部署到 Heroku

Heroku 可以非常简单地启动一个可通过其自己的域名访问的应用程序。对于开发目的，它们的免费层应该是足够的。

1. [创建一个账号](https://signup.heroku.com/)。
2. [安装并认证 CLI 客户端](https://devcenter.heroku.com/articles/heroku-cli)。
3. 在项目根目录中，在终端中运行以下命令创建一个 Git 仓库，并附加一个 Heroku 应用：

   ```bash
   git init
   heroku create
   git add .
   git commit -m "initial commit"
   ```

4. 与普通的在 Heroku 上运行的 JVM 应用程序（例如使用 Ktor 或 Spring Boot 编写的应用程序）不同，您的应用程序生成静态 HTML
   页面和 JavaScript 文件，需要相应地进行服务。您可以调整所需的构建包以正确地提供程序：

   ```bash
   heroku buildpacks:set heroku/gradle
   heroku buildpacks:add https://github.com/heroku/heroku-buildpack-static.git
   ```

5. 为了让 `heroku/gradle` 构建包正常运行，`build.gradle.kts` 文件中需要一个 `stage` 任务。
   该任务等效于 `build` 任务，文件底部已包含相应的别名：

   ```kotlin
   // Heroku 部署
   tasks.register("stage") {
       dependsOn("build")
   }
   ```

6. 在项目根目录中添加一个新的 `static.json` 文件以配置 `buildpack-static`。
7. 在文件中添加 `root` 属性：

   ```xml
   {
       "root": "build/distributions"
   }
   ```
   {validate="false"}

8. 您现在可以触发部署，例如，通过运行以下命令：

   ```bash
   git add -A
   git commit -m "add stage task and static content root configuration"
   git push heroku master
   ```

> 如果您从非主分支推送，请将命令调整为推送到 `main` 远程，例如，`git push heroku feature-branch:main`。
>
{style="tip"}

如果部署成功，您将看到可以在互联网上访问该应用程序的 URL。

![将 Web 应用部署到生产环境](deployment-to-production.png){width=700}

> 您可以在 `finished`
> 分支中找到项目的此状态 [此处](https://github.com/kotlin-hands-on/web-app-react-kotlin-js-gradle/tree/finished)。
>
{style="note"}

## 接下来做什么

### 添加更多功能 {collapsible="true"}

您可以使用生成的应用程序作为探索 React、Kotlin/JS 等领域更高级主题的起点。

* **搜索**。您可以添加一个搜索字段来过滤演讲列表 -
  按标题或作者进行过滤，例如。了解有关 [React 中 HTML 表单元素的工作原理](https://reactjs.org/docs/forms.html)。
* **持久性**。目前，每次重新加载页面时，应用程序都会丢失观看者的观看列表。
  考虑构建自己的后端，使用 Kotlin 可用的 Web 框架之一（例如 [Ktor](https://ktor.io/)）。
  或者，探索在客户端 [存储信息的方法](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)。
* **复杂的 API**。有大量的数据集和 API
  可用。您可以将各种数据拉入应用程序。例如，您可以构建一个 [猫照片的可视化器](https://thecatapi.com/)
  或 [免费的库存照片 API](https://unsplash.com/developers)。

### 改进样式：响应式和网格 {collapsible="true"}

应用程序设计仍然非常简单，在移动设备或窄窗口中可能看起来不太好。探索更多的 CSS DSL，使应用程序更易于访问。

### 加入社区并获取帮助 {collapsible="true"}

报告问题和获取帮助的最佳途径是 [kotlin-wrappers 问题跟踪器](https://github.com/JetBrains/kotlin-wrappers/issues)。
如果找不到与您的问题相关的票，可以随时提交新票。
您还可以加入官方的 [Kotlin Slack](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up)。
有 `#javascript` 和 `#react` 等频道。

### 了解更多关于协程的知识 {collapsible="true"}

如果您对了解如何编写并发代码感兴趣，请查看关于 [协程](coroutines-and-channels.md) 的教程。

### 了解更多关于 React 的知识 {collapsible="true" id="了解更多关于React的知识"}

现在您已经了解了基本的 React 概念以及它们如何转换为 Kotlin，您可以将[React 文档](https://react.dev/learn)中概述的其他概念转换为
Kotlin。
