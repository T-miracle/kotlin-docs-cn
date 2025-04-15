[//]: # (title: CocoaPods 概述和设置)

<tldr>
   这是一个本地集成方法。它适用于以下情况：<br/>

   * 你有一个使用 CocoaPods 的 iOS 项目的单体代码库。
   * 你的 Kotlin 跨平台项目包含 CocoaPods 依赖项。<br/>

   [选择最适合你的集成方法](multiplatform-ios-integration-overview.md)
</tldr>

Kotlin/Native 提供了与 [CocoaPods 依赖管理器](https://cocoapods.org/) 的集成支持。你可以添加对
Pod 库的依赖，也可以将包含原生目标的跨平台项目用作 CocoaPods 依赖。

你可以直接在 IntelliJ IDEA 或 Android Studio 中管理 Pod 依赖，并享受所有附加功能，例如代码高亮和补全。
你可以使用 Gradle 构建整个 Kotlin 项目，而无需切换到 Xcode。

仅当你需要修改 Swift/Objective-C 代码或在 Apple 模拟器或设备上运行应用程序时才需要使用 Xcode。
为了与 Xcode 正确配合工作，你需要 [更新你的 Podfile](#update-podfile-for-xcode)。

根据你的项目和目的，你可以添加以下依赖关系：[Kotlin 项目与 Pod 库之间的依赖](native-cocoapods-libraries.md)
以及 [Kotlin Gradle 项目与 Xcode 项目之间的依赖](native-cocoapods-xcode.md)。

## 设置与 CocoaPods 一起工作的环境 {id=set-up-an-environment-to-work-with-cocoapods}

使用你选择的安装工具安装 [CocoaPods 依赖管理器](https://cocoapods.org/)：

<tabs>
<tab title="RVM">

1. 安装 [Ruby 版本管理器](https://rvm.io/rvm/install) ，如果你尚未安装。

2. 安装 Ruby。你可以选择一个特定的版本：

    ```bash
    rvm install ruby 3.0.0
    ```

3. 安装 CocoaPods：

    ```bash
    sudo gem install -n /usr/local/bin cocoapods
    ```

</tab>
<tab title="Rbenv">

1. 安装 [rbenv（来自 GitHub）](https://github.com/rbenv/rbenv#installation) ，如果你尚未安装。
2. 安装 Ruby。你可以选择一个特定的版本：

    ```bash
    rbenv install 3.0.0
    ```

3. 将该 Ruby 版本设置为本地目录专用或全局机器通用：

    ```bash
    rbenv global 3.0.0
    ```

4. 安装 CocoaPods：

    ```bash
    sudo gem install -n /usr/local/bin cocoapods
    ```

</tab>
<tab title="Default Ruby">

> 这种安装方式不适用于带有 Apple M 芯片的设备。请使用其他工具来配置用于 CocoaPods 的环境。
>
{style="note"}

你可以使用 macOS 上默认可用的 Ruby 安装 CocoaPods 依赖管理工具：

```bash
sudo gem install cocoapods
```  

</tab>
<tab title="Homebrew">

> 使用 Homebrew 安装 CocoaPods 可能会导致兼容性问题。
>
> 安装 CocoaPods 时，Homebrew 还会安装 [Xcodeproj](https://github.com/CocoaPods/Xcodeproj) gem，它是与
> Xcode 一起工作的必要组件。
> 然而，Homebrew 无法更新 Xcodeproj。如果安装的 Xcodeproj 尚未支持最新版本的 Xcode，你将在 Pod 安装时遇到错误。
> 如果遇到此问题，请尝试使用其他工具安装 CocoaPods。
>
{style="warning"}

1. 安装 [Homebrew](https://brew.sh/) ，如果你尚未安装。

2. 安装 CocoaPods：

    ```bash
    brew install cocoapods
    ```  

</tab>
</tabs>

如果在安装过程中遇到问题，请查看 [可能的问题及解决方案](#possible-issues-and-solutions) 部分。

## 创建项目 {id=create-a-project}

当你的环境配置完成后，你可以创建一个新的 Kotlin 跨平台项目。为此，可以使用
Kotlin 跨平台网页向导或 Android Studio 的 Kotlin 跨平台插件。

### 使用网页向导 {id=using-web-wizard}

要使用网页向导创建项目并配置 CocoaPods 集成：

1. 打开 [Kotlin 跨平台向导](https://kmp.jetbrains.com) 并选择你的项目目标平台。
2. 点击 **Download** 按钮并解压下载的归档文件。
3. 在 Android Studio 的菜单中选择 **File | Open**。
4. 导航到解压后的项目文件夹，然后点击 **Open**。
5. 将 Kotlin CocoaPods Gradle 插件添加到版本目录。在 `gradle/libs.versions.toml` 文件中，
   将以下声明添加到 `[plugins]` 块：

   ```text
   kotlinCocoapods = { id = "org.jetbrains.kotlin.native.cocoapods", version.ref = "kotlin" }
   ```  

6. 导航到项目的根 `build.gradle.kts` 文件，并在 `plugins {}` 块中添加以下别名：

   ```kotlin
   alias(libs.plugins.kotlinCocoapods) apply false
   ```  

7. 打开你希望集成 CocoaPods 的模块，例如 `composeApp` 模块，并在 `plugins {}` 块中添加以下别名：

   ```kotlin
   alias(libs.plugins.kotlinCocoapods)
   ```  

Now you are ready to [configure CocoaPods in your Kotlin Multiplatform project](#configure-the-project).

### 在 Android Studio 中 {id=in-android-studio}

要在 Android Studio 中创建具有 CocoaPods 集成的项目：

1. 为 Android Studio 安装 [Kotlin 跨平台插件](https://plugins.jetbrains.com/plugin/14936-kotlin-multiplatform)
2. 在 Android Studio 中，选择菜单栏的 **File** | **New** | **New Project**
3. 在项目模板列表中，选择 **Kotlin 跨平台应用** 然后点击 **Next**
4. 为您的应用程序命名并点击 **Next**
5. 选择 **CocoaPods 依赖管理器** 作为 iOS 框架分发选项

   ![Android Studio 向导中的 Kotlin 跨平台插件](as-project-wizard.png){width=700}

6. 保持所有其他选项为默认设置。点击 **Finish**。

   插件会自动生成已配置好 CocoaPods 集成的项目。

## 配置项目 {id=configure-the-project}

要在跨平台项目中配置 Kotlin CocoaPods Gradle 插件：

1. 在项目的 `build.gradle(.kts)` 文件中，同时应用 CocoaPods 插件和 Kotlin 跨平台插件。

   > 如果您是使用 [Web 向导](#using-web-wizard) 或
   > [Android Studio 的 Kotlin 跨平台插件](#in-android-studio) 创建的项目，请跳过此步骤。
   >
   {style="note"}
    
    ```kotlin
    plugins {
        kotlin("multiplatform") version "%kotlinVersion%"
        kotlin("native.cocoapods") version "%kotlinVersion%"
    }
    ```

2. 配置 `version`、`summary`、`homepage` 和 `baseName` 在 `cocoapods` 块中的 Podspec 文件：
    
    ```kotlin
    plugins {
        kotlin("multiplatform") version "%kotlinVersion%"
        kotlin("native.cocoapods") version "%kotlinVersion%"
    }
 
    kotlin {
        cocoapods {
            // 必需属性
            // 在此处指定所需的 Pod 版本
            // 否则将使用 Gradle 项目版本
            version = "1.0"
            summary = "Kotlin/Native 模块的描述"
            homepage = "Kotlin/Native 模块主页的链接"
   
            // 可选属性
            // 在这里配置 Pod 名称，而不是修改 Gradle 项目的名称
            name = "MyCocoaPod"

            framework {
                // 必需属性               
                // 框架名称配置。使用此属性而不是已废弃的 'frameworkName'
                baseName = "MyFramework"
                
                // 可选属性
                // 指定框架链接类型。默认为动态链接。
                isStatic = false
                // 依赖导出
                // 如果有其他项目模块，取消注释并指定：
                // export(project(":<your other KMP module>"))
                transitiveExport = false // 默认值。
            }

            // 将自定义的 Xcode 配置映射到 NativeBuildType
            xcodeConfigurationToNativeBuildType["CUSTOM_DEBUG"] = NativeBuildType.DEBUG
            xcodeConfigurationToNativeBuildType["CUSTOM_RELEASE"] = NativeBuildType.RELEASE
        }
    }
    ```

    > 请参见 [Kotlin Gradle 插件仓库](https://github.com/JetBrains/kotlin/blob/master/libraries/tools/kotlin-gradle-plugin/src/common/kotlin/org/jetbrains/kotlin/gradle/targets/native/cocoapods/CocoapodsExtension.kt) 中的完整 Kotlin DSL 语法。
    >
    {style="note"}
    
3. 在 IntelliJ IDEA 中运行 **重新加载所有 Gradle 项目**（或在 Android Studio 中运行 **同步项目与 Gradle 文件**）
   以重新导入项目。
4. 生成 [Gradle wrapper](https://docs.gradle.org/current/userguide/gradle_wrapper.html)，以避免在
   Xcode 构建期间出现兼容性问题。

应用后，CocoaPods 插件会执行以下操作：

* 为所有 macOS、iOS、tvOS 和 watchOS 目标添加 `debug` 和 `release` 框架作为输出二进制文件。
* 创建一个 `podspec` 任务，该任务为项目生成一个 [Podspec](https://guides.cocoapods.org/syntax/podspec.html) 文件。

`Podspec` 文件包括指向输出框架的路径以及在 Xcode 项目构建过程中自动构建此框架的脚本阶段。

## 更新 Xcode 的 Podfile {id=update-podfile-for-xcode}

如果你想将 Kotlin 项目导入到 Xcode 项目中：

1. 在你的 Podfile 中做出以下更改：

    * 如果你的项目有任何 Git、HTTP 或自定义 Podspec 仓库依赖项，你应该在 Podfile 中指定 Podspec 的路径。

      例如，如果你添加了对 `podspecWithFilesExample` 的依赖，在 Podfile 中声明 Podspec 的路径：

      ```ruby
      target 'ios-app' do
         # ... 其他依赖项 ...
         pod 'podspecWithFilesExample', :path => 'cocoapods/externalSources/url/podspecWithFilesExample' 
      end
      ```

      `:path` 应该包含 Pod 的文件路径。

    * 当你从自定义 Podspec 仓库添加库时，应该在 Podfile 开头指定 [specs 的位置](https://guides.cocoapods.org/syntax/podfile.html#source)：

      ```ruby
      source 'https://github.com/Kotlin/kotlin-cocoapods-spec.git'
 
      target 'kotlin-cocoapods-xcproj' do
          # ... 其他依赖项 ...
          pod 'example'
      end
      ```

2. 在你的项目目录中运行 `pod install`。

   当你第一次运行 `pod install` 时，它会创建 `.xcworkspace` 文件。此文件包含你的原始 `.xcodeproj` 和 CocoaPods 项目。
3. 关闭你的 `.xcodeproj` 文件，并改为打开新的 `.xcworkspace` 文件。这样可以避免项目依赖关系的问题。
4. 在 IntelliJ IDEA 中运行 **重新加载所有 Gradle 项目**（或在 Android Studio 中运行 **同步项目与 Gradle 文件**）
   以重新导入项目。

如果你没有在 Podfile 中做出这些更改，`podInstall` 任务将会失败，CocoaPods 插件将在日志中显示错误消息。

## 可能的问题和解决方案 {id=possible-issues-and-solutions}

### CocoaPods 安装 {id=cocoapods-installation collapsible="true" collapsible="true"}

#### Ruby 安装 {id=ruby-installation}

CocoaPods 是用 Ruby 构建的，你可以使用 macOS 上默认的 Ruby 进行安装。
Ruby 1.9 或更高版本内置了 RubyGems 包管理框架，帮助你安装 [CocoaPods 依赖管理器](https://guides.cocoapods.org/using/getting-started.html#installation)。

如果你在安装 CocoaPods 并使其正常工作时遇到问题，请按照 [此指南](https://www.ruby-lang.org/en/documentation/installation/)
安装 Ruby，或参考 [RubyGems 网站](https://rubygems.org/pages/download/) 安装该框架。

#### 版本兼容性 {id=version-compatibility}

我们建议使用最新的 Kotlin 版本。如果你当前的版本早于 1.7.0，你需要额外安装
[`cocoapods-generate`](https://github.com/square/cocoapods-generate#installation) 插件。

然而，`cocoapods-generate` 与 Ruby 3.0.0 或更高版本不兼容。在这种情况下，请降级 Ruby 或升级 Kotlin
至 1.7.0 或更高版本。

### 使用 Xcode 时的构建错误 {id=build-errors-when-using-xcode collapsible="true" collapsible="true"}

某些 CocoaPods 安装方式可能会导致在 Xcode 中出现构建错误。
通常，Kotlin Gradle 插件会在 `PATH` 中发现 `pod` 可执行文件，但根据你的环境，这可能不一致。

为了显式设置 CocoaPods 安装路径，你可以手动将其添加到项目的 `local.properties` 文件中，或者使用 shell 命令：

* 如果使用代码编辑器，请将以下行添加到 `local.properties` 文件：

    ```text
    kotlin.apple.cocoapods.bin=/Users/Jane.Doe/.rbenv/shims/pod
    ```

* 如果使用终端，请运行以下命令：

    ```shell
    echo -e "kotlin.apple.cocoapods.bin=$(which pod)" >> local.properties
    ```

### 模块未找到 {id=module-not-found collapsible="true" collapsible="true"}

你可能会遇到 `module 'SomeSDK' not found` 错误，这与 [C 语言互操作](native-c-interop.md) 问题相关。
尝试以下解决方法以避免此错误：

#### 指定框架名称 {id=specify-the-framework-name}

1. 在下载的 Pod 目录 `[shared_module_name]/build/cocoapods/synthetic/IOS/Pods/...` 中查找 `module.modulemap` 文件
2. 检查模块内的框架名称，例如 `SDWebImageMapKit {}`。如果框架名称与 Pod 名称不匹配，请显式指定：

    ```kotlin
    pod("SDWebImage/MapKit") {
        moduleName = "SDWebImageMapKit"
    }
    ```
#### 指定头文件 {id=specify-headers}

如果 Pod 不包含 `.modulemap` 文件，比如 `pod("NearbyMessages")`，请显式指定主头文件：

```kotlin
pod("NearbyMessages") {
    version = "1.1.1"
    headers = "GNSMessages.h"
}
```

有关更多信息，请查看 [CocoaPods 文档](https://guides.cocoapods.org/)。
如果以上方法都无效，且你仍然遇到此错误，请在 [YouTrack](https://youtrack.jetbrains.com/newissue?project=kt) 上报告问题。

### Rsync 错误 {id=rsync-error collapsible="true" collapsible="true"}

你可能会遇到 `rsync error: some files could not be transferred` 错误。
这是一个 [已知问题](https://github.com/CocoaPods/CocoaPods/issues/11946)，当 Xcode 中的应用目标启用了用户脚本的沙盒时会发生。

解决此问题的方法：

1. 禁用应用目标中的用户脚本沙盒：

   ![禁用沙盒 CocoaPods](disable-sandboxing-cocoapods.png){width=700}

2. 停止可能被沙盒化的 Gradle 守护进程：

    ```shell
    ./gradlew --stop
    ```
