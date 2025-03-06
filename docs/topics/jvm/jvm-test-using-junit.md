[//]: # (title: 在 JVM 中使用 JUnit 测试代码 – 教程)

本教程将向你展示如何编写一个简单的单元测试并使用 Gradle 构建工具运行它。

教程中的示例使用了 [`kotlin.test`](https://kotlinlang.org/api/latest/kotlin.test/index.html) 库，并通过 JUnit 运行测试。

要开始使用，请首先下载并安装最新版本的 [IntelliJ IDEA](https://www.jetbrains.com/idea/download/index.html)。

## 添加依赖项 {id=add-dependencies}

1. 在 IntelliJ IDEA 中打开一个 Kotlin 项目。如果你没有项目，可以
   [创建一个](https://www.jetbrains.com/help/idea/create-your-first-kotlin-app.html#create-project)。

2. 打开 `build.gradle(.kts)` 文件并检查是否存在 `testImplementation` 依赖项。
   该依赖项允许你使用 `kotlin.test` 和 `JUnit`：

    <tabs group="build-script">
    <tab title="Kotlin" group-key="kotlin">

   ```kotlin
   dependencies {
       // 其他依赖项。
       testImplementation(kotlin("test"))
   }
   ```

    </tab>
    <tab title="Groovy" group-key="groovy">

   ```groovy
   dependencies {
       // 其他依赖项。
       testImplementation 'org.jetbrains.kotlin:kotlin-test'
   }
   ```

   </tab>
   </tabs>

3. 将 `test` 任务添加到 `build.gradle(.kts)` 文件中：

    <tabs group="build-script">
    <tab title="Kotlin" group-key="kotlin">

   ```kotlin
   tasks.test {
       useJUnitPlatform()
   }
   ```

    </tab>
    <tab title="Groovy" group-key="groovy">

   ```groovy
   test {
       useJUnitPlatform()
   }
   ```

   </tab>
   </tabs>

   > 如果你在构建脚本中使用 `useJUnitPlatform()` 函数，`kotlin-test` 库会自动包含 JUnit 5 作为依赖项。  
   > 这种设置在仅 JVM 项目和 Kotlin 跨平台（KMP）项目的 JVM 测试中，允许访问所有 JUnit 5 API 以及 `kotlin-test` API。
   >
   {style="note"}

以下是 `build.gradle.kts` 的完整代码：

```kotlin
plugins {
    kotlin("jvm") version "%kotlinVersion%"
}

group = "org.example"
version = "1.0-SNAPSHOT"

repositories {
    mavenCentral()
}

dependencies {
    testImplementation(kotlin("test"))
}

tasks.test {
    useJUnitPlatform()
}
```
{initial-collapse-state="collapsed" collapsible="true"}

## 添加代码以进行测试 {id=add-the-code-to-test-it}

1. 打开 `src/main/kotlin` 目录中的 `Main.kt` 文件。

   `src` 目录包含 Kotlin 源文件和资源。
   `Main.kt` 文件包含打印 `Hello, World!` 的示例代码。

2. 创建 `Sample` 类，并添加 `sum()` 函数，该函数将两个整数相加：

   ```kotlin
   class Sample() {

       fun sum(a: Int, b: Int): Int {
           return a + b
       }
   }
   ```

## 创建测试 {id=create-a-test}

1. 在 IntelliJ IDEA 中，为 `Sample` 类选择 **Code** | **Generate** | **Test...**：

   ![创建测试](generate-test.png)

2. 指定测试类的名称。例如，`SampleTest`：

   ![创建测试](create-test.png)

   IntelliJ IDEA 将在 `test` 目录中创建 `SampleTest.kt` 文件。  
   该目录包含 Kotlin 测试源文件和资源。

   > 你也可以手动在 `src/test/kotlin` 中创建测试的 `*.kt` 文件。
   >
   {style="note"}

3. 在 `SampleTest.kt` 中添加 `sum()` 函数的测试代码：

   * 使用 [`@Test` 注解](https://kotlinlang.org/api/latest/kotlin.test/kotlin.test/-test/index.html) 定义测试函数 `testSum()`。
   * 使用 [`assertEquals()`](https://kotlinlang.org/api/latest/kotlin.test/kotlin.test/assert-equals.html) 函数检查 `sum()` 函数是否返回预期值。

   ```kotlin
   import org.example.Sample
   import org.junit.jupiter.api.Assertions.*
   import kotlin.test.Test

   class SampleTest {

       private val testSample: Sample = Sample()

       @Test
       fun testSum() {
           val expected = 42
           assertEquals(expected, testSample.sum(40, 2))
       }
   }
   ```

## 运行测试 {id=run-a-test}

1. 使用装订线图标运行测试：

   ![运行测试](run-test.png)

   > 你也可以通过命令行界面使用 `./gradlew check` 命令运行所有项目测试。
   >
   {style="note"}

2. 在 **Run** 工具窗口中检查结果：

   ![检查测试结果。测试成功通过](test-successful.png)

   测试函数已成功执行。

3. 通过将 `expected` 变量值更改为 43 来确保测试正常工作：

   ```kotlin
   @Test
   fun testSum() {
       val expected = 43
       assertEquals(expected, classForTesting.sum(40, 2))
   }
   ```

4. 再次运行测试并检查结果：

   ![检查测试结果。测试失败](test-failed.png)

   测试执行失败。

## 下一步 {id=whats-next}

完成第一个测试后，你可以：

* 使用其他 [`kotlin.test`](https://kotlinlang.org/api/latest/kotlin.test/kotlin.test/) 函数编写更多测试。  
  例如，使用 [`assertNotEquals()`](https://kotlinlang.org/api/latest/kotlin.test/kotlin.test/assert-not-equals.html) 函数。
* 使用 [Kotlin Power-assert 编译器插件](power-assert.md) 改进测试输出。  
  该插件通过上下文信息丰富了测试输出。
* 使用 Kotlin 和 Spring Boot [创建你的第一个服务器端应用程序](jvm-get-started-spring-boot.md)。