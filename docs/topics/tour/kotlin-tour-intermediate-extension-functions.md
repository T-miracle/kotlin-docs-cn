[//]: # (title: 中级：扩展函数)

<no-index/>

<tldr>
    <p><img src="icon-1.svg" width="20" alt="第一步" /> <strong>扩展函数</strong><br />
        <img src="icon-2-todo.svg" width="20" alt="第二步" /> <a href="kotlin-tour-intermediate-scope-functions.md">作用域函数</a><br />
        <img src="icon-3-todo.svg" width="20" alt="第三步" /> <a href="kotlin-tour-intermediate-lambdas-receiver.md">带接收者的 Lambda 表达式</a><br />
        <img src="icon-4-todo.svg" width="20" alt="第四步" /> <a href="kotlin-tour-intermediate-classes-interfaces.md">类和接口</a><br />
        <img src="icon-5-todo.svg" width="20" alt="第五步" /> <a href="kotlin-tour-intermediate-objects.md">对象</a><br />
        <img src="icon-6-todo.svg" width="20" alt="第六步" /> <a href="kotlin-tour-intermediate-open-special-classes.md">开放类和特殊类</a><br />
        <img src="icon-7-todo.svg" width="20" alt="第七步" /> <a href="kotlin-tour-intermediate-properties.md">属性</a><br />
        <img src="icon-8-todo.svg" width="20" alt="第八步" /> <a href="kotlin-tour-intermediate-null-safety.md">空安全</a><br />
        <img src="icon-9-todo.svg" width="20" alt="第九步" /> <a href="kotlin-tour-intermediate-libraries-and-apis.md">库和 API</a></p>
</tldr>

在本章中，你将探索一些特殊的 Kotlin 函数，它们可以让你的代码更加简洁、可读性更强。
学习这些函数如何帮助你使用高效的设计模式，将你的项目提升到新的层次。

## 扩展函数 {id=extension-functions}

在软件开发中，你经常需要在不修改原始源代码的情况下改变程序的行为。
例如，你可能希望为来自第三方库的某个类添加额外的功能。

你可以通过添加 *扩展函数* 来扩展一个类。调用扩展函数的方式与调用类的成员函数完全相同，都是使用点号 `.`。

在介绍扩展函数的完整语法之前，你需要先理解什么是 **接收者**。
接收者是函数被调用的对象。换句话说，接收者就是信息所依附或共享的对象。


![An example of sender and receiver](receiver-highlight.png){width="500"}

在这个示例中，`main()` 函数调用了 [`.first()`](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin.collections/first.html)
函数，用于返回列表中的第一个元素。
`.first()` 函数是 **在** `readOnlyShapes` 变量上调用的，因此 `readOnlyShapes` 变量就是接收者。

要创建一个扩展函数，需要先写出你想要扩展的类名，然后接一个 `.`，再写上你的函数名。
接着补全函数声明的其余部分，包括它的参数和返回类型。

例如：

```kotlin
fun String.bold(): String = "<b>$this</b>"

fun main() {
    // “hello” 是接收者
    println("hello".bold())
    // <b>hello</b>
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-extension-function"}

在这个示例中：

* `String` 是被扩展的类。
* `bold` 是扩展函数的名称。
* `.bold()` 扩展函数的返回类型是 `String`。
* `"hello"` 是 `String` 的一个实例，作为接收者。
* 接收者在函数体内部通过 [关键字](keyword-reference.md) `this` 来访问。
* 使用了字符串模板（`$`）来访问 `this` 的值。
* `.bold()` 扩展函数接收一个字符串，并将其包裹在 `<b>` HTML 元素中，以实现加粗文本效果。

## 面向扩展的设计 {id=extension-oriented-design}

你可以在任何地方定义 扩展函数，这使你能够创建 面向扩展的设计。
这类设计将 核心功能 与 有用但非必需的特性 分离开来，从而让你的代码更易读、也更容易维护。

一个很好的例子是 Ktor 库中的 [`HttpClient`](https://api.ktor.io/ktor-client/ktor-client-core/io.ktor.client/-http-client/index.html)
类，它用于执行 网络请求。它的核心功能集中在一个名为 `request()` 的函数中，该函数接收 发起一次 HTTP 请求 所需的全部信息：

```kotlin
class HttpClient {
    fun request(method: String, url: String, headers: Map<String, String>): HttpResponse {
        // 网络相关代码
    }
}
```
{validate="false"}

在实践中，最常见的 HTTP 请求是 GET 或 POST 请求。为这些常见使用场景提供更短的名称是很合理的。
不过，它们并不需要编写新的网络代码，只是一次特定形式的 request 调用。
换句话说，它们非常适合被定义为独立的 `.get()` 和 `.post()` 扩展函数：

```kotlin
fun HttpClient.get(url: String): HttpResponse = request("GET", url, emptyMap())
fun HttpClient.post(url: String): HttpResponse = request("POST", url, emptyMap())
```
{validate="false"}

这些 `.get()` 和 `.post()` 函数扩展了 `HttpClient` 类。
由于它们是以 `HttpClient` 类的实例作为 接收者 来调用的，因此可以直接使用 `HttpClient` 类中的 `request()` 函数。
你可以通过这些扩展函数，使用合适的 HTTP 方法来调用 `request()` 函数，从而简化代码并提升可读性：

```kotlin
class HttpClient {
    fun request(method: String, url: String, headers: Map<String, String>): HttpResponse {
        println("向 $url 发起 $method 请求，携带的 headers 为：$headers")
        return HttpResponse("来自 $url 的响应")
    }
}

fun HttpClient.get(url: String): HttpResponse = request("GET", url, emptyMap())

fun main() {
    val client = HttpClient()

    // 直接使用 request() 发起 GET 请求
    val getResponseWithMember = client.request("GET", "https://example.com", emptyMap())

    // 使用 get() 扩展函数发起 GET 请求
    // client 实例是接收者
    val getResponseWithExtension = client.get("https://example.com")
}
```
{validate="false"}

这种 面向扩展 的方式在 Kotlin 的 [标准库](https://kotlinlang.org/api/latest/jvm/stdlib/) 以及其他库中被广泛使用。
例如，`String` 类就提供了大量的 [扩展函数](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-string/#extension-functions)，
用于帮助你更方便地处理字符串。

有关 扩展函数 的更多信息，请参阅 [扩展](extensions.md)。

## 练习 {id=practice}

### 练习 1 {initial-collapse-state="collapsed" collapsible="true" id="extension-functions-exercise-1"}

编写一个名为 `isPositive` 的扩展函数，它接收一个整数，并检查该整数是否为正数。

|---|---|
```kotlin
fun Int.// 在这里编写你的代码

fun main() {
    println(1.isPositive())
    // true
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-extension-functions-exercise-1"}

|---|---|
```kotlin
fun Int.isPositive(): Boolean = this > 0

fun main() {
    println(1.isPositive())
    // true
}
```
{initial-collapse-state="collapsed" collapsible="true" collapsed-title="Example solution" id="kotlin-tour-extension-functions-solution-1"}

### 练习 2 {initial-collapse-state="collapsed" collapsible="true" id="extension-functions-exercise-2"}

编写一个名为 `toLowercaseString` 的扩展函数，它接收一个字符串并返回其小写形式。

<deflist collapsible="true">
    <def title="提示">
        对于 <code>String</code> 类型，使用 <a href="https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/lowercase.html"><code>.lowercase()</code></a>
        函数。
    </def>
</deflist>

|---|---|
```kotlin
fun // 在这里编写你的代码

fun main() {
    println("Hello World!".toLowercaseString())
    // hello world!
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-extension-functions-exercise-2"}

|---|---|
```kotlin
fun String.toLowercaseString(): String = this.lowercase()

fun main() {
    println("Hello World!".toLowercaseString())
    // hello world!
}
```
{initial-collapse-state="collapsed" collapsible="true" collapsed-title="示例解决方案" id="kotlin-tour-extension-functions-solution-2"}

## 下一步 {id=next-step}

[中级：作用域函数](kotlin-tour-intermediate-scope-functions.md)