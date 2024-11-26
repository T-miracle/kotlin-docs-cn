[//]: # (title: 空值安全)

<no-index/>

<tldr>
    <p><img src="icon-1-done.svg" width="20" alt="第一步" /> <a href="kotlin-tour-hello-world.md">Hello world</a><br />
        <img src="icon-2-done.svg" width="20" alt="第二步" /> <a href="kotlin-tour-basic-types.md">基本类型</a><br />
        <img src="icon-3-done.svg" width="20" alt="第三步" /> <a href="kotlin-tour-collections.md">集合</a><br />
        <img src="icon-4-done.svg" width="20" alt="第四步" /> <a href="kotlin-tour-control-flow.md">控制流</a><br />
        <img src="icon-5-done.svg" width="20" alt="第五步" /> <a href="kotlin-tour-functions.md">函数</a><br />
        <img src="icon-6-done.svg" width="20" alt="第六步" /> <a href="kotlin-tour-classes.md">类</a><br />
        <img src="icon-7.svg" width="20" alt="最后一步" /> <strong>空值安全</strong><br /></p>
</tldr>

在 Kotlin 中，`null` 值是有可能出现的。当某些东西缺失或尚未设置时，Kotlin 就会使用 `null` 值。
你已经在 [集合](kotlin-tour-collections.md#kotlin-tour-map-no-key)
章节中看到过 Kotlin 返回 `null`值的例子，当你尝试访问一个在映射中不存在的键值对时。
虽然以这种方式使用 `null` 值很有用，但如果你的代码没有准备好处理这些值，你可能会遇到问题。

为了帮助防止程序中出现 `null` 值问题，Kotlin 提供了 `null` 安全性。
`null` 安全性在编译时检测潜在的 `null` 值问题，而不是在运行时。

`null` 安全性是一组功能的组合，它允许你：

* 明确声明在程序中何时允许 `null` 值。
* 检查 `null` 值。
* 使用安全调用来访问可能包含 `null` 值的属性或方法。
* 声明在检测到 `null` 值时要采取的操作。

## 可空类型 {id=nullable-types}

Kotlin 支持可空类型，这允许声明的类型具有 `null` 值。
默认情况下，类型 **不** 允许接受 `null` 值。可空类型通过在类型声明后显式添加 `?` 来声明。

例如：

```kotlin
fun main() {
    // neverNull 具有 String 类型
    var neverNull: String = "This can't be null"

    // 会抛出编译错误
    neverNull = null

    // nullable 具有可空 String 类型
    var nullable: String? = "You can keep a null here"

    // 这没问题
    nullable = null

    // 默认情况下，null 值是不被接受的
    var inferredNonNull = "The compiler assumes non-nullable"

    // 会抛出编译错误
    inferredNonNull = null

    // notNull 不接受 null 值
    fun strLength(notNull: String): Int {                 
        return notNull.length
    }

    println(strLength(neverNull)) // 18
    println(strLength(nullable))  // 会抛出编译错误
}
```

> `length` 是 [String](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-string/)
> 类的一个属性，表示字符串中字符的数量。
>
{style="tip"}

## 检查 `null` 值 {id=check-for-null-values}

你可以在条件表达式中检查 `null` 值的存在。在下面的例子中，`describeString()`
函数有一个 `if` 语句，它检查 `maybeString` 是否 **不** 为 `null` 并且其 `length` 是否大于零：

```kotlin
fun describeString(maybeString: String?): String {
    if (maybeString != null && maybeString.length > 0) {
        return "String of length ${maybeString.length}"
    } else {
        return "Empty or null string"
    }
}

fun main() {
    val nullString: String? = null
    println(describeString(nullString))
    // Empty or null string
}
```

## 使用安全调用 {id=use-safe-calls}

要安全地访问可能包含 `null` 值的对象的属性，可以使用安全调用操作符 `?.`。
安全调用操作符会在对象或其访问的属性为 `null` 时返回 `null`。
这在你想避免 `null` 值在代码中触发错误时非常有用。

在下面的例子中，`lengthString()` 函数使用安全调用来返回字符串的长度或 `null`：

```kotlin
fun lengthString(maybeString: String?): Int? = maybeString?.length

fun main() { 
    val nullString: String? = null
    println(lengthString(nullString))
    // null
}
```

> 安全调用可以链式使用，以便如果对象的任何属性包含 `null` 值，则返回 `null` 而不会抛出错误。
> 例如：
> 
> ```kotlin
>   person.company?.address?.country
> ```
>
{style="tip"}

安全调用操作符也可以用来安全地调用扩展函数或成员函数。
在这种情况下，会在调用函数之前执行 `null` 检查。如果检查发现 `null` 值，则跳过调用并返回 `null`。

在下面的例子中，`nullString` 为 `null`，因此调用 [`.uppercase()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/uppercase.html)
被跳过，返回 `null`：

```kotlin
fun main() {
    val nullString: String? = null
    println(nullString?.uppercase())
    // null
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-safe-call-function"}

## 使用 Elvis 操作符 {id=use-elvis-operator}

你可以使用 **Elvis 操作符** `?:` 提供一个默认值，当检测到 `null` 值时返回该默认值。

在 Elvis 操作符的左侧写出要检查的 `null` 值，在右侧写出检测到 `null` 值时应返回的默认值。

在下面的例子中，`nullString` 为 `null`，因此安全调用访问 `length` 属性返回 `null` 值。
结果，Elvis 操作符返回 `0`：

```kotlin
fun main() {
    val nullString: String? = null
    println(nullString?.length ?: 0)
    // 0
}
```

有关 Kotlin 中 `null` 安全性的更多信息，请参见 [空值安全](null-safety.md)。

## 实践 {id=practice}

### 练习 {collapsible="true" collapsible="true"}

你有一个 `employeeById` 函数，它可以访问公司员工的数据库。
不幸的是，这个函数返回 `Employee?` 类型的值，因此结果可能是 `null`。
你的目标是编写一个函数，当提供员工的 `id` 时返回该员工的薪水，如果数据库中缺少该员工，则返回 `0`。

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
{collapsible="true" collapsible="true" collapsed-title="示例解答" id="kotlin-tour-null-safety-solution"}

## 下一步是什么？

恭喜！现在你已经完成了 Kotlin 之旅，之后请查看我们有关 Kotlin 常用应用的教程：

* [创建后台应用程序](jvm-create-project-with-spring-boot.md)
* [为 Android 和 iOS 创建跨平台应用程序](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-create-first-app.html)
