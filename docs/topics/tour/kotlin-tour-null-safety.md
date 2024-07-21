[//]: # (title: 空值安全)

<tldr>
    <p><img src="icon-1-done.svg" width="20" alt="First step" /> <a href="kotlin-tour-hello-world.md">Hello world</a><br />
        <img src="icon-2-done.svg" width="20" alt="Second step" /> <a href="kotlin-tour-basic-types.md">基本类型</a><br />
        <img src="icon-3-done.svg" width="20" alt="Third step" /> <a href="kotlin-tour-collections.md">集合</a><br />
        <img src="icon-4-done.svg" width="20" alt="Fourth step" /> <a href="kotlin-tour-control-flow.md">控制流</a><br />
        <img src="icon-5-done.svg" width="20" alt="Fifth step" /> <a href="kotlin-tour-functions.md">函数</a><br />
        <img src="icon-6-done.svg" width="20" alt="Sixth step" /> <a href="kotlin-tour-classes.md">类</a><br />
        <img src="icon-7.svg" width="20" alt="Final step" /> <strong>空值安全</strong><br /></p>
</tldr>

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

>`length` 是 [String](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-string/) 类的一个属性，包含字符串中的字符数。
>
{style="tip"}

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