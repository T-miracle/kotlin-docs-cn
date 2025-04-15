[//]: # (title: 使用 IntelliJ IDEA 开始 Kotlin/Native 开发)

本教程演示如何使用 IntelliJ IDEA 创建 Kotlin/Native 应用程序。

开始前，请安装最新版 [IntelliJ IDEA](https://www.jetbrains.com/idea/download/index.html)。
本教程适用于 IntelliJ IDEA Community 版和 Ultimate 版。

> 如果你使用 macOS 并希望创建和运行 iOS 或桌面应用程序，还需
> [安装 Xcode 命令行工具](https://developer.apple.com/download/)。
> 更多信息请参阅 [环境配置](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-setup.html)。
> 
{style="note"}

## 准备工作 {id=before-you-start}

1. 下载并安装最新版 [IntelliJ IDEA](https://www.jetbrains.com/idea/) 及最新 [Kotlin 插件](releases.md)
2. 克隆 [项目模板](https://github.com/Kotlin/kmp-native-wizard)：在 IntelliJ IDEA
   中选择 **File** | **New** | **Project from Version Control**
3. 打开包含项目配置的构建脚本 `build.gradle.kts`。开发 Kotlin/Native 应用需要安装 Kotlin 跨平台 Gradle 插件，请确保使用最新版本：

   ```kotlin
   plugins {
       kotlin("multiplatform") version "%kotlinVersion%"
   }
   ```

4. 按照提示重新加载 Gradle 文件：

   ![加载 Gradle 变更按钮](load-gradle-changes.png){width=295}

> * 查阅 [跨平台 Gradle DSL 参考](multiplatform-dsl-reference.md) 了解这些配置的详细信息
> * 在 [Gradle 文档](gradle.md) 中了解更多关于 Gradle 构建系统的内容
>
{style="tip"}

## 构建并运行应用 {id=build-and-run-the-application}

打开 `src/nativeMain/kotlin/` 目录下的 `Main.kt` 文件，点击编辑器侧边栏的绿色运行按钮执行代码：

![运行应用程序](native-run-gutter.png){width=478}

IntelliJ IDEA 会通过 Gradle 任务执行代码，运行结果将显示在 **Run** 标签页中：

![应用输出](native-output-gutter-1.png){width=331}

首次运行后，IDE 顶部工具栏会显示对应的运行配置：

![Gradle 运行配置](native-run-config.png){width=503}

> IntelliJ IDEA Ultimate 用户可以安装 [Native Debugging Support](https://plugins.jetbrains.com/plugin/12775-native-debugging-support)
> 插件来调试已编译的原生可执行文件，该插件还会为导入的 Kotlin/Native 项目自动创建运行配置。

你可以[配置 IntelliJ IDEA](https://www.jetbrains.com/help/idea/compiling-applications.html#auto-build) 启用自动构建：

1. 进入 **Settings/Preferences | Build, Execution, Deployment | Compiler**
2. 在 **Compiler** 页面勾选 **Build project automatically**
3. 应用更改

现在当你修改类文件或保存文件时（**Ctrl+S**/**Cmd+S**），IntelliJ IDEA 会自动执行增量构建。

## 更新应用程序 {id=update-the-application}

### 统计姓名字母数 {id=count-the-letters-in-your-name}

1. 打开 `src/nativeMain/kotlin` 目录下的 `Main.kt` 文件

   `src` 目录包含 Kotlin 源文件。`Main.kt` 文件中的代码使用
   [`println()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.io/println.html) 函数输出"Hello, Kotlin/Native!"

2. 添加读取输入的代码。使用 [`readln()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.io/readln.html) 函数读取输入值并赋值给 `name` 变量：

   ```kotlin
   fun main() {
       // 读取输入值
       println("Hello, enter your name:")
       val name = readln()
   }
   ```

3. 要在 Gradle 中运行此应用，需在 `build.gradle.kts` 文件中指定 `System.in` 作为输入源并重新加载 Gradle 变更：

   ```kotlin
   kotlin {
       //...
       nativeTarget.apply {
           binaries {
               executable {
                   entryPoint = "main"
                   runTask?.standardInput = System.`in`
               }
           }
       }
       //...
   }
   ```
   {collapsible="true" collapsible="true" collapsed-title="runTask?.standardInput = System.`in`"}

4. 去除空格并统计字母数：
   * 使用 [`replace()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/replace.html) 函数移除姓名中的空格
   * 通过作用域函数 [`let`](scope-functions.md#let) 在对象上下文中执行操作
   * 使用[字符串模板](strings.md#string-templates)将姓名长度插入字符串（语法为 `${it.length}`）
     其中 `it` 是 [lambda 参数](coding-conventions.md#lambda-parameters) 的默认名称

   ```kotlin
   fun main() {
       // 读取输入值
       println("Hello, enter your name:")
       val name = readln()
       // 统计姓名字母数
       name.replace(" ", "").let {
           println("Your name contains ${it.length} letters")
       }
   }
   ```

5. 运行应用程序
6. 输入姓名查看结果：

   ![应用输出](native-output-gutter-2.png){width=422}

### 统计姓名中的唯一字母 {id=count-the-unique-letters-in-your-name}

1. 打开 `src/nativeMain/kotlin` 目录下的 `Main.kt` 文件
2. 为 `String` 声明新的[扩展函数](extensions.md#extension-functions) `countDistinctCharacters()`：

   * 使用 [`lowercase()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/lowercase.html) 函数将姓名转为小写
   * 使用 [`toList()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/to-list.html) 函数将输入字符串转为字符列表
   * 使用 [`distinct()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/distinct.html) 函数筛选唯一字符
   * 使用 [`count()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/count.html) 函数统计唯一字符数

   ```kotlin
   fun String.countDistinctCharacters() = lowercase().toList().distinct().count()
   ```

3. 使用 `countDistinctCharacters()` 函数统计姓名中的唯一字母：

   ```kotlin
   fun String.countDistinctCharacters() = lowercase().toList().distinct().count()

   fun main() {
       // 读取输入值
       println("Hello, enter your name:")
       val name = readln()
       // 统计字母
       name.replace(" ", "").let {
           println("Your name contains ${it.length} letters")
           // 输出唯一字母数
           println("Your name contains ${it.countDistinctCharacters()} unique letters")
       }
   }
   ```

4. 运行应用程序
5. 输入姓名查看结果：

   ![应用输出](native-output-gutter-3.png){width=422}

## 下一步 {id=whats-next}

完成第一个应用程序后，你可以继续学习 Kotlin/Native 的详细教程：
[使用 C 互操作和 libcurl 创建应用](native-app-with-c-and-libcurl.md)，该教程将说明如何创建原生 HTTP
客户端并与 C 语言库进行互操作。
