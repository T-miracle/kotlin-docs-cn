[//]: # (title: 通过命令行编译器对 Kotlin/Native 进行入门使用)

## 下载并安装编译器 {id=download-and-install-the-compiler}

Kotlin/Native 编译器适用于 macOS、Linux 和 Windows。它作为命令行工具提供，并且是标准 Kotlin 发行版的一部分。
你可以从我们的 [GitHub Releases](%kotlinLatestUrl%) 页面下载。

编译器支持不同的目标，包括 Linux、macOS、iOS 等。
[查看支持的目标列表](native-target-support.md)。
虽然可以进行跨平台编译，这意味着使用一个平台编译到另一个平台，但在这种情况下，你将针对你编译的同一平台进行目标设置。

> 尽管编译器的输出没有任何依赖或虚拟机要求，但编译器本身需要 Java 1.8 或更高版本的运行时环境。
> 它支持 [JDK 8 (JAVA SE 8) 或更高版本](https://www.oracle.com/java/technologies/downloads/)。
>
{style="note"}

要安装编译器，将其归档文件解压到你选择的目录，并将其 `/bin` 目录的路径添加到 `PATH` 环境变量中。

## 编写 “Hello, Kotlin/Native” 程序 {id=write-hello-kotlin-native-program}

该应用程序将在标准输出上打印“Hello, Kotlin/Native”。
选择一个工作目录，并在其中创建一个名为 `hello.kt` 的文件。用以下代码更新它：

```kotlin
fun main() {
  println("Hello, Kotlin/Native!")
}
```

## 从控制台编译代码 {id=compile-the-code-from-the-console}

要编译应用程序，请使用 [下载的](https://github.com/JetBrains/kotlin/releases) 编译器执行以下命令：

```bash
kotlinc-native hello.kt -o hello
```

`-o` 选项的值指定输出文件的名称，因此此调用应生成一个 `hello.kexe`（Linux 和 macOS）或 `hello.exe`（Windows）二进制文件。
有关可用选项的完整列表，请参见 [Kotlin 编译器选项](compiler-reference.md)。

虽然从控制台编译看起来简单明了，但对于包含数百个文件和库的大型项目，它并不适用。
对于实际项目，建议使用 [构建系统](native-gradle.md) 和 [IDE](native-get-started.md)。

## 运行程序 {id=run-the-program}

想要运行程序，请在命令行工具中将路径导航到包含 `hello.kexe`（或 `hello.exe`）文件的目录，并运行 `./hello.kexe`（或 `./hello.exe`）命令。
应用程序应在标准输出上打印“Hello, Kotlin/Native”。
