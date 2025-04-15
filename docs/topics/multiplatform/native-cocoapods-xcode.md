[//]: # (title: 使用 Kotlin Gradle 项目作为 CocoaPods 依赖)

要将带有原生目标的 Kotlin 跨平台项目用作 CocoaPods 依赖项，[请完成初始配置](native-cocoapods.md#set-up-an-environment-to-work-with-cocoapods)。
你可以通过它的名称和包含生成的 Podspec 的项目目录路径，在 Xcode 项目的 Podfile 中包含此类依赖项。

此依赖项将与此项目一起自动构建（并重新构建）。
这种方法简化了导入到 Xcode 的过程，因为无需手动编写相应的 Gradle 任务和 Xcode 构建步骤。

你可以在 Kotlin Gradle 项目和一个或多个目标的 Xcode 项目之间添加依赖关系。
也可以在 Gradle 项目和多个 Xcode 项目之间添加依赖关系。然而，在这种情况下，你需要为每个 Xcode 项目手动调用 `pod install` 来添加依赖项。
在其他情况下，这是自动完成的。

> * 为了正确地将依赖项导入到 Kotlin/Native 模块中，`Podfile` 必须包含
>   [`use_modular_headers!`](https://guides.cocoapods.org/syntax/podfile.html#use_modular_headers_bang) 或
>   [`use_frameworks!`](https://guides.cocoapods.org/syntax/podfile.html#use_frameworks_bang) 指令。
> * 如果你没有指定最低部署目标版本，而依赖的 Pod 需要更高的部署目标，则会出现错误。
>
{style="note"}

## 带有单目标的 Xcode 项目 {id=xcode-project-with-one-target}

1. 如果尚未创建，请创建一个带有 `Podfile` 的 Xcode 项目。
2. 确保在应用程序目标的 **Build Options** 下禁用 **User Script Sandboxing**：

   ![禁用 CocoaPods 沙盒](disable-sandboxing-cocoapods.png)

3. 在 Kotlin 项目的 `build.gradle(.kts)` 文件中，使用 `podfile = project.file(..)` 添加指向 Xcode 项目 `Podfile` 的路径。
   此步骤通过为你的 `Podfile` 调用 `pod install`，帮助同步 Xcode 项目与 Gradle 项目的依赖项。
4. 为 Pod 库指定最低部署目标版本。

    ```kotlin
    kotlin {
        iosArm64()

        cocoapods {
            version = "2.0"
            summary = "CocoaPods test library"
            homepage = "https://github.com/JetBrains/kotlin"
            ios.deploymentTarget = "16.0"
   
            pod("SDWebImage") {
                version = "5.20.0"
            }
            podfile = project.file("../ios-app/Podfile")
        }
    }
    ```

5. 将你要包含在 Xcode 项目中的 Gradle 项目的名称和路径添加到 `Podfile`。

    ```ruby
    use_frameworks!

    platform :ios, '16.0'

    target 'ios-app' do
            pod 'kotlin_library', :path => '../kotlin-library'
    end
    ```

6. 在项目目录中运行 `pod install`。

   当你第一次运行 `pod install` 时，它会创建 `.xcworkspace` 文件。该文件包含你原始的 `.xcodeproj` 和 CocoaPods 项目。
7. 关闭你的 `.xcodeproj` 并打开新的 `.xcworkspace` 文件。这样可以避免项目依赖项的问题。
8. 在 IntelliJ IDEA 中运行 **Reload All Gradle Projects**（或在 Android Studio 中运行 **Sync Project with Gradle Files**）以重新导入项目。

## 带有多个目标的 Xcode 项目 {id=xcode-project-with-several-targets}

1. 如果尚未创建，请创建一个带有 `Podfile` 的 Xcode 项目。
2. 在 Kotlin 项目的 `build.gradle(.kts)` 文件中，使用 `podfile = project.file(..)` 添加指向 Xcode 项目 `Podfile` 的路径。  
   此步骤通过为你的 `Podfile` 调用 `pod install`，帮助同步 Xcode 项目与 Gradle 项目的依赖项。
3. 使用 `pod()` 添加你希望在项目中使用的 Pod 库的依赖项。
4. 为每个目标指定 Pod 库的最低部署目标版本。

    ```kotlin
    kotlin {
        iosArm64()
        tvosArm64()

        cocoapods {
            version = "2.0"
            summary = "CocoaPods test library"
            homepage = "https://github.com/JetBrains/kotlin"
            ios.deploymentTarget = "16.0"
            tvos.deploymentTarget = "16.0"

            pod("SDWebImage") {
                version = "5.20.0"
            }
            podfile = project.file("../severalTargetsXcodeProject/Podfile") // 指定 Podfile 的路径
        }
    }
    ```

5. 将你要包含在 Xcode 项目中的 Gradle 项目的名称和路径添加到 `Podfile`。

    ```ruby
    target 'iosApp' do
      use_frameworks!
      platform :ios, '16.0'
      # Pods for iosApp
      pod 'kotlin_library', :path => '../kotlin-library'
    end

    target 'TVosApp' do
      use_frameworks!
      platform :tvos, '16.0'

      # TVosApp 的 Pods
      pod 'kotlin_library', :path => '../kotlin-library'
    end
    ```

6. 在项目目录中运行 `pod install`。

   当你第一次运行 `pod install` 时，它会创建 `.xcworkspace` 文件。该文件包含你原始的 `.xcodeproj` 和 CocoaPods 项目。
7. 关闭你的 `.xcodeproj` 并打开新的 `.xcworkspace` 文件。这样可以避免项目依赖项的问题。
8. 在 IntelliJ IDEA 中运行 **Reload All Gradle Projects**（或在 Android Studio 中运行 **Sync Project with Gradle Files**）以重新导入项目。

你可以在[这里](https://github.com/Kotlin/kmm-with-cocoapods-multitarget-xcode-sample)找到一个示例项目。

## 下一步 {id=whats-next}

查看[将框架连接到你的 iOS 项目](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-integrate-in-existing-app.html#connect-the-framework-to-your-ios-project)，
了解如何在 Xcode 项目的构建阶段添加自定义构建脚本。
