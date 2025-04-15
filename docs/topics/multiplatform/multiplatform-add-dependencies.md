[//]: # (title: 添加对跨平台库的依赖)

每个程序都需要一组库才能成功运行。
一个 Kotlin 跨平台项目可以依赖于适用于所有目标平台的多平台库、特定平台的库以及其他多平台项目。

要添加对库的依赖，请更新项目中包含共享代码的目录下的 `build.gradle(.kts)` 文件。
在 [`dependencies {}`](multiplatform-dsl-reference.md#dependencies) 块中，设置所需的依赖
[类型](gradle-configure-project.md#dependency-types)（例如，`implementation`）：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    sourceSets {
        commonMain.dependencies {
            implementation("com.example:my-library:1.0") // 共享给所有源代码集的库
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
                implementation 'com.example:my-library:1.0' // 共享给所有源代码集的库
            }
        }
    }
}
```

</tab>
</tabs>

或者，你可以 [在顶层设置依赖](gradle-configure-project.md#set-dependencies-at-top-level)。

## 对 Kotlin 库的依赖 {id=dependency-on-a-kotlin-library}

### 标准库 {id=standard-library}

每个源代码集中的标准库（`stdlib`）依赖都会自动添加。
标准库的版本与 `kotlin-multiplatform` 插件的版本相同。

对于特定平台的源代码集，将使用相应平台的库变体，而其余部分将添加通用标准库。
Kotlin Gradle 插件将根据 Gradle 构建脚本中的
`compilerOptions.jvmTarget` [编译器选项](gradle-compiler-options.md) 选择适当的 JVM 标准库。

了解如何 [更改默认行为](gradle-configure-project.md#dependency-on-the-standard-library)。

### 测试库 {id=test-libraries}

对于跨平台测试，可以使用 [`kotlin.test`](https://kotlinlang.org/api/latest/kotlin.test/) API。
当你创建一个跨平台项目时，可以在 `commonTest` 中使用单一依赖来为所有源代码集添加测试依赖：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    sourceSets {
        commonTest.dependencies {
            implementation(kotlin("test")) // 自动引入所有平台依赖
        }
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    sourceSets {
        commonTest {
            dependencies {
                implementation kotlin("test") // 自动引入所有平台依赖
            }
        }
    }
}
```

</tab>
</tabs>

### kotlinx 库 {id=kotlinx-libraries}

如果你使用跨平台库并需要[依赖共享代码](#library-shared-for-all-source-sets)，只需在共享源代码集中设置一次依赖。
使用库的基础构件名称，例如 `kotlinx-coroutines-core`：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    sourceSets {
        commonMain.dependencies {
            implementation("org.jetbrains.kotlinx:kotlinx-coroutines-core:%coroutinesVersion%")
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
                implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-core:%coroutinesVersion%'
            }
        }
    }
}
```

</tab>
</tabs>

如果你需要为[平台特定依赖](#library-used-in-specific-source-sets)添加 kotlinx 库，  
仍然可以在对应平台源代码集中使用该库的基础构件名称：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    sourceSets {
        jvmMain.dependencies {
            implementation("org.jetbrains.kotlinx:kotlinx-coroutines-core:%coroutinesVersion%")
        }
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    sourceSets {
        jvmMain {
            dependencies {
                implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-core:%coroutinesVersion%'
            }
        }
    }
}
```

</tab>
</tabs>

## 对 Kotlin 跨平台库的依赖 {id=dependency-on-kotlin-multiplatform-libraries}

你可以添加对已经采用 Kotlin 跨平台技术的库的依赖，例如
[SQLDelight](https://github.com/cashapp/sqldelight)。
这些库的作者通常会提供将其依赖项添加到项目中的指南。

> 在 [JetBrains 搜索平台](https://klibs.io/) 上查找 Kotlin 跨平台 库。
>
{style="tip"}

### 共享给所有源代码集的库 {id=library-shared-for-all-source-sets}

如果你想在所有源代码集中使用某个库，可以仅将其添加到通用源代码集中。
Kotlin 跨平台移动插件将自动将相应部分添加到其他源代码集中。

> 你不能在通用源代码集中设置对特定平台库的依赖。
>
{style="warning"}

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    sourceSets {
        commonMain.dependencies {
            implementation("io.ktor:ktor-client-core:%ktorVersion%")
        }
        androidMain.dependencies {
            // ktor-client 的平台部分依赖将被自动添加
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
                implementation 'io.ktor:ktor-client-core:%ktorVersion%'
            }
        }
        androidMain {
            dependencies {
                // ktor-client 的平台部分依赖将被自动添加
            }
        }
    }
}
```

</tab>
</tabs>

### 用于特定源代码集的库 {id=library-used-in-specific-source-sets}

如果你只想在特定的源代码集中使用某个多平台库，可以将其专门添加到这些源代码集中。
指定的库声明将仅在这些源代码集中可用。

> 在这种情况下请使用通用库名称，而不是特定于平台的名称。
> 就像下面的 SQLDelight 示例一样，使用 `native-driver`，而不是 `native-driver-iosx64`。
> 请在库的文档中找到确切的名称。
>
{style="note"}

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    sourceSets {
        commonMain.dependencies {
            // kotlinx.coroutines 将在所有源代码集中可用
            implementation("org.jetbrains.kotlinx:kotlinx-coroutines-core:%coroutinesVersion%")
        }
        androidMain.dependencies {

        }
        iosMain.dependencies {
            // SQLDelight 将仅在 iOS 源代码集中可用，而在 Android 或通用源代码集中不可用
            implementation("com.squareup.sqldelight:native-driver:%sqlDelightVersion%")
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
                // kotlinx.coroutines 将在所有源代码集中可用
                implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-core:%coroutinesVersion%'
            }
        }
        androidMain {
            dependencies {}
        }
        iosMain {
            dependencies {
                // SQLDelight 将仅在 iOS 源代码集中可用，而在 Android 或通用源代码集中不可用
                implementation 'com.squareup.sqldelight:native-driver:%sqlDelightVersion%'
            }
        }
    }
}
```

</tab>
</tabs>

## 对另一个多平台项目的依赖 {id=dependency-on-another-multiplatform-project}

你可以将一个多平台项目作为依赖连接到另一个项目。为此，只需将项目依赖添加到需要它的源代码集中。
如果你想在所有源代码集中使用该依赖，请将其添加到通用源代码集中。
在这种情况下，其他源代码集将自动获取它们的版本。

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    sourceSets {
        commonMain.dependencies {
            implementation(project(":some-other-multiplatform-module"))
        }
        androidMain.dependencies {
            // :some-other-multiplatform-module 的平台部分将被自动添加
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
                implementation project(':some-other-multiplatform-module')
            }
        }
        androidMain {
            dependencies {
                // :some-other-multiplatform-module 的平台部分将被自动添加
            }
        }
    }
}
```

</tab>
</tabs>

## 接下来做什么？ {id=whats-next}

查看有关在多平台项目中添加依赖的其他资源，并了解更多内容：

* [添加 Android 依赖](multiplatform-android-dependencies.md)
* [添加 iOS 依赖](multiplatform-ios-dependencies.md)
