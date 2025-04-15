[//]: # (title: Kotlin 跨平台简介)

Kotlin 对跨平台编程的支持是其关键优势之一。
它减少了为 [不同平台](multiplatform-dsl-reference.md#targets) 编写和维护相同代码所花费的时间，同时保留了原生编程的灵活性和优势。

![Kotlin Multiplatform](kotlin-multiplatform.svg){width=700}

## 学习关键概念 {id=learn-key-concepts}

Kotlin 跨平台允许你在不同平台之间共享代码，无论是移动端、Web 还是桌面端。  
代码的编译目标由 _targets_ 列表定义。

每个目标都有一个对应的 *源代码集*，它表示一组具有自身依赖项和编译器选项的源文件。  
例如，针对 JVM 的平台特定源代码集 `jvmMain` 可以使用平台特定的库和 API。

为了在目标子集之间共享代码，可以使用中间源代码集。例如，`appleMain` 源代码集代表在所有 Apple 平台之间共享的代码。  
在所有平台之间共享并编译到所有声明目标的代码有一个独立的源代码集 `commonMain`。它不能使用平台特定的 API，但可以利用跨平台库。

在为特定目标编译时，Kotlin 会将通用源代码集、相关中间源代码集以及目标特定源代码集结合起来。

有关此主题的更多详细信息，请参阅：

* [Kotlin 跨平台项目结构基础](multiplatform-discover-project.md)
* [跨平台项目结构的高级概念](multiplatform-advanced-project-structure.md)

## 使用代码共享机制 {id=use-code-sharing-mechanisms}

有时，在相似目标的子集之间共享代码会更加方便。Kotlin 跨平台提供了一种通过 *默认层次结构模板* 简化创建的方法。  
该模板包含一个基于项目中指定目标预定义的中间源代码集列表。

要从共享代码访问平台特定的 API，可以使用 Kotlin 的另一种机制——*预期声明和实际声明*。  
通过这种方式，你可以在通用代码中声明 `expect` 一个平台特定的 API，但为每个目标平台提供单独的 `actual` 实现。  
这种机制可以与不同的 Kotlin 概念一起使用，包括函数、类和接口。例如，可以在通用代码中定义一个函数，但使用对应源代码集中平台特定的库提供其实现。

有关此主题的更多详细信息，请参阅：

* [在平台上共享代码](multiplatform-share-on-platforms.md)
* [预期声明和实际声明](multiplatform-expect-actual.md)
* [层次化项目结构](multiplatform-hierarchy.md)

## 添加依赖 {id=add-dependencies}

Kotlin 跨平台项目可以依赖外部库和其他跨平台项目。对于通用代码，你可以在通用源代码集中添加对跨平台库的依赖。
Kotlin 会自动解析并将适当的平台特定部分添加到其他源代码集中。如果只需要平台特定的 API，可以将依赖添加到相应的源代码集中。

将 Android 特定的依赖添加到 Kotlin 跨平台项目中，类似于在纯 Android 项目中添加依赖。
在处理 iOS 特定的依赖时，你可以无缝集成 Apple SDK 框架，无需额外配置。对于外部库和框架，Kotlin
提供了与 Objective-C 和 Swift 的互操作性。

有关此主题的更多详细信息，请参阅：

* [添加对跨平台库的依赖](multiplatform-add-dependencies.md)
* [添加对 Android 库的依赖](multiplatform-android-dependencies.md)
* [添加对 iOS 库的依赖](multiplatform-ios-dependencies.md)

## 设置与 iOS 的集成 {id=set-up-integration-with-ios}

如果你的跨平台项目针对 iOS，你可以将 Kotlin 跨平台共享模块与 iOS 应用进行集成。

为此，你需要生成一个 iOS 框架，然后将其作为本地或远程依赖项添加到 iOS 项目中：

* **本地集成**：通过特殊脚本将跨平台项目与 Xcode 项目直接连接，或者在涉及本地 Pod 依赖项的设置中使用 CocoaPods 依赖管理器。
* **远程集成**：使用 XCFrameworks 设置 SPM 依赖项，或通过 CocoaPods 分发共享模块。

有关此主题的更多细节，请参见 [iOS 集成方法](multiplatform-ios-integration-overview.md)。

## 配置编译 {id=configure-compilations}

每个目标可以有多个编译，用于不同的目的，通常是生产或测试，但你也可以定义自定义编译。

使用 Kotlin 跨平台，你可以配置项目中的所有编译，设置目标中的特定编译，甚至创建单独的编译。
在配置编译时，你可以修改编译器选项、管理依赖关系或配置与原生语言的互操作性。

有关此主题的更多详细信息，请参阅 [配置编译](multiplatform-configure-compilations.md)。

## 构建最终二进制文件 {id=build-final-binaries}

默认情况下，目标会被编译成 `.klib` 工件，这可以被 Kotlin/Native 作为依赖使用，但不能执行或作为原生库使用。
然而，Kotlin 跨平台提供了额外的机制来构建最终的原生二进制文件。

你可以创建可执行的二进制文件、共享库和静态库，或 Objective-C 框架，每种都可以为不同的构建类型进行配置。
Kotlin 还提供了一种方法来构建通用（fat）框架和 XCFrameworks，以便进行 iOS 集成。

有关此主题的更多详细信息，请参阅 [构建原生二进制文件](multiplatform-build-native-binaries.md)。

## 创建跨平台库 {id=create-multiplatform-libraries}

你可以创建一个跨平台库，其中包含通用代码及其针对 JVM、Web 和原生平台的特定实现。

发布 Kotlin 跨平台库涉及在 Gradle 构建脚本中进行特定配置。你可以使用 Maven 仓库和 `maven-publish` 插件进行发布。
发布后，跨平台库可以作为依赖项在其他跨平台项目中使用。

有关此主题的更多详细信息，请参阅 [发布跨平台库](multiplatform-publish-lib.md)。

## 参考 {id=reference}

* [Kotlin 跨平台 Gradle 插件的 DSL 参考](multiplatform-dsl-reference.md)
* [Kotlin 跨平台兼容性指南](multiplatform-compatibility-guide.md)  