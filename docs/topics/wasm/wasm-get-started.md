[//]: # (title: 在 IntelliJ IDEA 中开始使用 Kotlin/Wasm)

> Kotlin/Wasm 是一个 [Alpha](components-stability.md) 功能。它可能随时更改。
>
{style="note"}

本教程演示了如何在 IntelliJ IDEA 中使用 Kotlin/Wasm 运行一个
[Compose Multiplatform](https://www.jetbrains.com/lp/compose-multiplatform/) 应用，并生成在
[GitHub pages](https://pages.github.com/) 上发布为网站的工件。

## 在开始之前 {id=before-you-start}

Create a project using the Kotlin Multiplatform wizard:

1. Open the [Kotlin Multiplatform wizard](https://kmp.jetbrains.com/#newProject).
2. On the **New Project** tab, change the project name to "WasmDemo" and the project ID to "wasm.project.demo".
3. Select the **Web** option.
4. Click the **Download** button and unpack the resulting archive.

![Kotlin Multiplatform wizard](wasm-compose-wizard.png){width=600}

## Open the project in IntelliJ IDEA

1. Download and install the latest version of [IntelliJ IDEA](https://www.jetbrains.com/idea/).
2. On the Welcome screen of IntelliJ IDEA, click **Open** or select **File | Open** in the menu bar.
3. Navigate to the unpacked "WasmDemo" folder and click **Open**.

## 运行应用程序 {id=run-the-application}

1. In IntelliJ IDEA, open the **Gradle** tool window by selecting **View** | **Tool Windows** | **Gradle**.

   > You need at least Java 11 as your Gradle JVM for the tasks to load successfully.
   >
   {style="note"}

2. In **composeApp** | **Tasks** | **kotlin browser**, select and run the **wasmJsBrowserRun** task.

   ![Run the Gradle task](wasm-gradle-task-window.png){width=600}

   Alternatively, you can run the following command in the terminal from the `composeApp` directory:

   ```bash
   ./gradlew wasmJsBrowserRun -t
   ```

3. 应用程序启动后，在浏览器中打开以下 URL：

   ```bash
   http://localhost:8080/
   ```

   >The port number can vary because the 8080 port may be unavailable. You can find the actual port number printed
   > in the Gradle build console.
   >
   {type="tip"}

   You can see a "Click me!" button. Click it:

   ![Click me](wasm-composeapp-browser-clickme.png){width=650}

   Now you can see the Compose Multiplatform logo:

   ![浏览器中的 Compose 应用](wasm-composeapp-browser.png){width=650}

## 生成工件 {id=generate-artifacts}

In **composeApp** | **Tasks** | **kotlin browser**, select and run the **wasmJsBrowserDistribution** task.

![Run the Gradle task](wasm-gradle-task-window-compose.png){width=600}

Alternatively, you can run the following command in the terminal from the `composeApp` directory:

```bash
./gradlew wasmJsBrowserDistribution
```

Once the application task completes, you can find the generated artifacts in the `composeApp/build/dist/wasmJs/productionExecutable`
folder:

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

Try the Kotlin/Wasm examples from the `kotlin-wasm-examples` repository:

* [Compose image viewer](https://github.com/Kotlin/kotlin-wasm-examples/tree/main/compose-imageviewer)
* [Jetsnack application](https://github.com/Kotlin/kotlin-wasm-examples/tree/main/compose-jetsnack)
* [Node.js example](https://github.com/Kotlin/kotlin-wasm-examples/tree/main/nodejs-example)
* [WASI example](https://github.com/Kotlin/kotlin-wasm-examples/tree/main/wasi-example)
* [Compose example](https://github.com/Kotlin/kotlin-wasm-examples/tree/main/compose-example)