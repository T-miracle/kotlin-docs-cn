[//]: # (title: 跨平台 Gradle DSL 参考)

Kotlin 跨平台 Gradle 插件是用于创建 [Kotlin 跨平台](multiplatform.md) 项目的工具。
这里提供了其内容的参考；在编写 Kotlin 跨平台项目的 Gradle 构建脚本时，请将其作为提醒。
了解 [Kotlin 跨平台项目的概念、如何创建和配置它们](multiplatform-get-started.md)。

## ID 和版本 {id=id-and-version}

Kotlin 跨平台 Gradle 插件的完全限定名是 `org.jetbrains.kotlin.multiplatform`。
如果使用 Kotlin Gradle DSL，可以通过 `kotlin("multiplatform")` 应用该插件。
插件版本与 Kotlin 版本相匹配。 最新版本为 %kotlinVersion%。

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
plugins {
    kotlin("multiplatform") version "%kotlinVersion%"
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
plugins {
    id 'org.jetbrains.kotlin.multiplatform' version '%kotlinVersion%'
}
```

</tab>
</tabs>

## 顶级块 {id=top-level-blocks}

`kotlin {}` 是 Gradle 构建脚本中用于跨平台项目配置的顶级块。
在 `kotlin {}` 中，可以编写以下块：

| **块**             | **描述**                                                                                                                            |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------| 
| _\<targetName\>_  | 声明项目的特定目标。 可用目标的名称列在 [目标](#targets) 部分。                                                                                           |
| `targets`         | 项目的所有目标。                                                                                                                          |
| `presets`         | 所有预定义目标。 用于一次 [配置多个预定义目标](#targets)。                                                                                              |
| `sourceSets`      | 配置预定义并声明项目的自定义 [源代码集](#source-sets)。                                                                                              |
| `compilerOptions` | 扩展级别的通用 [编译器选项](gradle-compiler-options.md)，作为所有目标和共享源代码集的默认值。 要使用它，请添加以下选择加入：`@OptIn(ExperimentalKotlinGradlePluginApi::class)`。 |

> `compilerOptions {}` 作为顶级块的支持是 [实验性](components-stability.md#stability-levels-explained)，并需要选择加入。
> 它可能随时被删除或更改。仅用于评估目的。
> 我们希望您在 [YouTrack](https://kotl.in/issue) 上提供反馈。
>
{style="warning"}

## 目标 {id=targets}

_目标_ 是构建的一部分，负责编译、测试和打包针对支持平台的软件。
Kotlin 为每个平台提供目标预设。请查看如何 [使用目标预设](multiplatform-set-up-targets.md)。

每个目标可以有一个或多个 [编译](#compilations)。 除了用于测试和生产目的的默认编译外，还可以
[创建自定义编译](multiplatform-configure-compilations.md#create-a-custom-compilation)。

跨平台项目的目标在 `kotlin {}` 内的相应块中描述，例如 `jvm`、`android`、`iosArm64`。 可用目标的完整列表如下：

<table>
    <tr>
        <th>目标平台</th>
        <th>目标预设</th>
        <th>备注</th>
    </tr>
    <tr>
        <td>Kotlin/JVM</td>
        <td><code>jvm</code></td>
        <td></td>
    </tr>
    <tr>
        <td rowspan="2">Kotlin/Wasm</td>
        <td><code>wasmJs</code></td>
        <td>如果计划在 JavaScript 运行时运行项目，请使用此选项。</td>
    </tr>
    <tr>
        <td><code>wasmWasi</code></td>
        <td>如果需要支持 <a href="https://github.com/WebAssembly/WASI">WASI</a> 系统接口，请使用此选项。</td>
    </tr>
    <tr>
        <td>Kotlin/JS</td>
        <td><code>js</code></td>
        <td>
            <p>选择执行环境：</p>
            <list>
                <li><code>browser {}</code> 用于在浏览器中运行的应用。</li>
                <li><code>nodejs {}</code> 用于在 Node.js 上运行的应用。</li>
            </list>
            <p>更多信息请参见 <a href="js-project-setup.md#execution-environments">设置 Kotlin/JS 项目</a>。</p>
        </td>
    </tr>
    <tr>
        <td>Kotlin/Native</td>
        <td></td>
        <td>
            <p>了解 macOS、Linux 和 Windows 主机当前支持的目标，请参见 <a href="native-target-support.md">Kotlin/Native 目标支持</a>。</p>
        </td>
    </tr>
    <tr>
        <td>Android 应用和库</td>
        <td><code>android</code></td>
        <td>
            <p>手动应用 Android Gradle 插件： <code>com.android.application</code> 或 <code>com.android.library</code>。</p>
            <p>每个 Gradle 子项目只能创建一个 Android 目标。</p>
        </td>
    </tr>
</table>

> 不被当前主机支持的目标在构建期间会被忽略，因此不会被发布。
>
{style="note"}

```groovy
kotlin {
    jvm()
    iosArm64()
    macosX64()
    js().browser()
}
```

目标的配置可以包括两个部分：

* 所有目标均可用的 [通用配置](#common-target-configuration)。
* 目标特定配置。

每个目标可以有一个或多个 [编译](#compilations)。

### 通用目标配置 {id=common-target-configuration}

在任何目标块中，可以使用以下声明：

| **名称**              | **描述**                                                                                                                                                                                                   | 
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------| 
| `attributes`        | 用于 [区分同一平台的多个目标](multiplatform-set-up-targets.md#distinguish-several-targets-for-one-platform) 的属性。                                                                                                      |
| `preset`            | 目标创建时使用的预设（如果有）。                                                                                                                                                                                         |
| `platformType`      | 指定该目标的 Kotlin 平台。 可用值：`jvm`、`androidJvm`、`js`、`wasm`、`native`、`common`。                                                                                                                                  |
| `artifactsTaskName` | 生成该目标结果工件的任务名称。                                                                                                                                                                                          |
| `components`        | 用于设置 Gradle 发布的组件。                                                                                                                                                                                       |
| `compilerOptions`   | 用于该目标的 [编译器选项](gradle-compiler-options.md)。 此声明将覆盖在 [顶级](multiplatform-dsl-reference.md#top-level-blocks) 配置的任何 `compilerOptions {}`。 要使用它，请添加以下选择加入：`@OptIn(ExperimentalKotlinGradlePluginApi::class)`。 |

> 将 `compilerOptions {}` 作为通用目标配置的支持是 [实验性](components-stability.md#stability-levels-explained)，并需要选择加入。
> 它可能随时被删除或更改。仅用于评估目的。
> 我们希望您在 [YouTrack](https://kotl.in/issue) 上提供反馈。
>
{style="warning"}

### JVM 目标 {id=jvm-targets}

除了 [通用目标配置](#common-target-configuration) 之外，`jvm` 目标具有特定功能函数：

| **名称**        | **描述**                                                    | 
|------------------|------------------------------------------------------------| 
| `withJava()`     | 将 Java 源文件包含到 JVM 目标的编译中。                   |

对于包含 Java 和 Kotlin 源文件的项目，请使用此功能。请注意，Java 源文件的默认源目录不遵循 Java 插件的默认设置。
相反，它们源自 Kotlin 源代码集。例如，如果 JVM 目标的默认名称为 `jvm`，则路径为 `src/jvmMain/java`（用于生产 Java 源文件）和
`src/jvmTest/java`（用于测试 Java 源文件）。
了解有关 [JVM 编译中的 Java 源文件](multiplatform-configure-compilations.md#use-java-sources-in-jvm-compilations) 的更多信息。

```kotlin
kotlin {
    jvm {
        withJava()
    } 
}
```

### Web 目标 {id=web-targets}

`js {}` 块描述 Kotlin/JS 目标的配置，`wasmJs {}` 块描述与 JavaScript 互操作的 Kotlin/Wasm 目标的配置。
根据目标执行环境，它们可以包含以下两个块之一：

| **名称**                | **描述**         | 
|-----------------------|----------------| 
| [`browser`](#browser) | 浏览器目标的配置。      |
| [`nodejs`](#node-js)  | Node.js 目标的配置。 |

了解有关 [配置 Kotlin/JS 项目](js-project-setup.md) 的更多信息。

单独的 `wasmWasi {}` 块描述支持 WASI 系统接口的 Kotlin/Wasm 目标的配置。
在这里，仅可用 [`nodejs`](#node-js) 执行环境：

```kotlin
kotlin {
    wasmWasi {
        nodejs()
        binaries.executable()
    }
}
```

所有 Web 目标 `js`、`wasmJs` 和 `wasmWasi` 也支持 `binaries.executable()` 调用。
这明确指示 Kotlin 编译器生成可执行文件。
有关更多信息，请参见 Kotlin/JS 文档中的 [执行环境](js-project-setup.md#execution-environments)。

#### 浏览器 {id=browser}

`browser {}` 可以包含以下配置块：

| **名称**         | **描述**                                           | 
|----------------|--------------------------------------------------| 
| `testRuns`     | 测试执行的配置。                                         |
| `runTask`      | 项目运行的配置。                                         |
| `webpackTask`  | 使用 [Webpack](https://webpack.js.org/) 进行项目打包的配置。 |
| `dceTask`      | [死代码消除](javascript-dce.md) 的配置。                  |
| `distribution` | 输出文件的路径。                                         |

```kotlin
kotlin {
    js().browser {
        webpackTask { /* ... */ }
        testRuns { /* ... */ }
        dceTask {
            keep("myKotlinJsApplication.org.example.keepFromDce")
        }
        distribution {
            directory = File("$projectDir/customdir/")
        }
    }
}
```

#### Node.js {id=node-js}

`nodejs {}` 可以包含测试和运行任务的配置：

| **名称**     | **描述**   | 
|------------|----------| 
| `testRuns` | 测试执行的配置。 |
| `runTask`  | 项目运行的配置。 |

```kotlin
kotlin {
    js().nodejs {
        runTask { /* ... */ }
        testRuns { /* ... */ }
    }
}
```

### 本地目标 {id=native-targets}

对于本地目标，提供以下特定块：

| **名称**      | **描述**                         | 
|-------------|--------------------------------| 
| `binaries`  | 要生成的 [二进制文件](#binaries) 的配置。   |
| `cinterops` | 与 C 库的 [互操作性](#cinterops) 的配置。 |

#### 二进制文件 {id=binaries}

以下是二进制文件的类型：

| **名称**       | **描述**          | 
|--------------|-----------------| 
| `executable` | 产品可执行文件。        |
| `test`       | 测试可执行文件。        |
| `sharedLib`  | 共享库。            |
| `staticLib`  | 静态库。            |
| `framework`  | Objective-C 框架。 |

```kotlin
kotlin {
    linuxX64 { // 请使用您的目标。
        binaries {
            executable {
                // 二进制文件配置。
            }
        }
    }
}
```

对于二进制文件配置，提供以下参数：

| **名称**        | **描述**                                                                  | 
|---------------|-------------------------------------------------------------------------| 
| `compilation` | 生成二进制文件的编译。默认情况下，`test` 二进制文件基于 `test` 编译，而其他二进制文件基于 `main` 编译。         |
| `linkerOpts`  | 在构建二进制文件时传递给系统链接器的选项。                                                   |
| `baseName`    | 输出文件的自定义基本名称。最终文件名将通过在此基本名称前后添加系统相关的前缀和后缀形成。                            |
| `entryPoint`  | 可执行二进制文件的入口点函数。默认情况下，它是根包中的 `main()`。                                   |
| `outputFile`  | 访问输出文件。                                                                 |
| `linkTask`    | 访问链接任务。                                                                 |
| `runTask`     | 访问可执行二进制文件的运行任务。对于 `linuxX64`、`macosX64` 或 `mingwX64` 之外的目标，此值为 `null`。 |
| `isStatic`    | 对于 Objective-C 框架，包含静态库而不是动态库。                                          |

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
binaries {
    executable("my_executable", listOf(RELEASE)) {
        // 基于测试编译构建二进制文件。
        compilation = compilations["test"]

        // 链接器的自定义命令行选项。
        linkerOpts = mutableListOf("-L/lib/search/path", "-L/another/search/path", "-lmylib")

        // 输出文件的基本名称。
        baseName = "foo"

        // 自定义入口点函数。
        entryPoint = "org.example.main"

        // 访问输出文件。
        println("可执行文件路径: ${outputFile.absolutePath}")

        // 访问链接任务。
        linkTask.dependsOn(additionalPreprocessingTask)

        // 访问运行任务。
        // 注意，非主机平台的 runTask 为 null。
        runTask?.dependsOn(prepareForRun)
    }

    framework("my_framework", listOf(RELEASE)) {
        // 在框架中包含静态库而不是动态库。
        isStatic = true
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
binaries {
    executable('my_executable', [RELEASE]) {
        // 基于测试编译构建二进制文件。
        compilation = compilations.test

        // 链接器的自定义命令行选项。
        linkerOpts = ['-L/lib/search/path', '-L/another/search/path', '-lmylib']

        // 输出文件的基本名称。
        baseName = 'foo'

        // 自定义入口点函数。
        entryPoint = 'org.example.main'

        // 访问输出文件。
        println("可执行文件路径: ${outputFile.absolutePath}")

        // 访问链接任务。
        linkTask.dependsOn(additionalPreprocessingTask)

        // 访问运行任务。
        // 注意，非主机平台的 runTask 为 null。
        runTask?.dependsOn(prepareForRun)
    }

    framework('my_framework' [RELEASE]) {
        // 在框架中包含静态库而不是动态库。
        isStatic = true
    }
}
```

</tab>
</tabs>

了解更多关于 [构建本地二进制文件](multiplatform-build-native-binaries.md) 的信息。

#### CInterops {id=cinterops}

`cinterops` 是与原生库互操作的描述集合。要提供与库的互操作性，向 `cinterops` 添加条目并定义其参数：

| **名称**           | **描述**                   | 
|------------------|--------------------------| 
| `definitionFile` | 描述本地 API 的 `.def` 文件。    |
| `packageName`    | 生成的 Kotlin API 的包前缀。     |
| `compilerOpts`   | 通过 cinterop 工具传递给编译器的选项。 |
| `includeDirs`    | 查找头文件的目录。                |

了解更多关于 [配置与原生语言的互操作性](multiplatform-configure-compilations.md#configure-interop-with-native-languages) 的信息。

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    linuxX64 { // 替换为所需的目标。
        compilations.getByName("main") {
            val myInterop by cinterops.creating {
                // 描述本地 API 的 Def 文件。
                // 默认路径是 src/nativeInterop/cinterop/<interop-name>.def
                definitionFile.set(project.file("def-file.def"))

                // 生成的 Kotlin API 的包。
                packageName("org.sample")

                // 传递给 cinterop 工具的编译器选项。
                compilerOpts("-Ipath/to/headers")

                // 头文件搜索目录（类似于 -I<path> 编译器选项）。
                includeDirs.allHeaders("path1", "path2")

                // includeDirs.allHeaders 的简写。
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
    linuxX64 { // 替换为所需的目标。
        compilations.main {
            cinterops {
                myInterop {
                    // 描述本地 API 的 Def 文件。
                    // 默认路径是 src/nativeInterop/cinterop/<interop-name>.def
                    definitionFile = project.file("def-file.def")

                    // 生成的 Kotlin API 的包。
                    packageName 'org.sample'

                    // 传递给 cinterop 工具的编译器选项。
                    compilerOpts '-Ipath/to/headers'

                    // 头文件搜索目录（类似于 -I<path> 编译器选项）。
                    includeDirs.allHeaders("path1", "path2")

                    // includeDirs.allHeaders 的简写。
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

### Android 目标 {id=android-targets}

Kotlin 跨平台插件包含两个针对 Android 目标的特定函数。  
这两个函数帮助你配置 [构建变体](https://developer.android.com/studio/build/build-variants)：

| **名称**                        | **描述**                                                                                         | 
|-------------------------------|------------------------------------------------------------------------------------------------| 
| `publishLibraryVariants()`    | 指定要发布的构建变体。了解更多关于 [发布 Android 库](multiplatform-publish-lib.md#publish-an-android-library) 的信息。 |
| `publishAllLibraryVariants()` | 发布所有构建变体。                                                                                      |

```kotlin
kotlin {
    android {
        publishLibraryVariants("release", "debug")
    }
}
```

了解更多关于 [Android 的编译](multiplatform-configure-compilations.md#compilation-for-android) 的信息。

> `kotlin` 内部的 `android` 配置并不会替换任何 Android 项目的构建配置。  
> 了解更多关于为 Android 项目编写构建脚本的信息，请参见 [Android 开发者文档](https://developer.android.com/studio/build)。
>
{style="note"}

## Source sets

The `sourceSets {}` block describes source sets of the project. A source set contains Kotlin source files that participate
in compilations together, along with their resources, dependencies, and language settings. 

A multiplatform project contains [predefined](#predefined-source-sets) source sets for its targets;
developers can also create [custom](#custom-source-sets) source sets for their needs.

### Predefined source sets

Predefined source sets are set up automatically upon creation of a multiplatform project.
Available predefined source sets are the following:

| **Name**                            | **Description**                                                                                                                                                                                       | 
|-------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `commonMain`                        | Code and resources shared between all platforms. Available in all multiplatform projects. Used in all main [compilations](#compilations) of a project.                                                |
| `commonTest`                        | Test code and resources shared between all platforms. Available in all multiplatform projects. Used in all test compilations of a project.                                                            |
| _\<targetName\>\<compilationName\>_ | Target-specific sources for a compilation. _\<targetName\>_ is the name of a predefined target and _\<compilationName\>_ is the name of a compilation for this target. Examples: `jsTest`, `jvmMain`. |

With Kotlin Gradle DSL, the sections of predefined source sets should be marked `by getting`.

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    sourceSets {
        val commonMain by getting { /* ... */ }
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin { 
    sourceSets { 
        commonMain { /* ... */ } 
    }
}
```

</tab>
</tabs>

Learn more about [source sets](multiplatform-discover-project.md#source-sets).

### Custom source sets

Custom source sets are created by the project developers manually.
To create a custom source set, add a section with its name inside the `sourceSets` section.
If using Kotlin Gradle DSL, mark custom source sets `by creating`.

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin { 
    sourceSets { 
        val myMain by creating { /* ... */ } // create a new source set by the name 'MyMain'
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin { 
    sourceSets { 
        myMain { /* ... */ } // create or configure a source set by the name 'myMain' 
    }
}
```

</tab>
</tabs>

Note that a newly created source set isn't connected to other ones. To use it in the project's compilations,
[connect it with other source sets](multiplatform-hierarchy.md#manual-configuration).

### Source set parameters

Configurations of source sets are stored inside the corresponding blocks of `sourceSets {}`. A source set has the following parameters:

| **Name**           | **Description**                                                                        | 
|--------------------|----------------------------------------------------------------------------------------|
| `kotlin.srcDir`    | Location of Kotlin source files inside the source set directory.                       |
| `resources.srcDir` | Location of resources inside the source set directory.                                 |
| `dependsOn`        | [Connection with another source set](multiplatform-hierarchy.md#manual-configuration). |
| `dependencies`     | [Dependencies](#dependencies) of the source set.                                       |
| `languageSettings` | [Language settings](#language-settings) applied to the source set.                     |

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin { 
    sourceSets { 
        val commonMain by getting {
            kotlin.srcDir("src")
            resources.srcDir("res")

            dependencies {
                /* ... */
            }
        }
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin { 
    sourceSets { 
        commonMain {
            kotlin.srcDir('src')
            resources.srcDir('res')

            dependencies {
                /* ... */
            }
        }
    }
}
``` 

</tab>
</tabs>

## Compilations

A target can have one or more compilations, for example, for production or testing. There are [predefined compilations](#predefined-compilations)
that are added automatically upon target creation. You can additionally create [custom compilations](#custom-compilations).

To refer to all or some particular compilations of a target, use the `compilations` object collection.
From `compilations`, you can refer to a compilation by its name.

Learn more about [configuring compilations](multiplatform-configure-compilations.md).

### Predefined compilations

Predefined compilations are created automatically for each target of a project except for Android targets.
Available predefined compilations are the following:

| **Name** | **Description**                     | 
|----------|-------------------------------------|
| `main`   | Compilation for production sources. |
| `test`   | Compilation for tests.              |

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    jvm {
        val main by compilations.getting {
            output // get the main compilation output
        }

        compilations["test"].runtimeDependencyFiles // get the test runtime classpath
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    jvm {
        compilations.main.output // get the main compilation output
        compilations.test.runtimeDependencyFiles // get the test runtime classpath
    }
}
```

</tab>
</tabs>

### Custom compilations

In addition to predefined compilations, you can create your own custom compilations.
To create a custom compilation, add a new item into the `compilations` collection.
If using Kotlin Gradle DSL, mark custom compilations `by creating`.

Learn more about creating a [custom compilation](multiplatform-configure-compilations.md#create-a-custom-compilation).

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    jvm() {
        compilations {
            val integrationTest by compilations.creating {
                defaultSourceSet {
                    dependencies {
                        /* ... */
                    }
                }

                // Create a test task to run the tests produced by this compilation:
                tasks.register<Test>("integrationTest") {
                    /* ... */
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
                    /* ... */
                }
            }

            // Create a test task to run the tests produced by this compilation:
            tasks.register('jvmIntegrationTest', Test) {
                /* ... */
            }
        }
    }
}
```

</tab>
</tabs>

### Compilation parameters

A compilation has the following parameters:

| **Name**                 | **Description**                                                                                                                                                           | 
|--------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `defaultSourceSet`       | The compilation's default source set.                                                                                                                                     |
| `kotlinSourceSets`       | Source sets participating in the compilation.                                                                                                                             |
| `allKotlinSourceSets`    | Source sets participating in the compilation and their connections via `dependsOn()`.                                                                                     |
| `compilerOptions`        | Compiler options applied to the compilation. For the list of available options, see [Compiler options](gradle-compiler-options.md).                                       |
| `compileKotlinTask`      | Gradle task for compiling Kotlin sources.                                                                                                                                 |
| `compileKotlinTaskName`  | Name of `compileKotlinTask`.                                                                                                                                              |
| `compileAllTaskName`     | Name of the Gradle task for compiling all sources of a compilation.                                                                                                       |
| `output`                 | The compilation output.                                                                                                                                                   |
| `compileDependencyFiles` | Compile-time dependency files (classpath) of the compilation. For all Kotlin/Native compilations, this automatically includes standard library and platform dependencies. |
| `runtimeDependencyFiles` | Runtime dependency files (classpath) of the compilation.                                                                                                                  |

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    jvm {
        val main by compilations.getting {
            compilerOptions.configure { 
                // Set up the Kotlin compiler options for the 'main' compilation:
                jvmTarget.set(JvmTarget.JVM_1_8)
            }
        
            compileKotlinTask // get the Kotlin task 'compileKotlinJvm' 
            output // get the main compilation output
        }
        
        compilations["test"].runtimeDependencyFiles // get the test runtime classpath
    }

    // Configure all compilations of all targets:
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
    jvm {
        compilations.main.compilerOptions.configure { 
            // Setup the Kotlin compiler options for the 'main' compilation:
            jvmTarget = JvmTarget.JVM_1_8
        }

        compilations.main.compileKotlinTask // get the Kotlin task 'compileKotlinJvm' 
        compilations.main.output // get the main compilation output
        compilations.test.runtimeDependencyFiles // get the test runtime classpath
    }

    // Configure all compilations of all targets:
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

Alternatively, to configure compiler options that are common for all targets, you can use the `compilerOptions {}` [top-level block](multiplatform-dsl-reference.md#top-level-blocks):

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    
    // Configure all compilations of all targets:
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
    
    // Configure all compilations of all targets:
    compilerOptions {
        allWarningsAsErrors = true
    }
}
```

</tab>
</tabs>

> The support for `compilerOptions {}` as a top-level block is [Experimental](components-stability.md#stability-levels-explained)
> and requires opt-in. It may be dropped or changed at any time. Use it only for evaluation purposes. We would appreciate
> your feedback on it in [YouTrack](https://kotl.in/issue).
>
{style="warning"}


## Dependencies

The `dependencies {}` block of the source set declaration contains the dependencies of this source set.

Learn more about [configuring dependencies](gradle-configure-project.md).

There are four types of dependencies:

| **Name**         | **Description**                                                                     | 
|------------------|-------------------------------------------------------------------------------------|
| `api`            | Dependencies used in the API of the current module.                                 |
| `implementation` | Dependencies used in the module but not exposed outside it.                         |
| `compileOnly`    | Dependencies used only for compilation of the current module.                       |
| `runtimeOnly`    | Dependencies available at runtime but not visible during compilation of any module. |

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    sourceSets {
        val commonMain by getting {
            dependencies {
                api("com.example:foo-metadata:1.0")
            }
        }
        val jvmMain by getting {
            dependencies {
                implementation("com.example:foo-jvm:1.0")
            }
        }
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    sourceSets {
        commonMain {
            dependencies {
                api 'com.example:foo-metadata:1.0'
            }
        }
        jvmMain {
            dependencies {
                implementation 'com.example:foo-jvm:1.0'
            }
        }
    }
}
```

</tab>
</tabs>

Additionally, source sets can depend on each other and form a hierarchy.
In this case, the [`dependsOn()`](#source-set-parameters) relation is used.

Source set dependencies can also be declared in the top-level `dependencies {}` block of the build script.
In this case, their declarations follow the pattern `<sourceSetName><DependencyKind>`, for example, `commonMainApi`.

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
dependencies {
    "commonMainApi"("com.example:foo-common:1.0")
    "jvm6MainApi"("com.example:foo-jvm6:1.0")
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
dependencies {
    commonMainApi 'com.example:foo-common:1.0'
    jvm6MainApi 'com.example:foo-jvm6:1.0'
}
```

</tab>
</tabs>

## Language settings

The `languageSettings {}` block of a source set defines certain aspects of project analysis and build. The following language settings are available:

| **Name**                | **Description**                                                                                                                                                                 | 
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `languageVersion`       | Provides source compatibility with the specified version of Kotlin.                                                                                                             |
| `apiVersion`            | Allows using declarations only from the specified version of Kotlin bundled libraries.                                                                                          |
| `enableLanguageFeature` | Enables the specified language feature. The available values correspond to the language features that are currently experimental or have been introduced as such at some point. |
| `optIn`                 | Allows using the specified [opt-in annotation](opt-in-requirements.md).                                                                                                         |
| `progressiveMode`       | Enables the [progressive mode](whatsnew13.md#progressive-mode).                                                                                                                 |

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    sourceSets.all {
        languageSettings.apply {
            languageVersion = "%languageVersion%" // possible values: '1.6', '1.7', '1.8', '1.9', `2.0`
            apiVersion = "%apiVersion%" // possible values: '1.6', '1.7', '1.8', '1.9', `2.0`
            enableLanguageFeature("InlineClasses") // language feature name
            optIn("kotlin.ExperimentalUnsignedTypes") // annotation FQ-name
            progressiveMode = true // false by default
        }
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    sourceSets.all {
        languageSettings {
            languageVersion = '%languageVersion%' // possible values: '1.6', '1.7', '1.8', '1.9', `2.0`
            apiVersion = '%apiVersion%' // possible values: '1.6', '1.7', '1.8', '1.9', `2.0`
            enableLanguageFeature('InlineClasses') // language feature name
            optIn('kotlin.ExperimentalUnsignedTypes') // annotation FQ-name
            progressiveMode = true // false by default
        }
    }
}
```

</tab>
</tabs>
