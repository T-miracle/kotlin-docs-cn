[//]: # (title: Kotlin/JVM 入门)

本教程演示了如何使用 IntelliJ IDEA 创建一个控制台应用程序。

要开始，请首先下载并安装最新版本的 [IntelliJ IDEA](https://www.jetbrains.com/idea/download/index.html)。

## 创建项目 {id=create-a-project}

1. 在 IntelliJ IDEA 中，选择 **File** | **New** | **Project** 。
2. 在左侧列表中，选择 **Kotlin** 。
3. 为新项目命名，并根据需要更改其位置。

   > 选中 **Create Git repository** 复选框，将新项目置于版本控制下。您可以随时在以后进行此操作。
   >
   {style="tip"}
   
   ![Create a console application](jvm-new-project.png){width=700}

4. 选择 **IntelliJ** 构建系统。它是一个不需要下载额外依赖的原生构建器。

   如果您想创建需要进一步配置的更复杂项目，选择 Maven 或 Gradle。对于 Gradle，选择构建脚本的语言：Kotlin 或 Groovy。
5. 从 **JDK 列表** 中，选择您希望在项目中使用的 [JDK](https://www.oracle.com/java/technologies/downloads/)。
   * 如果 JDK 已安装在您的计算机上，但未在 IDE 中定义，选择 **Add JDK** 并指定 JDK 主目录的路径。
   * 如果您的计算机上没有必要的 JDK，选择 **Download JDK**。

6. 启用 **Add sample code** 选项以创建一个带有 `"Hello World!"` 示例应用程序的文件。

   > 您还可以启用 **Generate code with onboarding tips** 选项，为示例代码添加一些额外的有用注释。
   >
   {style="tip"}

7. 点击 **Create**。

   > 如果您选择了 Gradle 构建系统，您的项目中将包含一个构建脚本文件：`build.gradle(.kts)`。
   > 它包括 `kotlin("jvm")` 插件以及控制台应用程序所需的依赖项。请确保使用插件的最新版本：
   > 
   > ```kotlin
   > plugins {
   >     kotlin("jvm") version "%kotlinVersion%"
   >     application
   > }
   > ```
   > {style="note"}

## 创建应用程序 {id=create-an-application}

1. 打开 `src/main/kotlin` 中的 `Main.kt` 文件。  
   `src` 目录包含 Kotlin 源代码文件和资源。`Main.kt` 文件中包含示例代码，该代码将打印  
   `Hello, Kotlin!`，以及若干行循环迭代器的值。

   ![带有 main 方法的 Main.kt](jvm-main-kt-initial.png){width=700}

2. 修改代码，使其请求你的名字并对你说 `Hello`：

   * 创建一个输入提示，并使用 [`readln()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.io/readln.html) 方法将返回的值赋给 `name` 变量。
   * 使用字符串模板代替拼接，在输出文本中直接在变量名前添加美元符号 `$`，例如 `$name`。

   ```kotlin
   fun main() {
       println("What's your name?")
       val name = readln()
       println("Hello, $name!")
   
       // ...
   }
   ```  

## 运行应用程序 {id=run-the-application}

现在应用程序已准备好运行。最简单的运行方式是点击侧边栏中的绿色 **Run** 图标，并选择 **Run 'MainKt'**。

![运行控制台应用程序](jvm-run-app.png){width=350}

您可以在 **Run** 工具窗口中看到结果。

![Kotlin 运行输出](jvm-output-1.png){width=600}

输入您的名字，接受来自应用程序的问候！

![Kotlin 运行输出](jvm-output-2.png){width=600}

恭喜！您刚刚运行了您的第一个 Kotlin 应用程序。

## 接下来做什么？ {id=whats-next}

一旦您创建了这个应用程序，您可以开始深入学习 Kotlin 语法：

* 添加来自 [Kotlin 示例](https://play.kotlinlang.org/byExample/overview) 的示例代码
* 安装适用于 IDEA 的 [JetBrains Academy 插件](https://plugins.jetbrains.com/plugin/10081-jetbrains-academy) 并完成
  [Kotlin Koans 课程](https://plugins.jetbrains.com/plugin/10081-jetbrains-academy/docs/learner-start-guide.html?section=Kotlin%20Koans) 中的练习