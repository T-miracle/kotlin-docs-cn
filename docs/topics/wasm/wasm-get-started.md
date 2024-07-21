[//]: # (title: 在 IntelliJ IDEA 中开始使用 Kotlin/Wasm)

> Kotlin/Wasm 是一个 [Alpha](components-stability.md) 功能。它可能随时更改。
>
{style="note"}

本教程演示了如何在 IntelliJ IDEA 中使用 Kotlin/Wasm 运行一个
[Compose Multiplatform](https://www.jetbrains.com/lp/compose-multiplatform/) 应用，并生成在
[GitHub pages](https://pages.github.com/) 上发布为网站的工件。

## 在开始之前 {id=before-you-start}

1. 下载并安装最新版本的 [IntelliJ IDEA](https://www.jetbrains.com/idea/)。
2. 在 IntelliJ IDEA 中选择 **File** | **New** | **Project from Version Control** 克隆 [Kotlin/Wasm examples](https://github.com/Kotlin/kotlin-wasm-examples/tree/main) 仓库。

   你也可以从命令行克隆它：

   ```bash
   git clone git@github.com:Kotlin/kotlin-wasm-examples.git
   ```

> 或者，你可以使用我们的实验性 [web wizard](https://kmp.jetbrains.com/) 下载一个示例项目。
>
{style="note"}

## 运行应用程序 {id=run-the-application}

1. 通过选择 **View** | **Tool Windows** | **Gradle** 打开 **Gradle** 工具窗口。

   > 你需要至少 Java 11 作为你的 Gradle JVM，才能成功加载示例。
   >
   {style="note"}

2. 在 **compose-example** | **Tasks** | **kotlin browser** 中，选择并运行 **wasmJsBrowserRun** 任务。

   ![运行 Gradle 任务](wasm-gradle-task-window.png){width=650}

   或者，你可以在 `compose-example` 目录下的终端中运行以下命令：

   ```bash
   ./gradlew wasmJsBrowserRun -t
   ```

3. 应用程序启动后，在浏览器中打开以下 URL：

   ```bash
   http://localhost:8081/
   ```

   你会看到一个 "Hello World!" 按钮。点击它：

   ![点击 hello world](wasm-composeapp-browser-hello.png){width=650}

   你会看到 Compose Multiplatform 的 logo：

   ![浏览器中的 Compose 应用](wasm-composeapp-browser.png){width=650}

## 生成工件 {id=generate-artifacts}

在 **compose-example** | **Tasks** | **kotlin browser** 中，选择并运行 **wasmJsBrowserDistribution** 任务。

![运行 Gradle 任务](wasm-gradle-task-window-compose.png){width=650}

或者，你可以在 `compose-example` 目录下的终端中运行以下命令：

```bash
./gradlew wasmJsBrowserDistribution
```
应用程序任务完成后，你可以在 `composeApp/build/dist/wasmJs/productionExecutable` 文件夹中找到生成的工件：

![工件目录](wasm-composeapp-directory.png){width=600}

## 在 GitHub pages 上发布 {id=publish-on-github-pages}

1. 将 `productionExecutable` 目录中的所有内容复制到你想要创建网站的仓库中。
2. 按照 GitHub 的 [创建你的网站](https://docs.github.com/en/pages/getting-started-with-github-pages/creating-a-github-pages-site#creating-your-site) 的说明操作。

   > 在你将更改推送到 GitHub 后，你的网站更改可能需要最多 10 分钟才能发布。
   >
   {style="note"}

3. 在浏览器中，导航到你的 GitHub pages 域名。

   ![导航到 GitHub pages](wasm-composeapp-github-hello.png){width=650}

   恭喜！你已将你的工件发布在 GitHub pages 上。

## 下一步是什么？ {id=whats-next}

* [加入 Kotlin/Wasm 社区](https://slack-chats.kotlinlang.org/c/webassembly)

* 尝试来自 `kotlin-wasm-examples` 仓库的其他 Kotlin/Wasm 示例：
  * [Compose 图片查看器](https://github.com/Kotlin/kotlin-wasm-examples/tree/main/compose-imageviewer)
  * [Jetsnack 应用](https://github.com/Kotlin/kotlin-wasm-examples/tree/main/compose-jetsnack)
  * [Node.js 示例](https://github.com/Kotlin/kotlin-wasm-examples/tree/main/nodejs-example)
  * [WASI 示例](https://github.com/Kotlin/kotlin-wasm-examples/tree/main/wasi-example)