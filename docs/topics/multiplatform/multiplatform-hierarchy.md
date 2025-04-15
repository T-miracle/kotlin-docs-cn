[//]: # (title: 分层项目结构)

Kotlin 跨平台项目支持分层的源代码集结构。  
这意味着你可以安排一个中间源代码集的层级结构，以便在部分（但不是全部）
[支持的目标](multiplatform-dsl-reference.md#targets) 之间共享通用代码。使用中间源代码集有助于你：

* 为某些目标提供特定的 API。例如，一个库可以在中间源代码集中为 Kotlin/Native 目标添加原生特定的 API，而不为 Kotlin/JVM 添加这些 API。
* 为某些目标使用特定的 API。例如，你可以从 Kotlin 跨平台库为某些组成中间源代码集的目标提供的丰富 API 中受益。
* 在项目中使用平台相关的库。例如，你可以从中间 iOS源代码集访问 iOS 特定的依赖项。

Kotlin 工具链确保每个源代码集只能访问该源代码集编译的所有目标都可用的 API。  
这可以防止出现像使用 Windows 特定的 API 然后将其编译为 macOS，
导致链接错误或运行时的未定义行为的情况。

设置源代码集层级的推荐方法是使用 [默认层级模板](#default-hierarchy-template)。  
该模板涵盖了最常见的情况。如果你有一个更高级的项目，你可以 [手动配置](#manual-configuration)。  
这种方法更底层：它更灵活，但需要更多的努力和知识。

## 默认层级模板 {id=default-hierarchy-template}

从 Kotlin 1.9.20 开始，Kotlin Gradle 插件内置了一个默认的 [层次结构模板](#see-the-full-hierarchy-template)。  
该模板为一些常见的用例预定义了中间源代码集。  
插件会根据项目中指定的目标，自动设置这些源代码集。

考虑项目中包含共享代码模块的以下 `build.gradle(.kts)` 文件：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    androidTarget()
    iosArm64()
    iosSimulatorArm64()
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    androidTarget()
    iosArm64()
    iosSimulatorArm64()
}
```

</tab>
</tabs>

当你在代码中声明 `androidTarget`、`iosArm64` 和 `iosSimulatorArm64` 这些目标时，Kotlin Gradle
插件会从模板中找到合适的共享源代码集并为你创建它们。最终的层级结构如下所示：

![使用默认层级模板的示例](default-hierarchy-example.svg)

彩色的源代码集实际上是已创建并存在于项目中的，而灰色的源代码集则是来自默认模板并被忽略的。
例如，Kotlin Gradle 插件没有创建 `watchos` 源代码集，因为项目中没有 watchOS 目标。

如果你添加了一个 watchOS 目标，例如 `watchosArm64`，那么 `watchos`源代码集会被创建，`apple`、`native`
和 `common`源代码集中的代码也会被编译到 `watchosArm64`。

Kotlin Gradle 插件为默认层次结构模板中的所有源代码集提供了类型安全和静态访问器，因此与
[手动配置](#manual-configuration) 相比，可以在不使用 `by getting` 或 `by creating` 构造的情况下引用它们。

如果您未先声明对应目标就尝试访问共享模块 `build.gradle(.kts)` 文件中的源代码集，将会看到警告：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    androidTarget()
    iosArm64()
    iosSimulatorArm64()

    sourceSets {
        iosMain.dependencies {
            implementation("org.jetbrains.kotlinx:kotlinx-coroutines-core:%coroutinesVersion%")
        }
        // 警告：未声明目标就访问源代码集
        linuxX64Main { }
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    androidTarget()
    iosArm64()
    iosSimulatorArm64()

    sourceSets {
        iosMain {
            dependencies {
                implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-core:%coroutinesVersion%'
            }
        }
        // 警告：未声明目标就访问源代码集
        linuxX64Main { }
    }
}
```

</tab>
</tabs>

> 在这个示例中，`apple` 和 `native`源代码集仅编译到 `iosArm64` 和 `iosSimulatorArm64` 目标。  
> 尽管它们的名称可能让人误解，但它们实际上可以访问完整的 iOS API。  
> 这对于像 `native` 这样的源代码集可能会让人感到困惑，因为你可能会期望此源代码集仅能访问所有原生目标上可用的 API。
> 未来这种行为可能会发生变化。
>
{style="note"}

### 额外配置 {id=additional-configuration}

你可能需要对默认的层级模板进行调整。
如果你之前通过 `dependsOn` 调用 [手动](#manual-configuration) 引入了中间源代码集，这会取消默认层级模板的使用，并导致出现以下警告：

```none
The Default Kotlin Hierarchy Template was not applied to '<project-name>':
Explicit .dependsOn() edges were configured for the following source sets:
[<... names of the source sets with manually configured dependsOn-edges...>]

Consider removing dependsOn-calls or disabling the default template by adding
    'kotlin.mpp.applyDefaultHierarchyTemplate=false'
to your gradle.properties

Learn more about hierarchy templates: https://kotl.in/hierarchy-template
```

要解决这个问题，可以通过以下几种方式来配置你的项目：

* [用默认层级模板替换你的手动配置](#replacing-a-manual-configuration)
* [在默认层级模板中创建额外的源代码集](#creating-additional-source-sets)
* [修改默认层级模板创建的源代码集](#modifying-source-sets)

#### 替换手动配置 {id=replacing-a-manual-configuration}

**问题场景**：你所有的中间源代码集目前都由默认层级模板覆盖。

**解决方案**：在共享模块的 `build.gradle(.kts)` 文件中，移除所有手动 `dependsOn()` 调用和通过
`by creating` 构造的源代码集。要查看所有默认源代码集的完整列表，请参阅[完整层级结构模板](#see-the-full-hierarchy-template)。

#### 创建额外的源代码集 {id=creating-additional-source-sets}

**问题场景**：你想添加默认层级模板尚未提供的源代码集，
例如，在 macOS 和 JVM 目标之间添加一个源代码集。

**解决方案**：

1. 在共享模块的 `build.gradle(.kts)` 文件中，通过显式调用 `applyDefaultHierarchyTemplate()` 重新应用模板。
2. 使用 `dependsOn()` [手动配置](#manual-configuration)其他源代码集：

    <tabs group="build-script">
    <tab title="Kotlin" group-key="kotlin">

    ```kotlin
    kotlin {
        jvm()
        macosArm64()
        iosArm64()
        iosSimulatorArm64()
    
        // 再次应用默认层级模板。例如，它会创建 iosMain源代码集：
        applyDefaultHierarchyTemplate()
    
        sourceSets {
            // 创建一个额外的 jvmAndMacos源代码集：
            val jvmAndMacos by creating {
                dependsOn(commonMain.get())
            }
    
            macosArm64Main.get().dependsOn(jvmAndMacos)
            jvmMain.get().dependsOn(jvmAndMacos)
        }
    }
    ```

    </tab>
    <tab title="Groovy" group-key="groovy">

    ```groovy
    kotlin {
        jvm()
        macosArm64()
        iosArm64()
        iosSimulatorArm64()
    
        // 再次应用默认层级模板。例如，它会创建 iosMain源代码集：
        applyDefaultHierarchyTemplate()
    
        sourceSets {
            // 创建一个额外的 jvmAndMacos源代码集：
            jvmAndMacos {
                dependsOn(commonMain.get())
            }
            macosArm64Main {
                dependsOn(jvmAndMacos.get())
            }
            jvmMain {
                dependsOn(jvmAndMacos.get())
            }
        } 
    }
    ```

    </tab>
    </tabs>

#### 修改源代码集 {id=modifying-source-sets}

**问题场景**：您已经拥有与模板生成的源代码集完全相同的名称，但这些源代码集在项目中的不同目标集之间共享。
例如，一个 `nativeMain` 源代码集仅在特定于桌面的目标之间共享：
`linuxX64`、`mingwX64` 和 `macosX64`。

**解决方案**：目前没有办法修改模板的源代码集之间的默认 `dependsOn` 关系。
源代码集的实现和含义，例如
`nativeMain`，在所有项目中必须保持一致。

但是，您仍然可以执行以下操作之一：

* 查找适用于您的目的的不同源代码集，可能是在默认层级模板中或手动创建的源代码集中。
* 完全放弃模板，通过在 `gradle.properties` 文件中添加 `kotlin.mpp.applyDefaultHierarchyTemplate=false` 并手动配置所有源代码集。

> 我们正在开发一个 API，用于创建您自己的层级模板。
> 这对于层级配置与默认模板有显著差异的项目非常有用。
>
> 该 API 尚未准备好，但如果您急于尝试，
> 请查看 `applyHierarchyTemplate {}` 块和 `KotlinHierarchyTemplate.default` 的声明作为示例。
> 请注意，该 API 仍在开发中，可能尚未经过测试，并可能在未来的版本中发生变化。
>
{style="tip"}


查看完整的层次结构模板 {id=see-the-full-hierarchy-template initial-collapse-state="collapsed" collapsible="true"}

当您声明项目编译的目标时，插件根据模板中指定的目标选择共享源代码集，并在您的项目中创建这些源代码集。

![默认层级模板](full-template-hierarchy.svg)

> 该示例仅显示了项目的生产部分，省略了 `Main` 后缀
> （例如，使用 `common` 代替 `commonMain`）。但是，`*Test` 源代码集的情况也是一样的。
>
{style="tip"}

## 手动配置 {id=manual-configuration}

您可以手动在源代码集结构中引入一个中间源代码集。
它将保存多个目标的共享代码。

例如，如果您想在原生 Linux、Windows 和 macOS 目标 (`linuxX64`、`mingwX64` 和 `macosX64`) 之间共享代码，可以按照以下步骤操作：

1. 在共享模块的 `build.gradle(.kts)` 文件中，添加中间源代码集 `desktopMain`，它包含这些目标平台的共享逻辑。
2. 使用 `dependsOn` 关系建立源代码集层次结构。将 `commonMain` 与 `desktopMain` 连接，然后将
   `desktopMain` 与每个目标源代码集连接起来：

    <tabs group="build-script">
    <tab title="Kotlin" group-key="kotlin">
    
    ```kotlin
    kotlin {
        linuxX64()
        mingwX64()
        macosX64()
    
        sourceSets {
            val desktopMain by creating {
                dependsOn(commonMain.get())
            }
    
            linuxX64Main.get().dependsOn(desktopMain)
            mingwX64Main.get().dependsOn(desktopMain)
            macosX64Main.get().dependsOn(desktopMain)
        }
    }
    ```
    
    </tab>
    <tab title="Groovy" group-key="groovy">
    
    ```groovy
    kotlin {
        linuxX64()
        mingwX64()
        macosX64()
    
        sourceSets {
            desktopMain {
                dependsOn(commonMain.get())
            }
            linuxX64Main {
                dependsOn(desktopMain)
            }
            mingwX64Main {
                dependsOn(desktopMain)
            }
            macosX64Main {
                dependsOn(desktopMain)
            }
        }
    }
    ```
    
    </tab>
    </tabs>

结果层级结构如下所示：

![手动配置的层级结构](manual-hierarchical-structure.svg)

您可以为以下目标组合共享源代码集：

* JVM 或 Android + JS + Native
* JVM 或 Android + Native
* JS + Native
* JVM 或 Android + JS
* Native

Kotlin 目前不支持为以下组合共享源代码集：

* 多个 JVM 目标
* JVM + Android 目标
* 多个 JS 目标

如果您需要从共享的本地源代码集中访问平台特定的 API，IntelliJ IDEA
将帮助您检测可以在共享的本地代码中使用的公共声明。
对于其他情况，请使用 Kotlin 的 [预期声明和实际声明](multiplatform-expect-actual.md) 机制。
