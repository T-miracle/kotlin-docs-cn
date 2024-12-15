[//]: # (title: 配置编译)

Kotlin 跨平台项目使用编译来生成构件。每个目标可以有一个或多个编译，例如用于生产和测试目的。

对于每个目标，默认的编译包括：

* JVM、JS 和原生目标的 `main` 和 `test` 编译。
* 针对 Android 目标的每个 [Android 构建变体](https://developer.android.com/studio/build/build-variants) 的[编译](#compilation-for-android)。

![编译](compilations.svg)

如果你需要编译生产代码和单元测试之外的内容，例如集成测试或性能测试，你可以[创建一个自定义编译](#create-a-custom-compilation)。

你可以在以下位置配置如何生成构件：

* [项目中的所有编译](#configure-all-compilations)。
* [单个目标的编译](#configure-compilations-for-one-target)，因为一个目标可以有多个编译。
* [特定的编译](#configure-one-compilation)。

查看可用于所有或特定目标的 [编译参数列表](multiplatform-dsl-reference.md#compilation-parameters) 和 [编译器选项](gradle-compiler-options.md)。

## 配置所有编译 {id=configure-all-compilations}

以下示例配置了一个对所有目标通用的编译器选项：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    targets.all {
        compilations.all {
            compilerOptions.configure {
                allWarningsAsErrors.set(true)
            }
        }
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    targets.all {
        compilations.all {
            compilerOptions.configure {
                allWarningsAsErrors = true
            }
        }
    }
}
```

</tab>
</tabs>

或者，你可以使用 `compilerOptions {}` [顶级块](multiplatform-dsl-reference.md#top-level-blocks)：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    @OptIn(ExperimentalKotlinGradlePluginApi::class)
    compilerOptions {
        allWarningsAsErrors.set(true)
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    compilerOptions {
        allWarningsAsErrors = true
    }
}
```

</tab>
</tabs>

> 将 `compilerOptions {}` 作为顶级块的支持是[实验性](components-stability.md#stability-levels-explained)的，需要进行选择加入。
> 它可能会随时被删除或更改。仅将其用于评估目的。
> 我们非常感谢您在 [YouTrack](https://kotl.in/issue) 上提供的反馈。
>
{style="warning"}

## 为单个目标配置编译 {id=configure-compilations-for-one-target}

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    jvm().compilations.all {
        compilerOptions.configure {
            jvmTarget.set(JvmTarget.JVM_1_8)
        }
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    jvm().compilations.all {
        compilerOptions.configure {
            jvmTarget = JvmTarget.JVM_1_8
        }
    }
}
```

</tab>
</tabs>

或者，你可以在目标级别使用 `compilerOptions {}` 块：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    jvm {
        @OptIn(ExperimentalKotlinGradlePluginApi::class)
        compilerOptions {
            jvmTarget.set(JvmTarget.JVM_1_8)
        }
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    jvm {
        compilerOptions {
            jvmTarget = JvmTarget.JVM_1_8
        }
    }
}
```

</tab>
</tabs>

> 在目标级别支持 `compilerOptions {}` 块是[实验性](components-stability.md#stability-levels-explained)的，
> 需要选择加入。它可能会随时被删除或更改。
> 仅将其用于评估目的。我们非常感谢您在 [YouTrack](https://kotl.in/issue) 上提供的反馈。
>
{style="warning"}

## 配置一个编译 {id=configure-one-compilation}

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    jvm {
        val main by compilations.getting {
            compilerOptions.configure {
                jvmTarget.set(JvmTarget.JVM_1_8)
            }
        }
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    jvm {
        compilations.main {
            compilerOptions.configure {
                jvmTarget = JvmTarget.JVM_1_8
            }
        }
    }
}
```

</tab>
</tabs>

## 创建自定义编译 {id=create-a-custom-compilation}

如果你需要编译生产代码和单元测试之外的内容，例如集成测试或性能测试，可以创建一个自定义编译。
 
例如，为 `jvm()` 目标的集成测试创建一个自定义编译，可以向 `compilations` 集合中添加一个新项。
 
> 对于自定义编译，你需要手动设置所有依赖关系。
> 自定义编译的默认源代码集不依赖于 `commonMain` 和 `commonTest` 源代码集。
>
{style="note"}

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    jvm() {
        compilations {
            val main by getting
            
            val integrationTest by compilations.creating {
                defaultSourceSet {
                    dependencies {
                        // 依赖于 main 编译的编译类路径和输出进行编译：
                        implementation(main.compileDependencyFiles + main.output.classesDirs)
                        implementation(kotlin("test-junit"))
                        /* ... */
                    }
                }
                
                // 创建一个测试任务来运行此编译产生的测试：
                tasks.register<Test>("integrationTest") {
                    // 使用包含编译依赖项（包括 'main'）、运行时依赖项和此编译输出的类路径运行测试：
                    classpath = compileDependencyFiles + runtimeDependencyFiles + output.allOutputs
                    
                    // 仅运行此编译输出的测试：
                    testClassesDirs = output.classesDirs
                }
            }
        }
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    jvm() {
        compilations.create('integrationTest') {
            defaultSourceSet {
                dependencies {
                    def main = compilations.main
                    // 依赖于 main 编译的编译类路径和输出进行编译：
                    implementation(main.compileDependencyFiles + main.output.classesDirs)
                    implementation kotlin('test-junit')
                    /* ... */
                }
            }
           
            // 创建一个测试任务来运行此编译产生的测试：
            tasks.register('jvmIntegrationTest', Test) {
                // 使用包含编译依赖项（包括 'main'）、运行时依赖项和此编译输出的类路径运行测试：
                classpath = compileDependencyFiles + runtimeDependencyFiles + output.allOutputs
                
                // 仅运行此编译输出的测试：
                testClassesDirs = output.classesDirs
            }
        }
    }
}
```

</tab>
</tabs>

你还需要在其他情况下创建自定义编译，例如，如果你想将不同
JVM 版本的编译合并到最终构件中，或者你已经在 Gradle 中设置了源代码集并想迁移到跨平台项目。

## 在 JVM 编译中使用 Java 源代码 {id=use-java-sources-in-jvm-compilations}

在使用 [项目向导](https://kmp.jetbrains.com/) 创建项目时，Java 源代码会包含在 JVM 目标的编译中。

在构建脚本中，以下部分应用了 Gradle 的 `java` 插件，并配置了目标以配合它：

```kotlin
kotlin {
    jvm {
        withJava()
    }
}
```

Java 源文件被放置在 Kotlin 源根目录的子目录中。例如，路径如下：

![Java 源文件](java-source-paths.png){width=200}

公共源代码集不能包含 Java 源代码。

由于当前的限制，Kotlin 插件会替代 Java 插件配置的一些任务：

* 目标的 JAR 任务，而不是 `jar`（例如，`jvmJar`）。
* 目标的测试任务，而不是 `test`（例如，`jvmTest`）。
* 资源由编译的等效任务处理，而不是 `*ProcessResources` 任务。

此目标的发布由 Kotlin 插件处理，不需要 Java 插件特有的步骤。

## 配置与本地语言的互操作性 {id=configure-interop-with-native-languages}

Kotlin 提供了与[本地语言的互操作性](native-c-interop.md)和用于特定编译的 DSL 配置。

| 本地语言                   | 支持的平台                            | 备注                                  |
|------------------------|----------------------------------|-------------------------------------|
| C                      | 所有平台，除了 WebAssembly              |                                     |
| Objective-C            | Apple 平台（macOS、iOS、watchOS、tvOS） |                                     |
| 通过 Objective-C 的 Swift | Apple 平台（macOS、iOS、watchOS、tvOS） | Kotlin 只能使用带有 `@objc` 属性的 Swift 声明。 |

编译可以与多个本地库交互。通过在 [定义文件](native-definition-file.md) 或构建文件的
[`cinterops` 块](multiplatform-dsl-reference.md#cinterops) 中配置互操作性：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    linuxX64 { // 替换为你需要的目标。
        compilations.getByName("main") {
            val myInterop by cinterops.creating {
                // 描述本地 API 的 def 文件。
                // 默认路径是 src/nativeInterop/cinterop/<interop-name>.def
                definitionFile.set(project.file("def-file.def"))
                
                // 放置生成的 Kotlin API 的包名。
                packageName("org.sample")
                
                // 传递给编译器的选项，通过 cinterop 工具。
                compilerOpts("-Ipath/to/headers")
              
                // 查找头文件的目录。
                includeDirs.apply {
                    // 头文件搜索目录（相当于 -I<path> 编译器选项）。
                    allHeaders("path1", "path2")
                    
                    // 额外的目录，搜索 'headerFilter' def 文件选项中列出的头文件。
                    // -headerFilterAdditionalSearchPrefix 命令行选项的等效项。
                    headerFilterOnly("path1", "path2")
                }
                // includeDirs.allHeaders 的快捷方式。
                includeDirs("include/directory", "another/directory")
            }
            
            val anotherInterop by cinterops.creating { /* ... */ }
        }
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    linuxX64 { // 替换为你需要的目标。
        compilations.main {
            cinterops {
                myInterop {
                    // 描述本地 API 的 def 文件。
                    // 默认路径是 src/nativeInterop/cinterop/<interop-name>.def
                    definitionFile = project.file("def-file.def")
                    
                    // 放置生成的 Kotlin API 的包名。
                    packageName 'org.sample'
                    
                    // 传递给编译器的选项，通过 cinterop 工具。
                    compilerOpts '-Ipath/to/headers'
                    
                    // 查找头文件的目录（相当于 -I<path> 编译器选项）。
                    includeDirs.allHeaders("path1", "path2")
                    
                    // 额外的目录，搜索 'headerFilter' def 文件选项中列出的头文件。
                    // -headerFilterAdditionalSearchPrefix 命令行选项的等效项。
                    includeDirs.headerFilterOnly("path1", "path2")
                    
                    // includeDirs.allHeaders 的快捷方式。
                    includeDirs("include/directory", "another/directory")
                }
                
                anotherInterop { /* ... */ }
            }
        }
    }
}
```

</tab>
</tabs>

## Android 编译 {id=compilation-for-android}
 
默认情况下，为 Android 目标创建的编译与 [Android 构建变体](https://developer.android.com/studio/build/build-variants) 相关联：
每个构建变体下会创建一个相同名称的 Kotlin 编译。

然后，对于每个为每个变体编译的 [Android 源集](https://developer.android.com/studio/build/build-variants#sourcesets)，
会根据该源集的名称并在前面加上目标名称创建一个 Kotlin 源集，
例如 Android 源集 `debug` 对应的 Kotlin 源集 `androidDebug`，以及名为 `androidTarget` 的 Kotlin 目标。  
这些 Kotlin 源集会被相应地添加到变体的编译中。

默认源代码集 `commonMain` 会被添加到每个生产（应用程序或库）变体的编译中。
`commonTest` 源代码集同样被添加到单元测试和仪器测试变体的编译中。

`kapt` 注解处理也得到支持，但由于当前的限制，它要求在配置 `kapt` 依赖项之前先创建 Android 目标，
这需要在顶层的 `dependencies {}` 块中完成，而不是在 Kotlin 源代码集依赖项中完成。

```kotlin
kotlin {
    androidTarget { /* ... */ }
}

dependencies {
    kapt("com.my.annotation:processor:1.0.0")
}
```

## 源代码集层级的编译 {id=compilation-of-the-source-set-hierarchy}

Kotlin 可以使用 `dependsOn` 关系构建一个 [源代码集层级](multiplatform-share-on-platforms.md#share-code-on-similar-platforms)。

![源代码集层级](jvm-js-main.svg)

如果源代码集 `jvmMain` 依赖于源代码集 `commonMain`，则：

* 每当 `jvmMain` 为某个特定目标编译时，`commonMain` 也参与该编译，并且也被编译成相同目标的二进制形式，例如 JVM 类文件。
* `jvmMain` 的源代码可以访问 `commonMain` 的声明，包括内部声明，还可以访问 `commonMain` 的
[依赖项](multiplatform-add-dependencies.md)，即使是指定为 `implementation` 依赖项的。
* `jvmMain` 可以包含针对 `commonMain` 的 [预期声明](multiplatform-expect-actual.md) 的平台特定实现。
* `commonMain` 的资源总是会与 `jvmMain` 的资源一起处理和复制。
* `jvmMain` 和 `commonMain` 的 [语言设置](multiplatform-dsl-reference.md#language-settings) 应该保持一致。

语言设置的一致性通过以下方式进行检查：
* `jvmMain` 应设置一个大于或等于 `commonMain` 的 `languageVersion`。
* `jvmMain` 应启用 `commonMain` 启用的所有不稳定语言特性（对于 bugfix 特性没有此要求）。
* `jvmMain` 应使用 `commonMain` 使用的所有实验性注解。
* `apiVersion`、bugfix 语言特性和 `progressiveMode` 可以任意设置。

## 在 Gradle 中配置 Isolated Projects 特性 {id=configure-isolated-projects-feature-in-gradle}

> 此功能是[实验性](components-stability.md#stability-levels-explained)的，目前在 Gradle 中处于预先 alpha 状态。  
> 仅在 Gradle 版本 8.10 或更高版本中使用，并仅限于评估目的。该功能可能随时被废弃或更改。  
> 我们非常欢迎你在[YouTrack](https://youtrack.jetbrains.com/issue/KT-57279/Support-Gradle-Project-Isolation-Feature-for-Kotlin-Multiplatform)上提供反馈。  
> 需要显式开启（详见下文）。
> 
{style="warning"}

Gradle 提供了[Isolated Projects](https://docs.gradle.org/current/userguide/isolated_projects.html)功能，
通过“隔离”各个项目来提高构建性能。
该功能在项目之间分离构建脚本和插件，允许它们安全地并行运行。

要启用此功能，请按照 Gradle 的说明[设置系统属性](https://docs.gradle.org/current/userguide/isolated_projects.html#how_do_i_use_it)。

如果你想在启用 Gradle 中的 Isolated Projects 之前检查兼容性，可以使用新的 Kotlin Gradle 插件模型来测试你的项目。
将以下 Gradle 属性添加到 `gradle.properties` 文件中：

```none
kotlin.kmp.isolated-projects.support=enable
```

如果你决定稍后启用 Isolated Projects 功能，记得移除此 Gradle 属性。
Kotlin Gradle 插件会直接应用并管理此 Gradle 属性。

有关 Isolated Projects 功能的更多信息，请参阅[Gradle 的文档](https://docs.gradle.org/current/userguide/isolated_projects.html)。