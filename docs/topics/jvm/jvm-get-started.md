[//]: # (title: Create a console app – tutorial)

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

   If you want to create a more complex project that needs further configuration, select Maven or Gradle. For Gradle,
   choose a language for the build script: Kotlin or Groovy.
5. From the **JDK** list, select the [JDK](https://www.oracle.com/java/technologies/downloads/) that you want to use in
   your project.
   * If the JDK is installed on your computer, but not defined in the IDE, select **Add JDK** and specify the path to the
   JDK home directory. 
   * If you don't have the necessary JDK on your computer, select **Download JDK**.

6. 启用 **Add sample code** 选项以创建一个带有 `"Hello World!"` 示例应用程序的文件。

   > 您还可以启用 **Generate code with onboarding tips** 选项，为示例代码添加一些额外的有用注释。
   >
   {style="tip"}

7. 点击 **Create**。

    > If you chose the Gradle build system, you have in your project a build script file: `build.gradle(.kts)`. It includes
    > the `kotlin("jvm")` plugin and dependencies required for your console application. Make sure that you use the latest
    > version of the plugin:
    > 
    > <tabs group="build-script">
    > <tab title="Kotlin" group-key="kotlin">
    > 
    > ```kotlin
    > plugins {
    >     kotlin("jvm") version "%kotlinVersion%"
    >     application
    > }
    > ```
    > 
    > </tab>
    > <tab title="Groovy" group-key="groovy">
    > 
    > ```groovy
    > plugins {
    >     id 'org.jetbrains.kotlin.jvm' version '%kotlinVersion%'
    >     id 'application'
    > }
    > ```
    > 
    > </tab>
    > </tabs>
    > 
    {style="note"}

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

* Take the [Kotlin tour](kotlin-tour-welcome.md) 
* Install the [JetBrains Academy plugin](https://plugins.jetbrains.com/plugin/10081-jetbrains-academy) for IDEA and complete 
  exercises from the [Kotlin Koans course](https://plugins.jetbrains.com/plugin/10081-jetbrains-academy/docs/learner-start-guide.html?section=Kotlin%20Koans)
