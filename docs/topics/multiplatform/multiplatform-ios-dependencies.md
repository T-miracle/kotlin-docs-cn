[//]: # (title: 添加 iOS 依赖项)

[//]: # (TODO 等待校对)

Apple SDK 依赖项（如 Foundation 或 Core Bluetooth）在 Kotlin
Multiplatform 项目中作为一组预构建的库提供。它们不需要任何额外的配置。

你还可以在 iOS 源代码集中重用 iOS 生态系统中的其他库和框架。
如果其 API 使用 `@objc` 属性导出到 Objective-C，Kotlin 支持与 Objective-C 依赖项和 Swift 依赖项的互操作性。
目前还不支持纯 Swift 依赖项。

与 CocoaPods 依赖管理器的集成也受支持，但存在相同的限制——你不能使用纯 Swift pods。

我们推荐 [使用 CocoaPods](#with-cocoapods) 来处理 Kotlin Multiplatform 项目中的 iOS 依赖项。
只有在你希望专门调整互操作过程或有其他强烈原因时，才 [手动管理依赖项](#without-cocoapods)。

### 使用 CocoaPods {id=with-cocoapods}

1. 执行 [初始 CocoaPods 集成设置](native-cocoapods.md#set-up-an-environment-to-work-with-cocoapods)。
2. 通过在项目的 `build.gradle(.kts)` 文件中包含 `pod()` 函数调用，添加对你想要使用的 CocoaPods 仓库中的 Pod 库的依赖项。

    <tabs group="build-script">
    <tab title="Kotlin" group-key="kotlin">

    ```kotlin
    kotlin {
        cocoapods {
            //..
            pod("FirebaseAuth") {
                version = "10.16.0"
            }
        }
    }
    ```

    </tab>
    <tab title="Groovy" group-key="groovy">

    ```groovy
    kotlin {
        cocoapods {
            //..
            pod('FirebaseAuth') {
                version = '10.16.0'
            }
        }
    }
    ```

    </tab>
    </tabs>

   你可以添加以下对 Pod 库的依赖项：
  * [来自 CocoaPods 仓库](native-cocoapods-libraries.md#from-the-cocoapods-repository)
  * [本地存储的库](native-cocoapods-libraries.md#on-a-locally-stored-library)
  * [自定义 Git 仓库](native-cocoapods-libraries.md#from-a-custom-git-repository)
  * [自定义 Podspec 仓库](native-cocoapods-libraries.md#from-a-custom-podspec-repository)
  * [使用自定义 cinterop 选项](native-cocoapods-libraries.md#with-custom-cinterop-options)

3. 在 IntelliJ IDEA 中运行 **Reload All Gradle Projects**（或在 Android Studio 中运行
   **Sync Project with Gradle Files**）来重新导入项目。

要在 Kotlin 代码中使用该依赖项，导入包 `cocoapods.<library-name>`。对于上述示例，导入如下：

```kotlin
import cocoapods.FirebaseAuth.*
```

### 不使用 CocoaPods {id=without-cocoapods}

如果你不想使用 CocoaPods，可以使用 cinterop 工具为 Objective-C 或 Swift 声明创建 Kotlin 绑定。
这样你就可以从 Kotlin 代码中调用它们。

对于 [库](#add-a-library-without-cocoapods) 和
[框架](#add-a-framework-without-cocoapods) 的步骤略有不同，但基本思路相同。

1. 下载你的依赖项。
2. 构建它以获取其二进制文件。
3. 创建一个特殊的 `.def` 文件，该文件向 cinterop 描述此依赖项。
4. 调整你的构建脚本，以便在构建过程中生成绑定。

#### 添加不使用 CocoaPods 的库 {id=add-a-library-without-cocoapods}

1. 下载库的源代码，并将其放置在项目中可以引用的位置。

2. 构建该库（库的作者通常会提供构建指南），并获取二进制文件的路径。

3. 在项目中创建一个 `.def` 文件，例如 `DateTools.def`。

4. 在此文件中添加第一行：`language = Objective-C`。如果你想使用纯 C 依赖项，可以省略 language 属性。

5. 为两个必需的属性提供值：
   * `headers` 描述 cinterop 将处理哪些头文件。
   * `package` 设置这些声明应放入的包名。

   例如：
    ```none
    headers = DateTools.h
    package = DateTools
    ```

6. 在构建脚本中添加与此库的互操作性信息：
   * 传递 `.def` 文件的路径。如果你的 `.def` 文件与 cinterop 同名并且放置在
    `src/nativeInterop/cinterop/` 目录中，则可以省略此路径。
   * 使用 `includeDirs` 选项告诉 cinterop 在哪里查找头文件。
   * 配置链接库二进制文件。

    <tabs group="build-script">
    <tab title="Kotlin" group-key="kotlin">

    ```kotlin
    kotlin {
        iosArm64() {
            compilations.getByName("main") {
                val DateTools by cinterops.creating {
                    // `.def` 文件的路径
                    definitionFile.set(project.file("src/nativeInterop/cinterop/DateTools.def"))

                    // 头文件搜索目录（类似于 `-I<path>` 编译器选项）
                    includeDirs("include/this/directory", "path/to/another/directory")
                }
                val anotherInterop by cinterops.creating { /* ... */ }
            }

            binaries.all {
                // 链接库所需的链接器选项。
                linkerOpts("-L/path/to/library/binaries", "-lbinaryname")
            }
        }
    }
    ```

    </tab>
    <tab title="Groovy" group-key="groovy">

    ```groovy
    kotlin {
        iosArm64 {
            compilations.main {
                cinterops {
                    DateTools {
                        // `.def` 文件的路径
                        definitionFile = project.file("src/nativeInterop/cinterop/DateTools.def")

                        // 头文件搜索目录（类似于 `-I<path>` 编译器选项）
                        includeDirs("include/this/directory", "path/to/another/directory")
                    }
                    anotherInterop { /* ... */ }
                }
            }

            binaries.all {
                // 链接库所需的链接器选项。
                linkerOpts "-L/path/to/library/binaries", "-lbinaryname"
            }
        }
    }
    ```

    </tab>
    </tabs>

7. 构建项目。

现在你可以在 Kotlin 代码中使用这个依赖项。为此，导入你在 `.def` 文件中的 `package`
属性中设置的包。对于上述示例，这将是：

```kotlin
import DateTools.*
```

#### 添加不使用 CocoaPods 的框架 {id=add-a-framework-without-cocoapods}

1. 下载框架的源代码，并将其放置在项目中可以引用的位置。

2. 构建框架（框架的作者通常会提供构建指南），并获取二进制文件的路径。

3. 在项目中创建一个 `.def` 文件，例如 `MyFramework.def`。

4. 在此文件中添加第一行：`language = Objective-C`。如果你想使用纯 C 依赖项，可以省略 language 属性。

5. 为这两个必需的属性提供值：
  * `modules` – 应由 cinterop 处理的框架名称。
  * `package` – 这些声明应放入的包名。

   例如：

    ```none
    modules = MyFramework
    package = MyFramework
    ```

6. 在构建脚本中添加与框架的互操作性信息：
   * 传递 `.def` 文件的路径。如果你的 `.def` 文件与 cinterop 同名并且放置在
     `src/nativeInterop/cinterop/` 目录中，则可以省略此路径。
   * 使用 `-framework` 选项将框架名称传递给编译器和链接器。
     使用 `-F` 选项将框架源代码和二进制文件的路径传递给编译器和链接器。

    <tabs group="build-script">
    <tab title="Kotlin" group-key="kotlin">

    ```kotlin
    kotlin {
        iosArm64() {
            compilations.getByName("main") {
                val DateTools by cinterops.creating {
                    // `.def` 文件的路径
                    definitionFile.set(project.file("src/nativeInterop/cinterop/DateTools.def"))

                    compilerOpts("-framework", "MyFramework", "-F/path/to/framework/")
                }
                val anotherInterop by cinterops.creating { /* ... */ }
            }

            binaries.all {
                // 告诉链接器框架的位置。
                linkerOpts("-framework", "MyFramework", "-F/path/to/framework/")
            }
       }
    }
    ```

    </tab>
    <tab title="Groovy" group-key="groovy">

    ```groovy
    kotlin {
        iosArm64 {
            compilations.main {
                cinterops {
                    DateTools {
                        // `.def` 文件的路径
                        definitionFile = project.file("src/nativeInterop/cinterop/MyFramework.def")

                        compilerOpts("-framework", "MyFramework", "-F/path/to/framework/")
                    }
                    anotherInterop { /* ... */ }
                }
            }

            binaries.all {
                // 告诉链接器框架的位置。
                linkerOpts("-framework", "MyFramework", "-F/path/to/framework/")
            }
        }
    }
    ```

    </tab>
    </tabs>

7. 构建项目。

现在你可以在 Kotlin 代码中使用这个依赖项。为此，导入你在 `.def` 文件中的 package
属性中设置的包。对于上述示例，这将是：

```kotlin
import MyFramework.*
```

了解更多关于 [Objective-C 和 Swift 互操作性](native-objc-interop.md) 和
[从 Gradle 配置 cinterop](multiplatform-dsl-reference.md#cinterops)。

## 接下来做什么？ {id=whats-next}

查看有关在多平台项目中添加依赖项的其他资源，并了解更多内容：

* [连接平台特定库](multiplatform-share-on-platforms.md#connect-platform-specific-libraries)
* [添加对多平台库或其他多平台项目的依赖](multiplatform-add-dependencies.md)
* [添加 Android 依赖项](multiplatform-android-dependencies.md)