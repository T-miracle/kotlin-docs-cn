[//]: # (title: 设置 Kotlin 跨平台的目标)

你可以在使用 [项目向导](https://kmp.jetbrains.com/) 创建项目时添加目标。
如果需要稍后添加目标，可以手动为 [支持的平台](multiplatform-dsl-reference.md#targets) 添加目标预设。

了解更多关于 [目标的附加设置](multiplatform-dsl-reference.md#common-target-configuration)。

```kotlin
kotlin {
    jvm() // 使用默认名称 'jvm' 创建一个 JVM 目标
        
    linuxX64() {
        /* 在这里为 'linux' 目标指定附加设置 */
    }
}
```

每个目标可以包含一个或多个 [编译](multiplatform-configure-compilations.md)。
除了用于测试和生产的默认编译，你还可以 [创建自定义编译](multiplatform-configure-compilations.md#create-a-custom-compilation)。

## 区分同一平台的多个目标 {id=distinguish-several-targets-for-one-platform}

在跨平台库中，你可以为一个平台设置多个目标。
例如，这些目标可以提供相同的 API，但在运行时使用不同的库，如测试框架和日志解决方案。
由于无法明确选择哪个目标，依赖于这样的跨平台库可能会导致无法解析。

为了解决这个问题，库作者和消费者需要在目标上标记自定义属性，Gradle 会在依赖解析时使用这些属性。
 
例如，假设有一个支持 JUnit 和 TestNG 的测试库。
库作者需要为两个目标添加如下属性：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
val testFrameworkAttribute = Attribute.of("com.example.testFramework", String::class.java)

kotlin {
    jvm("junit") {
        attributes.attribute(testFrameworkAttribute, "junit")
    }
    jvm("testng") {
        attributes.attribute(testFrameworkAttribute, "testng")
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
def testFrameworkAttribute = Attribute.of('com.example.testFramework', String)

kotlin {
    jvm('junit') {
        attributes.attribute(testFrameworkAttribute, 'junit')
    }
    jvm('testng') {
        attributes.attribute(testFrameworkAttribute, 'testng')
    }
}
```

</tab>
</tabs>

消费者需要在发生歧义的单个目标中添加该属性。