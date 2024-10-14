[//]: # (title: Android 源码集布局)

新的 Android 源集布局在 Kotlin 1.8.0 中引入，并在 1.9.0 中成为默认布局。
以下指南帮助你了解弃用的布局与新布局之间的主要区别，以及如何迁移你的项目。

> 注意：你无需实施所有建议，只需根据项目需求应用相关部分即可。
>
{style="tip"}

## 检查兼容性 {id=check-the-compatibility}

新布局要求 Android Gradle 插件 7.0 或更高版本，并在 Android Studio 2022.3 及更高版本中支持。
请检查你使用的 Android Gradle 插件版本，并在必要时进行升级。

## 重命名 Kotlin 源代码集 {id=rename-kotlin-source-sets}

如果适用，请按照以下模式重命名项目中的源代码集：

| 之前的源代码集布局                              | 新的源代码集布局                            |
|----------------------------------------|-------------------------------------|
| `targetName` + `AndroidSourceSet.name` | `targetName` + `AndroidVariantType` |

`{AndroidSourceSet.name}` 与 `{KotlinSourceSet.name}` 的映射关系如下：

|             | 之前的源代码集布局                 | 新的源代码集布局                       |
|-------------|---------------------------|--------------------------------|
| main        | androidMain               | androidMain                    |
| test        | androidTest               | android<b>Unit</b>Test         |
| androidTest | android<b>Android</b>Test | android<b>Instrumented</b>Test |

## 移动源代码文件 {id=move-source-files}

如果适用，请按照以下模式将源代码文件移动到新目录：

| 之前的源代码集布局                | 新的源代码集布局                            |
|--------------------------|-------------------------------------|
| 布局中包含额外的 `/kotlin` 源代码目录 | `src/{KotlinSourceSet.name}/kotlin` |

`{AndroidSourceSet.name}` 与 `{SourceDirectories included}` 的映射关系如下：

|             | 之前的源代码集布局                                                          | 新的源代码集布局                                                                                                |
|-------------|--------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| main        | `src/androidMain/kotlin`<br/>`src/main/kotlin`<br/>`src/main/java` | `src/androidMain/kotlin`<br/>`src/main/kotlin`<br/>`src/main/java`                                      |
| test        | `src/androidTest/kotlin`<br/>`src/test/kotlin`<br/>`src/test/java` | `src/android<b>Unit</b>Test/kotlin`<br/>`src/test/kotlin`<br/>`src/test/java`                           |
| androidTest | `src/android<b>Android</b>Test/kotlin`<br/>`src/androidTest/java`  | `src/android<b>Instrumented</b>Test/kotlin`<br/>`src/androidTest/java`, <b>`src/androidTest/kotlin`</b> |

## 移动 AndroidManifest.xml 文件

如果你的项目中有 `AndroidManifest.xml` 文件，请按照以下模式将其移动到新目录：

| 之前的源代码集布局                                              | 新的源代码集布局                                              |
|--------------------------------------------------------|-------------------------------------------------------|
| src/{<b>Android</b>SourceSet.name}/AndroidManifest.xml | src/{<b>Kotlin</b>SourceSet.name}/AndroidManifest.xml |

`{AndroidSourceSet.name}` 与 `{AndroidManifest.xml 位置}` 的映射关系如下：

|       | 之前的源代码集布局                       | 新的源代码集布局                                      |
|-------|---------------------------------|-----------------------------------------------|
| main  | `src/main/AndroidManifest.xml`  | `src/<b>android</b>Main/AndroidManifest.xml`  |
| debug | `src/debug/AndroidManifest.xml` | `src/<b>android</b>Debug/AndroidManifest.xml` |

## 检查 Android 与 common tests 之间的关系

新的 Android 源代码集布局改变了 Android 设备测试（在新布局中重命名为 `androidInstrumentedTest`）与
common tests 之间的关系。

以前，`androidAndroidTest` 与 `commonTest` 之间默认存在 `dependsOn` 关系。这意味着以下几点：

- `commonTest` 中的代码可在 `androidAndroidTest` 中使用。
- `commonTest` 中的 `expect` 声明必须在 `androidAndroidTest` 中有相应的 `actual` 实现。
- 在 `commonTest` 中声明的测试也会作为 Android 仪器测试运行。

在新的 Android 源代码集布局中，`dependsOn` 关系不再是默认设置。如果你希望保持之前的行为，
可以在 `build.gradle.kts` 文件中手动声明以下关系：

```kotlin
kotlin {
    // ...
    sourceSets {
        val commonTest by getting
        val androidInstrumentedTest by getting {
            dependsOn(commonTest)
        }
    }
}
```

## 调整 Android 风味的实现 {id=adjust-the-implementation-of-android-flavors}

之前，Kotlin Gradle 插件会提前创建与 Android 源代码集对应的源代码集，这些源代码集包含 `debug` 和
`release` 构建类型，或者像 `demo` 和 `full` 这样的自定义风味。  
它通过使用诸如 `val androidDebug by getting { ... }` 这样的表达式来访问这些源代码集。

新的 Android 源代码集布局使用 Android 的 [`onVariants`](https://developer.android.com/reference/tools/gradle-api/8.0/com/android/build/api/variant/AndroidComponentsExtension#onVariants(com.android.build.api.variant.VariantSelector,kotlin.Function1)) 来创建源代码集。  
这使得类似的表达式无效，
导致出现 `org.gradle.api.UnknownDomainObjectException: KotlinSourceSet with name 'androidDebug' not found` 这样的错误。

为了解决这个问题，可以在你的 `build.gradle.kts` 文件中使用新的 `invokeWhenCreated()` API：

```kotlin
kotlin {
// ...
    @OptIn(ExperimentalKotlinGradlePluginApi::class)
    sourceSets.invokeWhenCreated("androidFreeDebug") {
// ...
    }
}
```