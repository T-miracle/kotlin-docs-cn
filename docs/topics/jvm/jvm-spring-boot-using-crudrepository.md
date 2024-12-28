[//]: # (title: 使用 Spring Data CrudRepository 进行数据库访问)
[//]: # (description: 在用 Kotlin 编写的 Spring Boot 项目中使用 Spring Data 接口。)

<tldr>
    <p>这是 <strong>Spring Boot 和 Kotlin 入门</strong> 教程的第四部分：</p><br/>  
    <p><img src="icon-1-done.svg" width="20" alt="第一步"/> 用 Kotlin 创建一个 Spring Boot 项目<br/>
      <img src="icon-2-done.svg" width="20" alt="第二步"/> 向 Spring Boot 项目添加一个数据类<br/>
      <img src="icon-3-done.svg" width="20" alt="第三步"/> 为 Spring Boot 项目添加数据库支持<br/>
      <img src="icon-4-todo.svg" width="20" alt="第四步"/> <strong> 使用 Spring Data CrudRepository 进行数据库访问</strong><br/></p>  
</tldr>

在这一部分，你将迁移服务层，改为使用 [Spring Data](https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/repository/CrudRepository.html)
的 `CrudRepository` 进行数据库访问，而不是使用 `JdbcTemplate`。  
_CrudRepository_ 是一个 Spring Data 接口，用于对指定类型的仓库执行通用的 [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) 操作。  
它提供了多个开箱即用的方法用来和数据库进行交互。

## 更新您的应用程序 {id=update-your-application}

首先，你需要调整 `Message` 类以便与 `CrudRepository` API 配合使用：

1. 在 `Message` 类上添加 `@Table` 注解，用于声明和数据库表的映射关系。
   在 `id` 字段前添加 `@Id` 注解。

   > 这些注解还需要额外的导入。
   >
   {style="note"}

    ```kotlin
    // Message.kt
    package demo
   
    import org.springframework.data.annotation.Id
    import org.springframework.data.relational.core.mapping.Table
    
    @Table("MESSAGES")
    data class Message(@Id val id: String?, val text: String)
    ```

   此外，为了使 `Message` 类的使用更加符合语法习惯，
   你可以将 `id` 属性的默认值设置为 `null`，并调整数据类属性的顺序：

    ```kotlin
    @Table("MESSAGES")
    data class Message(val text: String, @Id val id: String? = null)
    ```

   现在，如果你需要创建一个新的 `Message` 实例，你只需指定 `text` 属性作为参数：

    ```kotlin
    val message = Message("Hello") // id 为 null
    ```

2. 为 `CrudRepository` 声明一个接口，使其能够与 `Message` 数据类一起使用。创建 `MessageRepository.kt` 文件并添加以下代码：

    ```kotlin
    // MessageRepository.kt
    package demo
   
    import org.springframework.data.repository.CrudRepository
    
    interface MessageRepository : CrudRepository<Message, String>
    ```

3. 更新 `MessageService` 类。现在它将使用 `MessageRepository`，而不是执行 SQL 查询：

    ```kotlin
    // MessageService.kt
    package demo

    import org.springframework.data.repository.findByIdOrNull
    import org.springframework.stereotype.Service
    
    @Service
    class MessageService(private val db: MessageRepository) {
        fun findMessages(): List<Message> = db.findAll().toList()
    
        fun findMessageById(id: String): Message? = db.findByIdOrNull(id)
    
        fun save(message: Message): Message = db.save(message)
    }
    ```

    <deflist collapsible="true">
       <def title="扩展函数">
          <p><code>findByIdOrNull()</code> 函数是一个 <a href="extensions.md#extension-functions">扩展函数</a>，用于 Spring Data JDBC 中的 <code>CrudRepository</code> 接口。</p>
       </def>
       <def title="CrudRepository save() 函数">
          <p><a href="https://docs.spring.io/spring-data/jdbc/docs/current/reference/html/#jdbc.entity-persistence">该函数的工作原理</a>是基于假设新对象在数据库中没有 id，因此，插入时 <b>id 应为 null</b>。</p>
          <p>如果 id 不为 <i>null</i>，<code>CrudRepository</code> 会假设该对象已经存在于数据库中，此时执行的是 <i>更新</i> 操作，而非 <i>插入</i> 操作。插入操作后，<code>id</code> 将由数据存储生成并赋值给 <code>Message</code> 实例。这就是为什么 <code>id</code> 属性应该使用 <code>var</code> 关键字声明的原因。</p>
          <p></p>
       </def>
    </deflist>


4. 更新 `messages` 表的定义，以生成插入对象的 id。由于 `id` 是一个字符串，你可以使用 `RANDOM_UUID()` 函数来默认生成 id 值：

    ```sql
    -- schema.sql 
    CREATE TABLE IF NOT EXISTS messages (
        id      VARCHAR(60)  DEFAULT RANDOM_UUID() PRIMARY KEY,
        text    VARCHAR      NOT NULL
    );
    ```

5. 更新位于 `src/main/resources` 文件夹中的 `application.properties` 文件中的数据库名称：

   ```none
   spring.application.name=demo
   spring.datasource.driver-class-name=org.h2.Driver
   spring.datasource.url=jdbc:h2:file:./data/testdb2
   spring.datasource.username=name
   spring.datasource.password=password
   spring.sql.init.schema-locations=classpath:schema.sql
   spring.sql.init.mode=always
   ```

这是应用程序的完整代码：

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
{initial-collapse-state="collapsed" collapsible="true"}

```kotlin
// Message.kt
package demo

import org.springframework.data.annotation.Id
import org.springframework.data.relational.core.mapping.Table

@Table("MESSAGES")
data class Message(val text: String, @Id val id: String? = null)
```
{initial-collapse-state="collapsed" collapsible="true"}

```kotlin
// MessageRepository.kt
package demo

import org.springframework.data.repository.CrudRepository

interface MessageRepository : CrudRepository<Message, String>
```
{initial-collapse-state="collapsed" collapsible="true"}

```kotlin
// MessageService.kt
package demo

import org.springframework.data.repository.findByIdOrNull
import org.springframework.stereotype.Service

@Service
class MessageService(private val db: MessageRepository) {
    fun findMessages(): List<Message> = db.findAll().toList()

    fun findMessageById(id: String): Message? = db.findByIdOrNull(id)

    fun save(message: Message): Message = db.save(message)
}
```
{initial-collapse-state="collapsed" collapsible="true"}

```kotlin
// MessageController.kt
package demo

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
        // 如果消息为空（未找到），则将响应代码设置为 404
        this?.let { ResponseEntity.ok(it) } ?: ResponseEntity.notFound().build()
}
```
{collapsible="true" collapsible="true"}

## 运行应用程序 {id=run-the-application}

应用程序已经准备好重新运行。  
通过将 `JdbcTemplate` 替换为 `CrudRepository`，功能没有变化，因此应用程序应该与之前一样正常工作。

## 接下来做什么 {id=whats-next}

获取您的个人语言地图，帮助您浏览 Kotlin 特性并跟踪学习进度：

<a href="https://resources.jetbrains.com/storage/products/kotlin/docs/Kotlin_Language_Features_Map.pdf">
   <img src="get-kotlin-language-map.png" width="700" alt="获取 Kotlin 语言地图" style="block"/>
</a>

* 了解更多关于 [从 Kotlin 代码调用 Java](java-interop.md) 和 [从 Java 代码调用 Kotlin](java-to-kotlin-interop.md) 的内容。
* 了解如何使用 [Java 到 Kotlin 转换器](mixing-java-kotlin-intellij.md#converting-an-existing-java-file-to-kotlin-with-j2k) 将现有的 Java 代码转换为 Kotlin。
* 查看我们的 Java 到 Kotlin 迁移指南：
   * [Java 和 Kotlin 中的字符串](java-to-kotlin-idioms-strings.md)。
   * [Java 和 Kotlin 中的集合](java-to-kotlin-collections-guide.md)。
   * [Java 和 Kotlin 中的空值处理](java-to-kotlin-nullability-guide.md)。
