[//]: # (title: 添加对 Pod 库的依赖)

要在 Kotlin 项目和 Pod 库之间添加依赖关系，需要[完成初始配置](native-cocoapods.md#set-up-an-environment-to-work-with-cocoapods) 。
然后，您可以为不同类型的 Pod 库添加依赖项。

当您添加新的依赖项并在 IDE 中重新导入项目时，新依赖项将会自动添加。
无需执行额外的步骤。

如果需要将您的 Kotlin 项目与 Xcode 一起使用，您应当[在项目的 Podfile 中进行更改](native-cocoapods.md#update-podfile-for-xcode) 。

Kotlin 项目需要在 `build.gradle(.kts)` 文件中通过调用 `pod()` 函数来添加 Pod 依赖项。
每个依赖项都需要单独调用该函数。您可以在函数的配置块中指定依赖项的参数。

> 如果您未指定最低部署目标版本，并且某个依赖项 Pod 需要更高的部署目标版本，
> 您将会遇到错误。
>
{style="note"}

您可以在[这里](https://github.com/Kotlin/kmm-with-cocoapods-sample)找到一个示例项目。

## 来自 CocoaPods 仓库 {id=from-the-cocoapods-repository}

1. 在 `pod()` 函数中指定 Pod 库的名称。
   
   在配置块中，您可以使用 `version` 参数指定库的版本。如果想使用库的最新版本，可以直接省略该参数。

   > 您可以添加对子规格（subspecs）的依赖。
   >
   {style="note"}

2. 指定 Pod 库的最低部署目标版本。

    ```kotlin
    kotlin {
        iosArm64()

        cocoapods {
            iosArm64.deploymentTarget = "13.5"

            summary = "CocoaPods 测试库"
            homepage = "https://github.com/JetBrains/kotlin"

            pod("FirebaseAuth") {
                version = "10.16.0"
            }
        }
    }
    ```  

3. 在 IntelliJ IDEA 中运行 **Reload All Gradle Projects**（或在 Android Studio 中运行 **Sync Project with Gradle Files**）
   以重新导入项目。

要从 Kotlin 代码中使用这些依赖项，请导入包 `cocoapods.<library-name>`：

```kotlin
import cocoapods.FirebaseAuth.*
```  

## 在本地存储的库上 {id=on-a-locally-stored-library}

1. 在 `pod()` 函数中指定 Pod 库的名称。

   在配置块中，通过在 `source` 参数中使用 `path()` 函数来指定本地 Pod 库的路径。

   > 您也可以添加对子规格（subspecs）的本地依赖。  
   > `cocoapods` 块可以同时包含对本地存储的 Pods 和 CocoaPods 仓库中 Pods 的依赖。
   >
   {style="note"}

2. 指定 Pod 库的最低部署目标版本。

    ```kotlin
    kotlin {
        iosArm64()

        cocoapods {
            summary = "CocoaPods 测试库"
            homepage = "https://github.com/JetBrains/kotlin"

            iosArm64.deploymentTarget = "13.5"

            pod("pod_dependency") {
                version = "1.0"
                source = path(project.file("../pod_dependency"))
            }
            pod("subspec_dependency/Core") {
                version = "1.0"
                source = path(project.file("../subspec_dependency"))
            }
            pod("FirebaseAuth") {
                version = "10.16.0"
            }
        }
    }
    ```  

   > 您还可以在配置块中通过 `version` 参数指定库的版本。  
   > 如果想使用库的最新版本，可以省略该参数。
   >
   {style="note"}

3. 在 IntelliJ IDEA 中运行 **Reload All Gradle Projects**（或在 Android Studio 中运行 **Sync Project with Gradle Files**）
   以重新导入项目。

要从 Kotlin 代码中使用这些依赖项，请导入包 `cocoapods.<library-name>`：

```kotlin
import cocoapods.pod_dependency.*
import cocoapods.subspec_dependency.*
import cocoapods.FirebaseAuth.*
```

## 来自自定义 Git 仓库 {id=from-a-custom-git-repository}

1. 在 `pod()` 函数中指定 Pod 库的名称。

   在配置块中，指定 Git 仓库的路径：在 `source` 参数值中使用 `git()` 函数。

   此外，您可以在 `git()` 后指定以下参数：
   * `commit` – 使用仓库中的特定提交
   * `tag` – 使用仓库中的特定标签
   * `branch` – 使用仓库中的特定分支

   `git()` 函数会按照以下优先顺序处理传递的参数：`commit`、`tag`、`branch`。  
   如果您未指定某个参数，Kotlin 插件将使用 `master` 分支中的 `HEAD`。

   > 您可以结合使用 `branch`、`commit` 和 `tag` 参数，以获取特定版本的 Pod。
   >
   {style="note"}

2. 指定 Pod 库的最低部署目标版本。

    ```kotlin
    kotlin {
        iosArm64()

        cocoapods {
            summary = "CocoaPods test library"
            homepage = "https://github.com/JetBrains/kotlin"

            iosArm64.deploymentTarget = "13.5"

            pod("FirebaseAuth") {
                source = git("https://github.com/firebase/firebase-ios-sdk") {
                    tag = "10.16.0"
                }
            }

            pod("JSONModel") {
                source = git("https://github.com/jsonmodel/jsonmodel.git") {
                    branch = "key-mapper-class"
                }
            }

            pod("CocoaLumberjack") {
                source = git("https://github.com/CocoaLumberjack/CocoaLumberjack.git") {
                    commit = "3e7f595e3a459c39b917aacf9856cd2a48c4dbf3"
                }
            }
        }
    }
    ```

3. 在 IntelliJ IDEA 中运行 **Reload All Gradle Projects**（或在 Android Studio 中运行 **Sync Project with Gradle Files**）
   以重新导入项目。

要从 Kotlin 代码中使用这些依赖项，请导入包 `cocoapods.<library-name>`：

```kotlin
import cocoapods.Alamofire.*
import cocoapods.JSONModel.*
import cocoapods.CocoaLumberjack.*
```

## 来自自定义 Podspec 仓库 {id=from-a-custom-podspec-repository}

1. 在 `specRepos` 块中使用 `url()` 指定自定义 Podspec 仓库的 HTTP 地址。
2. 在 `pod()` 函数中指定 Pod 库的名称。
3. 指定 Pod 库的最低部署目标版本。

    ```kotlin
    kotlin {
        iosArm64()

        cocoapods {
            summary = "CocoaPods test library"
            homepage = "https://github.com/JetBrains/kotlin"

            iosArm64.deploymentTarget = "13.5"

            specRepos {
                url("https://github.com/Kotlin/kotlin-cocoapods-spec.git")
            }
            pod("example")
        }
    }
    ```

4. 在 IntelliJ IDEA 中运行 **Reload All Gradle Projects**（或在 Android Studio 中运行 **Sync Project with Gradle Files**）
   以重新导入项目。

> 为了与 Xcode 正常协作，您应当在 Podfile 开头指定 specs 的位置。  
> 例如，
> ```ruby
> source 'https://github.com/Kotlin/kotlin-cocoapods-spec.git'
> ```
>
{style="note"}

要从 Kotlin 代码中使用这些依赖项，请导入包 `cocoapods.<library-name>`：

```kotlin
import cocoapods.example.*
```

## 使用自定义 cinterop 选项 {id=with-custom-cinterop-options}

1. 在 `pod()` 函数中指定 Pod 库的名称。

   在配置块中，指定 cinterop 选项：
   * `extraOpts` – 指定 Pod 库的选项列表。例如，特定的标志：`extraOpts = listOf("-compiler-option")`。
   * `packageName` – 指定包名。如果指定了该选项，您可以使用包名导入库：  
     `import <packageName>`。

2. 指定 Pod 库的最低部署目标版本。

    ```kotlin
    kotlin {
        iosArm64()

        cocoapods {
            summary = "CocoaPods test library"
            homepage = "https://github.com/JetBrains/kotlin"

            iosArm64.deploymentTarget = "13.5"

            pod("YandexMapKit") {
                packageName = "YandexMK"
            }
        }
    }
    ```

3. 在 IntelliJ IDEA 中运行 **Reload All Gradle Projects**（或在 Android Studio 中运行 **Sync Project with Gradle Files**）
   以重新导入项目。

要从 Kotlin 代码中使用这些依赖项，请导入包 `cocoapods.<library-name>`：
   
```kotlin
import cocoapods.YandexMapKit.*
```
   
如果使用了 `packageName` 参数，您可以通过包名导入库：`import <packageName>`。
   
```kotlin
import YandexMK.YMKPoint
import YandexMK.YMKDistance
```

### 对带有 `@import` 指令的 Objective-C 头文件的支持 {id=support-for-objective-c-headers-with-import-directives}

> 该功能是[实验性的](components-stability.md#stability-levels-explained)。  
> 它可能会在任何时候被废弃或更改。仅供评估使用。  
> 我们欢迎您在 [YouTrack](https://kotl.in/issue) 提供反馈。
>
{style="warning"}

一些 Objective-C 库，特别是作为 Swift 库的包装器的库，
其头文件中包含 `@import` 指令。默认情况下，cinterop 不支持这些指令。

要启用对 `@import` 指令的支持，请在 `pod()` 函数的配置块中指定 `-fmodules` 选项：

```kotlin
kotlin {
    iosArm64()

    cocoapods {
        summary = "CocoaPods test library"
        homepage = "https://github.com/JetBrains/kotlin"

        iosArm64.deploymentTarget = "13.5"

        pod("PodName") {
            extraOpts = listOf("-compiler-option", "-fmodules")
        }
    }
}
```

### 在依赖的 Pods 之间共享 Kotlin cinterop {id=share-kotlin-cinterop-between-dependent-pods}

如果您使用 `pod()` 函数添加了多个 Pod 依赖，可能会遇到当您的 Pods 之间存在 API 依赖时的问题。

为了解决此类问题，使用 `useInteropBindingFrom()` 函数。
它在为新 Pod 构建绑定时，利用了为另一个 Pod 生成的 cinterop 绑定。

您应该在设置依赖项之前声明依赖的 Pod：

```kotlin
// pod("WebImage") 的 cinterop：
fun loadImage(): WebImage

// pod("Info") 的 cinterop：
fun printImageInfo(image: WebImage)

// 您的代码：
printImageInfo(loadImage())
```

如果在这种情况下未配置正确的 cinterop 依赖关系，代码将无效，因为 `WebImage` 类型将来自不同的 cinterop 文件，因此，来自不同的包。