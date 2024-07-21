[//]: # (title: 在 IntelliJ IDEA 中开始使用 Kotlin/Wasm)

> Kotlin/Wasm 目前处于 [Alpha](components-stability.md) 阶段，可能随时发生变化。
>
{style="note"}

本教程演示了如何在 IntelliJ IDEA 中使用 Kotlin/Wasm 运行一个
[Compose Multiplatform](https://www.jetbrains.com/lp/compose-multiplatform/) 应用，并生成在
[GitHub pages](https://pages.github.com/) 上发布为网站的工件。

## 在开始之前 {id=before-you-start}

使用 Kotlin Multiplatform 启动向导创建一个项目：

1. 打开 [Kotlin Multiplatform 启动向导](https://kmp.jetbrains.com/#newProject)。
2. 在 **新项目** 选项卡中，将项目名称和 ID 更改为你喜欢的名称。在本教程中，我们将名称设置为 "WasmDemo"，ID 设置为 "wasm.project.demo"。

   > 这些是项目目录的名称和 ID。你也可以保持默认设置。
   >
   {style="tip"}

3. 选择 **Web** 选项，确保没有其他选项被选中。
4. 点击 **下载** 按钮，并解压缩生成的归档文件。

![Kotlin Multiplatform 启动向导](wasm-compose-wizard.png){width=600}

## 在 IntelliJ IDEA 中打开项目 {id=open-the-project-in-intellij-idea}

1. 下载并安装最新版本的 [IntelliJ IDEA](https://www.jetbrains.com/idea/)。
2. 在 IntelliJ IDEA 的欢迎屏幕上，点击 **Open** 或在菜单栏选择 **File | Open**。
3. 导航到解压后的 "WasmDemo" 文件夹，并点击 **Open**。

## 运行应用程序 {id=run-the-application}

1. 在 IntelliJ IDEA 中，通过选择 **View** | **Tool Windows** | **Gradle** 打开 **Gradle** 工具窗口。

   > 你需要至少 Java 11 作为你的 Gradle JVM，以确保任务成功加载。
   >
   {style="note"}

2. 在 **composeApp** | **Tasks** | **kotlin browser** 下，选择并运行 **wasmJsBrowserRun** 任务。

   ![运行 Gradle 任务](wasm-gradle-task-window.png){width=600}

   另外，你也可以在 `WasmDemo` 根目录的终端中运行以下命令：

   ```bash
   ./gradlew wasmJsBrowserRun -t
   ```

3. 应用程序启动后，在浏览器中打开以下 URL：

   ```bash
   http://localhost:8080/
   ```

   > 端口号可能会有所不同，因为 8080 端口可能不可用。你可以在 Gradle 构建控制台中找到实际的端口号。
   >
   {style="tip"}

   你会看到一个 "Click me!" 按钮，点击它：
    
   ![点击我](wasm-composeapp-browser-clickme.png){width=650}
    
   现在你会看到 Compose Multiplatform 的 logo：

   ![浏览器中的 Compose 应用](wasm-composeapp-browser.png){width=650}

## 生成工件 {id=generate-artifacts}

在 **composeApp** | **Tasks** | **kotlin browser** 下，选择并运行 **wasmJsBrowserDistribution** 任务。

![运行 Gradle 任务](wasm-gradle-task-window-compose.png){width=600}

另外，你也可以在 `WasmDemo` 根目录的终端中运行以下命令：

```bash
./gradlew wasmJsBrowserDistribution
```

任务完成后，你可以在 `composeApp/build/dist/wasmJs/productionExecutable` 目录中找到生成的工件：

![工件目录](wasm-composeapp-directory.png){width=600}

## 在 GitHub pages 上发布 {id=publish-on-github-pages}

1. 将 `productionExecutable` 目录中的所有内容复制到你想要创建网站的仓库中。
2. 按照 GitHub 的 [创建你的网站](https://docs.github.com/en/pages/getting-started-with-github-pages/creating-a-github-pages-site#creating-your-site) 的说明操作。

   > 在你将更改推送到 GitHub 后，你的网站更改可能需要最多 10 分钟才能发布。
   >
   {style="note"}

3. 在浏览器中，导航到你的 GitHub pages 域名。

   ![Navigate to GitHub pages](wasm-composeapp-github-clickme.png){width=650}

   恭喜！你已将你的工件发布在 GitHub pages 上。

## 下一步是什么？ {id=whats-next}

加入 Kotlin/Wasm 社区，加入 Kotlin Slack：

<a href="https://slack-chats.kotlinlang.org/c/webassembly"><img src="join-slack-channel.svg" width="700" style="block" alt="加入 Kotlin/Wasm 社区"/></a>

尝试来自 `kotlin-wasm-examples` 仓库的 Kotlin/Wasm 示例：

* [Compose 图像查看器](https://github.com/Kotlin/kotlin-wasm-examples/tree/main/compose-imageviewer)
* [Jetsnack 应用程序](https://github.com/Kotlin/kotlin-wasm-examples/tree/main/compose-jetsnack)
* [Node.js 示例](https://github.com/Kotlin/kotlin-wasm-examples/tree/main/nodejs-example)
* [WASI 示例](https://github.com/Kotlin/kotlin-wasm-examples/tree/main/wasi-example)
* [Compose 示例](https://github.com/Kotlin/kotlin-wasm-examples/tree/main/compose-example)