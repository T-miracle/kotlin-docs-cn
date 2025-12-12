[//]: # (title: Add a data class to Spring Boot project)

<web-summary>Add a Kotlin data class to Spring Boot project.</web-summary>

<tldr>
    <p>这是 <strong>Spring Boot 和 Kotlin 入门</strong> 教程的第二部分：</p><br/>  
    <p><img src="icon-1-done.svg" width="20" alt="第一步"/> 用 Kotlin 创建一个 Spring Boot 项目 <br/>
      <img src="icon-2.svg" width="20" alt="第二步"/> <strong> 向 Spring Boot 项目添加一个数据类</strong><br/>
      <img src="icon-3-todo.svg" width="20" alt="第三步"/> 为 Spring Boot 项目添加数据库支持<br/>
      <img src="icon-4-todo.svg" width="20" alt="第四步"/> 使用 Spring Data CrudRepository 进行数据库访问<br/></p>  
</tldr>

在本教程的这一部分，您将为应用程序添加更多功能，并探索更多 Kotlin 的语言特性，例如数据类。  
这需要修改 `MessageController` 类，使其返回一个包含序列化对象集合的 JSON 文档。

## 更新您的应用程序 {id=update-your-application}

1. In the same package, next to the `DemoApplication.kt` file, create a `Message.kt` file.
2. In the `Message.kt` file, create a data class with two properties: `id` and `text`:

    ```kotlin
    // Message.kt
    package com.example.demo
   
    data class Message(val id: String?, val text: String)
    ```  

   `Message` 类将用于数据传输：一个序列化的 `Message` 对象列表将构成控制器响应浏览器请求时生成的 JSON 文档。

   <deflist collapsible="true">  
       <def title="数据类 – data class Message">  
          <p>在 Kotlin 中，<a href="data-classes.md">数据类</a> 的主要目的是保存数据。这类类通过 <code>data</code> 关键字标记，一些标准功能和实用函数可以根据类的结构自动派生。</p>  
          <p>在此示例中，您将 <code>Message</code> 声明为数据类，因为其主要目的是存储数据。</p>  
       </def>  
       <def title="val 和 var 属性">  
          <p>在 Kotlin 中，<a href="properties.md">类的属性</a>可以通过以下方式声明：</p>  
          <list>  
             <li><i>可变</i>，使用 <code>var</code> 关键字</li>  
             <li><i>只读</i>，使用 <code>val</code> 关键字</li>  
          </list>  
          <p><code>Message</code> 类通过 <code>val</code> 关键字声明了两个属性：<code>id</code> 和 <code>text</code>。  
          编译器会自动生成这两个属性的 getter 方法。  
          在创建 <code>Message</code> 类的实例后，无法重新分配这些属性的值。</p>  
       </def>  
       <def title="可空类型 – String?">  
          <p>Kotlin 提供了<a href="null-safety.md#nullable-types-and-non-nullable-types">对可空类型的内置支持</a>。Kotlin 的类型系统区分了可以包含 <code>null</code> 的引用（<i>可空引用</i>）和不能包含 <code>null</code> 的引用（<i>非空引用</i>）。<br/>  
          例如，常规的 <code>String</code> 类型变量不能包含 <code>null</code>。如果需要允许 <code>null</code>，可以通过写作 <code>String?</code> 来声明一个可空字符串变量。</p>  
          <p>在本例中，<code>Message</code> 类的 <code>id</code> 属性被声明为可空类型。  
          因此，可以通过为 <code>id</code> 传递 <code>null</code> 值来创建 <code>Message</code> 类的实例：</p>  
          <code-block lang="kotlin">  
          Message(null, "Hello!")  
          </code-block>  
       </def>  
   </deflist>  
3. 在 `MessageController.kt` 文件中，替换 `index()` 函数，创建一个 `listMessages()` 函数，返回一个 `Message` 对象的列表：

    ```kotlin
    // MessageController.kt
    package com.example.demo
   
    import org.springframework.web.bind.annotation.GetMapping
    import org.springframework.web.bind.annotation.RequestMapping
    import org.springframework.web.bind.annotation.RestController

    @RestController
    @RequestMapping("/")
    class MessageController {
        @GetMapping
        fun listMessages() = listOf(
            Message("1", "Hello!"),
            Message("2", "Bonjour!"),
            Message("3", "Privet!"),
        )
    }
    ```

    <deflist collapsible="true">
       <def title="Collections – listOf()">
          <p>The Kotlin Standard Library provides implementations for basic collection types: sets, lists, and maps.<br/>
          Each collection type can be <i>read-only</i> or <i>mutable</i>:</p>
          <list>
              <li>A <i>read-only</i> collection comes with operations for accessing collection elements.</li>
              <li>A <i>mutable</i> collection comes also with write operations for adding, removing, and updating its elements.</li>
          </list>
          <p>Kotlin 标准库还提供了相应的工厂函数来创建这些集合的实例。</p>
          <p>在本教程中，您使用 <a href="https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/list-of.html"><code>listOf()</code></a> 函数创建一个 `Message` 对象的列表。  
          这是一个创建 <i>只读</i> 列表的工厂函数：您不能对列表进行添加或删除操作。<br/>
          如果需要对列表执行写操作，可以调用 <a href="https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/mutable-list-of.html"><code>mutableListOf()</code></a> 函数来创建一个可变列表实例。</p>
       </def>
       <def title="尾随逗号">
          <p><a href="coding-conventions.md#trailing-commas">尾随逗号</a>是系列元素中最后一项后的逗号符号：</p>
            <code-block lang="kotlin">
            Message("3", "Privet!"),
            </code-block>
          <p>这是 Kotlin 语法的一个便捷特性，完全是可选的 – 即使没有尾随逗号，您的代码仍然可以正常工作。</p>
          <p>在上面的例子中，创建一个 `Message` 对象的列表时，最后一个 `<code>listOf()</code>` 函数参数后面加了一个尾随逗号。</p>
       </def>
    </deflist>

`MessageController` 的响应现在将是一个包含 `Message` 对象集合的 JSON 文档。

> 如果 Jackson 库在类路径中，Spring 应用程序中的任何控制器默认都会渲染 JSON 响应。  
> 正如您在 [`build.gradle.kts` 文件中指定了 `spring-boot-starter-web` 依赖项](jvm-create-project-with-spring-boot.md#explore-the-project-gradle-build-file)，您已经作为 _传递性_ 依赖项引入了 Jackson。  
> 因此，当端点返回可以序列化为 JSON 的数据结构时，应用程序将响应一个 JSON 文档。
>
{style="note"}

以下是 `DemoApplication.kt`、`MessageController.kt` 和 `Message.kt` 文件的完整代码：

```kotlin
// DemoApplication.kt
package com.example.demo

import org.springframework.boot.autoconfigure.SpringBootApplication
import org.springframework.boot.runApplication

@SpringBootApplication
class DemoApplication

fun main(args: Array<String>) {
    runApplication<DemoApplication>(*args)
}
```
{initial-collapse-state="collapsed" collapsible="true"}

```kotlin
// MessageController.kt
package com.example.demo

import org.springframework.web.bind.annotation.GetMapping
import org.springframework.web.bind.annotation.RequestMapping
import org.springframework.web.bind.annotation.RestController

@RestController
@RequestMapping("/")
class MessageController {
    @GetMapping
    fun listMessages() = listOf(
        Message("1", "Hello!"),
        Message("2", "Bonjour!"),
        Message("3", "Privet!"),
    )
}
```
{initial-collapse-state="collapsed" collapsible="true"}

```kotlin
// Message.kt
package com.example.demo

data class Message(val id: String?, val text: String)
```
{collapsible="true" collapsible="true"}

## 运行应用程序 {id=run-the-application}

Spring 应用程序已经准备好运行：

1. 再次运行应用程序。

2. 应用程序启动后，打开以下 URL：

    ```text
    http://localhost:8080
    ```

   您将看到一个包含消息集合的 JSON 格式的页面：

   ![运行应用程序](messages-in-json-format.png){width=800}

## 下一步 {id=next-step}

在教程的下一部分，您将为项目添加并配置数据库，并进行 HTTP 请求。

**[继续下一章节](jvm-spring-boot-add-db-support.md)**