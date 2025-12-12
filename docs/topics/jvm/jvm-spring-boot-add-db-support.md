[//]: # (title: Add database support for Spring Boot project)

<web-summary>Add a database support for Sprint Boot project written in Kotlin using JDBC template.</web-summary>

<tldr>
    <p>这是<strong>Spring Boot 和 Kotlin 入门</strong>教程的第三部分：</p><br/>  
    <p><img src="icon-1-done.svg" width="20" alt="第一步"/> 用 Kotlin 创建一个 Spring Boot 项目 <br/>
      <img src="icon-2-done.svg" width="20" alt="第二步"/> 向 Spring Boot 项目添加一个数据类<br/>
      <img src="icon-3.svg" width="20" alt="第三步"/> <strong> 为 Spring Boot 项目添加数据库支持</strong><br/>
      <img src="icon-4-todo.svg" width="20" alt="第四步"/> 使用 Spring Data CrudRepository 进行数据库访问<br/></p>  
</tldr>

In this part of the tutorial, you'll add and configure a database to your project using _Java Database Connectivity_ (JDBC).
In JVM applications, you use JDBC to interact with databases.
For convenience, the Spring Framework provides the `JdbcTemplate` class that simplifies the use of JDBC and helps to avoid common errors.

## 添加数据库支持 {id=add-database-support}

在基于 Spring 框架的应用程序中，通常的做法是将数据库访问逻辑实现于所谓的 _服务_ 层——这里是业务逻辑所在的位置。  
在 Spring 中，您应该使用 `@Service` 注解标记类，以表示该类属于应用程序的服务层。  
在本应用程序中，您将创建 `MessageService` 类来实现这个功能。

在相同的包中，创建 `MessageService.kt` 文件，并实现 `MessageService` 类，如下所示：

```kotlin
// MessageService.kt
package com.example.demo

import org.springframework.stereotype.Service
import org.springframework.jdbc.core.JdbcTemplate
import java.util.*

@Service
class MessageService(private val db: JdbcTemplate) {
    fun findMessages(): List<Message> = db.query("select * from messages") { response, _ ->
        Message(response.getString("id"), response.getString("text"))
    }

    fun save(message: Message): Message {
        db.update(
            "insert into messages values ( ?, ? )",
            message.id, message.text
        )
        return message
    }
}
```

<deflist collapsible="true">  
   <def title="构造函数参数和依赖注入 – (private val db: JdbcTemplate)">  
      <p>在 Kotlin 中，类有一个主构造函数。它还可以有一个或多个 <a href="classes.md#secondary-constructors">次构造函数</a>。  
      <i>主构造函数</i> 是类头的一部分，位于类名和可选类型参数之后。在我们的例子中，构造函数是 <code>(val db: JdbcTemplate)</code>。</p>  
      <p><code>val db: JdbcTemplate</code> 是构造函数的参数：</p>  
      <code-block lang="kotlin">  
      @Service  
      class MessageService(private val db: JdbcTemplate)  
      </code-block>  
  </def>  
   <def title="尾部 lambda 和 SAM 转换">  
      <p><code>findMessages()</code> 函数调用了 <code>JdbcTemplate</code> 类的 <code>query()</code> 函数。  
      <code>query()</code> 函数接受两个参数：一个 SQL 查询字符串和一个回调，用于将每一行映射为一个对象：</p>  
      <code-block lang="sql">  
      db.query("...", RowMapper { ... } )  
      </code-block><br/>  
      <p><code>RowMapper</code> 接口声明了一个方法，因此可以通过 lambda 表达式来实现它，省略接口名。  
      Kotlin 编译器知道 lambda 表达式需要转换成哪个接口，因为它作为函数调用的参数使用。这种转换称为 <a href="java-interop.md#sam-conversions">Kotlin 中的 SAM 转换</a>：</p>  
      <code-block lang="sql">  
      db.query("...", { ... } )  
      </code-block><br/>  
      <p>经过 SAM 转换后，<code>query</code> 函数将接收到两个参数：第一个是 String 类型，最后一个是 lambda 表达式。  
      根据 Kotlin 的约定，如果函数的最后一个参数是函数类型，那么传入的 lambda 表达式可以放在括号外面。这种语法也称为 <a href="lambdas.md#passing-trailing-lambdas">尾部 lambda</a>：</p>  
      <code-block lang="sql">  
      db.query("...") { ... }  
      </code-block>  
   </def>  
   <def title="未使用的 lambda 参数使用下划线">  
      <p>对于具有多个参数的 lambda，您可以使用下划线 <code>_</code> 来替代不使用的参数名。</p>  
      <p>因此，最终的 query 函数调用语法如下所示：</p>  
      <code-block lang="kotlin">  
      db.query("select * from messages") { response, _ ->  
          Message(response.getString("id"), response.getString("text"))  
      }  
      </code-block>  
   </def>  
</deflist>

## 更新 MessageController 类 {id=update-the-messagecontroller-class}

更新 `MessageController.kt` 以使用新的 `MessageService` 类：

```kotlin
// MessageController.kt
package com.example.demo

import org.springframework.http.ResponseEntity
import org.springframework.web.bind.annotation.GetMapping
import org.springframework.web.bind.annotation.PostMapping
import org.springframework.web.bind.annotation.RequestBody
import org.springframework.web.bind.annotation.RequestMapping
import org.springframework.web.bind.annotation.RestController
import java.net.URI

@RestController
@RequestMapping("/")
class MessageController(private val service: MessageService) {
    @GetMapping
    fun listMessages() = service.findMessages()

    @PostMapping
    fun post(@RequestBody message: Message): ResponseEntity<Message> {
        val savedMessage = service.save(message)
        return ResponseEntity.created(URI("/${savedMessage.id}")).body(savedMessage)
    }
}
```

<deflist collapsible="true">  
   <def title="@PostMapping 注解">  
      <p>负责处理 HTTP POST 请求的方法需要使用 <code>@PostMapping</code> 注解。为了能够将作为 HTTP Body 内容发送的 JSON 转换为对象，您需要在方法参数上使用 <code>@RequestBody</code> 注解。  
      由于 Jackson 库已经包含在应用程序的类路径中，转换会自动发生。</p>  
   </def>  
   <def title="ResponseEntity">  
      <p><code>ResponseEntity</code> 代表整个 HTTP 响应：状态码、头部信息和主体内容。</p>  
      <p>使用 <code>created()</code> 方法，您可以配置响应状态码（201），并设置 Location 头部，指示已创建资源的上下文路径。</p>  
   </def>  
</deflist>  

## 更新 MessageService 类 {id=update-the-messageservice-class}

`Message` 类的 `id` 被声明为一个可空的 String：

```kotlin
data class Message(val id: String?, val text: String)
```

然而，在数据库中存储 `null` 作为 `id` 值是不正确的：您需要优雅地处理这种情况。

更新 `MessageService.kt` 文件中的代码，当 `id` 为 `null` 时，在将消息存储到数据库时生成一个新值：

```kotlin
// MessageService.kt
package com.example.demo

import org.springframework.stereotype.Service
import org.springframework.jdbc.core.JdbcTemplate
import org.springframework.jdbc.core.query
import java.util.UUID

@Service
class MessageService(private val db: JdbcTemplate) {
    fun findMessages(): List<Message> = db.query("select * from messages") { response, _ ->
        Message(response.getString("id"), response.getString("text"))
    }

    fun save(message: Message): Message {
        val id = message.id ?: UUID.randomUUID().toString() // Generate new id if it is null
        db.update(
            "insert into messages values ( ?, ? )",
            id, message.text
        )
        return message.copy(id = id) // Return a copy of the message with the new id
    }
}
```

<deflist collapsible="true">  
   <def title="Elvis 操作符 – ?:">  
      <p>代码 <code>message.id ?: UUID.randomUUID().toString()</code> 使用了 <a href="null-safety.md#elvis-operator">Elvis 操作符 (空值合并表达式) <code>?:</code></a>。  
      如果 <code>?:</code> 左侧的表达式不为 <code>null</code>，Elvis 操作符返回该值；否则，它返回右侧的表达式。  
      注意，只有在左侧表达式为 <code>null</code> 时，右侧的表达式才会被求值。</p>  
   </def>  
</deflist>  

应用程序代码已经准备好与数据库工作。现在需要配置数据源。

## 配置数据库 {id=configure-the-database}

在应用程序中配置数据库：

1. 在 `src/main/resources` 目录下创建 `schema.sql` 文件。该文件将存储数据库对象定义：

   ![创建数据库模式](create-database-schema.png){width=400}

2. 用以下代码更新 `src/main/resources/schema.sql` 文件：

   ```sql
   -- schema.sql
   CREATE TABLE IF NOT EXISTS messages (
   id       VARCHAR(60)  PRIMARY KEY,
   text     VARCHAR      NOT NULL
   );
   ```

   这会创建一个 `messages` 表，包含 `id` 和 `text` 两列。表结构与 `Message` 类的结构匹配。

3. 打开位于 `src/main/resources` 文件夹中的 `application.properties` 文件，并添加以下应用程序属性：

   ```none
   spring.application.name=demo
   spring.datasource.driver-class-name=org.h2.Driver
   spring.datasource.url=jdbc:h2:file:./data/testdb
   spring.datasource.username=name
   spring.datasource.password=password
   spring.sql.init.schema-locations=classpath:schema.sql
   spring.sql.init.mode=always
   ```

   这些设置启用 Spring Boot 应用程序的数据库连接。  
   请参阅 [Spring 文档](https://docs.spring.io/spring-boot/appendix/application-properties/index.html) 了解常见应用程序属性的完整列表。

## 通过 HTTP 请求将消息添加到数据库 {id=add-messages-to-database-via-http-request}

您应该使用 HTTP 客户端来操作之前创建的端点。在 IntelliJ IDEA 中，使用内嵌的 HTTP 客户端：

1. 运行应用程序。应用程序启动并运行后，您可以执行 POST 请求将消息存储到数据库中。

2. 在项目根目录创建 `requests.http` 文件，并添加以下 HTTP 请求：

   ```http request
   ### 发送 "Hello!"
   POST http://localhost:8080/
   Content-Type: application/json
   
   {
     "text": "Hello!"
   }
   
   ### 发送 "Bonjour!"
   
   POST http://localhost:8080/
   Content-Type: application/json
   
   {
     "text": "Bonjour!"
   }
   
   ### 发送 "Privet!"
   
   POST http://localhost:8080/
   Content-Type: application/json
   
   {
     "text": "Privet!"
   }
   
   ### 获取所有消息
   GET http://localhost:8080/
   ```

3. 执行所有 POST 请求。使用请求声明旁边的绿色 **Run** 图标来执行请求。  
   这些请求将文本消息写入数据库：

   ![执行 POST 请求](execute-post-requests.png)

4. 执行 GET 请求，并在 **Run** 工具窗口中查看结果：

   ![执行 GET 请求](execute-get-requests.png)

### 执行请求的另一种方式 {id=alternative-way-to-execute-requests collapsible="true"}

您还可以使用其他 HTTP 客户端或 cURL 命令行工具。例如，在终端中运行以下命令以获得相同的结果：

```bash
curl -X POST --location "http://localhost:8080" -H "Content-Type: application/json" -d "{ \"text\": \"Hello!\" }"

curl -X POST --location "http://localhost:8080" -H "Content-Type: application/json" -d "{ \"text\": \"Bonjour!\" }"

curl -X POST --location "http://localhost:8080" -H "Content-Type: application/json" -d "{ \"text\": \"Privet!\" }"

curl -X GET --location "http://localhost:8080"
```

## 通过 id 检索消息 {id=retrieve-messages-by-id}

扩展应用程序的功能，以便通过 id 检索单独的消息。

1. 在 `MessageService` 类中，添加新的函数 `findMessageById(id: String)` 以通过 id 检索单独的消息：

    ```kotlin
    // MessageService.kt
    package com.example.demo

    import org.springframework.stereotype.Service
    import org.springframework.jdbc.core.JdbcTemplate
    import org.springframework.jdbc.core.query
    import java.util.*
    
    @Service
    class MessageService(private val db: JdbcTemplate) {
        fun findMessages(): List<Message> = db.query("select * from messages") { response, _ ->
            Message(response.getString("id"), response.getString("text"))
        }
    
        fun findMessageById(id: String): Message? = db.query("select * from messages where id = ?", id) { response, _ ->
            Message(response.getString("id"), response.getString("text"))
        }.singleOrNull()
    
        fun save(message: Message): Message {
            val id = message.id ?: UUID.randomUUID().toString() // 如果 id 为 null，则生成新 id
            db.update(
                "insert into messages values ( ?, ? )",
                id, message.text
            )
            return message.copy(id = id) // 返回带有新 id 的消息副本
        }
    }
    ```
<deflist collapsible="true">
    <def title="vararg argument position in the parameter list">
        <p>The <code>query()</code> function takes three arguments:</p>
        <list>
            <li>SQL query string that requires a parameter to run</li>
            <li><code>id</code>, which is a parameter of type String</li>
            <li><code>RowMapper</code> instance, which implemented by a lambda expression</li>
        </list>
        <p>The second parameter for the <code>query()</code> function is declared as a <i>variable argument</i> (<code>vararg</code>). In Kotlin, the position of the variable arguments parameter is not required to be the last in the parameters list.</p>
    </def>
    <def title="singleOrNull() function">
       <p>The <a href="https://kotlinlang.org/api/core/kotlin-stdlib/kotlin.collections/single-or-null.html"><code>singleOrNull()</code></a> function returns a single element, or <code>null</code> if the array is empty or has more than one element with the same value.</p>
    </def>
   </deflist>
   > 用于通过 id 获取消息的 `.query()` 函数是 Spring 框架提供的 [Kotlin 扩展函数](extensions.md#extension-functions)，  
   > 它需要额外的导入 `import org.springframework.jdbc.core.query`，如上面代码所示。
   >
   {style="warning"}

2. 在 `MessageController` 类中添加新的 `index(...)` 函数，带有 `id` 参数：

    ```kotlin
    // MessageController.kt
    package com.example.demo

    import org.springframework.http.ResponseEntity
    import org.springframework.web.bind.annotation.GetMapping
    import org.springframework.web.bind.annotation.PathVariable
    import org.springframework.web.bind.annotation.PostMapping
    import org.springframework.web.bind.annotation.RequestBody
    import org.springframework.web.bind.annotation.RequestMapping
    import org.springframework.web.bind.annotation.RestController
    import java.net.URI
    
    @RestController
    @RequestMapping("/")
    class MessageController(private val service: MessageService) {
        @GetMapping
        fun listMessages() = ResponseEntity.ok(service.findMessages())
        
        @PostMapping
        fun post(@RequestBody message: Message): ResponseEntity<Message> {
            val savedMessage = service.save(message)
            return ResponseEntity.created(URI("/${savedMessage.id}")).body(savedMessage)
        }
        
        @GetMapping("/{id}")
        fun getMessage(@PathVariable id: String): ResponseEntity<Message> =
            service.findMessageById(id).toResponseEntity()
        
        private fun Message?.toResponseEntity(): ResponseEntity<Message> =
            // 如果消息为 null（未找到），则设置响应代码为 404
            this?.let { ResponseEntity.ok(it) } ?: ResponseEntity.notFound().build() 
    }
    ```

    <deflist collapsible="true">
    <def title="Retrieving a value from the context path">
       <p>The message <code>id</code> is retrieved from the context path by the Spring Framework as you annotated the new function by <code>@GetMapping(&quot;/{id}&quot;)</code>. By annotating the function argument with <code>@PathVariable</code>, you tell the framework to use the retrieved value as a function argument. The new function makes a call to <code>MessageService</code> to retrieve the individual message by its id.</p>
    </def>
    <def title="Extension function with nullable receiver">
         <p>Extensions can be defined with a nullable receiver type. If the receiver is <code>null</code>, then <code>this</code> is also <code>null</code>. So when defining an extension with a nullable receiver type, it is recommended performing a <code>this == null</code> check inside the function body.</p>
         <p>You can also use the null-safe invocation operator (<code>?.</code>) to perform the null check as in the <code>toResponseEntity()</code> function above:</p>
         <code-block lang="kotlin">
         this?.let { ResponseEntity.ok(it) }
         </code-block>
      </def>
      <def title="ResponseEntity">
         <p><code>ResponseEntity</code> 代表 HTTP 响应，包括状态码、头部信息和主体内容。它是一个通用包装器，允许您发送定制的 HTTP 响应回客户端，并对内容有更多控制。</p>
      </def>
   </deflist>

这是应用程序的完整代码：

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
// Message.kt
package com.example.demo

data class Message(val id: String?, val text: String)
```
{initial-collapse-state="collapsed" collapsible="true"}

```kotlin
// MessageService.kt
package com.example.demo

import org.springframework.stereotype.Service
import org.springframework.jdbc.core.JdbcTemplate
import org.springframework.jdbc.core.query
import java.util.*

@Service
class MessageService(private val db: JdbcTemplate) {
    fun findMessages(): List<Message> = db.query("select * from messages") { response, _ ->
        Message(response.getString("id"), response.getString("text"))
    }

    fun findMessageById(id: String): Message? = db.query("select * from messages where id = ?", id) { response, _ ->
        Message(response.getString("id"), response.getString("text"))
    }.singleOrNull()

    fun save(message: Message): Message {
        val id = message.id ?: UUID.randomUUID().toString()
        db.update(
            "insert into messages values ( ?, ? )",
            id, message.text
        )
        return message.copy(id = id)
    }
}
```
{initial-collapse-state="collapsed" collapsible="true"}

```kotlin
// MessageController.kt
package com.example.demo

import org.springframework.http.ResponseEntity
import org.springframework.web.bind.annotation.GetMapping
import org.springframework.web.bind.annotation.PathVariable
import org.springframework.web.bind.annotation.PostMapping
import org.springframework.web.bind.annotation.RequestBody
import org.springframework.web.bind.annotation.RequestMapping
import org.springframework.web.bind.annotation.RestController
import java.net.URI

@RestController
@RequestMapping("/")
class MessageController(private val service: MessageService) {
    @GetMapping
    fun listMessages() = ResponseEntity.ok(service.findMessages())

    @PostMapping
    fun post(@RequestBody message: Message): ResponseEntity<Message> {
        val savedMessage = service.save(message)
        return ResponseEntity.created(URI("/${savedMessage.id}")).body(savedMessage)
    }

    @GetMapping("/{id}")
    fun getMessage(@PathVariable id: String): ResponseEntity<Message> =
        service.findMessageById(id).toResponseEntity()

    private fun Message?.toResponseEntity(): ResponseEntity<Message> =
        this?.let { ResponseEntity.ok(it) } ?: ResponseEntity.notFound().build()
}
```
{collapsible="true" collapsible="true"}

## 运行应用程序 {id=run-the-application}

Spring 应用程序已经准备好运行：

1. 再次运行应用程序。

2. 打开 `requests.http` 文件并添加新的 GET 请求：

    ```http request
    ### 通过 id 获取消息
    GET http://localhost:8080/id
    ```

3. 执行 GET 请求，从数据库中检索所有消息。

4. 在 **Run** 工具窗口中复制其中一个 id，并将其添加到请求中，如下所示：

    ```http request
    ### 通过 id 获取消息
    GET http://localhost:8080/f910aa7e-11ee-4215-93ed-1aeeac822707
    ```

    > 将您自己的消息 id 替换掉上面提到的 id。
    >
    {style="note"}

5. 执行 GET 请求并在 **Run** 工具窗口中查看结果：

   ![通过 id 检索消息](retrieve-message-by-its-id.png){width=706}

## 下一步 {id=next-step}

最后一步将向您展示如何使用更流行的数据库连接方式——Spring Data。

**[继续下一章节](jvm-spring-boot-using-crudrepository.md)**
