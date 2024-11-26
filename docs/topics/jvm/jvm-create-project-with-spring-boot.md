[//]: # (title: 使用 Kotlin 创建一个 Spring Boot 项目)
[//]: # (description: 在 IntelliJ IDEA 中使用 Kotlin 创建一个 Spring Boot 应用程序)

<tldr>
    <p>这是<strong>Spring Boot 和 Kotlin 入门</strong>教程的第一部分：</p><br/>  
    <p><img src="icon-1.svg" width="20" alt="第一步"/> <strong>用 Kotlin 创建一个 Spring Boot 项目</strong><br/><img src="icon-2-todo.svg" width="20" alt="第二步"/> 向 Spring Boot 项目添加一个数据类<br/><img src="icon-3-todo.svg" width="20" alt="第三步"/> 为 Spring Boot 项目添加数据库支持<br/><img src="icon-4-todo.svg" width="20" alt="第四步"/> 使用 Spring Data CrudRepository 进行数据库访问<br/></p>  
</tldr> 

教程的第一部分向您展示如何使用项目向导在 IntelliJ IDEA 中创建一个 Spring Boot 项目。

### 开始之前 {id=before-you-start}

下载并安装最新版本的[IntelliJ IDEA Ultimate Edition](https://www.jetbrains.com/idea/download/index.html)。

> 如果您使用的是 IntelliJ IDEA Community Edition 或其他 IDE，可以通过[基于 Web 的项目生成器](https://start.spring.io)生成一个 Spring Boot 项目。
> 
{style="note"}

### 创建一个 Spring Boot 项目 {id=create-a-spring-boot-project}

通过 IntelliJ IDEA Ultimate Edition 中的项目向导使用 Kotlin 创建一个新的 Spring Boot 项目：

> 您也可以使用[带有 Spring Boot 插件的 IntelliJ IDEA](https://www.jetbrains.com/help/idea/spring-boot.html)创建一个新项目。
>
{style="note"}

1. 在 IntelliJ IDEA 中，选择 **File** | **New** | **Project**。
2. 在左侧面板中，选择 **New Project** | **Spring Boot**。
3. 在项目向导窗口中，指定以下字段和选项：
   * **Name**: demo
   * **Language**: Kotlin
   * **Type**: Gradle - Kotlin

     > 该选项指定构建系统和 DSL。
     >
     {style="tip"}

   * **Package name**: demo
   * **JDK**: Java JDK

     > 本教程使用 **Amazon Corretto version 21**。
     > 如果您尚未安装 JDK，可以从下拉列表中下载。
     >
     {style="note"}
   
   * **Java**: 17

   ![创建 Spring Boot 项目](create-spring-boot-project.png){width=800}

4. 确保已填写所有字段，然后单击 **Next**。

5. 选择本教程所需的以下依赖项：

   * **Web | Spring Web**
   * **SQL | Spring Data JDBC**
   * **SQL | H2 Database**

   ![设置 Spring Boot 项目](set-up-spring-boot-project.png){width=800}

6. 单击 **Create** 以生成并设置项目。

   > IDE 将生成并打开一个新项目。下载和导入项目依赖项可能需要一些时间。
   >
   {style="tip"}

7. 完成后，您可以在 **Project view** 中观察到以下结构：

   ![Spring Boot 项目视图](spring-boot-project-view.png){width=400}

   生成的 Gradle 项目符合 Maven 的标准目录布局：
   * 应用程序的包和类位于 `main/kotlin` 文件夹下。
   * 应用程序的入口点是 `DemoApplication.kt` 文件中的 `main()` 方法。

### 探索项目的 Gradle 构建文件 {id=explore-the-project-gradle-build-file collapsible="true" collapsible="true"}

打开 `build.gradle.kts` 文件：这是 Gradle 的 Kotlin 构建脚本，包含应用程序所需依赖项的列表。

Gradle 文件是 Spring Boot 的标准配置，但它还包含必要的 Kotlin 依赖项，包括 Kotlin-Spring Gradle 插件——`kotlin("plugin.spring")`。

以下是完整的脚本，并对其各部分及依赖项进行了解释：

```kotlin
// build.gradle.kts
plugins {
    kotlin("jvm") version "1.9.24" // 要使用的 Kotlin 版本
    kotlin("plugin.spring") version "1.9.24" // Kotlin Spring 插件
    id("org.springframework.boot") version "3.3.4"
    id("io.spring.dependency-management") version "1.1.6"
}

group = "com.example"
version = "0.0.1-SNAPSHOT"

java {
    toolchain {
        languageVersion = JavaLanguageVersion.of(17)
    }
}

repositories {
    mavenCentral() // Maven 中央仓库
}

dependencies {
    implementation("org.springframework.boot:spring-boot-starter-data-jdbc")
    implementation("org.springframework.boot:spring-boot-starter-web")
    implementation("com.fasterxml.jackson.module:jackson-module-kotlin") // 用于使用 JSON 的 Kotlin 的 Jackson 扩展
    implementation("org.jetbrains.kotlin:kotlin-reflect") // Kotlin 反射库，使用 Spring 所需
    runtimeOnly("com.h2database:h2")
    testImplementation("org.springframework.boot:spring-boot-starter-test")
    testImplementation("org.jetbrains.kotlin:kotlin-test-junit5")
    testRuntimeOnly("org.junit.platform:junit-platform-launcher")
}

kotlin {
    compilerOptions {
        freeCompilerArgs.addAll("-Xjsr305=strict") // `-Xjsr305=strict` 启用 JSR-305 注释的严格模式
    }
}

tasks.withType<Test> {
    useJUnitPlatform() // 使用 JUnit 平台进行测试
}
```

如您所见，Gradle 构建文件中添加了几个与 Kotlin 相关的构件：

1. 在 `plugins` 块中，有两个 Kotlin 插件：

   * `kotlin("jvm")` – 该插件定义了项目中使用的 Kotlin 版本
   * `kotlin("plugin.spring")` – Kotlin Spring 编译插件，用于将 `open` 修饰符添加到 Kotlin 类，以使其与 Spring 框架功能兼容

2. 在 `dependencies` 块中，列出了几个与 Kotlin 相关的模块：

   * `com.fasterxml.jackson.module:jackson-module-kotlin` – 该模块为 Kotlin 类和数据类的序列化与反序列化提供支持
   * `org.jetbrains.kotlin:kotlin-reflect` – Kotlin 反射库

3. 在依赖项部分之后，您可以看到 `kotlin` 插件配置块。  
   在这里，您可以添加额外的参数给编译器，以启用或禁用各种语言特性。

### 探索生成的 Spring Boot 应用程序 {id=explore-the-generated-spring-boot-application}

打开 `DemoApplication.kt` 文件：

```kotlin
// DemoApplication.kt
package demo

import org.springframework.boot.autoconfigure.SpringBootApplication
import org.springframework.boot.runApplication

@SpringBootApplication
class DemoApplication

fun main(args: Array<String>) {
    runApplication<DemoApplication>(*args)
}
```

<deflist collapsible="true">
   <def title="声明类 – class DemoApplication">
      <p>在包声明和导入语句之后，您可以看到第一个类声明，<code>class DemoApplication</code>。</p>
      <p>在 Kotlin 中，如果一个类没有任何成员（属性或函数），您可以省略类体（<code>{}</code>）。</p>
   </def>
   <def title="@SpringBootApplication 注解">
      <p><a href="https://docs.spring.io/spring-boot/docs/current/reference/html/using.html#using.using-the-springbootapplication-annotation"><code>@SpringBootApplication</code> 注解</a> 是 Spring Boot 应用程序中的便捷注解。
      它启用 Spring Boot 的 <a href="https://docs.spring.io/spring-boot/docs/current/reference/html/using.html#using.auto-configuration">自动配置</a>、<a href="https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/ComponentScan.html">组件扫描</a>，并能够在“应用程序类”中定义额外的配置。</p>
   </def>
   <def title="程序入口点 – main()">
      <p><a href="basic-syntax.md#program-entry-point"><code>main()</code></a> 函数是程序的入口点。</p>
      <p>它被声明为 <a href="functions.md#function-scope">顶级函数</a>，在 <code>DemoApplication</code> 类之外。<code>main()</code> 函数调用 Spring 的 <code>runApplication(*args)</code> 函数，以便使用 Spring 框架启动应用程序。</p>
   </def>
   <def title="可变参数 – args: Array&lt;String&gt;">
      <p>如果查看 <code>runApplication()</code> 函数的声明，您会看到该函数的参数被标记为 <a href="functions.md#可变参数数量varargs"><code>vararg</code> 修饰符</a>：<code>vararg args: String</code>。这意味着您可以将任意数量的 String 类型参数传递给该函数。</p>
   </def>
   <def title="展开操作符 – (*args)">
      <p><code>args</code> 是传递给 <code>main()</code> 函数的参数，声明为一个字符串数组。由于它是一个字符串数组，并且您希望将其内容传递给函数，因此使用展开操作符（在数组前面加上星号 <code>*</code>）。</p>
   </def>
</deflist>


### 创建 Controller {id=create-a-controller}

应用程序已准备好运行，但首先让我们更新其逻辑。

在 Spring 应用中，控制器用于处理 Web 请求。  
在与 `DemoApplication.kt` 文件相同的包中，创建 `MessageController.kt` 文件，并定义 `MessageController` 类，如下所示：

```kotlin
// MessageController.kt
package demo

import org.springframework.web.bind.annotation.GetMapping
import org.springframework.web.bind.annotation.RequestParam
import org.springframework.web.bind.annotation.RestController

@RestController
class MessageController {
    @GetMapping("/")
    fun index(@RequestParam("name") name: String) = "Hello, $name!"
}
```

<deflist collapsible="true">
   <def title="@RestController 注解">
      <p>您需要告诉 Spring <code>MessageController</code> 是一个 REST Controller，因此应使用 <code>@RestController</code> 注解标记它。</p>
      <p>这个注解意味着该类将被组件扫描识别，因为它与我们的 <code>DemoApplication</code> 类在同一个包中。</p>
   </def>
   <def title="@GetMapping 注解">
      <p><code>@GetMapping</code> 标记 REST Controller的函数，这些函数实现对应 HTTP GET 请求的端点：</p>
      <code-block lang="kotlin">
      @GetMapping("/")
      fun index(@RequestParam("name") name: String) = "Hello, $name!"
      </code-block>
   </def>
   <def title="@RequestParam 注解">
      <p>函数参数 <code>name</code> 被 <code>@RequestParam</code> 注解标记。这个注解表示方法参数应该绑定到 Web 请求的参数。</p>
      <p>因此，如果您在根路径访问应用程序并提供一个名为 "name" 的请求参数，如 <code>/?name=&lt;your-value&gt;</code>，参数的值将作为参数传递给 <code>index()</code> 函数。</p>
   </def>
   <def title="单表达式函数 – index()">
      <p>由于 <code>index()</code> 函数只包含一个语句，您可以将其声明为 <a href="functions.md#single-expression-functions">单表达式函数</a>。</p>
      <p>这意味着花括号可以省略，函数体通过等号 <code>=</code> 后面的语句来指定。</p>
   </def>
   <def title="函数返回类型的类型推断">
      <p><code>index()</code> 函数没有显式声明返回类型。相反，编译器通过查看等号 <code>=</code> 右边的语句结果来推断返回类型。</p>
      <p><code>Hello, $name!</code> 表达式的类型是 <code>String</code>，因此函数的返回类型也是 <code>String</code>。</p>
   </def>
   <def title="字符串模板 – $name">
      <p><code>Hello, $name!</code> 表达式在 Kotlin 中称为 <a href="strings.md#string-templates"><i>字符串模板</i></a>。</p>
      <p>字符串模板是包含嵌入式表达式的字符串字面量。</p>
      <p>这是一种便捷的替代字符串连接操作的方式。</p>
   </def>
</deflist>

## 运行应用程序 {id=run-the-application}

Spring 应用程序现在已经准备好运行：

1. 点击 `main()` 方法旁边的绿色运行图标：

   ![运行 Spring Boot 应用程序](run-spring-boot-application.png){width=706}

   > 您也可以在终端中运行 `./gradlew bootRun` 命令。
   >
   {style="tip"}

   这将在您的计算机上启动本地服务器。

2. 一旦应用程序启动，打开以下 URL：

    ```text
    http://localhost:8080?name=John
    ```

   您应该看到响应为 "Hello, John!"：

   ![Spring Application response](spring-application-response.png){width=706}

## 下一步 {id=next-step}

在接下来的教程部分，您将学习 Kotlin 数据类以及如何在您的应用程序中使用它们。

**[继续下一章节](jvm-spring-boot-add-data-class.md)**