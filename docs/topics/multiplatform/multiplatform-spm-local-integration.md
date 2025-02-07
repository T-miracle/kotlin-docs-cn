[//]: # (title: 从本地 Swift 包使用 Kotlin)

<tldr>
   这是一个本地集成方法。如果满足以下条件，它可以为你工作：<br/>

   * 你有一个包含本地 SPM 模块的 iOS 应用。
   * 你已经在本地机器上设置了一个针对 iOS 的 Kotlin 跨平台项目。
   * 你现有的 iOS 项目使用静态链接方式。<br/>

   [选择最适合你的集成方法](multiplatform-ios-integration-overview.md)
</tldr>

在本教程中，您将学习如何通过 Swift 包管理器（SPM）将 Kotlin 跨平台项目中的 Kotlin 框架集成到本地包中。

![直接集成示意图](direct-integration-scheme.svg){width=700}

为了设置集成，您需要添加一个特殊的脚本，该脚本使用 `embedAndSignAppleFrameworkForXcode` Gradle 任务作为项目构建设置中的预操作。
为了让您在 Xcode 项目中看到公共代码所做的更改，您只需要重新构建 Kotlin 跨平台项目。

通过这种方式，您可以更轻松地在本地 Swift 包中使用 Kotlin 代码，相较于常规的直接集成方法，
后者需要将脚本添加到构建阶段，并要求同时重新构建 Kotlin 跨平台和 iOS 项目以获取公共代码的更改。

> 如果您不熟悉 Kotlin 跨平台，请首先学习如何[设置环境](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-setup.html)
> 和 [从头开始创建一个跨平台应用](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-create-first-app.html)。
>
{style="tip"}

## 设置项目 {id=set-up-the-project}

此功能从 Kotlin 2.0.0 版本开始可用。

> 要检查 Kotlin 版本，请导航到 Kotlin 跨平台项目根目录中的 `build.gradle(.kts)` 文件。
> 您可以在文件顶部的 `plugins {}` 块中查看当前版本。
> 
> 或者，也可以检查 `gradle/libs.versions.toml` 文件中的版本目录。
> 
{style="tip"}

本教程假设您的项目采用了 [直接集成](multiplatform-direct-integration.md)
方法，并在项目构建阶段使用了 `embedAndSignAppleFrameworkForXcode` 任务。
如果您是通过 CocoaPods 插件或通过 Swift 包的 `binaryTarget` 连接 Kotlin 框架，请先进行迁移。

### 从 SPM 的 binaryTarget 集成迁移到本地直接集成 {id=migrate-from-spm-binarytarget-integration-to-local-direct-integration initial-collapse-state="collapsed" collapsible="true"}

要从使用 `binaryTarget` 的 SPM 集成迁移：

1. 在 Xcode 中，使用 **Product** | **Clean Build Folder** 或快捷键 <shortcut>Cmd + Shift + K</shortcut> 清理构建目录。
2. 在每个 `Package.swift` 文件中，移除对包含 Kotlin 框架的包的依赖项以及目标对这些产品的依赖项。

### 从 CocoaPods 插件迁移到直接集成 {id=migrate-from-cocoapods-plugin-to-direct-integration initial-collapse-state="collapsed" collapsible="true"}

> 如果您在 `cocoapods {}` 块中依赖其他 Pods，则必须继续使用 CocoaPods 集成方法。
> 当前，无法在多模式 SPM 项目中同时依赖 Pods 和 Kotlin 框架。
>
{style="warning"}

要从 CocoaPods 插件迁移：

1. 在 Xcode 中，使用 **Product** | **Clean Build Folder** 或快捷键 <shortcut>Cmd + Shift + K</shortcut> 清理构建目录。
2. 在包含 `Podfile` 的目录中运行以下命令：

    ```none
   pod deintegrate
   ```

3. 从您的 `build.gradle(.kts)` 文件中移除 `cocoapods {}` 块。
4. 删除 `.podspec` 和 `Podfile` 文件。

## 将框架连接到您的项目 {id=connect-the-framework-to-your-project}

> 当前尚不支持集成到 `swift build`。
>
{style="note"}

为了在本地 Swift 包中使用 Kotlin 代码，请将从跨平台项目生成的 Kotlin 框架连接到您的 Xcode 项目：

1. 在 Xcode 中，进入 **Product** | **Scheme** | **Edit scheme**，或者点击顶部栏的方案图标并选择 **Edit scheme**：

   ![编辑方案](xcode-edit-schemes.png){width=700}

2. 选择 **Build** | **Pre-actions** 项目，然后点击 **+** | **New Run Script Action**：

   ![新建运行脚本动作](xcode-new-run-script-action.png){width=700}

3. 调整以下脚本并将其添加为操作：

   ```bash
   cd "<跨平台项目根路径>"
   ./gradlew :<Shared module name>:embedAndSignAppleFrameworkForXcode 
   ```

   - 在 `cd` 命令中，指定您的 Kotlin 跨平台项目根目录的路径，例如 `$SRCROOT/..`。
   - 在 `./gradlew` 命令中，指定共享模块的名称，例如 `:shared` 或 `:composeApp`。
  
4. 在 **Provide build settings from** 部分选择您应用的目标：

   ![已填写运行脚本动作](xcode-filled-run-script-action.png){width=700}

5. 您现在可以将共享模块导入到本地 Swift 包中并使用 Kotlin 代码。例如，定义以下函数：

   ```Swift
   import Shared
   
   public func greetingsFromSpmLocalPackage() -> String {
       return Greeting.greet()
   }
   ```

   ![SPM 使用示例](xcode-spm-usage.png){width=700}

6. 在 iOS 项目的 `ContentView.swift` 文件中，通过导入本地包即可使用该函数：

   ```Swift
   import SwiftUI
   import SpmLocalPackage
   
   struct ContentView: View {
       var body: some View {
           VStack {
               Image(systemName: "globe")
                   .imageScale(.large)
                   .foregroundStyle(.tint)
               Text(greetingsFromSpmLocalPackage())
           }
           .padding()
       }
   }
   
   #Preview {
       ContentView()
   }
   ```

7. 在 Xcode 中构建项目。如果一切设置正确，项目将成功构建。

还有一些因素需要注意：

- 如果您有自定义的构建配置（不同于默认的 `Debug` 或 `Release`），请在 **Build Settings**
  标签下的 **User-Defined** 部分中添加 `KOTLIN_FRAMEWORK_BUILD_TYPE` 设置，并将其值设置为 `Debug` 或 `Release`。
- 如果遇到脚本沙盒化错误，双击项目名称打开 iOS 项目设置，
  然后在 **Build Settings** 标签下的 **Build Options** 中禁用 **User Script Sandboxing**。

## 接下来做什么 {id=whats-next}

* [选择您的集成方法](multiplatform-ios-integration-overview.md)
* [了解如何设置 Swift 包导出](native-spm.md)  
