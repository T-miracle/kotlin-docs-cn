[//]: # (title: 构建最终的原生二进制文件)

默认情况下，Kotlin/Native 目标被编译为 `*.klib` 库构件，该构件可以被 Kotlin/Native
本身作为依赖使用，但不能作为可执行文件或原生库使用。

要声明最终的原生二进制文件，如可执行文件或共享库，请使用原生目标的 `binaries` 属性。
该属性表示为此目标构建的原生二进制文件集合，除了默认的 `*.klib`
构件之外，还提供了一组用于声明和配置它们的方法。

> `kotlin-multiplatform` 插件默认不会创建任何生产环境的二进制文件。
> 默认唯一可用的二进制文件是一个用于运行单元测试的调试测试可执行文件，该文件来自 `test` 编译。
>
{style="note"}

由 Kotlin/Native 编译器生成的二进制文件可能包含第三方代码、数据或衍生作品。
这意味着如果您分发 Kotlin/Native 编译的最终二进制文件，
您应始终在二进制分发中包含必要的[许可证文件](native-binary-licenses.md)。

## 声明二进制文件 {id=declare-binaries}

使用以下工厂方法来声明 `binaries` 集合的元素。

| 工厂方法       | 二进制类型             | 可用目标                                     |
|----------------|------------------------|----------------------------------------------|
| `executable`   | 产品可执行文件          | 所有原生目标                                  |
| `test`         | 测试可执行文件          | 所有原生目标                                  |
| `sharedLib`    | 共享原生库              | 所有原生目标，除了 `WebAssembly`             |
| `staticLib`    | 静态原生库              | 所有原生目标，除了 `WebAssembly`             |
| `framework`    | Objective-C 框架        | 仅限 macOS、iOS、watchOS 和 tvOS 目标        |

最简单的版本不需要任何额外参数，并为每种构建类型创建一个二进制文件。
目前，有两种构建类型可用：

* `DEBUG` – 生成包含调试信息的非优化二进制文件
* `RELEASE` – 生成无调试信息的优化二进制文件

以下代码片段创建了两个可执行的二进制文件，分别是调试版和发布版：

```kotlin
kotlin {
    linuxX64 { // 可根据需要定义目标。
        binaries {
            executable {
                // 二进制文件配置。
            }
        }
    }
}
```

如果不需要[额外配置](multiplatform-dsl-reference.md#native-targets)，可以省略 lambda 表达式：

```kotlin
binaries {
    executable()
}
```

您可以指定为哪些构建类型创建二进制文件。在以下示例中，仅创建了 `debug` 可执行文件：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
binaries {
    executable(listOf(DEBUG)) {
        // 二进制文件配置。
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
binaries {
    executable([DEBUG]) {
        // 二进制文件配置。
    }
}
```

</tab>
</tabs>

您还可以声明具有自定义名称的二进制文件：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
binaries {
    executable("foo", listOf(DEBUG)) {
        // 二进制文件配置。
    }

    // 可以省略构建类型列表
    // （在这种情况下，将使用所有可用的构建类型）。
    executable("bar") {
        // 二进制文件配置。
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
binaries {
    executable('foo', [DEBUG]) {
        // 二进制文件配置。
    }

    // 可以省略构建类型列表
    // （在这种情况下，将使用所有可用的构建类型）。
    executable('bar') {
        // 二进制文件配置。
    }
}
```

</tab>
</tabs>

第一个参数设置名称前缀，这是二进制文件的默认名称。例如，在 Windows
上，这段代码会生成文件 `foo.exe` 和 `bar.exe`。您还可以使用名称前缀来[在构建脚本中访问二进制文件](#access-binaries)。

## 访问二进制文件 {id=access-binaries}

您可以访问二进制文件来[配置它们](multiplatform-dsl-reference.md#native-targets)或获取它们的属性（例如，输出文件的路径）。

您可以通过其唯一名称获取二进制文件。此名称基于名称前缀（如果指定）、构建类型 和
二进制类型，遵循以下模式：`<可选名称前缀><构建类型><二进制类型>`，例如 `releaseFramework` 或
`testDebugExecutable`。

> 静态库和共享库分别具有后缀 `static` 和 `shared`，例如 `fooDebugStatic` 或 `barReleaseShared`。
>
{style="note"}

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
// 如果没有找到相应的二进制文件，将会失败。
binaries["fooDebugExecutable"]
binaries.getByName("fooDebugExecutable")

// 如果没有找到相应的二进制文件，返回 null。
binaries.findByName("fooDebugExecutable")
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
// 如果没有找到相应的二进制文件，将会失败。
binaries['fooDebugExecutable']
binaries.fooDebugExecutable
binaries.getByName('fooDebugExecutable')

// 如果没有找到相应的二进制文件，返回 null。
binaries.findByName('fooDebugExecutable')
```

</tab>
</tabs>

或者，您可以使用类型化的 getter 方法通过名称前缀和构建类型来访问二进制文件。

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
// 如果没有找到相应的二进制文件，将会失败。
binaries.getExecutable("foo", DEBUG)
binaries.getExecutable(DEBUG)          // 如果未设置名称前缀，可省略第一个参数。
binaries.getExecutable("bar", "DEBUG") // 也可以使用字符串指定构建类型。

// 对于其他二进制类型，也有类似的 getter 方法：
// getFramework, getStaticLib 和 getSharedLib。

// 如果没有找到相应的二进制文件，返回 null。
binaries.findExecutable("foo", DEBUG)

// 对于其他二进制类型，也有类似的 getter 方法：
// findFramework, findStaticLib 和 findSharedLib。
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
// 如果没有找到相应的二进制文件，将会失败。
binaries.getExecutable('foo', DEBUG)
binaries.getExecutable(DEBUG)          // 如果未设置名称前缀，可省略第一个参数。
binaries.getExecutable('bar', 'DEBUG') // 也可以使用字符串指定构建类型。

// 对于其他二进制类型，也有类似的 getter 方法：
// getFramework, getStaticLib 和 getSharedLib。

// 如果没有找到相应的二进制文件，返回 null。
binaries.findExecutable('foo', DEBUG)

// 对于其他二进制类型，也有类似的 getter 方法：
// findFramework, findStaticLib 和 findSharedLib。
```

</tab>
</tabs>

## 导出依赖项到二进制文件 {id=export-dependencies-to-binaries}

在构建 Objective-C 框架或原生库（共享或静态）时，您可能需要打包的不仅是当前项目的类，还包括其依赖项的类。
使用 `export` 方法指定要导出的依赖项到二进制文件中。

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    sourceSets {
        macosMain.dependencies {
            // 将会被导出。
            api(project(":dependency"))
            api("org.example:exported-library:1.0")
            // 不会被导出。
            api("org.example:not-exported-library:1.0")
        }
    }
    macosX64("macos").binaries {
        framework {
            export(project(":dependency"))
            export("org.example:exported-library:1.0")
        }
        sharedLib {
            // 可以将不同的依赖项集导出到不同的二进制文件。
            export(project(':dependency'))
        }
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    sourceSets {
        macosMain.dependencies {
            // 将会被导出。
            api project(':dependency')
            api 'org.example:exported-library:1.0'
            // 不会被导出。
            api 'org.example:not-exported-library:1.0'
        }
    }
    macosX64("macos").binaries {
        framework {
            export project(':dependency')
            export 'org.example:exported-library:1.0'
        }
        sharedLib {
            // 可以将不同的依赖项集导出到不同的二进制文件。
            export project(':dependency')
        }
    }
}
```

</tab>
</tabs>

例如，您实现了多个 Kotlin 模块，并希望从 Swift 访问它们。
虽然在 Swift 应用程序中使用多个 Kotlin/Native 框架是有限的，但您可以创建一个总框架，并将所有这些模块导出到其中。

> 您只能导出对应源集的 [`api` 依赖项](gradle-configure-project.md#dependency-types)。
>
{style="note"}

当您导出一个依赖项时，它会将该依赖项的所有 API 包含到框架的 API 中。
编译器会将这个依赖项的代码添加到框架中，即使您只使用了它的一小部分。
这会禁用对导出依赖项（以及它的依赖项在一定程度上）的死代码消除。

默认情况下，导出是非传递性的。这意味着，如果您导出库 `foo` 并且 `foo` 依赖于库 `bar`，
只有 `foo` 的方法会被添加到输出框架中。

您可以使用 `transitiveExport` 选项来改变这种行为。如果设置为 `true`，库 `bar`
的声明也会被导出。

> 不建议使用 `transitiveExport`：它会将所有导出依赖项的传递依赖添加到框架中。
> 这可能会增加编译时间和二进制文件的大小。
>
> 在大多数情况下，您无需将所有这些依赖项添加到框架 API 中。
> 请明确使用 `export` 来导出您需要直接从 Swift 或 Objective-C 代码中访问的依赖项。
>
{style="warning"}

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
binaries {
    framework {
        export(project(":dependency"))
        // 传递导出。
        transitiveExport = true
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
binaries {
    framework {
        export project(':dependency')
        // 传递导出。
        transitiveExport = true
    }
}
```

</tab>
</tabs>

## 构建通用框架 {id=build-universal-frameworks}

默认情况下，由 Kotlin/Native 生成的 Objective-C 框架只支持一个平台。然而，您可以使用
[`lipo` 工具](https://llvm.org/docs/CommandGuide/llvm-lipo.html) 将这些框架合并成一个通用（fat）二进制文件。
这一操作特别适用于 32 位和 64 位 iOS 框架。在这种情况下，您可以在
32 位和 64 位设备上使用生成的通用框架。

> 通用框架必须与初始框架具有相同的基本名称。否则，您将会遇到错误。
>
{style="warning"}

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
import org.jetbrains.kotlin.gradle.tasks.FatFrameworkTask

kotlin {
    // 创建并配置目标。
    val watchos32 = watchosArm32("watchos32")
    val watchos64 = watchosArm64("watchos64")
    configure(listOf(watchos32, watchos64)) {
        binaries.framework {
            baseName = "my_framework"
        }
    }
    // 创建一个构建 fat 框架的任务。
    tasks.register<FatFrameworkTask>("debugFatFramework") {
        // 通用框架必须与初始框架具有相同的基本名称。
        baseName = "my_framework"
        // 默认的目标目录是 "<build directory>/fat-framework"。
        destinationDir = buildDir.resolve("fat-framework/debug")
        // 指定要合并的框架。
        from(
            watchos32.binaries.getFramework("DEBUG"),
            watchos64.binaries.getFramework("DEBUG")
        )
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
import org.jetbrains.kotlin.gradle.tasks.FatFrameworkTask

kotlin {
    // 创建并配置目标。
    targets {
        watchosArm32("watchos32")
        watchosArm64("watchos64")
        configure([watchos32, watchos64]) {
            binaries.framework {
                baseName = "my_framework"
            }
        }
    }
    // 创建一个构建 fat 框架的任务。
    tasks.register("debugFatFramework", FatFrameworkTask) {
        // 通用框架必须与初始框架具有相同的基本名称。
        baseName = "my_framework"
        // 默认的目标目录是 "<build directory>/fat-framework"。
        destinationDir = file("$buildDir/fat-framework/debug")
        // 指定要合并的框架。
        from(
            targets.watchos32.binaries.getFramework("DEBUG"),
            targets.watchos64.binaries.getFramework("DEBUG")
        )
    }
}
```

</tab>
</tabs>

## 构建 XCFrameworks {id=build-xcframeworks}

所有 Kotlin 跨平台项目可以使用 XCFrameworks 作为输出，以将所有目标平台和架构的逻辑汇聚到一个单独的包中。
与 [通用（fat）框架](#build-universal-frameworks) 不同，您不需要在将应用程序发布到 App Store 之前删除所有不必要的架构。

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
import org.jetbrains.kotlin.gradle.plugin.mpp.apple.XCFramework

plugins {
    kotlin("multiplatform") version "%kotlinVersion%"
}

kotlin {
    val xcf = XCFramework()
    val iosTargets = listOf(iosX64(), iosArm64(), iosSimulatorArm64())
    
    iosTargets.forEach {
        it.binaries.framework {
            baseName = "shared"
            xcf.add(this)
        }
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
import org.jetbrains.kotlin.gradle.plugin.mpp.apple.XCFrameworkConfig

plugins {
    id 'org.jetbrains.kotlin.multiplatform' version '%kotlinVersion%'
}

kotlin {
    def xcf = new XCFrameworkConfig(project)
    def iosTargets = [iosX64(), iosArm64(), iosSimulatorArm64()]
    
    iosTargets.forEach {
        it.binaries.framework {
            baseName = 'shared'
            xcf.add(it)
        }
    }
}
```

</tab>
</tabs>

当您声明 XCFramework 时，Kotlin Gradle 插件会注册几个 Gradle 任务：

* `assembleXCFramework`
* `assemble<Framework name>DebugXCFramework`
* `assemble<Framework name>ReleaseXCFramework`

<anchor name="build-frameworks"/>

如果您在项目中使用 [CocoaPods 集成](native-cocoapods.md)，可以使用 Kotlin CocoaPods Gradle
插件来构建 XCFramework。它包括以下任务，这些任务构建包含所有已注册目标的 XCFramework，并生成 podspec 文件：

* `podPublishReleaseXCFramework`，生成发布版本的 XCFramework 以及 podspec 文件。
* `podPublishDebugXCFramework`，生成调试版本的 XCFramework 以及 podspec 文件。
* `podPublishXCFramework`，生成调试和发布版本的 XCFramework 以及 podspec 文件。

这将帮助您通过 CocoaPods 将项目的共享部分与移动应用程序分开分发。您还可以使用 XCFrameworks
发布到私有或公共的 podspec 仓库。

> 不建议将 Kotlin 框架发布到公共仓库，如果这些框架是针对不同版本的
> Kotlin 构建的。这可能会导致最终用户项目中的冲突。
>
{style="warning"}

## 自定义 Info.plist 文件 {id=customize-the-infoplist-file}

在生成框架时，Kotlin/Native 编译器会生成信息属性列表文件 `Info.plist`。
你可以通过相应的二进制选项自定义其属性：

| 属性                           | 二进制选项                  |
|--------------------------------|-----------------------------|
| `CFBundleIdentifier`           | `bundleId`                  |
| `CFBundleShortVersionString`   | `bundleShortVersionString`  |
| `CFBundleVersion`              | `bundleVersion`             |

要启用该功能，可以传递 `-Xbinary=$option=$value` 编译器标志，或者为特定框架设置 Gradle DSL 的 `binaryOption("option", "value")`：

```kotlin
binaries {
    framework {
        binaryOption("bundleId", "com.example.app")
        binaryOption("bundleVersion", "2")
    }
}
```
