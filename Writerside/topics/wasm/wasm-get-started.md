[//]: # (title: 在 IntelliJ IDEA 中开始使用 Kotlin/Wasm)

> Kotlin/Wasm 是一个[实验性](components-stability.md)功能。它可能随时被删除或更改。它仅在[Kotlin 1.8.20](releases.md)及更高版本中可用。
>
{style="warning"}

本教程演示了如何在 IntelliJ IDEA 中使用 Kotlin/Wasm 应用程序。

## 开始之前

1. 下载并安装最新版本的[IntelliJ IDEA](https://www.jetbrains.com/idea/)。
2. 通过在 IntelliJ IDEA 中选择 **File** | **New** | **Project from Version Control** 来克隆[Kotlin/Wasm 示例](https://github.com/Kotlin/kotlin-wasm-examples/tree/main)仓库。

   您也可以通过命令行克隆它：

   ```bash
   git clone git@github.com:Kotlin/kotlin-wasm-examples.git
   ```

## 运行应用程序

1. 打开 **Gradle** 工具窗口：**View** | **Tool Windows** | **Gradle**。
2. 在 **kotlin-wasm-browser-example** | **Tasks** | **kotlin browser** 中，选择并运行 **wasmJsBrowserRun** 任务。

   ![运行 Gradle 任务](wasm-gradle-task-window.png){width=650}

   或者，您可以在项目目录中的终端中运行以下命令：

   ```bash
   ./gradlew wasmJsBrowserRun -t
   ```

3. 应用程序启动后，在浏览器中打开以下 URL：

   ```bash
   http://localhost:8080/
   ```

   您应该看到 "Hello, World!" 文本：

   ![运行 Kotlin/Wasm 应用程序](wasm-app-run.png){width=650}

### 故障排除 {id=故障排除}

尽管大多数浏览器都支持 WebAssembly，但您需要更新浏览器的设置。

要运行 Kotlin/Wasm 项目，您需要更新目标环境的设置：

<tabs>
<tab title="Chrome">

* 对于版本 109：

  使用 `--js-flags=--experimental-wasm-gc` 命令行参数运行应用程序。

* 对于版本 110 或更高版本：

    1. 在浏览器中转到 `chrome://flags/#enable-webassembly-garbage-collection`。
    2. 启用 **WebAssembly Garbage Collection**。
    3. 重新启动浏览器。

</tab>
<tab title="Firefox">

对于版本 109 或更高版本：

1. 在浏览器中转到 `about:config`。
2. 启用 `javascript.options.wasm_function_references` 和 `javascript.options.wasm_gc` 选项。
3. 重新启动浏览器。

</tab>
<tab title="Edge">

对于版本 109 或更高版本：

使用 `--js-flags=--experimental-wasm-gc` 命令行参数运行应用程序。

</tab>
</tabs>

## 更新您的应用程序

1. 打开 `Simple.kt` 并更新代码：

   ```kotlin
   import kotlinx.browser.document
   import kotlinx.browser.window
   import kotlinx.dom.appendElement
   import kotlinx.dom.appendText
   
   fun main() {
       document.body?.appendText("Hello, ${greet()}!")
   
       document.body?.appendElement("button") {
           this.textContent = "Click me, I'm a button!"
           addEventListener("click") {
               window.setTimeout({
                   window.alert("👋")
                   null
               }, 1000)
           }
       }
   }
   
   fun greet() = "world"
   ```

   此代码向文档添加了一个按钮和一个动作。

2. 再次运行应用程序。一旦应用程序启动，打开浏览器中的以下 URL：

   ```text
   http://localhost:8080
   ```

   您应该在按钮中看到 "Hello, World" 文本：

   ![在浏览器中运行 Kotlin/Wasm 应用程序](wasm-updated-app-run.png){width=650}

3. 单击按钮以查看警报消息：

   ![警报操作](wasm-button-click.png){width=650}

现在，您可以使用在浏览器中运行的 Kotlin/Wasm 代码了！

## 下一步是什么？

尝试从 `kotlin-wasm-examples` 仓库中尝试其他 Kotlin/Wasm 示例：

* [Compose 图像查看器](https://github.com/Kotlin/kotlin-wasm-examples/tree/main/compose-imageviewer)
* [Jetsnack 应用程序](https://github.com/Kotlin/kotlin-wasm-examples/tree/main/compose-jetsnack)
* [Node.js 示例](https://github.com/Kotlin/kotlin-wasm-examples/tree/main/nodejs-example)
* [WASI 示例](https://github.com/Kotlin/kotlin-wasm-examples/tree/main/wasi-example)