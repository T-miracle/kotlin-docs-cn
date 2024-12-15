[//]: # (title: 发布跨平台库)

你可以使用 [`maven-publish` Gradle 插件](https://docs.gradle.org/current/userguide/publishing_maven.html)
将一个跨平台库发布到本地 Maven 仓库。在 `shared/build.gradle.kts` 文件中，指定库的 group、version，以及要发布到的
[repositories（仓库）](https://docs.gradle.org/current/userguide/publishing_maven.html#publishing_maven:repositories)。
该插件会自动创建发布内容。

```kotlin
plugins {
    //...
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

> 你还可以将一个跨平台库发布到 GitHub 仓库。
> 更多信息请参见 GitHub 关于 [GitHub packages（GitHub 软件包）](https://docs.github.com/en/packages) 的文档。
>
{style="tip"}

## 发布结构 {id=structure-of-publications}

在使用 `maven-publish` 时，Kotlin 插件会为每个可以在当前主机上构建的目标自动创建发布内容，但 Android 目标除外，
因为它需要[额外的步骤来配置发布](#publish-an-android-library)。

跨平台库的发布内容中包含一个额外的 _根（root）_ 发布 `kotlinMultiplatform`，
代表整个库，并在被添加为公共源代码集的依赖项时自动解析为适当的特定平台构件。
了解更多关于[添加依赖项](multiplatform-add-dependencies.md)。

这个 `kotlinMultiplatform` 发布包括元数据构件，并将其他发布作为其变体引用。

> 一些仓库（例如 Maven Central）要求根模块包含一个不带分类符的 JAR 构件，例如 `kotlinMultiplatform-1.0.jar`。  
> Kotlin 跨平台插件会自动生成包含嵌入式元数据构件的所需构件。  
> 这意味着你不需要通过向库的根模块添加空构件来定制你的构建以满足仓库的要求。
>
{style="note"}

`kotlinMultiplatform` 发布可能还需要源代码和文档构件，如果仓库有这样的要求。
在这种情况下，可以在发布范围内使用 [`artifact(...)`](https://docs.gradle.org/current/javadoc/org/gradle/api/publish/maven/MavenPublication.html#artifact-java.lang.Object-)
添加这些构件。

## 主机要求 {id=host-requirements}

Kotlin/Native 支持交叉编译，允许任何主机生成所需的 `.klib` 工件。

对于具有 Apple 目标的项目，通常需要一台 Apple 机器来生成工件。  
然而，如果你想使用其他主机，可以在 `gradle.properties` 文件中设置这个
[实验性](components-stability.md#stability-levels-explained) 选项：

```none
kotlin.native.enableKlibsCrossCompilation=true
```

> 要为 Apple 目标构建 [最终二进制文件](multiplatform-build-native-binaries.md)，仍然需要使用 Mac 机器。
>
{style="note"}

为了避免发布过程中的问题，建议从单一主机发布所有工件，以避免在仓库中重复发布。
例如，Maven Central 明确禁止重复发布，并会导致构建失败。
<!-- TBD: add the actual error -->

### 如果你使用 Kotlin 1.7.0 或更早版本 {id=if-you-use-kotlin-170-or-earlier collapsible="true" collapsible="true"}

在 1.7.20 之前，Kotlin/Native 编译器不支持所有交叉编译选项。
如果你使用更早的版本，你可能需要从多个主机发布跨平台项目：
例如，从 Windows 主机编译 Windows 目标，从 Linux 主机编译 Linux 目标，依此类推。
这可能会导致模块的重复发布。避免这种情况的最简单方法是升级到更新版本的 Kotlin，并按照上述方式从单一主机发布。

如果无法升级，为每个目标指定一个主要主机，并在 `shared/build.gradle(.kts)` 文件中检查它：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    jvm()
    js()
    mingwX64()
    linuxX64()
  
    val publicationsFromMainHost =
        listOf(jvm(), js()).map { it.name } + "kotlinMultiplatform"
  
    publishing {
        publications {
            matching { it.name in publicationsFromMainHost }.all {
                val targetPublication = this@all
                tasks.withType<AbstractPublishToMaven>()
                    .matching { it.publication == targetPublication }
                    .configureEach { onlyIf { findProperty("isMainHost") == "true" } }
            }
        }
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    jvm()
    js()
    mingwX64()
    linuxX64()
  
    def publicationsFromMainHost =
        [jvm(), js()].collect { it.name } + "kotlinMultiplatform"
  
    publishing {
        publications {
            matching { it.name in publicationsFromMainHost }.all { targetPublication ->
                tasks.withType(AbstractPublishToMaven)
                    .matching { it.publication == targetPublication }
                    .configureEach { onlyIf { findProperty("isMainHost") == "true" } }
            }
        }
    }
}
```

</tab>
</tabs>

## 发布一个 Android 库 {id=publish-an-android-library}

要发布一个 Android 库，你需要提供额外的配置。

默认情况下，不会发布 Android 库的任何构件。要发布由一组 [Android 变体](https://developer.android.com/studio/build/build-variants)
生成的构件，请在 `shared/build.gradle.kts` 文件中的 Android 目标块中指定变体名称：

```kotlin
kotlin {
    androidTarget {
        publishLibraryVariants("release")
    }
}

```

这个示例适用于没有 [产品变种](https://developer.android.com/studio/build/build-variants#product-flavors) 的 Android 库。
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