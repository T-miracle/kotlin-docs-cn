[//]: # (title: 开始使用 Kotlin/Native)

在本教程中，您将学习如何创建 Kotlin/Native 应用程序。选择最适合您的工具并通过以下方式创建应用：

* **[集成开发环境](#in-ide)**。您可以克隆版本控制系统中的项目模板，并在 IntelliJ IDEA 中使用它。
* **[Gradle 构建系统](#using-gradle)**。为了更好地理解底层工作原理，请手动为您的项目创建构建文件。
* **[命令行工具](#using-the-command-line-compiler)**。您可以使用 Kotlin/Native 编译器（作为标准 Kotlin 发行版的一部分提供），直接在命令行工具中创建应用。

  控制台编译看似简单直接，但对于具有数百个文件和库的大型项目而言并不理想。对于此类项目，我们建议使用集成开发环境或构建系统。

使用 Kotlin/Native，您可以编译到[不同目标平台](native-target-support.md)，包括 Linux、macOS 和 Windows。
虽然可以进行跨平台编译（即使用一个平台为另一个平台编译），但在本教程中，您将定位到与编译平台相同的目标。

> 如果您使用 Mac 并希望为 macOS 或其他 Apple 目标平台创建和运行应用程序，还需要先安装
> [Xcode 命令行工具](https://developer.apple.com/download/)，启动它并接受许可条款。
>
{style="note"}

## 在集成开发环境中 {id=in-ide}

在本节中，您将学习如何使用 IntelliJ IDEA 创建 Kotlin/Native 应用程序。
您可以使用 Community Edition 或 Ultimate Edition。

### 创建项目 {id=create-the-project}

1. 下载并安装最新版本的 [IntelliJ IDEA](https://www.jetbrains.com/idea/)。
2. 克隆 [项目模板](https://github.com/Kotlin/kmp-native-wizard)，在 IntelliJ IDEA
   中选择 **File** | **New** | **Project from Version Control** 并使用以下 URL：

   ```none
   https://github.com/Kotlin/kmp-native-wizard
   ```

3. 打开 `gradle/libs.versions.toml` 文件，这是项目依赖项的版本目录。
   要创建 Kotlin/Native 应用程序，您需要 Kotlin 跨平台 Gradle 插件，其版本与 Kotlin 相同。
   确保使用最新的 Kotlin 版本：

   ```none
   [versions]
   kotlin = "%kotlinVersion%"
   ```

4. 按照提示重新加载 Gradle 文件：

   ![加载 Gradle 变更按钮](load-gradle-changes.png){width=295}

如需了解这些设置的更多信息，请参阅 [跨平台 Gradle DSL 参考文档](multiplatform-dsl-reference.md)。

### 构建并运行应用程序 {id=build-and-run-the-application}

打开 `src/nativeMain/kotlin/` 目录中的 `Main.kt` 文件：

* `src` 目录包含 Kotlin 源代码文件
* `Main.kt` 文件包含使用 [`println()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.io/println.html) 函数打印 "Hello, Kotlin/Native!" 的代码

点击侧边栏的绿色图标运行代码：

![运行应用程序](native-run-gutter.png){width=478}

IntelliJ IDEA 会使用 Gradle 任务运行代码，并在 **Run** 标签页中显示输出结果：

![应用输出](native-output-gutter-1.png){width=331}

首次运行后，IDE 会在顶部创建对应的运行配置：

![Gradle 运行配置](native-run-config.png){width=503}

> IntelliJ IDEA Ultimate 用户可以安装
> [Native Debugging Support](https://plugins.jetbrains.com/plugin/12775-native-debugging-support)
> 插件，该插件支持调试已编译的原生可执行文件，并会为导入的
> Kotlin/Native 项目自动创建运行配置。

你可以[配置 IntelliJ IDEA](https://www.jetbrains.com/help/idea/compiling-applications.html#auto-build) 启用自动构建：

1. 进入 **Settings | Build, Execution, Deployment | Compiler**
2. 在 **Compiler** 页面勾选 **Build project automatically**
3. 应用更改

现在，当您在类文件中进行修改或保存文件时（<shortcut>Ctrl + S</shortcut>/<shortcut>Cmd + S</shortcut>），
IntelliJ IDEA 会自动执行项目的增量构建。

### 更新应用程序 {id=update-the-application}

让我们为应用程序添加一个统计姓名字母数量的功能：

1. 在 `Main.kt` 文件中添加读取输入的代码。使用 [`readln()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.io/readln.html)
   函数读取输入值并赋值给 `name` 变量：

   ```kotlin
   fun main() {
       // 读取输入值
       println("Hello, enter your name:")
       val name = readln()
   }
   ```

2. 要在 Gradle 中运行此应用，需在 `build.gradle.kts` 文件中指定 `System.in` 作为输入源并重新加载 Gradle 变更：

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

3. 去除空格并统计字母数：

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

4. 运行应用程序
5. 输入姓名查看结果：

   ![应用输出](native-output-gutter-2.png){width=422}

现在让我们只统计您姓名中的不重复字母数量：

1. 在 `Main.kt` 文件中为 `String` 声明新的[扩展函数](extensions.md#extension-functions)
   `.countDistinctCharacters()`：

   * 使用 [`.lowercase()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/lowercase.html) 函数将姓名转为小写
   * 使用 [`toList()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/to-list.html) 函数将输入字符串转为字符列表
   * 使用 [`distinct()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/distinct.html) 函数筛选唯一字符
   * 使用 [`count()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/count.html) 函数统计唯一字符数

   ```kotlin
   fun String.countDistinctCharacters() = lowercase().toList().distinct().count()
   ```

2. 使用 `.countDistinctCharacters()` 函数统计姓名中的唯一字母：

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

3. 运行应用程序。
4. 输入您的姓名并查看结果：

   ![应用输出](native-output-gutter-3.png){width=422}

## 使用 Gradle {id=using-gradle}

在本节中，您将学习如何使用 [Gradle](https://gradle.org) 手动创建 Kotlin/Native 应用程序。
这是 Kotlin/Native 和 Kotlin 跨平台项目的默认构建系统，也常用于 Java、
Android 和其他生态系统。

### 创建项目文件 {id=create-project-files}

1. 首先安装兼容版本的 [Gradle](https://gradle.org/install/)。请查看 [兼容性表格](gradle-configure-project.md#apply-the-plugin)
   确认 Kotlin Gradle 插件(KGP)与可用 Gradle 版本的兼容性。
2. 创建空项目目录，在其中创建包含以下内容的 `build.gradle(.kts)` 文件：

   <tabs group="build-script">
   <tab title="Kotlin" group-key="kotlin">

   ```kotlin
   // build.gradle.kts
   plugins {
       kotlin("multiplatform") version "%kotlinVersion%"
   }

   repositories {
       mavenCentral()
   }

   kotlin {
       macosArm64("native") {  // macOS平台
       // linuxArm64("native") // Linux平台
       // mingwX64("native")   // Windows平台
           binaries {
               executable()
           }
       }
   }

   tasks.withType<Wrapper> {
       gradleVersion = "%gradleVersion%"
       distributionType = Wrapper.DistributionType.BIN
   }
   ```

   </tab>
   <tab title="Groovy" group-key="groovy">

   ```groovy
   // build.gradle
   plugins {
       id 'org.jetbrains.kotlin.multiplatform' version '%kotlinVersion%'
   }

   repositories {
       mavenCentral()
   }

   kotlin {
       macosArm64('native') {  // macOS平台
       // linuxArm64('native') // Linux平台
       // mingwX64('native')   // Windows平台
           binaries {
               executable()
           }
       }
   }

   wrapper {
       gradleVersion = '%gradleVersion%'
       distributionType = 'BIN'
   }
   ```

   </tab>
   </tabs>

   您可以使用不同的 [目标平台名称](native-target-support.md)，如 `macosArm64`、`iosArm64`、`linuxArm64`
   和 `mingwX64` 来定义代码编译的目标平台。
   这些目标名称可选择性地接受平台名称作为参数(本例中为 `native`).。
   平台名称用于生成项目中的源路径和任务名称。

3. 在项目目录中创建空的 `settings.gradle(.kts)` 文件。
4. 创建 `src/nativeMain/kotlin` 目录，并在其中放置包含以下内容的 `hello.kt` 文件：

   ```kotlin
   fun main() {
       println("Hello, Kotlin/Native!")
   }
   ```

按照约定，所有源代码都应位于 `src/<目标名称>[Main|Test]/kotlin` 目录中，其中 `Main`
用于源代码，`Test` 用于测试代码。`<目标名称>` 对应构建文件中指定的目标平台(本例中为 `native`)。

### 构建并运行项目 {id=build-and-run-the-project}

1. 从项目根目录执行构建命令：

   ```bash
   ./gradlew nativeBinaries
   ```

   该命令会在 `build/bin/native` 目录下生成两个子目录：`debugExecutable` 和
   `releaseExecutable`，其中包含相应的二进制文件。

   默认情况下，二进制文件名与项目目录名相同。

2. 运行以下命令执行项目：

   ```bash
   build/bin/native/debugExecutable/<项目名称>.kexe
   ```

终端将输出 "Hello, Kotlin/Native!"。

### 在 IDE 中打开项目 {id=open-the-project-in-ide}

现在您可以在任何支持 Gradle 的 IDE 中打开项目。如果使用 IntelliJ IDEA：

1. 选择 **File** | **Open**
2. 选择项目目录并点击 **Open**
   IntelliJ IDEA 会自动检测是否为 Kotlin/Native 项目

如果项目存在问题，IntelliJ IDEA 会在 **Build** 标签页显示错误信息

## 使用命令行编译器 {id=using-the-command-line-compiler}

在本节中，您将学习如何使用命令行工具中的 Kotlin 编译器创建 Kotlin/Native 应用程序

### 下载并安装编译器 {id=download-and-install-the-compiler}

要安装编译器：

1. 访问 Kotlin 的 [GitHub 发布页面](%kotlinLatestUrl%)
2. 查找名称中包含 `kotlin-native` 的文件，下载适合您操作系统的版本，例如 `kotlin-native-prebuilt-linux-x86_64-2.0.21.tar.gz`
3. 将压缩包解压到您选择的目录
4. 打开 shell 配置文件，将编译器的 `/bin` 目录路径添加到 `PATH` 环境变量：

   ```bash
   export PATH="/<编译器路径>/kotlin-native/bin:$PATH"
   ```

> 虽然编译器输出没有依赖项或虚拟机要求，但编译器本身需要 Java 1.8 或更高版本的运行时环境。
> 支持 [JDK 8 (JAVA SE 8) 或更高版本](https://www.oracle.com/java/technologies/downloads/)。
>
{style="note"}

### 创建程序 {id=create-the-program}

选择工作目录并创建名为 `hello.kt` 的文件，添加以下代码：

```kotlin
fun main() {
    println("Hello, Kotlin/Native!")
}
```

### 从控制台编译代码 {id=compile-the-code-from-the-console}

使用下载的编译器执行以下命令来编译应用程序：

```bash
kotlinc-native hello.kt -o hello
```

`-o` 选项的值指定输出文件名，此命令将在 macOS 和 Linux 上生成 `hello.kexe` 二进制文件（在 Windows 上生成 `hello.exe`）。

完整选项列表请参阅 [Kotlin 编译器选项](compiler-reference.md)。

### 运行程序 {id=run-the-program}

在命令行工具中，导航至包含二进制文件的目录并执行以下命令来运行程序：

<tabs>
<tab title="macOS 和 Linux">

```none
./hello.kexe
```

</tab>
<tab title="Windows">

```none
./hello.exe
```

</tab>
</tabs>

应用程序会向标准输出打印 "Hello, Kotlin/Native"。

## 下一步 {id=whats-next}

* 完成 [使用 C 语言互操作和 libcurl 创建应用](native-app-with-c-and-libcurl.md) 教程，了解如何创建原生 HTTP 客户端并与 C 语言库交互
* 学习如何 [为实际项目编写 Kotlin/Native 的 Gradle 构建脚本](multiplatform-dsl-reference.md)
* 在 [文档](gradle.md) 中了解更多关于 Gradle 构建系统的内容