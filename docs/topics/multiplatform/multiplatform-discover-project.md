[//]: # (title: Kotlin 跨平台项目结构基础)

使用 Kotlin 跨平台 ，你可以在不同平台之间共享代码。
这篇文章解释了 共享代码 的约束，如何区分 共享 和 平台特定 的代码部分，以及如何指定 共享代码 所适用的平台。

你还将学习 Kotlin 跨平台 项目设置的核心概念，例如 公共代码 、目标平台 、平台特定 和 中间 源代码集，以及 测试集成。
这将帮助你在未来设置 跨平台 项目。

这里展示的模型相比于 Kotlin 使用的模型进行了简化。
然而，这个基础模型对于大多数情况应该是足够的。

## 公共代码 {id=common-code}

_公共代码_ 是在不同平台之间共享的 Kotlin 代码。

思考一下下面的简单 "Hello, World" 示例：

```kotlin
fun greeting() {
    println("Hello, Kotlin Multiplatform!")
}
```

在平台之间共享的 Kotlin 代码通常位于 `commonMain` 目录中。
代码文件的位置很重要，因为它会影响这段代码编译到哪些平台。

Kotlin 编译器将源代码作为输入，并生成一组平台特定的二进制文件作为结果。
在编译跨平台项目时，它可以从相同的代码生成多个二进制文件。
例如，编译器可以从同一个 Kotlin 文件生成 JVM 的 `.class` 文件和本地可执行文件：

![公共代码](common-code-diagram.svg){width=700}

并不是所有的 Kotlin 代码都可以编译到所有平台。
Kotlin 编译器会阻止你在公共代码中使用特定于平台的函数或类，因为这些代码不能编译到其他平台。

例如，你不能在公共代码中使用 `java.io.File` 依赖项。
它是 JDK 的一部分，而公共代码也会被编译为本地代码，在本地代码中没有 JDK 类可用：

![未解析的 Java 引用](unresolved-java-reference.png){width=500}

在公共代码中，你可以使用 Kotlin 跨平台库。
这些库提供了一个公共 API，可以在不同平台上以不同方式实现。
在这种情况下，特定于平台的 API 作为额外部分存在，尝试在公共代码中使用这些 API 会导致错误。

例如，`kotlinx.coroutines` 是一个支持所有目标平台的 Kotlin 跨平台库，但它也有一个特定于平台的部分，将 `kotlinx.coroutines`
的并发原语转换为 JDK 的并发原语，比如 `fun CoroutinesDispatcher.asExecutor(): Executor`。
这个 API 的附加部分在 `commonMain` 中不可用。

## 目标 {id=targets}

目标 定义了 Kotlin 编译公共代码的各个平台。这些平台可以是 JVM、JS、Android、iOS 或 Linux。
例如，前面的示例将公共代码编译到了 JVM 和本地目标。

_Kotlin 目标_ 是描述编译目标的标识符。
它定义了生成的二进制文件的格式、可用的语言构造以及允许的依赖项。

> 目标也可以称为平台。请参阅完整的 [支持的目标列表](multiplatform-dsl-reference.md#targets)。
>
> {style="note"}

你应该首先在 Kotlin 中 _声明_ 一个目标，以指示编译代码到特定的目标平台。
在 Gradle 中，你可以在 `kotlin {}` 块中使用预定义的 DSL 调用来声明目标：

```kotlin
kotlin {
    jvm() // 声明一个 JVM 目标
    iosArm64() // 声明一个对应于 64 位 iPhone 的目标
}
```

通过这个方式，每个跨平台项目就定义了一组支持的目标。
有关在构建脚本中声明目标的更多信息，请参阅 [分层项目结构](multiplatform-hierarchy.md) 部分。

声明了 `jvm` 和 `iosArm64` 目标后，`commonMain` 中的公共代码将被编译到这些目标：

![目标](target-diagram.svg){width=700}

要理解哪些代码会被编译到特定目标，你可以将目标视为附加在 Kotlin 源文件上的标签。
Kotlin 使用这些标签来确定如何编译你的代码、生成哪些二进制文件以及代码中允许使用哪些语言构造和依赖项。

如果你还想将 `greeting.kt` 文件编译成 `.js` 文件，你只需声明 JS 目标。
然后，`commonMain` 中的代码将获得一个额外的 `js` 标签，对应于 JS 目标，这将指示 Kotlin 生成 `.js` 文件：

![目标标签](target-labels-diagram.svg){width=700}

这就是 Kotlin 编译器如何处理 编译到所有声明目标的公共代码 的方式。
请参阅 [源代码集](#source-sets) 了解如何编写特定于平台的代码。

## 源代码集 {id=source-sets}

_Kotlin 源代码集_ 是一组源文件，具有自己的目标、依赖项和编译选项。
这是跨平台项目中共享代码的主要方式。

在跨平台项目中，每个源代码集：

* 有一个在给定项目中唯一的名称。
* 包含一组源文件和资源，通常存储在以源代码集名称命名的目录中。
* 指定一组目标，将该源代码集中的代码编译到这些目标中。这些目标会影响该源代码集中可用的语言构造和依赖项。
* 定义自己的依赖项和编译选项。

Kotlin 提供了一些预定义的源代码集。
其中之一是 `commonMain`，它在所有跨平台项目中存在，并编译到所有声明的目标。

在 Kotlin 跨平台项目中，你可以通过 `src` 目录中的目录来操作源代码集。
例如，一个包含 `commonMain`、`iosMain` 和 `jvmMain` 源代码集的项目具有以下结构：

![共享源代码](src-directory-diagram.png){width=350}

在 Gradle 脚本中，你可以在 `kotlin.sourceSets {}` 块中通过名称访问源代码集：

```kotlin
kotlin {
    // 目标声明：
    // …

    // 源代码集声明：
    sourceSets {
        commonMain {
            // 配置 commonMain 源集
        }
    }
}
```

除了 `commonMain`，其他源代码集可以是平台特定的或中间的。

### 平台特定源代码集 {id=platform-specific-source-sets}

虽然使用公共代码很方便，但并不总是可行。
`commonMain` 中的代码会编译到所有声明的目标，但 Kotlin 不允许在其中使用任何平台特定的 API。

在一个具有本地和 JS 目标的跨平台项目中，`commonMain` 中的以下代码不能编译：

```kotlin
// commonMain/kotlin/common.kt
// 在公共代码中不能编译
fun greeting() {
    java.io.File("greeting.txt").writeText("Hello, Multiplatform!")
}
```

作为解决方案，Kotlin 创建了平台特定的源代码集，也称为平台源代码集。
每个目标都有一个对应的平台源代码集，只为该目标进行编译。
例如，`jvm` 目标有一个对应的 `jvmMain` 源代码集，只编译到 JVM。
Kotlin 允许在这些源代码集中使用特定于平台的依赖项，例如在 `jvmMain` 中使用 JDK：

```kotlin
// jvmMain/kotlin/jvm.kt
// 你可以在 `jvmMain` 源代码集中使用 Java 依赖项
fun jvmGreeting() {
    java.io.File("greeting.txt").writeText("Hello, Multiplatform!")
}
```

### 编译到特定目标 {id=compilation-to-a-specific-target}

编译到特定目标涉及多个源代码集。
当 Kotlin 将跨平台项目编译到特定目标时，它会收集所有标记为该目标的源代码集，并从中生成二进制文件。

考虑一个包含 `jvm`、`iosArm64` 和 `js` 目标的示例。
Kotlin 为公共代码创建 `commonMain` 源代码集，并为特定目标创建相应的 `jvmMain`、`iosArm64Main` 和 `jsMain` 源代码集：

![编译到特定目标](specific-target-diagram.svg){width=700}

在编译到 JVM 时，Kotlin 选择所有标记为 "JVM" 的源代码集，即 `jvmMain` 和 `commonMain`。
然后，它将这些源代码集一起编译为 JVM 类文件：

![编译到 JVM](compilation-jvm-diagram.svg){width=700}

由于 Kotlin 将 `commonMain` 和 `jvmMain` 一起编译，生成的二进制文件包含来自 `commonMain` 和 `jvmMain` 的声明。

在处理跨平台项目时，请记住：

* 如果你希望 Kotlin 将代码编译到特定平台，请声明相应的目标。
* 要选择存储代码的目录或源文件，首先确定你希望在哪些目标之间共享代码：

  * 如果代码在所有目标之间共享，它应该在 `commonMain` 中声明。
  * 如果代码仅用于一个目标，它应该在该目标的特定平台源代码集中定义
    （例如，JVM 的 `jvmMain`）。
* 在特定平台源代码集中编写的代码可以访问公共源代码集中的声明。
  例如，`jvmMain` 中的代码可以使用 `commonMain` 中的代码。
  然而，反之则不行：`commonMain` 不能使用 `jvmMain` 中的代码。
* 在特定平台源代码集中编写的代码可以使用对应的平台依赖项。
  例如，`jvmMain` 中的代码可以使用仅限 Java 的库，如
  [Guava](https://github.com/google/guava) 或 [Spring](https://spring.io/)。

### 中间源代码集 {id=intermediate-source-sets}

简单的跨平台项目通常只有公共代码和特定于平台的代码。
`commonMain` 源代码集表示在所有声明的目标之间共享的公共代码。
特定于平台的源代码集，如 `jvmMain`，表示仅编译到相应目标的特定于平台的代码。

在实践中，你通常需要更细粒度的代码共享。

考虑一个示例，你需要支持所有现代的 Apple 设备和 Android 设备：

```kotlin
kotlin {
    androidTarget()
    iosArm64()   // 64 位 iPhone 设备
    macosArm64() // 现代 Apple Silicon 版 Mac
    watchosX64() // 现代 64 位 Apple Watch 设备
    tvosArm64()  // 现代 Apple TV 设备  
}
```

你需要一个源代码集来添加一个为所有 Apple 设备生成 UUID 的函数：

```kotlin
import platform.Foundation.NSUUID

fun randomUuidString(): String {
    // 你需要访问 Apple 特定的 API
    return NSUUID().UUIDString()
}
```

你不能将这个函数添加到 `commonMain` 中。`commonMain` 会编译到所有声明的目标，包括 Android，但
`platform.Foundation.NSUUID` 是一个特定于 Apple 的 API，在 Android 上不可用。
如果你尝试在 `commonMain` 中引用 `NSUUID`，Kotlin 会显示错误。

你可以将这段代码复制并粘贴到每个特定于 Apple 的源代码集中：
`iosArm64Main`、`macosArm64Main`、`watchosX64Main` 和 `tvosArm64Main`。
但这种方法不推荐，因为代码重复容易出错。

为了解决这个问题，你可以使用 _中间源代码集_。
中间源代码集是一个 Kotlin 源代码集，它编译到项目中的部分目标，但不是全部目标。
你也可以将中间源代码集称为分层源代码集或简单的层级。

Kotlin 默认创建一些中间源代码集。
在这个特定的案例中，生成的项目结构将如下所示：

![中间源代码集](intermediate-source-sets-diagram.svg){width=700}

这里，底部的多彩块是特定于平台的源代码集。为了清晰起见，目标标签已被省略。

`appleMain` 块是 Kotlin 为共享编译到 Apple 特定目标的代码而创建的中间源代码集。
`appleMain` 源代码集仅编译到 Apple 目标。
因此，Kotlin 允许在 `appleMain` 中使用 Apple 特定的 API，你可以在这里添加 `randomUUID()` 函数。

> 请参阅 [分层项目结构](multiplatform-hierarchy.md) 以查找
> Kotlin 默认创建并设置的所有中间源代码集，并了解如果
> Kotlin 默认不提供你需要的中间源代码集，你应该怎么做。
>
{style="tip"}

在编译到特定目标时，Kotlin 会获取所有标记为该目标的源代码集，包括中间源代码集。
因此，在编译到 `iosArm64` 平台目标时，`commonMain`、`appleMain` 和 `iosArm64Main` 源代码集中的所有代码都会被合并：

![本地可执行文件](native-executables-diagram.svg){width=700}

> 如果某些源代码集中没有源文件，这是可以的。
> 例如，在 iOS 开发中，通常不需要提供仅针对 iOS 设备但不针对 iOS 模拟器的代码。因此，`iosArm64Main` 很少使用。
>
{style="tip"}

#### Apple 设备和模拟器目标 {id=apple-device-and-simulator-targets collapsible="true" collapsible="true"}

当你使用 Kotlin Multiplatform 开发 iOS 移动应用程序时，通常使用 `iosMain` 源代码集。
虽然你可能认为这是 `ios` 目标的特定平台源代码集，但实际上没有单一的 `ios` 目标。
大多数移动项目至少需要两个目标：

* **设备目标** 用于生成可以在 iOS 设备上执行的二进制文件。
  目前 iOS 只有一个设备目标：`iosArm64`。
* **模拟器目标** 用于生成在你的机器上启动的 iOS 模拟器的二进制文件。
  如果你有一台 Apple silicon Mac 电脑，选择 `iosSimulatorArm64` 作为模拟器目标。
  如果你有一台基于 Intel 的 Mac 电脑，使用 `iosX64`。

如果你只声明 `iosArm64` 设备目标，你将无法在本地机器上运行和调试你的应用程序和测试。

像 `iosArm64Main`、`iosSimulatorArm64Main` 和 `iosX64Main`
这样的特定平台源代码集通常是空的，因为 iOS 设备和模拟器的 Kotlin 代码通常是相同的。
你可以只使用 `iosMain` 中间源代码集来在它们之间共享代码。

同样适用于其他非 Mac 的 Apple 目标。
例如，如果你有 Apple TV 的 `tvosArm64` 设备目标，以及在 Apple silicon 和基于 Intel 设备上的
Apple TV 模拟器的 `tvosSimulatorArm64` 和 `tvosX64` 模拟器目标，你可以使用 `tvosMain` 中间源代码集来处理所有这些目标。

### 测试集成 {id=integration-with-tests}

实际项目除了主要生产代码外还需要测试。
这就是为什么默认创建的所有源代码集都带有 `Main` 和 `Test` 后缀。
`Main` 包含生产代码，而 `Test` 包含该代码的测试。
它们之间的连接是自动建立的，测试可以直接使用 `Main` 代码提供的 API 而无需额外配置。

`Test` 对应的源代码集与 `Main` 类似。
例如，`commonTest` 是 `commonMain` 的对应项，编译到所有声明的目标，允许编写通用测试。
特定于平台的测试源代码集，如 `jvmTest`，用于编写特定于平台的测试，例如特定于 JVM 或需要
JVM API 的测试。

除了拥有用于编写通用测试的源代码集外，你还需要一个跨平台测试框架。
Kotlin 提供了默认的 [`kotlin.test`](https://kotlinlang.org/api/latest/kotlin.test/) 库，其中包含
`@kotlin.Test` 注解和各种断言方法，如 `assertEquals` 和 `assertTrue`。

你可以像在各自的源代码集中编写主代码一样，编写特定于平台的测试。
对于每个源代码集，你可以有特定于平台的依赖，例如 JVM 的 `JUnit` 和 iOS 的 `XCTest`。
要运行特定目标的测试，请使用 `<targetName>Test` 任务。

了解如何创建和运行多平台应用程序的测试，可以参考
[Test your multiplatform app tutorial](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-run-tests.html)。

## 接下来做什么？

* [了解更多关于在 Gradle 脚本中声明和使用预定义源集](multiplatform-hierarchy.md)
* [探索跨平台项目结构的高级概念](multiplatform-advanced-project-structure.md)
* [了解更多关于目标编译和创建自定义编译的内容](multiplatform-configure-compilations.md)
