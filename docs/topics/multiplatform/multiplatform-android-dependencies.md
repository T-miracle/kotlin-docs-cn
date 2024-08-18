[//]: # (title: 添加 Android 依赖项)

将 Android 特定依赖添加到 Kotlin 跨平台模块的工作流程与纯 Android 项目相同：
在你的 Gradle 文件中声明依赖并导入项目。
之后，你就可以在 Kotlin 代码中使用这个依赖。

我们建议在 Kotlin 跨平台项目中，通过将依赖添加到特定的 Android 源代码集中来声明 Android 依赖。
为此，请更新项目中 `shared` 目录下的 `build.gradle(.kts)` 文件：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
sourceSets["androidMain"].dependencies {
    implementation("com.example.android:app-magic:12.3")
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
sourceSets {
    androidMain {
        dependencies {
            implementation 'com.example.android:app-magic:12.3'
        }
    }
}
```

</tab>
</tabs>

将 Android 项目中的顶级依赖项移动到多平台项目中的特定源代码集中可能会比较困难，尤其是当顶级依赖项具有复杂的配置名称时。
例如，要将 `debugImplementation` 依赖从 Android 项目的顶级移动过来，你需要在名为
`androidDebug` 的源代码集中添加一个 implementation 依赖。
为了减少处理类似迁移问题所需的工作量，你可以在 `android {}` 块内添加一个 `dependencies {}` 块：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
android {
    //...
    dependencies {
        implementation("com.example.android:app-magic:12.3")
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
android {
    //...
    dependencies {
        implementation 'com.example.android:app-magic:12.3'
    }
}
```

</tab>
</tabs>

在这里声明的依赖将与顶级块中的依赖完全相同，但以这种方式声明它们可以在你的构建脚本中将
Android 依赖项在视觉上分离开来，从而减少混淆。

也支持将依赖放入脚本末尾的独立 `dependencies {}` 块中，这是一种符合
Android 项目习惯的方式。然而，我们强烈建议**不要**这样做，因为在顶级块中配置带有
Android 依赖项的构建脚本，而又在每个源代码集中配置其他目标依赖项，这样做可能会引起混淆。

## 接下来做什么？ {id=whats-next}

查看有关在多平台项目中添加依赖的其他资源，并了解更多内容：

* [在官方 Android 文档中添加依赖](https://developer.android.com/studio/build/dependencies)
* [添加多平台库或其他多平台项目的依赖](multiplatform-add-dependencies.md)
* [添加 iOS 依赖](multiplatform-ios-dependencies.md)