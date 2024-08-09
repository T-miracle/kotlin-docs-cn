[//]: # (title: 跨平台项目结构的先进概念)

[//]: # (TODO 等待校对)

这篇文章解释了 Kotlin 跨平台项目结构的高级概念，以及它们如何映射到 Gradle 的实现。
如果你需要处理 Gradle 构建的低级抽象（如配置、任务、发布等）或创建用于
Kotlin 跨平台构建的 Gradle 插件，这些信息将非常有用。

如果你有以下需求，这篇文章将对你有所帮助：

* 需要在 Kotlin 未创建源代码集的一组目标之间共享代码。
* 想要为 Kotlin 跨平台构建创建 Gradle 插件，或需要处理 Gradle
  构建的低级抽象，例如配置、任务、发布等。

关于跨平台项目中的依赖管理，一个关键的理解点是
Gradle 风格的项目或库依赖与 Kotlin 特有的源代码集之间的 `dependsOn` 关系之间的区别：

* `dependsOn` 是通用源代码集与平台特定源代码集之间的关系，它启用了 [源代码集层次结构](#dependson-and-source-set-hierarchies)
  并在跨平台项目中共享代码。
  对于默认的源代码集，层次结构会自动管理，但在特定情况下你可能需要更改它。
* 库和项目依赖通常按常规方式工作，但为了在跨平台项目中正确管理它们，你需要了解
  [Gradle 依赖项是如何解析的](#dependencies-on-other-libraries-or-projects)，以便将它们转化为用于编译的细粒度
  **源代码集 → 源代码集** 依赖项。

> 在深入高级概念之前，我们建议先学习 [跨平台项目结构的基础知识](multiplatform-discover-project.md)。
>
{style="tip"}

## `dependsOn` 和 源代码集层次结构 {id="dependson-and-source-set-hierarchies"}

通常，你会处理 _依赖项_，而不是 _`dependsOn`_ 关系。
然而，研究 `dependsOn` 对于理解 Kotlin 跨平台项目的底层工作原理至关重要。

`dependsOn` 是两个 Kotlin 源代码集之间特有的关系。
它可以是通用源代码集与平台特定源代码集之间的连接，例如，当 `jvmMain` 源代码集依赖于 `commonMain`，`iosArm64Main`
依赖于 `iosMain`，等等。

考虑一个包含 Kotlin 源代码集 `A` 和 `B` 的一般示例。表达式 `A.dependsOn(B)` 告诉 Kotlin：

1. `A` 可以访问 `B` 的 API，包括内部声明。
2. `A` 可以为 `B` 中的预期声明提供实际实现。这是一个必要且充分的条件，因为
   `A` 只有在 `A.dependsOn(B)`（直接或间接）时才能为 `B` 提供 `actuals`。
3. `B` 应该编译到 `A` 编译的所有目标，以及它自己的目标。
4. `A` 继承 `B` 的所有常规依赖项。

`dependsOn` 关系创建了一个类似树状结构的源代码集层次结构。
下面是一个典型的移动开发项目示例，其中包含 `androidTarget`、`iosArm64`（iPhone 设备）和
`iosSimulatorArm64`（Apple Silicon Mac 的 iPhone 模拟器）：

![DependsOn 树结构](dependson-tree-diagram.svg){width=700}

箭头表示 `dependsOn` 关系。
这些关系在平台二进制文件的编译过程中得以保留。
这就是 Kotlin 理解 `iosMain` 可以访问 `commonMain` 的 API，但不能访问 `iosArm64Main` 的原因：

![编译过程中的 DependsOn 关系](dependson-relations-diagram.svg){width=700}

`dependsOn` 关系通过 `KotlinSourceSet.dependsOn(KotlinSourceSet)` 调用进行配置，例如：

```kotlin
kotlin {
    // 目标声明
    sourceSets {
        // 配置 dependsOn 关系的示例
        iosArm64Main.dependsOn(commonMain)
    }
}
```

* 此示例展示了如何在构建脚本中定义 `dependsOn` 关系。
  不过，Kotlin Gradle 插件会默认创建源代码集并设置这些关系，因此你不需要手动执行此操作。
* `dependsOn` 关系是在构建脚本中的 `dependencies {}` 块之外单独声明的。
  这是因为 `dependsOn` 不是常规依赖项，而是 Kotlin 源代码集之间的一种特定关系，用于在不同目标之间共享代码。

你不能使用 `dependsOn` 来声明对已发布库或其他 Gradle 项目的常规依赖。
例如，你不能将 `commonMain` 设置为依赖于 `kotlinx-coroutines-core` 库的 `commonMain`，也不能调用
`commonTest.dependsOn(commonMain)`。

### 声明自定义源代码集 {id="declaring-custom-source-sets"}

在某些情况下，你可能需要在项目中创建一个自定义的中间源代码集。
考虑一个编译到 JVM、JS 和 Linux 的项目，你希望仅在 JVM 和 JS 之间共享某些源代码。
在这种情况下，你应该为这一对目标找到一个特定的源代码集，如
[跨平台项目结构的基础知识](multiplatform-discover-project.md) 中所述。

Kotlin 不会自动创建这样的源代码集，这意味着你需要使用 `by creating` 构造手动创建它：

```kotlin
kotlin {
    jvm()
    js()
    linuxX64()

    sourceSets {
        // 创建一个名为 "jvmAndJs" 的源代码集
        val jvmAndJsMain by creating {
            // …
        }
    }
}
```

然而，Kotlin 仍然不知道如何处理或编译这个源代码集。
如果你绘制一个图表，这个源代码集将是孤立的，并且不会有任何目标标签：

![缺少 dependsOn 关系](missing-dependson-diagram.svg){width=700}

要修复这个问题，可以通过添加多个 `dependsOn` 关系将 `jvmAndJsMain` 包含到层次结构中：

```kotlin
kotlin {
    jvm()
    js()
    linuxX64()

    sourceSets {
        val jvmAndJsMain by creating {
            // 别忘了添加 dependsOn 到 commonMain
            dependsOn(commonMain.get())
        }

        jvmMain {
            dependsOn(jvmAndJsMain)
        }

        jsMain {
            dependsOn(jvmAndJsMain)
        }
    }
}
```

在这里，`jvmMain.dependsOn(jvmAndJsMain)` 将 JVM 目标添加到 `jvmAndJsMain`，而
`jsMain.dependsOn(jvmAndJsMain)` 则将 JS 目标添加到 `jvmAndJsMain`。

最终的项目结构将如下所示：

![最终项目结构](final-structure-diagram.svg){width=700}

> 手动配置 `dependsOn` 关系会禁用默认层次结构模板的自动应用。
> 请参阅 [附加配置](multiplatform-hierarchy.md#additional-configuration)
> 了解更多此类情况以及如何处理它们。
>
{style="note"}

## 对其他库或项目的依赖关系 {id="dependencies-on-other-libraries-or-projects"}

在跨平台项目中，你可以设置常规依赖项，无论是在已发布的库上还是在另一个 Gradle 项目上。

Kotlin 跨平台通常以典型的 Gradle 方式声明依赖项。与 Gradle 类似，你可以：

* 在构建脚本中使用 `dependencies {}` 块。
* 为依赖项选择适当的范围，例如 `implementation` 或 `api`。
* 引用依赖项时，如果它在存储库中发布，可以指定其坐标，
  例如 `"com.google.guava:guava:32.1.2-jre"`；如果它是同一构建中的一个 Gradle 项目，可以使用其路径，
  例如 `project(":utils:concurrency")`。

在跨平台项目中配置依赖项具有一些特殊特性。每个 Kotlin 源代码集都有自己的 `dependencies {}` 块。
这使得你可以在特定于平台的源代码集中声明特定于平台的依赖项：

```kotlin
kotlin {
    // 目标声明
    sourceSets {
        jvmMain.dependencies {
            // 这是 jvmMain 的依赖项，因此可以添加特定于 JVM 的依赖项
            implementation("com.google.guava:guava:32.1.2-jre")
        }
    }
}
```

通用依赖项更为复杂。考虑一个跨平台项目，该项目声明了对一个跨平台库的依赖，
例如 `kotlinx.coroutines`：

```kotlin
kotlin {
    androidTarget()     // Android
    iosArm64()          // iPhone 设备
    iosSimulatorArm64() // Apple Silicon Mac 上的 iPhone 模拟器

    sourceSets {
        commonMain.dependencies {
            implementation("org.jetbrains.kotlinx:kotlinx-coroutines-core:1.7.3")
        }
    }
}
```

在依赖解析中有三个重要概念：

1. 跨平台依赖会沿着 `dependsOn` 结构传播。
   当你向 `commonMain` 添加一个依赖时，它会自动添加到所有直接或间接声明了 `dependsOn` 关系的源代码集中。

   在这种情况下，依赖项确实被自动添加到所有 `*Main` 源代码集中：`iosMain`、`jvmMain`、
   `iosSimulatorArm64Main` 和 `iosX64Main`。所有这些源代码集都继承了来自
   `commonMain` 源代码集的 `kotlin-coroutines-core` 依赖，因此你不必手动复制和粘贴到所有这些源代码集中：

   ![跨平台依赖的传播](dependency-propagation-diagram.svg){width=700}

   > 传播机制允许你通过选择特定的源代码集来选择接收声明依赖项的范围。
   > 例如，如果你只想在 iOS 上使用 `kotlinx.coroutines`，而不在 Android 上使用，你可以只将此依赖项添加到 `iosMain` 中。
   >
   {style="tip"}

2. _源代码集 → 跨平台库_ 依赖项，如上例中的 `commonMain` 到 `org.jetbrians.kotlinx:kotlinx-coroutines-core:1.7.3`，
   代表了依赖解析的中间状态。解析的最终状态始终由
   _源代码集 → 源代码集_ 依赖项表示。

   > 最终的 _源代码集 → 源代码集_ 依赖项不是 `dependsOn` 关系。
   >
   {style="note"}

   为了推断细粒度的 _源代码集 → 源代码集_ 依赖项，Kotlin 读取与每个跨平台库一起发布的源代码集结构。
   在此步骤之后，每个库将内部表示为其源代码集的集合，而不是整体。
   例如，`kotlinx-coroutines-core` 的示例如下：

   ![源代码集结构的序列化](structure-serialization-diagram.svg){width=700}

3. Kotlin 处理每个依赖关系并将其解析为来自依赖项的源代码集集合。
   集合中的每个依赖源代码集必须具有 _兼容的目标_。
   如果依赖源代码集编译到 _至少相同的目标_，则它与消费者源代码集兼容。

   考虑一个示例，其中示例项目中的 `commonMain` 编译到 `androidTarget`、`iosX64` 和 `iosSimulatorArm64`：

  * 首先，它解析对 `kotlinx-coroutines-core.commonMain` 的依赖。
    这是因为 `kotlinx-coroutines-core` 编译到所有可能的 Kotlin 目标。
    因此，它的 `commonMain` 编译到所有可能的目标，包括所需的 `androidTarget`、`iosX64` 和 `iosSimulatorArm64`。
  * 其次，`commonMain` 依赖于 `kotlinx-coroutines-core.concurrentMain`。
    由于 `kotlinx-coroutines-core` 中的 `concurrentMain` 编译到除 JS
    外的所有目标，它与消费者项目的 `commonMain` 目标匹配。

   然而，像 `iosX64Main` 这样的源代码集与消费者的 `commonMain` 不兼容。
   尽管 `iosX64Main` 编译到 `commonMain` 的目标之一，即 `iosX64`，
   它不编译到 `androidTarget` 或 `iosSimulatorArm64`。

   依赖解析的结果直接影响了 `kotlinx-coroutines-core` 中哪些代码是可见的：

   ![在通用代码中 JVM 特定 API 的错误](dependency-resolution-error.png){width=700}

### 在源代码集之间对齐公共依赖的版本

在 Kotlin 跨平台项目中，公共源代码集会被编译多次以生成 klib，并作为每个配置的
[编译](multiplatform-configure-compilations.md) 的一部分。
为了生成一致的二进制文件，每次编译公共代码时都应使用相同版本的跨平台依赖项。
Kotlin Gradle 插件帮助对齐这些依赖项，确保每个源代码集的实际依赖版本相同。

在上面的示例中，假设你想将 `androidx.navigation:navigation-compose:2.7.7` 依赖项添加到
`androidMain` 源代码集中。你的项目明确为 `commonMain` 源代码集声明了 `kotlinx-coroutines-core:1.7.3`
依赖项，但 Compose Navigation 库版本 2.7.7 需要 Kotlin 协程 1.8.0 或更高版本。

由于 `commonMain` 和 `androidMain` 一起编译，Kotlin Gradle
插件会在两个版本的协程库之间进行选择，并将 `kotlinx-coroutines-core:1.8.0` 应用到 `commonMain` 源代码集中。
但为了确保公共代码在所有配置的目标上都能一致编译，iOS 源代码集也需要限制为相同的依赖版本。
因此，Gradle 也将 `kotlinx.coroutines-*:1.8.0` 依赖项传播到 `iosMain` 源代码集中。

![源代码集之间依赖的对齐](multiplatform-source-set-dependency-alignment.svg){width=700}

依赖项在 `*Main` 源代码集和 [`*Test` 源代码集](multiplatform-discover-project.md#integration-with-tests) 之间分别对齐。
`*Test` 源代码集的 Gradle 配置包含了所有 `*Main` 源代码集的依赖项，但反之不然。
因此，你可以使用较新的库版本来测试项目，而不会影响主代码。

例如，你在 `*Main` 源代码集中使用了 Kotlin 协程 1.7.3 依赖项，并将其传播到项目中的每个源代码集中。
然而，在 `iosTest` 源代码集中，你决定将版本升级到 1.8.0 以测试新的库版本。
根据相同的算法，这个依赖项将被传播到整个 `*Test` 源代码集树中，因此每个 `*Test` 源代码集都将使用
`kotlinx.coroutines-*:1.8.0` 依赖项进行编译。

![测试源代码集与主源代码集分开解析依赖](test-main-source-set-dependency-alignment.svg)

## 编译

与单平台项目不同，Kotlin 跨平台项目需要多次启动编译器来构建所有的工件。
每次编译器启动称为 _Kotlin 编译_。

例如，以下是生成 iPhone 设备二进制文件的 Kotlin 编译过程：

![Kotlin 编译用于 iOS](ios-compilation-diagram.svg){width=700}

Kotlin 编译被归类到目标下。默认情况下，Kotlin 为每个目标创建两个编译：一个是用于生产源代码的
`main` 编译，另一个是用于测试源代码的 `test` 编译。

在构建脚本中，编译的访问方式类似。你首先选择一个 Kotlin 目标，然后访问内部的
`compilations` 容器，最后通过名称选择所需的编译：

```kotlin
kotlin {
    // 声明并配置 JVM 目标
    jvm {
        val mainCompilation: KotlinJvmCompilation = compilations.getByName("main")
    }
}
```
