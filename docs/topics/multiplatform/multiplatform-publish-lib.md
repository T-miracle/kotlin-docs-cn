[//]: # (title: 设置跨平台库的发布)

你可以将你的跨平台库发布到不同的位置：

* [发布到本地 Maven 仓库](#publishing-to-a-local-maven-repository)
* 发布到 Maven Central 仓库。了解如何设置帐户凭证、定制库元数据，并在 [我们的教程](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-publish-libraries.html) 中配置发布插件。
* 发布到 GitHub 仓库。更多信息，请参见 GitHub 的 [GitHub 包](https://docs.github.com/en/packages) 文档。

## 发布到本地 Maven 仓库 {id=publishing-to-a-local-maven-repository}

你可以使用 `maven-publish` Gradle 插件将一个跨平台库发布到本地 Maven 仓库：

1. 在 `shared/build.gradle.kts` 文件中，添加 [`maven-publish` Gradle 插件](https://docs.gradle.org/current/userguide/publishing_maven.html)。
2. 为库指定 `group` 和 `version`，以及应该发布到的 [repositories](https://docs.gradle.org/current/userguide/publishing_maven.html#publishing_maven:repositories)：

   ```kotlin
   plugins {
       // ...
       id("maven-publish")
   }

   group = "com.example"
   version = "1.0"

   publishing {
       repositories {
           maven {
               //...
           }
       }
   }
   ```

当与 `maven-publish` 一起使用时，Kotlin 插件会自动为每个可以在当前主机上构建的目标创建发布，
除了 Android 目标，它需要 [额外的步骤来配置发布](#publish-an-android-library)。

## 发布结构 {id=structure-of-publications}

当与 `maven-publish` 一起使用时，Kotlin 插件会自动为每个可以在当前主机上构建的目标创建发布，除了 Android 目标，它需要 [额外的步骤来配置发布](#publish-an-android-library)。
跨平台库的发布包括一个额外的 _root_ 发布 `kotlinMultiplatform`，它代表整个库，并在作为依赖项添加到公共源代码集时，自动解析为适当的特定平台工件。  
了解更多关于 [添加依赖项](multiplatform-add-dependencies.md) 的信息。

这个 `kotlinMultiplatform` 发布包括元数据构件，并将其他发布作为其变体引用。

一些仓库，例如 Maven Central，要求根模块包含一个没有分类符的 JAR 工件，例如 `kotlinMultiplatform-1.0.jar`。  
Kotlin 跨平台插件会自动生成所需的工件，并包含嵌入的元数据工件。  
这意味着你不需要为库的根模块添加一个空的工件来满足仓库的要求。

> Learn more about JAR artifact generation with [Gradle](multiplatform-configure-compilations.md#compilation-for-jvm)
> and [Maven](maven.md#create-jar-file) build systems.
>
{style="tip"}

`kotlinMultiplatform` 发布可能还需要源代码和文档构件，如果仓库有这样的要求。
在这种情况下，可以在发布范围内使用 [`artifact(...)`](https://docs.gradle.org/current/javadoc/org/gradle/api/publish/maven/MavenPublication.html#artifact-java.lang.Object-)
添加这些构件。

## 主机要求 {id=host-requirements}

Kotlin/Native 支持交叉编译，允许任何主机生成必要的 `.klib` 工件。  
然而，仍然有一些细节需要你注意。

### 针对 Apple 目标的编译 {id=compilation-for-apple-targets}
<primary-label ref="experimental-opt-in"/>

要为具有 Apple 目标的项目生成工件，通常需要一台 Apple 机器。  
然而，如果你想使用其他主机，可以在你的 `gradle.properties` 文件中设置这个选项：

```none
kotlin.native.enableKlibsCrossCompilation=true
```

交叉编译目前是实验性的，并且有一些限制。如果：

* 你的库有 [cinterop 依赖](native-c-interop.md)。
* 你的项目中设置了 [CocoaPods 集成](native-cocoapods.md)。
* 你需要为 Apple 目标构建或测试 [最终二进制文件](multiplatform-build-native-binaries.md)。

你仍然需要使用一台 Mac 机器。

### 重复发布 {id=duplicating-publications}

为了避免发布过程中的问题，建议从单一主机发布所有工件，以避免在仓库中重复发布。
例如，Maven Central 明确禁止重复发布，并会导致构建失败。
<!-- TBD: add the actual error -->

## 发布一个 Android 库 {id=publish-an-android-library}

要发布一个 Android 库，你需要提供额外的配置。

默认情况下，Android 库不会发布任何构件。要发布由一组 Android [构建变体](https://developer.android.com/build/build-variants) 生成的工件，  
请在 `shared/build.gradle.kts` 文件中的 Android 目标块中指定变体名称：

```kotlin
kotlin {
    androidTarget {
        publishLibraryVariants("release")
    }
}

```

这个示例适用于没有 [产品变种](https://developer.android.com/build/build-variants#product-flavors) 的 Android 库。
对于带有产品变种的库，变体名称还会包含这些风味，比如 `fooBarDebug` 或 `fooBarRelease`。

默认的发布设置如下：
* 如果发布的变体具有相同的构建类型（例如，全部为 `release` 或 `debug`），它们将与任何消费者构建类型兼容。
* 如果发布的变体具有不同的构建类型，则只有 `release` 变体与未包含在发布变体中的消费者构建类型兼容。
  所有其他变体（如 `debug`）将只匹配消费者端的相同构建类型，除非消费者项目指定了
  [匹配回退](https://developer.android.com/reference/tools/gradle-api/4.2/com/android/build/api/dsl/BuildType)。

如果希望每个已发布的 Android 变体仅与使用相同构建类型的库消费者兼容，
请设置此 Gradle 属性：`kotlin.android.buildTypeAttribute.keep=true`。

你还可以按产品变种分组发布变体，使得不同构建类型的输出放在一个模块中，并且构建类型成为构件的分类符
（`release` 构建类型仍然以无分类符发布）。此模式默认情况下是禁用的，可以在 `shared/build.gradle.kts` 文件中启用，如下所示：

```kotlin
kotlin {
    androidTarget {
        publishLibraryVariantsGroupedByFlavor = true
    }
}
```

> 不建议发布按产品变种分组的变体，特别是在它们有不同依赖的情况下，因为这些依赖会被合并到一个依赖列表中。
>
{style="note"}

## 禁用源代码发布 {id=disable-sources-publication}

默认情况下，Kotlin Multiplatform Gradle 插件会为所有指定的目标发布源代码。
然而，你可以在 `shared/build.gradle.kts` 文件中使用 `withSourcesJar()` API 配置并禁用源代码发布：

* 要禁用所有目标的源代码发布：

  ```kotlin
  kotlin {
      withSourcesJar(publish = false)
  
      jvm()
      linuxX64()
  }
  ```

* 要仅禁用指定目标的源代码发布：

  ```kotlin
  kotlin {
       // 仅禁用 JVM 的源代码发布：
      jvm {
          withSourcesJar(publish = false)
      }
      linuxX64()
  }
  ```

* 要禁用所有目标的源代码发布，除了指定目标：

  ```kotlin
  kotlin {
      // 禁用所有目标的源代码发布，除了 JVM：
      withSourcesJar(publish = false)
  
      jvm {
          withSourcesJar(publish = true)
      }
      linuxX64()
  }
  ```

## 禁用 JVM 环境属性发布 {id=disable-jvm-environment-attribute-publication}

从 Kotlin 2.0.0 开始，Gradle 属性 [`org.gradle.jvm.environment`](https://docs.gradle.org/current/userguide/variant_attributes.html#sub:jvm_default_attributes)
会自动与所有 Kotlin 变体一起发布，以帮助区分 Kotlin Multiplatform 库的 JVM 和 Android 变体。
该属性指示哪个库变体适用于哪个 JVM 环境，Gradle 使用此信息来帮助你的项目进行依赖解析。
目标环境可以是 "android"、"standard-jvm" 或 "no-jvm"。

你可以通过在 `gradle.properties` 文件中添加以下 Gradle 属性来禁用该属性的发布：

```none
kotlin.publishJvmEnvironmentAttribute=false
```

## 推广您的库 {id=promote-your-library}

您的库可以展示在 [JetBrains 搜索平台](https://klibs.io/) 上。
该平台旨在帮助开发者根据目标平台轻松查找 Kotlin 跨平台库。

符合标准的库会被自动收录。有关如何添加您的库的更多信息，请参阅 [常见问题](https://klibs.io/faq)。

## 下一步做什么 {id=whats-next}

* [了解如何将 Kotlin 跨平台库发布到 Maven Central 仓库](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-publish-libraries.html)
* [查看库作者指南，了解为 Kotlin 跨平台设计库的最佳实践和技巧](api-guidelines-build-for-multiplatform.md)