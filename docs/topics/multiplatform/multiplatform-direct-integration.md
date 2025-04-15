[//]: # (title: 直接集成)

<tldr>
   这是一种本地集成方法。如果符合以下条件，它适合你：<br/>

   * 你已经在本地机器上设置了一个目标为 iOS 的 Kotlin Multiplatform 项目。
   * 你的 Kotlin Multiplatform 项目没有 CocoaPods 依赖项。<br/>
   
   [选择最适合你的集成方法](multiplatform-ios-integration-overview.md)
</tldr>

如果你想通过共享代码在 Kotlin Multiplatform 项目和 iOS 项目之间同时进行开发，
可以使用特殊脚本设置直接集成。

这个脚本将 Kotlin 框架连接到 iOS 项目的过程自动化了：

![直接集成示意图](direct-integration-scheme.svg){width=700}

该脚本使用了专门为 Xcode 环境设计的 `embedAndSignAppleFrameworkForXcode` Gradle 任务。  
在设置过程中，你需要将其添加到 iOS 应用构建的运行脚本阶段。
之后，Kotlin 构件会在运行 iOS 应用构建之前被构建并包含到派生数据中。

一般来说，脚本会：

* 将编译后的 Kotlin 框架复制到 iOS 项目结构中的正确目录。
* 处理嵌入框架的代码签名过程。
* 确保 Kotlin 框架中的代码更改能够反映到 Xcode 中的 iOS 应用。

## 如何设置 {id=how-to-set-up}

如果您当前正在使用 CocoaPods 插件来连接您的 Kotlin 框架，请先进行迁移。  
如果您的项目没有 CocoaPods 依赖项，请[跳过此步骤](#connect-the-framework-to-your-project)。

### 从 CocoaPods 插件迁移 {id=migrate-from-the-cocoapods-plugin}

要从 CocoaPods 插件迁移：

1. 在 Xcode 中，使用 **Product** | **Clean Build Folder** 清理构建目录，或者使用快捷键 
   <shortcut>Cmd + Shift + K</shortcut>。
2. 在包含 `Podfile` 文件的目录中，运行以下命令：

    ```none
   pod deintegrate
   ```

3. 从 `build.gradle(.kts)` 文件中删除 `cocoapods {}` 块。
4. 删除 `.podspec` 和 `Podfile` 文件。

### 将框架连接到你的项目 {id=connect-the-framework-to-your-project}

要将从 Kotlin Multiplatform 项目生成的 Kotlin 框架连接到你的 Xcode 项目，请进行以下步骤：

1. `embedAndSignAppleFrameworkForXcode` 任务只有在声明了 `binaries.framework` 配置选项时才会注册。
   在你的 Kotlin Multiplatform 项目中，检查 `build.gradle.kts` 文件中的 iOS 目标声明。
2. 在 Xcode 中，双击项目名称以打开 iOS 项目设置。
3. 在项目设置的 **Build Phases** 标签页中，点击 **+** 并选择 **New Run Script Phase**。

   ![添加运行脚本阶段](xcode-run-script-phase-1.png){width=700}

4. 调整以下脚本并将结果复制到运行脚本阶段：

   ```bash
   cd "<跨平台项目的根路径>"
   ./gradlew :<共享模块名称>:embedAndSignAppleFrameworkForXcode 
   ```

   * 在 `cd` 命令中，指定你的 Kotlin Multiplatform 项目根目录的路径，例如 `$SRCROOT/..`。
   * 在 `./gradlew` 命令中，指定共享模块的名称，例如 `:shared` 或 `:composeApp`。

   ![添加脚本](xcode-run-script-phase-2.png){width=700}

5. 将 **Run Script** 阶段拖动到 **Compile Sources** 阶段之前。

   ![拖动 Run Script 阶段](xcode-run-script-phase-3.png){width=700}

6. 在 **Build Settings** 标签页中，在 **Build Options** 下禁用 **User Script Sandboxing** 选项：

   ![禁用脚本沙箱](disable-sandboxing-in-xcode-project-settings.png){width=700}

   > 如果您在未先禁用沙盒的情况下构建了 iOS 项目，则可能需要重启 Gradle 守护进程。  
   > 停止可能已被沙盒化的 Gradle 守护进程：
   > ```shell
   > ./gradlew --stop
   > ```
   >
   > {style="tip"}

7. 在 Xcode 中构建项目。如果一切设置正确，项目将成功构建。

> 如果你使用了与默认的 `Debug` 或 `Release` 不同的自定义构建配置，在 **Build Settings** 标签页中，
> 在 **User-Defined** 下添加 `KOTLIN_FRAMEWORK_BUILD_TYPE` 设置，并将其设置为 `Debug` 或 `Release`。
>
{style="note"}

## 接下来该做什么？ {id=whats-next}

你还可以在使用 Swift 包管理器时利用本地集成功能。
[了解如何在本地包中添加 Kotlin 框架依赖](multiplatform-spm-local-integration.md)。