[//]: # (title: 在平台上共享代码)

使用 Kotlin 跨平台时，你可以利用 Kotlin 提供的机制共享代码：
 
* [在项目中使用的所有平台之间共享代码](#share-code-on-all-platforms)。
用于共享适用于所有平台的通用业务逻辑。
* [在项目中使用的部分平台之间共享代码](#share-code-on-similar-platforms)，但不是所有平台。
你可以通过分层结构在类似平台之间重用代码。

如果需要从共享代码访问特定平台的 API，可以使用 Kotlin 的
[预期声明 和 实际声明](multiplatform-expect-actual.md) 机制。

## 在所有平台之间共享代码 {id=share-code-on-all-platforms}

如果你有适用于所有平台的业务逻辑，不需要为每个平台编写相同的代码——只需将它共享在通用的源代码集中。

![为所有平台共享的代码](flat-structure.svg)

某些源代码集的依赖项是默认设置的，你不需要手动指定任何 `dependsOn` 关系，比如下面的：
* 对于依赖于通用源代码集的所有特定平台的源代码集，如 `jvmMain`、`macosX64Main` 等。
* 在特定目标的 `main` 和 `test` 源代码集之间，例如 `androidMain` 和 `androidUnitTest`。

如果需要从共享代码访问特定平台的 API，可以使用 Kotlin 的 [预期声明 和 实际声明](multiplatform-expect-actual.md) 机制。

## 在类似平台之间共享代码 {id=share-code-on-similar-platforms}

在开发过程中，你经常需要创建多个本地目标，这些目标可能会重用大量的通用逻辑和第三方 API。

例如，在一个典型的针对 iOS 的跨平台项目中，通常有两个与 iOS 相关的目标：
一个用于 iOS ARM64 设备，另一个用于 x64 模拟器。
它们有各自的平台特定源代码集，但实际上，设备和模拟器之间很少需要不同的代码，它们的依赖项也大致相同。
因此，iOS 特定的代码可以在它们之间共享。

显然，在这种设置中，最好为这两个 iOS 目标创建一个共享的源代码集，其中的 Kotlin/Native
代码仍然可以直接调用 iOS 设备和模拟器通用的 API。

在这种情况下，你可以通过 [分层结构](multiplatform-hierarchy.md)
在项目中的本地目标之间共享代码，具体方法如下：

* [使用默认的层次结构模板](multiplatform-hierarchy.md#default-hierarchy-template)
* [手动配置分层结构](multiplatform-hierarchy.md#manual-configuration)

了解更多关于 [在库中共享代码](#share-code-in-libraries) 和 [连接平台特定的库](#connect-platform-specific-libraries) 的信息。

## 在多个库中共享代码 {id=share-code-in-libraries}

得益于分层项目结构，多个库也可以为目标子集提供通用 API。
当 [库发布](multiplatform-publish-lib.md) 时，其中间源代码集的 API 会嵌入到库的构件中，同时包含项目结构信息。
当你使用该库时，项目中的中间源代码集只能访问与每个源代码集的目标相对应的库的 API。

例如，查看 `kotlinx.coroutines` 仓库中的以下源代码集层次结构：

![库的分层结构](lib-hierarchical-structure.svg)

`concurrent` 源代码集声明了函数 `runBlocking`，并且为 JVM 和本地目标编译。
一旦 `kotlinx.coroutines` 库使用分层项目结构更新并发布后，你可以依赖它，并在共享 JVM 和本地目标的源代码集中调用
`runBlocking`，因为它匹配库的 `concurrent` 源代码集的“目标签名”。

## 连接平台特定的库 {id=connect-platform-specific-libraries}

为了共享更多的本地代码而不受平台特定依赖的限制，可以连接 [平台特定的库](native-platform-libs.md)。
Kotlin/Native 自带的库（如 Foundation、UIKit 和 POSIX）默认在共享源代码集中可用。

此外，如果你在项目中使用 [Kotlin CocoaPods Gradle](native-cocoapods.md) 插件，你可以通过
[`cinterop` 机制](native-c-interop.md) 使用第三方本地库。

## 下一步做什么？

* 查看使用 Kotlin 的 [expected 和 actual 声明](multiplatform-expect-actual.md) 的代码共享示例
* 了解更多关于 [分层项目结构](multiplatform-hierarchy.md)
* 查看我们关于 [跨平台项目中源文件命名的建议](coding-conventions.md#source-file-names)
