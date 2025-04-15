[//]: # (title: CocoaPods Gradle 插件 DSL 参考)

Kotlin CocoaPods Gradle 插件是一个用于创建 Podspec 文件的工具。这些文件是将你的 Kotlin
项目与 [CocoaPods 依赖管理器](https://cocoapods.org/) 集成所必需的。

本参考包含 Kotlin CocoaPods Gradle 插件的完整代码块、函数和属性列表，
你可以在使用 [CocoaPods 集成](native-cocoapods.md) 时使用。

* 了解如何 [设置环境并配置 Kotlin CocoaPods Gradle 插件](native-cocoapods.md)。
* 根据你的项目和目的，你可以在 [Kotlin 项目和 Pod 库](native-cocoapods-libraries.md) 之间以及
  [Kotlin Gradle 项目和 Xcode 项目](native-cocoapods-xcode.md) 之间添加依赖关系。

## 启用插件 {id=enable-the-plugin}

要应用 CocoaPods 插件，请在 `build.gradle(.kts)` 文件中添加以下内容：

```kotlin
plugins {
   kotlin("multiplatform") version "%kotlinVersion%"
   kotlin("native.cocoapods") version "%kotlinVersion%"
}
```

插件版本与 [Kotlin 发布版本](releases.md) 匹配。最新稳定版本是 %kotlinVersion%。

## cocoapods 代码块 {id=cocoapods-block}

`cocoapods` 代码块是 CocoaPods 配置的顶级代码块。它包含有关 Pod 的常规信息，包括必需的信息（如 Pod 版本、摘要和主页）以及可选功能。

你可以在其中使用以下代码块、函数和属性：

| **名称**                              | **描述**                                                                                                                                                                                                                  | 
|---------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `version`                             | Pod 的版本。如果未指定，则使用 Gradle 项目版本。如果未配置这些属性中的任何一个，则会出错。                                                                             |
| `summary`                             | 从该项目构建的 Pod 的必需描述。                                                                                                                                                                       |
| `homepage`                            | 从该项目构建的 Pod 的主页链接，必需。                                                                                                                                                              |
| `authors`                             | 指定从该项目构建的 Pod 的作者。                                                                                                                                                                            |
| `podfile`                             | 配置现有的 `Podfile` 文件。                                                                                                                                                                                          |
| `noPodspec()`                         | 设置插件不为 `cocoapods` 部分生成 Podspec 文件。                                                                                                                                                    |
| `name`                                | 从该项目构建的 Pod 的名称。如果未提供，则使用项目名称。                                                                                                                                          |
| `license`                             | 从该项目构建的 Pod 的许可证、其类型和文本。                                                                                                                                                          |
| `framework`                           | `framework` 代码块配置插件生成的框架。                                                                                                                                                             |
| `source`                              | 从该项目构建的 Pod 的位置。                                                                                                                                                                                 |
| `extraSpecAttributes`                 | 配置其他 Podspec 属性，如 `libraries` 或 `vendored_frameworks`。                                                                                                                                                   |
| `xcodeConfigurationToNativeBuildType` | 将自定义 Xcode 配置映射到 NativeBuildType："Debug" 映射到 `NativeBuildType.DEBUG`，"Release" 映射到 `NativeBuildType.RELEASE`。                                                                                               |
| `publishDir`                          | 配置 Pod 发布的输出目录。                                                                                                                                                                              |
| `pods`                                | 返回 Pod 依赖项的列表。                                                                                                                                                                                              |
| `pod()`                               | 向从该项目构建的 Pod 添加 CocoaPods 依赖项。                                                                                                                                                                  |
| `specRepos`                           | 使用 `url()` 添加规范仓库。当使用私有 Pod 作为依赖项时，这是必需的。有关更多信息，请参阅 [CocoaPods 文档](https://guides.cocoapods.org/making/private-cocoapods.html)。 |

### 目标 {id=targets}

| iOS                 | macOS        | tvOS                 | watchOS                 |
|---------------------|--------------|----------------------|-------------------------|
| `iosArm64`          | `macosArm64` | `tvosArm64`          | `watchosArm64`          |
| `iosX64`            | `macosX64`   | `tvosX64`            | `watchosX64`            |
| `iosSimulatorArm64` |              | `tvosSimulatorArm64` | `watchosSimulatorArm64` |
|                     |              |                      | `watchosArm32`          |
|                     |              |                      | `watchosDeviceArm64`    |

对于每个目标，使用 `deploymentTarget` 属性指定 Pod 库的最低目标版本。

应用后，CocoaPods 会为所有目标添加 `debug` 和 `release` 框架作为输出二进制文件。

```kotlin
kotlin {
    iosArm64()
   
    cocoapods {
        version = "2.0"
        name = "MyCocoaPod"
        summary = "CocoaPods 测试库"
        homepage = "https://github.com/JetBrains/kotlin"
        
        extraSpecAttributes["vendored_frameworks"] = 'CustomFramework.xcframework'
        license = "{ :type => 'MIT', :text => 'License text'}"
        source = "{ :git => 'git@github.com:vkormushkin/kmmpodlibrary.git', :tag => '$version' }"
        authors = "Kotlin Dev"
        
        specRepos {
            url("https://github.com/Kotlin/kotlin-cocoapods-spec.git")
        }
        pod("example")
        
        xcodeConfigurationToNativeBuildType["CUSTOM_RELEASE"] = NativeBuildType.RELEASE
   }
}
```

### framework 代码块 {id=framework-block}

`framework` 代码块嵌套在 `cocoapods` 代码块内，用于配置从项目构建的 Pod 的框架属性。

> 注意，`baseName` 是必填字段。
>
{style="note"}

| **名称**             | **描述**                               | 
|--------------------|--------------------------------------|
| `baseName`         | 必需的框架名称。使用此属性代替已弃用的 `frameworkName`。 |
| `isStatic`         | 定义框架的链接类型。默认为动态链接。                   |
| `transitiveExport` | 启用依赖项导出。                             |                                               

```kotlin
kotlin {
    cocoapods {
        version = "2.0"
        framework {
            baseName = "MyFramework"
            isStatic = false
            export(project(":anotherKMMModule"))
            transitiveExport = true
        }
    }
}
```

## pod() 函数 {id=pod-function}

`pod()` 函数调用将 CocoaPods 依赖项添加到从该项目构建的 Pod 中。每个依赖项都需要一个单独的函数调用。

你可以在函数参数中指定 Pod 库的名称，并在其配置代码块中指定其他参数值，例如库的 `version` 和 `source`：

| **名称**                       | **描述**                                                                                                                                         | 
|------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------|
| `version`                    | 库版本号。如需使用最新版本库，可省略此参数。                                                                                                                         |
| `source`                     | 配置 Pod 来源：<list><li>使用 `git()` 指定 Git 仓库。在 `git()` 代码块后可指定 `commit` 使用特定提交、`tag` 使用特定标签、`branch` 使用特定分支</li><li>使用 `path()` 指定本地仓库</li></list> |
| `packageName`                | 指定包名称。                                                                                                                                         |
| `extraOpts`                  | 指定 Pod 库的选项列表。例如特定标志：<code-block lang="Kotlin">extraOpts = listOf("-compiler-option")</code-block>                                             |
| `linkOnly`                   | 指示 CocoaPods 插件仅使用动态框架的 Pod 依赖而不生成 cinterop 绑定。若与静态框架一起使用，该选项将完全移除 Pod 依赖。                                                                     |
| `interopBindingDependencies` | 包含其他 Pod 的依赖列表。在为新 Pod 构建 Kotlin 绑定时会使用此列表。                                                                                                    |
| `useInteropBindingFrom()`    | 指定现有 Pod 的名称作为依赖项（该 Pod 需在函数执行前声明）。该函数指示 CocoaPods 插件在为新的 Pod 构建绑定时使用现有 Pod 的 Kotlin 绑定。                                                       |

```kotlin
kotlin {
    iosArm64()
    
    cocoapods {
        version = "2.0"
        summary = "CocoaPods test library"
        homepage = "https://github.com/JetBrains/kotlin"
        ios.deploymentTarget = "16.0"
      
        pod("pod_dependency") {
            version = "1.0"
            extraOpts += listOf("-compiler-option")
            linkOnly = true
            source = path(project.file("../pod_dependency"))
        }
    }
}
```
