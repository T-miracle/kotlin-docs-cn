[//]: # (title: 添加 iOS 依赖项)

[//]: # (TODO)

Apple SDK 依赖项（如 Foundation 或 Core Bluetooth）在 Kotlin
Multiplatform 项目中作为一组预构建的库提供。它们不需要任何额外的配置。

To handle iOS dependencies in Kotlin Multiplatform projects, you can manage them with the [cinterop tool](#with-cinterop)
or use the [CocoaPods dependency manager](#with-cocoapods) (pure Swift pods are not supported).

### With cinterop

You can use the cinterop tool to create Kotlin bindings for Objective-C or Swift
declarations. This will allow you to call them from the Kotlin code.

The steps differ a bit for [libraries](#add-a-library) and [frameworks](#add-a-framework),
but the general workflow looks like this:

1. Download your dependency.
2. Build it to get its binaries.
3. Create a special `.def` [definition file](native-definition-file.md) that describes this dependency to cinterop.
4. Adjust your build script to generate bindings during the build.

#### Add a library

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

#### Add a framework

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

### With CocoaPods

1. Perform [initial CocoaPods integration setup](native-cocoapods.md#set-up-an-environment-to-work-with-cocoapods).
2. Add a dependency on a Pod library from the CocoaPods repository that you want to use by including the `pod()`
   function call in `build.gradle(.kts)` of your project.

    <tabs group="build-script">
    <tab title="Kotlin" group-key="kotlin">

    ```kotlin
    kotlin {
        cocoapods {
            version = "2.0"
            //..
            pod("SDWebImage") {
                version = "5.20.0"
            }
        }
    }
    ```

    </tab>
    <tab title="Groovy" group-key="groovy">

    ```groovy
    kotlin {
        cocoapods {
            version = '2.0'
            //..
            pod('SDWebImage') {
                version = '5.20.0'
            }
        }
    }
    ```

    </tab>
    </tabs>

   You can add the following dependencies on a Pod library:

   * [From the CocoaPods repository](native-cocoapods-libraries.md#from-the-cocoapods-repository)
   * [On a locally stored library](native-cocoapods-libraries.md#on-a-locally-stored-library)
   * [From a custom Git repository](native-cocoapods-libraries.md#from-a-custom-git-repository)
   * [From a custom Podspec repository](native-cocoapods-libraries.md#from-a-custom-podspec-repository)
   * [With custom cinterop options](native-cocoapods-libraries.md#with-custom-cinterop-options)

3. Run **Reload All Gradle Projects** in IntelliJ IDEA (or **Sync Project with Gradle Files** in Android Studio)
   to re-import the project.

To use the dependency in your Kotlin code, import the package `cocoapods.<library-name>`. For the example above, it's:

```kotlin
import cocoapods.SDWebImage.*
```

## What's next?

查看有关在多平台项目中添加依赖项的其他资源，并了解更多内容：

* [Connecting platform libraries](native-platform-libs.md)
* [Adding dependencies on multiplatform libraries or other multiplatform projects](multiplatform-add-dependencies.md)
* [Adding Android dependencies](multiplatform-android-dependencies.md)