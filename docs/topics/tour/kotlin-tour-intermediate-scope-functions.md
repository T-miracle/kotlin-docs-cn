[//]: # (title: 中级：作用域函数)

<no-index/>

<tldr>
    <p><img src="icon-1-done.svg" width="20" alt="第一步" /> <a href="kotlin-tour-intermediate-extension-functions.md">扩展函数</a><br />
        <img src="icon-2.svg" width="20" alt="第二步" /> <strong>作用域函数</strong><br />
        <img src="icon-3-todo.svg" width="20" alt="第三步" /> <a href="kotlin-tour-intermediate-lambdas-receiver.md">带接收者的 Lambda 表达式</a><br />
        <img src="icon-4-todo.svg" width="20" alt="第四步" /> <a href="kotlin-tour-intermediate-classes-interfaces.md">类和接口</a><br />
        <img src="icon-5-todo.svg" width="20" alt="第五步" /> <a href="kotlin-tour-intermediate-objects.md">对象</a><br />
        <img src="icon-6-todo.svg" width="20" alt="第六步" /> <a href="kotlin-tour-intermediate-open-special-classes.md">开放类和特殊类</a><br />
        <img src="icon-7-todo.svg" width="20" alt="第七步" /> <a href="kotlin-tour-intermediate-properties.md">属性</a><br />
        <img src="icon-8-todo.svg" width="20" alt="第八步" /> <a href="kotlin-tour-intermediate-null-safety.md">空安全</a><br />
        <img src="icon-9-todo.svg" width="20" alt="第九步" /> <a href="kotlin-tour-intermediate-libraries-and-apis.md">库和 API</a></p>
</tldr>

在本章中，你将在 扩展函数 的基础上，学习如何使用 作用域函数 来编写更加 Kotlin 风格（idiomatic）的代码。

## 作用域函数 {id=scope-functions}

在编程中，作用域 指的是 变量 或 对象 被识别和生效的范围。
最常被提及的作用域是 全局作用域 和 局部作用域：

* **全局作用域** – 在程序的任何位置都可以访问的变量或对象。
* **局部作用域** – 只能在其定义所在的代码块或函数内部访问的变量或对象。

在 Kotlin 中，还提供了一类 作用域函数，它们允许你围绕某个对象创建一个 临时作用域，并在其中执行一段代码。

作用域函数 能让代码更加简洁，因为在临时作用域中你不需要反复引用对象的名称。
根据不同的作用域函数，你可以通过关键字 `this` 来访问对象，或者通过关键字 `it` 将对象作为参数来使用。

Kotlin 一共提供了五个 作用域函数：`let`、`apply`、`run`、`also` 和 `with`。

每个作用域函数 都接收一个 Lambda 表达式，并返回 对象本身 或 Lambda 表达式的结果。
在本次导览中，我们将逐一讲解每个作用域函数及其使用方式。

> 你还可以观看 Sebastian Aigner（Kotlin 开发者布道师）关于 作用域函数 的演讲
> [回到 Stdlib：充分利用 Kotlin 的标准库](https://youtu.be/DdvgvSHrN9g?feature=shared&t=1511)
> 
{style="tip"}

### Let

当你希望在代码中执行 空值检查，并在之后对 返回的对象 继续执行操作时，可以使用 `let` 作用域函数。

请看下面的示例：

```kotlin
fun sendNotification(recipientAddress: String): String {
    println("Yo $recipientAddress!")
    return "Notification sent!"
}

fun getNextAddress(): String {
    return "sebastian@jetbrains.com"
}

fun main() {
    val address: String? = getNextAddress()
    sendNotification(address)
}
```
{validate = "false"}

该示例包含两个函数：
* `sendNotification()`：包含一个函数参数 `recipientAddress`，并返回一个字符串。
* `getNextAddress()`：不包含函数参数，并返回一个字符串。

该示例创建了一个名为 `address` 的变量，它的类型是 可空的 `String`。
但当你调用 `sendNotification()` 函数时就会出现问题，因为这个函数并不期望 `address` 可能是 `null` 值。结果，编译器会报错：

```text
Argument type mismatch: actual type is 'String?', but 'String' was expected.
```

在 入门导览 中，你已经知道可以使用 `if` 条件来进行 空值检查，或者使用 [Elvis 运算符 `?:`](kotlin-tour-null-safety.md#use-elvis-operator)。
但如果你希望在代码的后续部分继续使用 返回的对象 呢？你可以通过 一个 `if` 条件 **并且** 搭配一个 `else` 分支 来实现这一点：

```kotlin
fun sendNotification(recipientAddress: String): String {
    println("Yo $recipientAddress!")
    return "Notification sent!"
}

fun getNextAddress(): String {
    return "sebastian@jetbrains.com"
}

fun main() { 
    //sampleStart
    val address: String? = getNextAddress()
    val confirm = if(address != null) {
        sendNotification(address)
    } else { null }
    //sampleEnd
}
```
{kotlin-runnable="true" id="kotlin-tour-scope-function-let-non-null-if"}

不过，更加简洁的方式是使用 `let` 作用域函数：

```kotlin
fun sendNotification(recipientAddress: String): String {
    println("Yo $recipientAddress!")
    return "Notification sent!"
}

fun getNextAddress(): String {
    return "sebastian@jetbrains.com"
}

fun main() {
    //sampleStart
    val address: String? = getNextAddress()
    val confirm = address?.let {
        sendNotification(it)
    }
    //sampleEnd
}
```
{kotlin-runnable="true" id="kotlin-tour-scope-function-let-non-null"}

该示例：
* 创建了名为 `address` 和 `confirm` 的变量。
* 在 `address` 变量上对 `let` 作用域函数 使用了安全调用。
* 在 `let` 作用域函数 内创建了一个临时作用域。
* 将 `sendNotification()` 函数 以 Lambda 表达式 的形式传入 `let` 作用域函数。
* 在临时作用域中，通过 `it` 来引用 `address` 变量。
* 将返回结果赋值给 `confirm` 变量。

通过这种方式，你的代码可以正确处理 `address` 变量 可能为 `null` 的情况，并且你可以在后续代码中继续使用 `confirm` 变量。

### Apply

使用 `apply` 作用域函数可以在对象创建时（例如类实例）进行初始化，而不是在代码的后续部分再去设置。
这种方式可以让你的代码更易读，也更易于管理。

请看下面的示例：

```kotlin
class Client() {
    var token: String? = null
    fun connect() = println("connected!")
    fun authenticate() = println("authenticated!")
    fun getData(): String = "Mock data"
}

val client = Client()

fun main() {
    client.token = "asdf"
    client.connect()
    // connected!
    client.authenticate()
    // authenticated!
    client.getData()
}
```
{kotlin-runnable="true" id="kotlin-tour-scope-function-apply-before"}

该示例中有一个 `Client` 类，它包含一个名为 `token` 的属性和三个成员函数：`connect()`、`authenticate()` 和 `getData()`。

示例在 `main()` 函数中创建了 `client` 作为 `Client` 类的实例，然后再初始化它的 `token` 属性并调用其成员函数。

虽然这个示例已经很简洁，但在实际开发中，通常在创建类实例后，需要等待一段时间才能完成配置并使用它（及其成员函数）。
如果使用 `apply` 作用域函数，你可以在代码的同一个地方，完成类实例的创建、配置以及成员函数的调用：

```kotlin
class Client() {
    var token: String? = null
    fun connect() = println("connected!")
    fun authenticate() = println("authenticated!")
    fun getData(): String = "Mock data"
}
//sampleStart
val client = Client().apply {
    token = "asdf"
    connect()
    authenticate()
}

fun main() {
    client.getData()
    // connected!
    // authenticated!
}
//sampleEnd
```
{kotlin-runnable="true" id="kotlin-tour-scope-function-apply-after"}

该示例：

* 创建了 `client` 作为 `Client` 类的实例。
* 在 `client` 实例上使用了 `apply` 作用域函数。
* 在 `apply` 作用域函数内部创建了一个临时作用域，因此在访问属性或函数时无需显式引用 `client` 实例。
* 将一个 Lambda 表达式传入 `apply` 作用域函数，在其中更新 `token` 属性并调用 `connect()` 和 `authenticate()` 函数。
* 在 `main()` 函数中对 `client` 实例调用 `getData()` 成员函数。

如你所见，这种策略在处理较大代码块时非常方便。

### Run

与 `apply` 类似，你可以使用 `run` 作用域函数来初始化对象，但更适合在代码中的特定时刻初始化对象 **并** 立即计算一个结果。

我们继续前面 `apply` 函数的示例，这一次，你希望将 `connect()` 和 `authenticate()` 函数组合在一起，以便在每次请求时都调用它们。

例如：

```kotlin
class Client() {
    var token: String? = null
    fun connect() = println("connected!")
    fun authenticate() = println("authenticated!")
    fun getData(): String = "Mock data"
}

//sampleStart
val client: Client = Client().apply {
    token = "asdf"
}

fun main() {
    val result: String = client.run {
        connect()
        // connected!
        authenticate()
        // authenticated!
        getData()
    }
}
//sampleEnd
```
{kotlin-runnable="true" id="kotlin-tour-scope-function-run"}

该示例：

* 创建了 `client` 作为 `Client` 类的实例。
* 在 `client` 实例上使用了 `apply` 作用域函数。
* 在 `apply` 作用域函数内部创建了一个临时作用域，因此在访问属性或函数时无需显式引用 `client` 实例。
* 将一个 Lambda 表达式传入 `apply` 作用域函数，在其中更新 `token` 属性。

`main()` 函数：

* 创建了一个类型为 `String` 的 `result` 变量。
* 在 `client` 实例上使用了 `run` 作用域函数。
* 在 `run` 作用域函数内部创建了一个临时作用域，因此在访问属性或函数时无需显式引用 `client` 实例。
* 将一个 Lambda 表达式传入 `run` 作用域函数，在其中调用 `connect()`、`authenticate()` 和 `getData()` 函数。
* 将返回结果赋值给 `result` 变量。

现在，你可以在代码的后续部分继续使用返回的结果。

### Also

使用 `also` 作用域函数可以在对象上执行额外操作后，再返回该对象，以便在代码中继续使用它，例如记录日志。

请看下面的示例：

```kotlin
fun main() {
    val medals: List<String> = listOf("Gold", "Silver", "Bronze")
    val reversedLongUppercaseMedals: List<String> =
        medals
            .map { it.uppercase() }
            .filter { it.length > 4 }
            .reversed()
    println(reversedLongUppercaseMedals)
    // [BRONZE, SILVER]
}
```
{kotlin-runnable="true" id="kotlin-tour-scope-function-also-before"}

该示例：

* 创建了 `medals` 变量，包含一个字符串列表。
* 创建了 `reversedLongUpperCaseMedals` 变量，类型为 `List<String>`。
* 在 `medals` 变量上使用了 `.map()` 扩展函数。
* 将一个 Lambda 表达式传入 `.map()` 函数，在其中通过 `it` 关键字引用 `medals` 并调用 `.uppercase()` 扩展函数。
* 在 `medals` 变量上使用了 `.filter()` 扩展函数。
* 将一个 Lambda 表达式作为谓词传入 `.filter()` 函数，通过 `it` 引用 `medals` 并检查列表中的项是否超过 4 个字符。
* 使用 `.reversed()` 扩展函数对结果进行反转。
* 将最终结果赋值给 `reversedLongUpperCaseMedals` 变量。
* 打印 `reversedLongUpperCaseMedals` 变量中的列表。

在函数调用之间添加一些日志以观察 `medals` 变量的变化会很有用，`also` 函数可以帮助实现这一点：

```kotlin
fun main() {
    val medals: List<String> = listOf("Gold", "Silver", "Bronze")
    val reversedLongUppercaseMedals: List<String> =
        medals
            .map { it.uppercase() }
            .also { println(it) }
            // [GOLD, SILVER, BRONZE]
            .filter { it.length > 4 }
            .also { println(it) }
            // [SILVER, BRONZE]
            .reversed()
    println(reversedLongUppercaseMedals)
    // [BRONZE, SILVER]
}
```
{kotlin-runnable="true" id="kotlin-tour-scope-function-also-after"}

现在该示例：

* 在 `medals` 变量上使用了 `also` 作用域函数。
* 在 `also` 作用域函数内部创建了一个临时作用域，因此在将 `medals` 变量作为函数参数使用时无需显式引用它。
* 将一个 Lambda 表达式传入 `also` 作用域函数，在其中通过 `it` 关键字将 `medals` 变量作为参数调用 `println()` 函数。

由于 `also` 函数会返回对象，因此它不仅适用于日志记录，还适用于调试、链式调用多个操作，以及执行不会影响代码主流程的其他副作用操作。

### With

与其他作用域函数不同，`with` 不是扩展函数，因此语法有所不同。你需要将接收者对象作为参数传入 `with`。

当你希望对一个对象调用多个函数时，可以使用 `with` 作用域函数。

请看下面的示例：

```kotlin
class Canvas {
    fun rect(x: Int, y: Int, w: Int, h: Int): Unit = println("$x, $y, $w, $h")
    fun circ(x: Int, y: Int, rad: Int): Unit = println("$x, $y, $rad")
    fun text(x: Int, y: Int, str: String): Unit = println("$x, $y, $str")
}

fun main() {
    val mainMonitorPrimaryBufferBackedCanvas = Canvas()

    mainMonitorPrimaryBufferBackedCanvas.text(10, 10, "Foo")
    mainMonitorPrimaryBufferBackedCanvas.rect(20, 30, 100, 50)
    mainMonitorPrimaryBufferBackedCanvas.circ(40, 60, 25)
    mainMonitorPrimaryBufferBackedCanvas.text(15, 45, "Hello")
    mainMonitorPrimaryBufferBackedCanvas.rect(70, 80, 150, 100)
    mainMonitorPrimaryBufferBackedCanvas.circ(90, 110, 40)
    mainMonitorPrimaryBufferBackedCanvas.text(35, 55, "World")
    mainMonitorPrimaryBufferBackedCanvas.rect(120, 140, 200, 75)
    mainMonitorPrimaryBufferBackedCanvas.circ(160, 180, 55)
    mainMonitorPrimaryBufferBackedCanvas.text(50, 70, "Kotlin")
}
```
{kotlin-runnable="true" id="kotlin-tour-scope-function-with-before"}

该示例创建了一个 `Canvas` 类，它包含三个成员函数：`rect()`、`circ()` 和 `text()`。
每个成员函数都会根据提供的函数参数打印一条语句。

示例在调用这些成员函数之前，创建了 `mainMonitorPrimaryBufferBackedCanvas` 作为 `Canvas`
类的实例，并在该实例上依次调用多个成员函数，每次使用不同的函数参数。

你可以看到，这段代码可读性较差。如果使用 `with` 函数，代码会更加简洁明了：

```kotlin
class Canvas {
    fun rect(x: Int, y: Int, w: Int, h: Int): Unit = println("$x, $y, $w, $h")
    fun circ(x: Int, y: Int, rad: Int): Unit = println("$x, $y, $rad")
    fun text(x: Int, y: Int, str: String): Unit = println("$x, $y, $str")
}

fun main() {
    //sampleStart
    val mainMonitorSecondaryBufferBackedCanvas = Canvas()
    with(mainMonitorSecondaryBufferBackedCanvas) {
        text(10, 10, "Foo")
        rect(20, 30, 100, 50)
        circ(40, 60, 25)
        text(15, 45, "Hello")
        rect(70, 80, 150, 100)
        circ(90, 110, 40)
        text(35, 55, "World")
        rect(120, 140, 200, 75)
        circ(160, 180, 55)
        text(50, 70, "Kotlin")
    }
    //sampleEnd
}
```
{kotlin-runnable="true" id="kotlin-tour-scope-function-with-after"}

该示例：
* 使用 `with` 作用域函数，并将 `mainMonitorSecondaryBufferBackedCanvas` 实例作为接收者。
* 在 `with` 作用域函数内部创建了一个临时作用域，因此在调用成员函数时无需显式引用 `mainMonitorSecondaryBufferBackedCanvas` 实例。
* 将一个 Lambda 表达式传入 `with` 作用域函数，在其中依次调用多个成员函数，并使用不同的函数参数。

现在，这段代码可读性大大提高，你也就不太可能出错了。

## 使用场景概览 {id=use-case-overview}

本节介绍了 Kotlin 中可用的不同作用域函数及其主要使用场景，帮助你编写更加 idiomatic 的代码。
你可以将下表作为快速参考。
需要注意的是，使用这些函数时，并不需要完全理解它们的内部工作原理。

| 函数      | 通过 `x` 访问 | 返回值       | 使用场景                         |
|---------|-----------|-----------|------------------------------|
| `let`   | `it`      | Lambda 结果 | 在代码中执行空值检查，并在之后对返回对象进行进一步操作。 |
| `apply` | `this`    | `x`       | 在对象创建时进行初始化。                 |
| `run`   | `this`    | Lambda 结果 | 在对象创建时进行初始化 **并** 计算一个结果。    |
| `also`  | `it`      | `x`       | 在返回对象之前完成额外操作。               |
| `with`  | `this`    | Lambda 结果 | 对一个对象调用多个函数。                 |

有关作用域函数的更多信息，请参阅 [作用域函数](scope-functions.md)。

## 练习 {id=practice}

### 练习 1 {initial-collapse-state="collapsed" collapsible="true" id="scope-functions-exercise-1"}

将 `.getPriceInEuros()` 函数重写为 单表达式函数，使用安全调用操作符 `?.` 和 `let` 作用域函数。

<deflist collapsible="true">
    <def title="提示">
        使用安全调用操作符 <code>?.</code> 安全地访问 <code>getProductInfo()</code> 函数中的 <code>priceInDollars</code> 属性。  
        然后，使用 <code>let</code> 作用域函数将 <code>priceInDollars</code> 的值转换为欧元。
    </def>
</deflist>

|---|---|
```kotlin
data class ProductInfo(val priceInDollars: Double?)

class Product {
    fun getProductInfo(): ProductInfo? {
        return ProductInfo(100.0)
    }
}

// 重写这个函数
fun Product.getPriceInEuros(): Double? {
    val info = getProductInfo()
    if (info == null) return null
    val price = info.priceInDollars
    if (price == null) return null
    return convertToEuros(price)
}

fun convertToEuros(dollars: Double): Double {
    return dollars * 0.85
}

fun main() {
    val product = Product()
    val priceInEuros = product.getPriceInEuros()

    if (priceInEuros != null) {
        println("Price in Euros: €$priceInEuros")
        // Price in Euros: €85.0
    } else {
        println("Price information is not available.")
    }
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-scope-functions-exercise-1"}

|---|---|
```kotlin
data class ProductInfo(val priceInDollars: Double?)

class Product {
    fun getProductInfo(): ProductInfo? {
        return ProductInfo(100.0)
    }
}

fun Product.getPriceInEuros() = getProductInfo()?.priceInDollars?.let { convertToEuros(it) }

fun convertToEuros(dollars: Double): Double {
    return dollars * 0.85
}

fun main() {
    val product = Product()
    val priceInEuros = product.getPriceInEuros()

    if (priceInEuros != null) {
        println("Price in Euros: €$priceInEuros")
        // Price in Euros: €85.0
    } else {
        println("Price information is not available.")
    }
}
```
{initial-collapse-state="collapsed" collapsible="true" collapsed-title="Example solution" id="kotlin-tour-scope-functions-solution-1"}

### 练习 2 {initial-collapse-state="collapsed" collapsible="true" id="scope-functions-exercise-2"}

你有一个 `updateEmail()` 函数，用于更新用户的电子邮件地址。
使用 `apply` 作用域函数更新电子邮件地址，然后使用 `also` 作用域函数打印日志信息：`Updating email for user with ID: ${it.id}`。

|---|---|
```kotlin
data class User(val id: Int, var email: String)

fun updateEmail(user: User, newEmail: String): User = // Write your code here

fun main() {
    val user = User(1, "old_email@example.com")
    val updatedUser = updateEmail(user, "new_email@example.com")
    // Updating email for user with ID: 1

    println("Updated User: $updatedUser")
    // Updated User: User(id=1, email=new_email@example.com)
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-scope-functions-exercise-2"}

|---|---|
```kotlin
data class User(val id: Int, var email: String)

fun updateEmail(user: User, newEmail: String): User = user.apply {
    this.email = newEmail
}.also { println("Updating email for user with ID: ${it.id}") }

fun main() {
    val user = User(1, "old_email@example.com")
    val updatedUser = updateEmail(user, "new_email@example.com")
    // Updating email for user with ID: 1

    println("Updated User: $updatedUser")
    // Updated User: User(id=1, email=new_email@example.com)
}
```
{initial-collapse-state="collapsed" collapsible="true" collapsed-title="Example solution" id="kotlin-tour-scope-functions-solution-2"}

## 下一步

[中级：带接收者的 Lambda 表达式](kotlin-tour-intermediate-lambdas-receiver.md)
