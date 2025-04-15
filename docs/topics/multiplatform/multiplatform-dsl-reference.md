[//]: # (title: 跨平台 Gradle DSL 参考)

Kotlin 跨平台 Gradle 插件是用于创建 Kotlin 跨平台项目的工具。  
在这里，我们提供了它的内容参考；在编写 Kotlin 跨平台项目的 Gradle 构建脚本时，可以将其作为提醒。  
了解 [Kotlin 跨平台项目的概念，以及如何创建和配置它们](multiplatform-intro.md)。

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

| **块**                | **描述**                                                  |
|----------------------|---------------------------------------------------------|
| _&lt;targetName&gt;_ | 声明项目的特定目标。可用目标的名称列在 [Targets](#targets) 部分。             |
| `targets`            | 列出项目的所有目标。                                              |
| `sourceSets`         | 配置预定义的并声明自定义的 [源代码集](#source-sets) 以供项目使用。              |
| `compilerOptions`    | 指定用于所有目标和共享源代码集的默认设置的通用扩展级别 [编译器选项](#compiler-options)。 |

## 目标 {id=targets}

`_target_` 是构建过程的一部分，负责编译、测试和打包面向某个平台的软件。
Kotlin 为每个平台提供了相应的目标，因此你可以指示 Kotlin 为特定目标编译代码。  
了解更多关于 [设置目标](multiplatform-discover-project.md#targets)。

每个目标可以有一个或多个 [编译](#compilations)。 除了用于测试和生产目的的默认编译外，还可以
[创建自定义编译](multiplatform-configure-compilations.md#create-a-custom-compilation)。

跨平台项目的目标在 `kotlin {}` 内的相应块中进行描述，例如 `jvm`、`androidTarget`、`iosArm64`。  
可用目标的完整列表如下：

<table>
    <tr>
        <th>目标平台</th>
        <th>目标</th>
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
        <td><code>androidTarget</code></td>
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

在任何 target 块中，你可以使用以下声明：

| **名称**              | **描述**                                                                                                                      | 
|---------------------|-----------------------------------------------------------------------------------------------------------------------------| 
| `platformType`      | 该目标所针对的 Kotlin 平台。可用值：`jvm`、`androidJvm`、`js`、`wasm`、`native`、`common`。                                                     |
| `artifactsTaskName` | 构建该目标生成的工件的任务名称。                                                                                                            |
| `components`        | 用于设置 Gradle 发布的组件。                                                                                                          |
| `compilerOptions`   | 用于该目标的 [编译器选项](#compiler-options)。此声明将覆盖在 [顶层](multiplatform-dsl-reference.md#top-level-blocks) 配置的任何 `compilerOptions {}`。 |

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

| **名称**         | **描述**                                         |  
|----------------|------------------------------------------------|  
| `testRuns`     | 测试执行的配置。                                       |  
| `runTask`      | 项目运行的配置。                                       |  
| `webpackTask`  | 使用 [Webpack](https://webpack.js.org/) 打包项目的配置。 |  
| `distribution` | 输出文件的路径。                                       |  

```kotlin
kotlin {
    js().browser {
        webpackTask { /* ... */ }
        testRuns { /* ... */ }
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

| **名称**           | **描述**                      |
|------------------|-----------------------------|
| `definitionFile` | 描述本地 API 的 `.def` 文件。       |
| `packageName`    | 生成的 Kotlin API 的包前缀。        |
| `compilerOpts`   | 传递给编译器的选项，通过 cinterop 工具使用。 |
| `includeDirs`    | 查找头文件的目录。                   |
| `header`         | 要包含在绑定中的头文件。                |
| `headers`        | 要包含在绑定中的头文件列表。              |

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

                // Header files to be included in the bindings.
                header("path/to/header.h")
                headers("path/to/header1.h", "path/to/header2.h")
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

                    // 要包含在绑定中的头文件。
                    header("path/to/header.h")
                    headers("path/to/header1.h", "path/to/header2.h")
                }

                anotherInterop { /* ... */ }
            }
        }
    }
}
```

</tab>
</tabs>

有关更多的 cinterop 属性，请参见 [定义文件](native-definition-file.md#properties)。

### Android 目标 {id=android-targets}

Kotlin 跨平台插件包含两个针对 Android 目标的特定函数。  
这两个函数帮助你配置 [构建变体](https://developer.android.com/studio/build/build-variants)：

| **名称**                        | **描述**                                                                                         | 
|-------------------------------|------------------------------------------------------------------------------------------------| 
| `publishLibraryVariants()`    | 指定要发布的构建变体。了解更多关于 [发布 Android 库](multiplatform-publish-lib.md#publish-an-android-library) 的信息。 |
| `publishAllLibraryVariants()` | 发布所有构建变体。                                                                                      |

```kotlin
kotlin {
    androidTarget {
        publishLibraryVariants("release")
    }
}
```

了解更多关于 [Android 的编译](multiplatform-configure-compilations.md#compilation-for-android) 的信息。

> `kotlin {}` 块中的 `androidTarget` 配置不会替代任何 Android 项目的构建配置。  
> 了解更多关于为 Android 项目编写构建脚本的信息，请参阅 [Android 开发者文档](https://developer.android.com/studio/build)。
>
{style="note"}

## 源代码集 {id=source-sets}

`sourceSets {}` 块描述了项目的源代码集。源代码集包含了参与一起编译的 Kotlin 源文件，以及它们的资源、依赖项和语言设置。

一个跨平台项目包含为其目标平台 [预定义的](#predefined-source-sets) 源代码集；
开发者也可以根据需要创建 [自定义](#custom-source-sets) 源代码集。

### 预定义源代码集 {id=predefined-source-sets}

预定义源代码集会在创建跨平台项目时自动设置。可用的预定义源代码集如下：

| **名称**                                      | **描述**                                                                                                | 
|---------------------------------------------|-------------------------------------------------------------------------------------------------------|
| `commonMain`                                | 所有平台共享的代码和资源。适用于所有跨平台项目。在项目的所有主要[编译](#compilations)中使用。                                               |
| `commonTest`                                | 所有平台共享的测试代码和资源。适用于所有跨平台项目。在项目的所有测试编译中使用。                                                              |
| _&lt;targetName&gt;&lt;compilationName&gt;_ | 针对特定目标的编译源。 _&lt;targetName&gt;_ 是预定义目标的名称，_&lt;compilationName&gt;_ 是该目标的编译名称。例如：`jsTest`，`jvmMain`。 |

在 Kotlin Gradle DSL 中，预定义源代码集的部分应标记为 `by getting`。

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

了解更多关于[源代码集](multiplatform-discover-project.md#source-sets)的内容。

### 自定义源代码集 {id=custom-source-sets}

自定义源代码集由项目开发者手动创建。
要创建一个自定义源代码集，请在 `sourceSets` 部分中添加其名称。
如果使用 Kotlin Gradle DSL，需标记自定义源代码集为 `by creating`。

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin { 
    sourceSets { 
        val myMain by creating { /* ... */ } // 创建一个名为 'MyMain' 的新源代码集
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin { 
    sourceSets { 
        myMain { /* ... */ } // 创建或配置一个名为 'myMain' 的源代码集
    }
}
```

</tab>
</tabs>

请注意，新创建的源代码集并不会自动与其他源代码集连接。要在项目编译中使用它，请
[将其与其他源代码集连接](multiplatform-hierarchy.md#manual-configuration)。

### 源代码集参数 {id=source-set-parameters}

源代码集的配置存储在对应的 `sourceSets {}` 块中。一个源代码集包含以下参数：

| **名称**             | **描述**                                                          | 
|--------------------|-----------------------------------------------------------------|
| `kotlin.srcDir`    | 源代码集目录中 Kotlin 源文件的位置。                                          |
| `resources.srcDir` | 源代码集目录中资源文件的位置。                                                 |
| `dependsOn`        | [与另一个源代码集的连接](multiplatform-hierarchy.md#manual-configuration)。 |
| `dependencies`     | 源代码集的[依赖项](#dependencies)。                                      |
| `languageSettings` | 应用于源代码集的[语言设置](#language-settings)。                             |

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

## 编译 {id=compilations}

一个目标可以有一个或多个编译，例如用于生产或测试。创建目标时会自动添加[预定义编译](#predefined-compilations)。
你还可以额外创建[自定义编译](#custom-compilations)。

要引用某个目标的所有或部分特定编译，使用 `compilations` 对象集合。
通过 `compilations`，你可以根据编译的名称进行引用。

了解更多关于[配置编译](multiplatform-configure-compilations.md)的内容。

### 预定义编译 {id=predefined-compilations}

预定义编译会为项目的每个目标自动创建，Android 目标除外。
可用的预定义编译如下：

| **名称** | **描述**      | 
|--------|-------------|
| `main` | 用于生产源代码的编译。 |
| `test` | 用于测试的编译。    |

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    jvm {
        val main by compilations.getting {
            output // 获取 main 编译的输出
        }

        compilations["test"].runtimeDependencyFiles // 获取测试运行时的类路径
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    jvm {
        compilations.main.output // 获取 main 编译的输出
        compilations.test.runtimeDependencyFiles // 获取测试运行时的类路径
    }
}
```

</tab>
</tabs>

### 自定义编译 {id=custom-compilations}

除了预定义编译，你还可以创建自己的自定义编译。
要创建自定义编译，向 `compilations` 集合中添加一个新项。
如果使用 Kotlin Gradle DSL，需标记自定义编译为 `by creating`。

了解更多关于创建[自定义编译](multiplatform-configure-compilations.md#create-a-custom-compilation)的内容。

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

                // 创建一个测试任务来运行此编译生成的测试：
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

            // 创建一个测试任务来运行此编译生成的测试：
            tasks.register('jvmIntegrationTest', Test) {
                /* ... */
            }
        }
    }
}
```

</tab>
</tabs>

### 编译参数 {id=compilation-parameters}

一个编译包含以下参数：

| **名称**                   | **描述**                                                    | 
|--------------------------|-----------------------------------------------------------|
| `defaultSourceSet`       | 编译的默认源代码集。                                                |
| `kotlinSourceSets`       | 参与编译的源代码集。                                                |
| `allKotlinSourceSets`    | 参与编译的源代码集及其通过 `dependsOn()` 连接的其他源代码集。                    |
| `compilerOptions`        | 应用于编译的编译器选项。可用选项列表请参见[编译器选项](gradle-compiler-options.md)。 |
| `compileKotlinTask`      | 编译 Kotlin 源代码的 Gradle 任务。                                 |
| `compileKotlinTaskName`  | `compileKotlinTask` 的名称。                                  |
| `compileAllTaskName`     | 编译所有源代码的 Gradle 任务名称。                                     |
| `output`                 | 编译输出。                                                     |
| `compileDependencyFiles` | 编译时的依赖文件（类路径）。对于所有 Kotlin/Native 编译，这会自动包含标准库和平台依赖项。      |
| `runtimeDependencyFiles` | 运行时的依赖文件（类路径）。                                            |

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    jvm {
        val main by compilations.getting {
            compileTaskProvider.configure {
                compilerOptions {
                    // 为 'main' 编译设置 Kotlin 编译器选项：
                    jvmTarget.set(JvmTarget.JVM_1_8)
                }
            }
        
            compileKotlinTask // 获取 Kotlin 任务 'compileKotlinJvm' 
            output // 获取 main 编译的输出
        }
        
        compilations["test"].runtimeDependencyFiles // 获取测试运行时的类路径
    }

    // Configure all compilations of all targets:
    compilerOptions {
        allWarningsAsErrors.set(true)
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    jvm {
        compilations.main {
            compileTaskProvider.configure {
                compilerOptions {
                    // 为 'main' 编译设置 Kotlin 编译器选项：
                    jvmTarget = JvmTarget.JVM_1_8
                }
            }
        }

        compilations.main.compileKotlinTask // 获取 Kotlin 任务 'compileKotlinJvm' 
        compilations.main.output // 获取 main 编译的输出
        compilations.test.runtimeDependencyFiles // 获取测试运行时的类路径
    }

    // Configure all compilations of all targets:
    compilerOptions {
        allWarningsAsErrors = true
    }
}
```

</tab>
</tabs>

## 编译器选项 {id=compiler-options}

你可以在项目中在三个不同的层级配置编译器选项：

* **扩展层级**，在 `kotlin {}` 块中。
* **目标层级**，在目标块中。
* **编译单元层级**，通常在特定的编译任务中。

![Kotlin 编译器选项层级](compiler-options-levels.svg){width=700}

较高层级的设置作为下一级的默认值：

* 在扩展层级设置的编译器选项是目标层级选项的默认值，包括共享源集如 `commonMain`、`nativeMain` 和 `commonTest`。
* 在目标层级设置的编译器选项是编译单元（任务）层级选项的默认值，如 `compileKotlinJvm` 和 `compileTestKotlinJvm` 任务。

在较低层级的配置会覆盖较高层级的相似设置：

* 任务层级的编译器选项会覆盖目标层级或扩展层级的相似设置。
* 目标层级的编译器选项会覆盖扩展层级的相似设置。

有关可能的编译器选项列表，请参阅 [所有编译器选项](gradle-compiler-options.md#all-compiler-options)。

### 扩展层级 {id=extension-level}

要为项目中的所有目标配置编译器选项，可以在顶层使用 `compilerOptions {}` 块：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    // 配置所有目标的所有编译
    compilerOptions {
        allWarningsAsErrors.set(true)
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    // 配置所有目标的所有编译：
    compilerOptions {
        allWarningsAsErrors = true
    }
}
```

</tab>
</tabs>

### 目标层级 {id=target-level}

要为项目中的特定目标配置编译器选项，可以在目标块内使用 `compilerOptions {}` 块：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    jvm {
        // 配置 JVM 目标的所有编译
        compilerOptions {
            allWarningsAsErrors.set(true)
        }
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    jvm {
        // 配置 JVM 目标的所有编译
        compilerOptions {
            allWarningsAsErrors = true
        }
    }
}
```

</tab>
</tabs>

### 编译单元层级 {id=compilation-unit-level}

要为特定任务配置编译器选项，可以在任务中使用 `compilerOptions {}` 块：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
task.named<KotlinJvmCompile>("compileKotlinJvm") {
    compilerOptions {
        allWarningsAsErrors.set(true)
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
task.named<KotlinJvmCompile>("compileKotlinJvm") {
    compilerOptions {
        allWarningsAsErrors = true
    }
}
```

</tab>
</tabs>

要为特定编译配置编译器选项，可以在编译任务提供者内使用 `compilerOptions {}` 块：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    jvm {
        compilations.named(KotlinCompilation.MAIN_COMPILATION_NAME) {
            compileTaskProvider.configure {
                // 配置 'main' 编译：
                compilerOptions {
                    allWarningsAsErrors.set(true)
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
    jvm {
        compilations.named(KotlinCompilation.MAIN_COMPILATION_NAME) {
            compileTaskProvider.configure {
                // 配置 'main' 编译：
                compilerOptions {
                    allWarningsAsErrors = true
                }
            }
        }
    }
}
```

</tab>
</tabs>

## 依赖 {id=dependencies}

`sourceSets {}` 声明块中的 `dependencies {}` 包含了该源代码集的依赖项。

了解更多关于[配置依赖项](gradle-configure-project.md)。

依赖项有四种类型：

| **名称**           | **描述**                        | 
|------------------|-------------------------------|
| `api`            | 在当前模块的 API 中使用的依赖项。           |
| `implementation` | 在模块中使用但不会对外暴露的依赖项。            |
| `compileOnly`    | 仅用于当前模块编译的依赖项。                |
| `runtimeOnly`    | 仅在运行时可用，但在编译过程中对任何模块都不可见的依赖项。 |

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

此外，源代码集之间可以互相依赖并形成层次结构。
在这种情况下，使用 [`dependsOn()`](#source-set-parameters) 关系。

源代码集的依赖项也可以在构建脚本的顶级 `dependencies {}` 块中声明。
在这种情况下，它们的声明遵循 `<sourceSetName><DependencyKind>` 模式，例如，`commonMainApi`。

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

## 语言设置 {id=language-settings}

`sourceSets {}` 块中的 `languageSettings {}` 定义了项目分析和构建的某些方面。以下是可用的语言设置：

| **名称**                  | **描述**                                               | 
|-------------------------|------------------------------------------------------|
| `languageVersion`       | 提供与指定版本的 Kotlin 源代码兼容性。                              |
| `apiVersion`            | 只允许使用指定版本的 Kotlin 捆绑库中的声明。                           |
| `enableLanguageFeature` | 启用指定的语言特性。可用的值对应于当前实验性语言特性或在某个时间点引入的语言特性。            |
| `optIn`                 | 允许使用指定的 [opt-in 注解](opt-in-requirements.md)。         |
| `progressiveMode`       | 启用 [progressive 模式](whatsnew13.md#progressive-mode)。 | 

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    sourceSets.all {
        languageSettings.apply {
            languageVersion = "%languageVersion%" // 可选值: '1.8', '1.9', `2.0`, `2.1`
            apiVersion = "%apiVersion%" // 可选值: '1.8', '1.9', `2.0`, `2.1`
            enableLanguageFeature("InlineClasses") // 语言特性名称
            optIn("kotlin.ExperimentalUnsignedTypes") // 注解 FQ 名称
            progressiveMode = true // 默认值为 false
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
            languageVersion = '%languageVersion%' // 可选值: '1.8', '1.9', `2.0`, `2.1`
            apiVersion = '%apiVersion%' // 可选值: '1.8', '1.9', `2.0`, `2.1`
            enableLanguageFeature('InlineClasses') // 语言特性名称
            optIn('kotlin.ExperimentalUnsignedTypes') // 注解 FQ 名称
            progressiveMode = true // 默认值为 false
        }
    }
}
```

</tab>
</tabs>
