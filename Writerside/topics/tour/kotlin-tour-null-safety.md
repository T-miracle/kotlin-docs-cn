[//]: # (title: 空值安全)

<p><img src="icon-1-done.svg" width="20" alt="First step" /> <a href="kotlin-tour-hello-world.md">Hello world</a><br />
    <img src="icon-2-done.svg" width="20" alt="Second step" /> <a href="kotlin-tour-basic-types.md">基本类型</a><br />
    <img src="icon-3-done.svg" width="20" alt="Third step" /> <a href="kotlin-tour-collections.md">集合</a><br />
    <img src="icon-4-done.svg" width="20" alt="Fourth step" /> <a href="kotlin-tour-control-flow.md">控制流</a><br />
    <img src="icon-5-done.svg" width="20" alt="Fifth step" /> <a href="kotlin-tour-functions.md">函数</a><br />
    <img src="icon-6-done.svg" width="20" alt="Sixth step" /> <a href="kotlin-tour-classes.md">类</a><br />
    <img src="icon-7.svg" width="20" alt="Final step" /> <strong>空值安全</strong><br /></p>

在 Kotlin 中，变量可以拥有 `null` 值。
为了帮助防止程序中出现 `null` 值引发的问题，Kotlin 实施了空安全。
空安全在编译时检测潜在的 `null` 值问题，而不是在运行时。

空安全是一组功能的组合，允许你：
* 明确声明在程序中何时允许 `null` 值。
* 检查 `null` 值。
* 对可能包含 `null` 值的属性或函数使用安全调用。
* 声明在检测到 `null` 值时要执行的操作。

## 可空类型

Kotlin 支持可空类型，允许声明的类型具有 `null` 值的可能性。
默认情况下，类型**不允许**接受 `null` 值。
可空类型通过在类型声明后明确添加 `?` 来声明。

例如：

```kotlin
fun main() {
    // neverNull 具有 String 类型
    var neverNull: String = "This can't be null"

    // 引发编译器错误
    neverNull = null

    // nullable 具有可为 null 的 String 类型
    var nullable: String? = "You can keep a null here"

    // 这是可行的  
    nullable = null

    // 默认情况下，不接受空值
    var inferredNonNull = "The compiler assumes non-nullable"

    // 引发编译器错误
    inferredNonNull = null

    // notNull 不接受空值
    fun strLength(notNull: String): Int {                 
        return notNull.length
    }

    println(strLength(neverNull)) // 18
    println(strLength(nullable))  // Throws a compiler error
}
```
{kotlin-runnable="true" validate="false" kotlin-min-compiler-version="1.3" id="kotlin-tour-nullable-type"}
[打开训练场>>>](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6mG1A9DRmjAG4wBOAcigDZcYAWOAM4YAygBdWBAOYYxATwAOMStWpMcrBszace8UROkYAvBnIgAKnzzCwONAHIxGAEYwG3LucorVdDFasEADuwjgYkFgKeFxsGGxBrL7UjCwcniYePD5UtPRonjgusfxCWVxFJeKSaDLySskY6poFPJUw%2BtXSAPyZ5gCaECgR9hgA1jAwChjhrbx8bMogOaoY%2FlY2GJsA8gDS1I1z7ZlzK370AEJyGLBIONxiADTlTThcKDBhrDCOzjhgYCmYhgUEazS2aCQCRB7AgaF0vFM5is7ki0VimiEghQWE%2BDDhAFojsUlmc8gE%2BEFQjMIhAojE4gkIElchCoaxvlBYfCMqZTmhGv40BAxAibhBPr8ZgCgS91O9Po1UJhBBIADI%2FKRiPiEYWizydQy1Yj6ACSaGcwFW1ptB1Zq2%2BYhQrEweoRADpYrVtY0AL5kjAKGpiLhEVWsDXenWpHSeYikfwARgAHI0gwQQ2H1ZrtbrCiT4%2BTAiEwrT6Rj4hzmZRfSBHiAxBopDAxAAFCpiJDMrAIEAAKxw6jr4Dp6LYADU2II8HDe4n3QBOd2JgAMIF9QA?_gl=1*18vlkwc*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTg4NzE1MC4yMS4xLjE2OTk4ODg4NDkuNTkuMC4w&_ga=2.187130152.1684546783.1699700997-2060274793.1694940376)

>`length` 是 [String](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-string/) 类的一个属性，包含字符串中的字符数。
>
{type="tip"}

## 检查空值

你可以在条件表达式中检查 `null` 值的存在。
在下面的例子中，`describeString()` 函数有一个 `if` 语句，检查 `maybeString` 是否**不是** `null`，并且其 `length` 是否大于零：

```kotlin
fun describeString(maybeString: String?): String {
    if (maybeString != null && maybeString.length > 0) {
        return "String of length ${maybeString.length}"
    } else {
        return "Empty or null string"
    }
}

fun main() {
    var nullString: String? = null
    println(describeString(nullString))
    // Empty or null string
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-check-nulls"}
[打开训练场>>>](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAlgZzAJwEsAjGAZQBdC0BzACgFsBDATxIquvg3YJoH4AlFx40MwADqYM0gkgwMWbSr2oYAhAF4MaFABtdGAGSGMTVmWU0AdLpg1yACwwA%2BDAAYBYydJ8%2B8MchQ8THEQEVUIOVt7JwASYDMlDhs7akcAX1DvH3SMGF1sGC8pX2l%2FQOCMUIBRegAHcmYMCDxtPQNsS2oskoxMtH7JVEwmXlpPCV6AN0ZWnX1w4S6%2BDC153WzpOqpyXTRaHHxiCw5adfCBAU2MAHobjFqGppa2%2FQxOjkl0kAAaEHJZtQAgAFXSMchIFr0BAgABWjBmv3AEHqBFseAAajA8NgCBA0DCAIxWACcVkJbhA6SAA%3D%3D?_gl=1*18vlkwc*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTg4NzE1MC4yMS4xLjE2OTk4ODg4NDkuNTkuMC4w&_ga=2.187130152.1684546783.1699700997-2060274793.1694940376)

## 使用安全调用

为了安全地访问可能包含 `null` 值的对象的属性，可以使用安全调用运算符 `?.`。
安全调用运算符在对象的属性为 `null` 时返回 `null`。这在你想要避免 `null` 值触发代码错误的情况下非常有用。

在下面的例子中，`lengthString()` 函数使用安全调用来返回字符串的长度或 `null`：

```kotlin
fun lengthString(maybeString: String?): Int? = maybeString?.length

fun main() { 
    var nullString: String? = null
    println(lengthString(nullString))
    // null
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-safe-call-property"}
[打开训练场>>>](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgDYzQcwBcALAZQICcBLfACgFsBDATwCMYyr94MPq8B%2BAJTcAkmgL8MAXgyNW7Cn34A6HPmIAdNFtSZG1GoIzAMWjOYwA3BuQxoUWLLy49F%2BSTPuOzFgA6cCLDQaNUJSNzwaLycIwUEfcwB6RLsHLC0AXxAAGhACGzwYAgAFLAYCJAhyOgQQACsGaxzwCDpfShxyADUYcgBnSgg0WoBGZQBOZRGABhAMoA?_gl=1*18vlkwc*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTg4NzE1MC4yMS4xLjE2OTk4ODg4NDkuNTkuMC4w&_ga=2.187130152.1684546783.1699700997-2060274793.1694940376)

>安全调用可以链接在一起，这样如果对象的任何属性包含 `null` 值，那么就会返回 `null` 而不会抛出错误。例如：
>```kotlin
>person.company?.address?.country
>```
>
{style="note"}

安全调用运算符也可以用于安全调用扩展或成员函数。
在这种情况下，函数调用之前会执行空检查。
如果检查发现 `null` 值，那么调用将被跳过并返回 `null`。

在下面的例子中，`nullString` 为 `null`，因此
[`.uppercase()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/uppercase.html) 的调用被跳过，返回 `null`：

```kotlin
fun main() {
    var nullString: String? = null
    println(nullString?.uppercase())
    // null
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-safe-call-function"}
[打开训练场>>>](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6mG1AbjgE4ZooA2LAygC70EDm8GLjzS8A%2FBgC8TVi0rVqAB2GcWRZmyF9RAOhQKFMemBwBnGCWJz5AemvS2lAL4gANCE4NeMTgAUWOTiQIeiwEEAArHDpXcAgsBTwWQwA1QxM8CDQwgEZtAE5tbIAGEEcgA%3D%3D?_gl=1*entc14*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTg4NzE1MC4yMS4xLjE2OTk4ODg4NDkuNTkuMC4w&_ga=2.154528184.1684546783.1699700997-2060274793.1694940376)

## 使用 Elvis 运算符

可以使用 **Elvis 运算符** `?:` 在检测到 `null` 值时提供一个默认值。

在 Elvis 运算符的左侧写下应检查 `null` 值的内容。
在 Elvis 运算符的右侧写下在检测到 `null` 值时应返回的内容。

在下面的例子中，`nullString` 为 `null`，因此安全调用访问 `length` 属性返回 `null` 值。
因此，Elvis 运算符返回 `0`：

```kotlin
fun main() {
    var nullString: String? = null
    println(nullString?.length ?: 0)
    // 0
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-elvis-operator"}
[打开训练场>>>](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6mG1AbjgE4ZooA2LAygC70EDm8GLjzS8A%2FBgC8TVi0rVqAB2GcWRZmyF9RAOhQKFMemBwBnGCWJz5AemvS2lAL4gANCE4NeMTgAUWOTiQIeiwEEAArHDpXcAgsBTwWQwA1QxM8CDQwgEZtAE5tbIAGEEcgA%3D%3D?_gl=1*entc14*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTg4NzE1MC4yMS4xLjE2OTk4ODg4NDkuNTkuMC4w&_ga=2.154528184.1684546783.1699700997-2060274793.1694940376)

有关 Kotlin 中空安全的更多信息，请参阅 [空值安全](null-safety.md)。

## 练习

### 练习 {collapsible="true"}

你有一个名为 `employeeById` 的函数，该函数允许你访问公司员工的数据库。
不幸的是，该函数返回一个 `Employee?` 类型的值，因此结果可能是 `null`。
你的目标是编写一个函数，当提供员工的 `id` 时返回其薪水，如果员工在数据库中不存在，则返回 `0`。

|---|---|
```kotlin
data class Employee (val name: String, var salary: Int)

fun employeeById(id: Int) = when(id) {
    1 -> Employee("Mary", 20)
    2 -> null
    3 -> Employee("John", 21)
    4 -> Employee("Ann", 23)
    else -> null
}

fun salaryById(id: Int) = // 在这里写下你的代码
    
fun main() { 
    println((1..5).sumOf { id -> salaryById(id) })
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-null-safety-exercise"}
[打开训练场>>>](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIoEMAuyAEYA2yDOeGAogLYAO2EAnjDBgBQBuy2GAdsiTPBgMqoAnAJZsA5gBoMzARjwtkAqjwCSbVAEoAOm20AzAK5sMMcpRowAQlWVR6QqCrXqMAXgwB3ABYw2dqM%2BBtDGCMAEYMAFoAPmJTalp6TRAAWQUqJMkAJgAGLSMQzMiYtn1sbCCQgGYi2Ip4mESQACkITx0QLNC8kIwAFhrSOvNGgEE2dqzK7pCYbDw6aPZS8rYAX209Q1l5RSsbP0cNVwwAehOMAHVhVDoqCH0ZSFgMbwEYCuDNoxJkEXoAjAfDBkYRqbC%2BeihAB0UIArOooXh9CQAPK6DDADD2GpyXC7ay2ezOVZ5VYdEDoASiGCoAAKuFQuggAhICBAACtkMxyZByEJsDABAA1QV4IQQNhs6EATihoWyIFWQA%3D?_gl=1*entc14*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTg4NzE1MC4yMS4xLjE2OTk4ODg4NDkuNTkuMC4w&_ga=2.154528184.1684546783.1699700997-2060274793.1694940376)

|---|---|
```kotlin
data class Employee (val name: String, var salary: Int)

fun employeeById(id: Int) = when(id) {
    1 -> Employee("Mary", 20)
    2 -> null
    3 -> Employee("John", 21)
    4 -> Employee("Ann", 23)
    else -> null
}

fun salaryById(id: Int) = employeeById(id)?.salary ?: 0

fun main() {
 println((1..5).sumOf { id -> salaryById(id) })
}
```
{collapsible="true" collapsed-title="Example solution" id="kotlin-tour-null-safety-solution"}

## 下一步是什么？

恭喜！现在你已经完成了 Kotlin 导览，可以查看我们为流行的 Kotlin 应用程序提供的教程：
* [创建后端应用程序](jvm-create-project-with-spring-boot.md)
* [创建适用于 Android 和 iOS 的跨平台应用程序](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-getting-started.html)