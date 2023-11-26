[//]: # (title: 函数)

<p>
    <img src="icon-1-done.svg" width="20" alt="First step" /> <a href="kotlin-tour-hello-world.md">Hello world</a><br />
    <img src="icon-2-done.svg" width="20" alt="Second step" /> <a href="kotlin-tour-basic-types.md">基本类型</a><br />
    <img src="icon-3-done.svg" width="20" alt="Third step" /> <a href="kotlin-tour-collections.md">集合</a><br />
    <img src="icon-4-done.svg" width="20" alt="Fourth step" /> <a href="kotlin-tour-control-flow.md">控制流</a><br />
    <img src="icon-5.svg" width="20" alt="Fifth step" /> <strong>函数</strong><br />
    <img src="icon-6-todo.svg" width="20" alt="Sixth step" /> <a href="kotlin-tour-classes.md">类</a><br />
    <img src="icon-7-todo.svg" width="20" alt="Final step" /> <a href="kotlin-tour-null-safety.md">空值安全</a>
</p>

在 Kotlin 中，你可以使用`fun`关键字声明自己的函数。

```kotlin
fun hello() {
    return println("Hello, world!")
}

fun main() {
    hello()
    // Hello, world!
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-function-demo"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgBYwDa4gAoBKDYAHUw2oCcYAXFGzABxoEs17c1DyQAEngIAaDAHcINXFACE%2FYpQC%2BlSqkwBbAIacSZStWo58RRVWoB6CxiEmxk6XOUgRIelpoBzBgAVcW%2BiQpDQQQACstADctF3AIDRZ2XBgaADUUgGd2CDRQgEYAOgBOAryABhAlIA%3D%3D?_ga=2.216501526.1684546783.1699700997-2060274793.1694940376&_gl=1*1lhz8qx*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc5MjE1My4xOS4wLjE2OTk3OTIxNTMuNjAuMC4w)

在 Kotlin 中：
* 函数参数写在括号 `()` 内。
* 每个参数都必须有一个类型，多个参数之间用逗号 `,` 分隔。
* 返回类型写在函数的括号 `()` 后面，用冒号 `:` 分隔。
* 函数体写在花括号 `{}` 内。
* 使用 `return` 关键字来退出或从函数中返回值。

> 如果一个函数不返回有用的内容，返回类型和 `return` 关键字可以省略。了解更多信息，请参阅[无返回值的函数](#无返回值的函数)。
>
{style="note"}

在以下示例中：
* `x` 和 `y` 是函数参数。
* `x` 和 `y` 的类型是 `Int`。
* 函数的返回类型是 `Int`。
* 当调用函数时，该函数返回 `x` 和 `y` 的和。

```kotlin
fun sum(x: Int, y: Int): Int {
    return x + y
}

fun main() {
    println(sum(1, 2))
    // 3
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-simple-function"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgM4oLYAoAPeDASTQBcAaDATxPIoEoHKNgAdTDHgJxgopemQhgDUdLgF8uXVJlwBDAJZp8Tdlx48ADr1UUANmpwEAjDQBMTJlu0B6exgDM0kFRAVFvAOYCACoaKFEgQvLgIIABWigBuiu7gELg6yoYwvABqGVjKEGiRZgB0AJxFZgAMIFJAA%3D%3D?_ga=2.214682007.1684546783.1699700997-2060274793.1694940376&_gl=1*p26qel*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc5MjE1My4xOS4wLjE2OTk3OTIxNTMuNjAuMC4w)

> 我们在我们的[编码规范](coding-conventions.md#function-names)中建议以小写字母开头，
> 并使用驼峰命名法，不使用下划线来命名函数。
> 
{style="note"}

## 命名参数

为了简洁的代码，在调用函数时，你并不需要包含参数名。
然而，包含参数名能够使你的代码更易读。这被称为使用**命名参数**。
如果你包含参数名，那么你可以以任意顺序编写这些参数。

> 在以下示例中，[字符串模板](strings.md#string-templates)（`$`）被用于访问参数的值，
> 将它们转换为 `String` 类型，然后将它们连接成一个字符串以便打印。


```kotlin
fun printMessageWithPrefix(message: String, prefix: String) {
    println("[$prefix] $message")
}

fun main() {
    // 使用带有交换参数顺序的命名参数
    printMessageWithPrefix(prefix = "Log", message = "Hello")
    // [Log] Hello
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-named-arguments-function"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgA4CcCWaALgLIwDOZAhgOYwDqehAFgAo4xJ4AeAFALbkqteBgDKhfGmoAabO05cR4ydQCUGYAB1MGXbgKEANmh6aQAbQAkuDtwC6GSwIo0YZ1doC%2B27akx9KAh51LR1dAHpwjABVMnIMNEoBKAxKHGoUASIyDAB3RiYMMlzKLCwYFKw0pJhCGBwMCBxYHG1dPUkSQVcGZjZbXhsFDABeDDMAGQhqM1lnIRhR8ZAACRhDQwh3NvbIjHMp6gc1ja20TxBpEEI02kIWQ0pCJCa%2BBBAAK0oAN0pL8AgfCweEM9QAavUyHgIGh3gBGAB0AE4EXCAAwgTxAA%3D%3D%3D?_ga=2.214682007.1684546783.1699700997-2060274793.1694940376&_gl=1*p26qel*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc5MjE1My4xOS4wLjE2OTk3OTIxNTMuNjAuMC4w)

## 默认参数值

你可以为函数参数定义默认值。任何具有默认值的参数在调用函数时都可以省略。
要声明默认值，请在类型之后使用赋值运算符 `=`：

```kotlin
fun printMessageWithPrefix(message: String, prefix: String = "Info") {
    println("[$prefix] $message")
}

fun main() {
    // 使用两个参数调用的函数
    printMessageWithPrefix("Hello", "Log") 
    // [Log] Hello
    
    // 仅使用消息参数调用的函数
    printMessageWithPrefix("Hello")        
    // [Info] Hello
    
    printMessageWithPrefix(prefix = "Log", message = "Hello")
    // [Log] Hello
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-default-param-function"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgA4CcCWaALgLIwDOZAhgOYwDqehAFgAo4xJ4AeAFALbkqteBgDKhfGmoAabO05cR4ydQwBeDAB0QASTRII2gJQZgmzBku4ChADZoe2gNoASXB24BdDC4EUaMMbmAL7m5qiYfJQEPCZmFpYA9IkYAGLoYIR4EJhglLa2MFAYAO6MTBgARhDM2JQ4lAKEMDhk5pZWkiSCAQzMbB682gASMAWGILLaADIQ1MZaCRjJGE6z1N6j4%2B0dO0kp6WiZ2bn5hcU5tgCepeUYfkIwdQ1NLXtyNqT%2BtH2s8tyOEBbWwTEwdcHvFZOPQGTZjEHvd7WIhfR6%2FAYKHjuBTqLQgdbaWQPAK4kbw0GQlJrOZw7ZoYKTECEeq0QgsWyUQgGHB8BAgABWlAAbpRGZA%2BFg8IUcAA1FpkE58gCMADoAJwqpUABhAwSAA%3D?_ga=2.214682007.1684546783.1699700997-2060274793.1694940376&_gl=1*p26qel*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc5MjE1My4xOS4wLjE2OTk3OTIxNTMuNjAuMC4w)

> 你可以跳过具有默认值的特定参数，而不是省略它们全部。
> 然而，在跳过第一个参数之后，你必须为所有后续的参数命名。
>
{style="note"}

## 无返回值的函数 {id=无返回值的函数}

如果你的函数不返回有用的值，那么它的返回类型是 `Unit`。
`Unit` 是一个只有一个值的类型 - `Unit`。
你不必在函数体中显式声明返回 `Unit`。
这意味着你不必使用 `return` 关键字或声明返回类型：

```kotlin
fun printMessage(message: String) {
    println(message)
    // `return Unit` 或 `return` 是可选的
}

fun main() {
    printMessage("Hello")
    // Hello
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-unit-function"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgA4CcCWaALgLIwDOZAhgOYwAUAtuVbfBgMqH5rUCUGwADqYMo3AUIAbNI2Y0YvYaNEB6FRgAGOGIRQ5MAVTR5CGjBBybtu%2FWbxlzWQnghpKk4QF9hw1JgaUBHT8QiJi3CRytHSCIAASMJKSELGKYRhqGAlJKWieIAA0IISUOLSEAAqSlIRIFgwIIABWlABulIXgEAxYeJIwOABqA2QuaI0AjAB0AJxTEwAMIJ5AA%3D?_ga=2.208562706.1684546783.1699700997-2060274793.1694940376&_gl=1*hbdkhj*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc5MjE1My4xOS4wLjE2OTk3OTIxNTMuNjAuMC4w)

## 单表达式函数

为了使你的代码更加简洁，你可以使用单表达式函数。例如，`sum()` 函数可以被缩短为：

```kotlin
fun sum(x: Int, y: Int): Int {
    return x + y
}

fun main() {
    println(sum(1, 2))
    // 3
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-simple-function-before"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgM4oLYAoAPeDASTQBcAaDATxPIoEoHKNgAdTDHgJxgopemQhgDUdLgF8uXVJlwBDAJZp8Tdlx48ADr1UUANmpwEAjDQBMTJlu0B6exgDM0kFRAVFvAOYCACoaKFEgQvLgIIABWigBuiu7gELg6yoYwvABqGVjKEGiRZgB0AJxFZgAMIFJAA%3D%3D?_ga=2.208562706.1684546783.1699700997-2060274793.1694940376&_gl=1*hbdkhj*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc5MjE1My4xOS4wLjE2OTk3OTIxNTMuNjAuMC4w)

你可以移除花括号 `{}` 并使用赋值运算符 `=` 声明函数体。由于 Kotlin 的类型推断，你还可以省略返回类型。`sum()` 函数然后变成一行：

```kotlin
fun sum(x: Int, y: Int) = x + y

fun main() {
    println(sum(1, 2))
    // 3
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-simple-function-after"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgM4oLYAoAPeDASTQBcAaDATxPIoEoMBeDQjAajoB01%2BqTLgCGASzT4WwfhjkYADgCcJFADaScBAIw0ATEyaz5AehMYAzPwC%2BIKiAoilAcxgUACmpEUkEJbgQQACsRADcRO3AIXAUxNRglADUErDEINEDtADoATiztAAYQayA%3D?_ga=2.208562706.1684546783.1699700997-2060274793.1694940376&_gl=1*hbdkhj*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc5MjE1My4xOS4wLjE2OTk3OTIxNTMuNjAuMC4w)

> 只有当你的函数没有函数体 (`{}`) 时才可以省略返回类型。除非你的函数返回类型是 `Unit`。
> 
{style="note"}

### 函数练习

### 练习 1 {collapsible="true" id="functions-exercise-1"}

编写一个名为 `circleArea` 的函数，它以整数格式的圆的半径作为参数，并输出该圆的面积。

> 在此练习中，你需要导入一个包，以便通过 `PI` 访问圆周率的值。有关导入包的更多信息，请参见[包和导入](packages.md)。
>
{type = "note"}

|---|---|
```kotlin
import kotlin.math.PI

fun circleArea() {
    // 在这里写下你的代码
}
fun main() {
    println(circleArea(2))
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-functions-exercise-1"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcICWBbADhATgFwAQGsJsAbJAOwDoUBDbACwoAUBJAHTPYDMBXM3MJJjDEYAQUwxqACgCUuYO1xLcAehW4A6piTYYuAJ4RumftD10YE9gF8uvXDXKz5i5Wm1kSZKQKEjxklIATDIyNiAANCDY1JgA5jDYjMS0nFgoCCAAVtQAbtSR4BDoSCKYAGqWAM5IEGSZAIwUAJwUDQAMINZAA%3D%3D?_ga=2.208562706.1684546783.1699700997-2060274793.1694940376&_gl=1*hbdkhj*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc5MjE1My4xOS4wLjE2OTk3OTIxNTMuNjAuMC4w)

|---|---|
```kotlin
import kotlin.math.PI

fun circleArea(radius: Int): Double {
    return PI * radius * radius
}

fun main() {
    println(circleArea(2)) // 12.566370614359172
}
```
{collapsible="true" collapsed-title="Example solution" id="kotlin-tour-functions-solution-1"}

### 练习 2 {collapsible="true" id="functions-exercise-2"}

将上一个练习中的 `circleArea` 函数改写为单表达式函数。

|---|---|
```kotlin
import kotlin.math.PI

// 在这里写下你的代码

fun main() {
    println(circleArea(2))
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-functions-exercise-2"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcICWBbADhATgFwAQGsJsAbJAOwDoUBDbACwoAUBJAHTPYHpPcB1TJNhi4AnhACumXJFi46MTDHbsAZuLK4a5ABQBKXMHa5juNALIky2sEkxhiMAIKLq2gEy7d7AL4gANCDY1JgA5jDYjMS0KlgoCCAAVtQAbtT%2B4BDoSA6YAGoKAM5IEGTxAIwUAJwUZQAMIN5AA%3D%3D%3D?_ga=2.157115963.1684546783.1699700997-2060274793.1694940376&_gl=1*14jvb15*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc5MjE1My4xOS4wLjE2OTk3OTIxNTMuNjAuMC4w)

|---|---|
```kotlin
import kotlin.math.PI

fun circleArea(radius: Int): Double = PI * radius * radius

fun main() {
    println(circleArea(2)) // 12.566370614359172
}
```
{collapsible="true" collapsed-title="Example solution" id="kotlin-tour-functions-solution-2"}

### 练习 3 {collapsible="true" id="functions-exercise-3"}

你有一个函数，将以小时、分钟和秒表示的时间间隔转换为秒数。
在大多数情况下，你只需要传递一个或两个函数参数，而其余的参数都等于 0。
通过使用默认参数值和命名参数来改进函数和调用它的代码，以使代码更易读。

|---|---|
```kotlin
fun intervalInSeconds(hours: Int, minutes: Int, seconds: Int) =
    ((hours * 60) + minutes) * 60 + seconds

fun main() {
    println(intervalInSeconds(1, 20, 15))
    println(intervalInSeconds(0, 1, 25))
    println(intervalInSeconds(2, 0, 0))
    println(intervalInSeconds(0, 10, 0))
    println(intervalInSeconds(1, 0, 1))
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-functions-exercise-3"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgJZoC4wCcA3AQwBsBJNAZRkjSgGcAKACwhQMfgytwBoMAWxwp83XnkGM6EBhL4BKDAF4AOpgxbmbDlwwAqDADYADMoDUw0eOVGzGKzPpMNG1JiEkczZcA1aWgAOBDi4ZGjMYYSklDSy8swAjIIATKaCSQCsiooBgSFhEVF4MeRUtC4sGRgpGKk5eZrBoXjF0cTl8VXMqYI15k2BGIVtkR2xFQlMzDVJA7n5LUXjpZ1xlXIzdXOLaAC%2BIPwguCQEAOYwuAAKZCS4SBAEQgggAFYkpEfgEEJBWGRCAA1QiMLByV5JAB0AE4ofMQPsgA?_ga=2.157115963.1684546783.1699700997-2060274793.1694940376&_gl=1*14jvb15*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc5MjE1My4xOS4wLjE2OTk3OTIxNTMuNjAuMC4w)

|---|---|
```kotlin
fun intervalInSeconds(hours: Int = 0, minutes: Int = 0, seconds: Int = 0) =
    ((hours * 60) + minutes) * 60 + seconds

fun main() {
    println(intervalInSeconds(1, 20, 15))
    println(intervalInSeconds(minutes = 1, seconds = 25))
    println(intervalInSeconds(hours = 2))
    println(intervalInSeconds(minutes = 10))
    println(intervalInSeconds(hours = 1, seconds = 1))
}
```
{collapsible="true" collapsed-title="Example solution" id="kotlin-tour-functions-solution-3"}

## Lambda 表达式

Kotlin 允许你通过使用 Lambda 表达式编写更加简洁的函数代码。

例如，以下的 `uppercaseString()` 函数：

```kotlin
fun uppercaseString(string: String): String {
    return string.uppercase()
}
fun main() {
    println(uppercaseString("hello"))
    // HELLO
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-lambda-function-before"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAhQB2zATmAIYDOMAygC74CWaA5gBQnV33wZW0MCUHXbDMAA6mDOPwxKKfJhbd6AOhx5CpGIx6iAvqNSYAtkTqaho8eOzdKAGzSMVBYmQENGwkAAsYNmxA88WmLiAPQhGAASAKIAMjEA8jogADQglET49FIACjZElEgQ%2BAYIIABWRABuRCngEAbYNDYEAGoEJDQQaKUAjIoAnIo9AAwg2kA?_ga=2.157115963.1684546783.1699700997-2060274793.1694940376&_gl=1*14jvb15*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc5MjE1My4xOS4wLjE2OTk3OTIxNTMuNjAuMC4w)

也可以写成一个 Lambda 表达式：

```kotlin
fun main() {
    println({ string: String -> string.uppercase() }("hello"))
    // HELLO
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-lambda-function-after"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6mG1ADgE4EAuANkcBgM6MNoDm8GAMrcCvDAFoAfJxF8AdCho0YdMDg4wSGAL6FyIABYxmzCPuLFK1agHobGABIBRADIuA8pW0gANCEY4dLwwjAAKzDiMSBB0WAggAFY4AG44vuAQWDR4zCoAaioceBBo8QCMcgCccmUADCDaQA%3D%3D%3D?_ga=2.157115963.1684546783.1699700997-2060274793.1694940376&_gl=1*14jvb15*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc5MjE1My4xOS4wLjE2OTk3OTIxNTMuNjAuMC4w)

Lambda 表达式可能一开始看起来有点难理解，所以让我们来分解它。Lambda 表达式是在花括号 `{}` 内编写的。

在 Lambda 表达式中，你需要编写：
* 参数，后跟 `->`。
* `->` 后面是函数体。

在上一个例子中：
* `string` 是一个函数参数。
* `string` 的类型是 `String`。
* 函数返回调用 `string` 上的 [`.uppercase()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/uppercase.html) 函数的结果。

> 如果你声明一个没有参数的 Lambda，那么就不需要使用 `->`。例如：
> ```kotlin
> { println("Log message") }
> ```
>
{style="note"}

Lambda 表达式可以以多种方式使用。你可以：
* [将 Lambda 分配给一个变量，然后稍后调用它](#分配给变量)
* [将 Lambda 表达式作为参数传递给另一个函数](#传递给另一个函数)
* [从函数中返回 Lambda 表达式](#从函数返回)
* [单独调用一个 Lambda 表达式](#单独调用)

### 分配给变量 {id="分配给变量"}

要将 Lambda 表达式分配给变量，使用赋值运算符 `=`：

```kotlin
fun main() {
    val upperCaseString = { string: String -> string.uppercase() }
    println(upperCaseString("hello"))
    // HELLO
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-lambda-variable"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6mG1AbjgDYYoAOzMATgMI4DOMAygBd2BAOYYAvGQw9hY%2BBiEi04gLQA%2BGXJUA6Fm3ZheMEhgC%2BlatWbLB9Ivo7c%2BSsYXIgAFjHr0IH4mJLKwB6EIwACQBRABkYgHlKMxAAGhBBHHZRGEEABXocQSQIdiwEEAArHDpU8AgsZjx6DgA1Dh48CDRygEYdAE4dHoAGEDMgA%3D%3D?_ga=2.245261701.1684546783.1699700997-2060274793.1694940376&_gl=1*1wacgub*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc5MjE1My4xOS4wLjE2OTk3OTIxNTMuNjAuMC4w)

### 传递给另一个函数 {id=传递给另一个函数}

在将 Lambda 表达式作为参数传递给函数时非常有用的一个例子是在集合上使用 [`.filter()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter.html) 函数：

```kotlin
fun main() {
    val numbers = listOf(1, -2, 3, -4, 5, -6)
    val positives = numbers.filter { x -> x > 0 }
    val negatives = numbers.filter { x -> x < 0 }
    println(positives)
    // [1, 3, 5]
    println(negatives)
    // [-2, -4, -6]
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-lambda-filter"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6mG1l11AbjgDYZopYBGMATgM4YBeDEzy8ALgHkkhAIwAaDAFoATAoDMCxQBYFAVk0A2YrTqMWABwi88YvPRj8hbTj14A6JHiZieZDAA8lAD4AjBCABgwAXxMGZlYYAHMcW3tHVnYuPg8vH24%2FQMUQwIAeDEiYqmpzbgIxJiJLa1SHYyqMAHoOjABteQwNDF0AXViMGrqGwjQklLtWsa7elU0dJQNR9sookDkQMRxuRJgxAAUmFKQIbiwEEAArHEZd8Agscy8eADVXPAg0O4yNwATjcMnCICiQA%3D%3D%3D?_ga=2.245261701.1684546783.1699700997-2060274793.1694940376&_gl=1*1wacgub*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc5MjE1My4xOS4wLjE2OTk3OTIxNTMuNjAuMC4w)

`.filter()` 函数接受一个 Lambda 表达式作为谓词：
* `{ x -> x > 0 }` 获取列表的每个元素，仅返回正数。
* `{ x -> x < 0 }` 获取列表的每个元素，仅返回负数。

> 如果 Lambda 表达式是唯一的函数参数，你可以省略函数括号 `()`。
> 这是一个 [尾随 Lambda](#尾随-Lambda) 的例子，将在本章末尾更详细地讨论。
>
{type = "note"}

另一个很好的例子是使用 [`.map()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map.html) 函数来转换集合中的项：

```kotlin
fun main() {
    val numbers = listOf(1, -2, 3, -4, 5, -6)
    val doubled = numbers.map { x -> x * 2 }
    val tripled = numbers.map { x -> x * 3 }
    println(doubled)
    // [2, -4, 6, -8, 10, -12]
    println(tripled)
    // [3, -6, 9, -12, 15, -18]
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-lambda-map"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6mG1l11AbjgDYZopYBGMATgM4YBeDEzy8ALgHkkhAIwAaDAFoATAoDMCxQBYFAVk0A2YrTqMWUCCg5MYUQa3Zc%2BAOlwAHMhgAeSgHzeMACoMZQwAXxMGZgwxbjw3GzshNk4eXlccD2AAxX8fYLVwyIw3OLQxJiILK0TjKmoAegaMAG1VJR0MA00ADgUZAAZNGWUAXWLSggqiWPja4qbWjSVujABOYfaZfSUZHvH6jEowkDkQMRxuAHMYMQAFJhwxJAhuLAQQACscRlPwCCwbjwNm4ADU0ngIGgPjJnGtnIMQGEgA%3D?_ga=2.245261701.1684546783.1699700997-2060274793.1694940376&_gl=1*1wacgub*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc5MjE1My4xOS4wLjE2OTk3OTIxNTMuNjAuMC4w)

`.map()` 函数接受一个 Lambda 表达式作为转换函数：
* `{ x -> x * 2 }` 获取列表的每个元素，返回该元素乘以 2 的结果。
* `{ x -> x * 3 }` 获取列表的每个元素，返回该元素乘以 3 的结果。

### 函数类型

在你能够从函数中返回 Lambda 表达式之前，你首先需要了解**函数类型**。

你已经学到了关于基本类型的知识，但函数本身也有一种类型。
Kotlin 的类型推断可以从参数类型中推断出函数的类型。
但有时你可能需要显式指定函数类型。
编译器需要函数类型，以便知道对于该函数什么是允许的，什么是不允许的。

函数类型的语法为：
* 每个参数的类型写在括号 `()` 内，用逗号 `,` 分隔。
* 返回类型写在 `->` 后面。

例如：`(String) -> String` 或 `(Int, Int) -> Int`。

如果为 `upperCaseString()` 定义了一个函数类型，Lambda 表达式的样子如下：

```kotlin
val upperCaseString: (String) -> String = { string -> string.uppercase() }

fun main() {
    println(upperCaseString("hello"))
    // HELLO
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-lambda-function-type"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBuBDANgAgK4AccwCcBhFAZxgGUAXAgSwDsBzeDACmrqYEoMBaAPgwcGjDAF4MwDKRoi%2BgmZ0YA6XPgJgyMVjwC%2BAHXqGAZlnoYAtigY7JhjA4w5OVNPVZrCJcsKat9IAAWMGhoEAFcXPaOAPQxGAASAKIAMikA8oa6IAA0IFQoBIwwVAAKaChUxhAEFgggAFYoqLngEBY4tGiEAGqEpLQQ9PUAjMoAnMojAAwgukA?_ga=2.220170649.1684546783.1699700997-2060274793.1694940376&_gl=1*4xplvo*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc5MjE1My4xOS4wLjE2OTk3OTIxNTMuNjAuMC4w)

如果你的 Lambda 表达式没有参数，那么括号 `()` 将保持为空。例如：`() -> Unit`

> 你必须在 Lambda 表达式或函数类型中声明参数和返回类型。否则，编译器将无法知道你的 Lambda 表达式的类型。
>
> 例如，以下的写法是错误的：
>
> `val upperCaseString = { str -> str.uppercase() }`
>
{style="note"}

### 从函数返回 {id=从函数返回}

Lambda 表达式可以从函数中返回。为了让编译器理解 Lambda 表达式返回的类型，你必须声明一个函数类型。

在以下示例中，`toSeconds()` 函数具有函数类型 `(Int) -> Int`，因为它始终返回一个 Lambda 表达式，该表达式接受一个类型为 `Int` 的参数并返回一个 `Int` 值。

此示例使用 `when` 表达式来确定在调用 `toSeconds()` 时返回哪个 Lambda 表达式：

```kotlin
fun toSeconds(time: String): (Int) -> Int = when (time) {
    "hour" -> { value -> value * 60 * 60 }
    "minute" -> { value -> value * 60 }
    "second" -> { value -> value }
    else -> { value -> value }
}

fun main() {
    val timesInMinutes = listOf(2, 10, 15, 1)
    val min2sec = toSeconds("minute")
    val totalTimeInSeconds = timesInMinutes.map(min2sec).sum()
    println("Total time is $totalTimeInSeconds secs")
    // Total time is 1680 secs
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-lambda-return-from-function"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgC4QMo0jSgGcAKLASwFsZ4NcsAnCtAcwEo7SBJNLdjAFoAfBl5YMAXgwB3ABYxM5ajAHAAOpgzb1IORBSNdQ0cAwA3AIYAbFDBMWbdjACoMANgAMrj94C%2Bmto6IFQsKFgwxiIYZla29tFxzm5eGAFawcQEEERRpo7xDkn26UEYMNZZDrFOCaLFaZrpmqiYVJYspGqBQXHYKsS8ALJhEcRSGNYUxFgA8kikAEwANBgAjJ6rawCsW%2Bw92n2haItZYBM4%2BIQkpLrH4ZEg%2BxkF2BBYNgAqKrxXOSQXAbDUYwYgAOnaAAdSMdTgR2GDiCgqF0DhhIcw%2BNY0LcQJ93jZ%2BjQMNMMAASHAfazfGi%2FbJEcZnYi6Z5lAD0bIw%2BKpRPspLW7gAHN4mU0QMsQB9GKwYFgAArWSxYJAQRhUBAgABWlis4vAECokIo1hgjAAaqbiBQchq1mCAJxgjYgPxAA?_ga=2.220170649.1684546783.1699700997-2060274793.1694940376&_gl=1*4xplvo*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc5MjE1My4xOS4wLjE2OTk3OTIxNTMuNjAuMC4w)

### 单独调用 {id=单独调用}

Lambda 表达式可以通过在花括号 `{}` 后添加括号 `()` 并在括号内包含任何参数来单独调用：

```kotlin
fun main() {
    println({ string: String -> string.uppercase() }("hello"))
    // HELLO
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-lambda-standalone"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6mG1l11ADgE4EAuANkcBgM4vNoBzeBgDKfAgIwBaAHw9xggHQp69GIzA5uMEhgC%2BhciAAWMNmwhHixWnQD0djAAkAogBk3AeVs00ekAA0ICw4jAIwLAAKbDgsSBCMWAggAFY4AG44geAQWPR4bOoAaurceBBoyQCMigCcilUADCB6QA%3D%3D?_ga=2.220170649.1684546783.1699700997-2060274793.1694940376&_gl=1*4xplvo*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc5MjE1My4xOS4wLjE2OTk3OTIxNTMuNjAuMC4w)

### 尾随 Lambda {id="尾随-Lambda"}

正如你已经看到的，如果 Lambda 表达式是唯一的函数参数，你可以省略函数括号 `()`。
如果 Lambda 表达式作为函数的最后一个参数传递，那么该表达式可以写在函数括号 `()` 外部。
在这两种情况下，这种语法被称为**尾随 Lambda**。

例如，[`.fold()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.sequences/fold.html) 函数接受一个初始值和一个操作：

```kotlin
fun main() {
    // 初始值为零。
    // 该操作将初始值与列表中的每个项目相加。
    println(listOf(1, 2, 3).fold(0, { x, item -> x + item })) // 6

    // 或者，以尾随 lambda 的形式
    println(listOf(1, 2, 3).fold(0) { x, item -> x + item })  // 6
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-trailing-lambda"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6mG1l11A9PRgCoAWMGBeALnjgDYYAbgJQc8AZwwAvGACcIAOgy06jFuwwQADvJy8ImCSixTumrrwHDRHAO49WGGEPkBPTtxhZOmcx35JbgwwExR%2BfTxXfjdFVWptOQJufiJAiW4AeSRCAEYAGgwAJkKAZmJFJAh%2BKEIABkLgDAAPQp5vDABaAD4WjABqTw6AX2JSdQA2SniMdQBBfi85NEjotza%2FTSq5HwgkDBwMbjl8QLQAcwwIrAAjKBwZxOTUwnSsnILisoqqmvrSJqtIY%2BHp9QbtHyjBhMKZUGhoYYgfIgbg4OTnGDcAAKEW42ywCBAACscCIkeAIFhtHh%2BPIAGryCR4QyE3KKACcilydRAwyAA%3D%3D?_ga=2.220170649.1684546783.1699700997-2060274793.1694940376&_gl=1*4xplvo*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc5MjE1My4xOS4wLjE2OTk3OTIxNTMuNjAuMC4w)

有关 Lambda 表达式的更多信息，请参见[Lambda 表达式和匿名函数](lambdas.md#lambda-expressions-and-anonymous-functions)。

我们接下来要学习的是 Kotlin 中的[类](kotlin-tour-classes.md)。

## Lambda 表达式练习 {id=Lambda-表达式练习}

### 练习 1 {collapsible="true" id="lambdas-exercise-1"}

你有一个 Web 服务支持的操作列表，所有请求的公共前缀，以及特定资源的 ID。
要请求在 ID 为 5 的资源上执行 `title` 操作，
你需要创建以下 URL：`https://example.com/book-info/5/title`。使用 Lambda 表达式从操作列表创建 URL 列表。

|---|---|
```kotlin
fun main() {
    val actions = listOf("title", "year", "author")
    val prefix = "https://example.com/book-info"
    val id = 5
    val urls = // 在这里写下你的代码
        println(urls)
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-lambdas-exercise-1"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6mG1AbjgDYY5gAueEaAzhgLwb15OLAPJJC5EGxb0YEgDQYJATxg4ATvMUgcKFgAsIGkMUrVaDDAAc1MJHgAevLXpYtLneAHpPMeziyWMgB0kFieAEYQEADWALQESBASpmZ0jHhQTgCsKeaMKGr03HzeGADqangsMBhKEAUYkLAYejA2uWbU1gTSRAVFJmgAviBykuoA5jAsAAr0OCyJalgIIABWOHSj4BABeDJqAGptnOxoqwCMQQCcQRcADCBDQA?_ga=2.250048134.1684546783.1699700997-2060274793.1694940376&_gl=1*wq2wgu*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc5MjE1My4xOS4wLjE2OTk3OTIxNTMuNjAuMC4w)

|---|---|
```kotlin
fun main() {
    val actions = listOf("title", "year", "author")
    val prefix = "https://example.com/book-info"
    val id = 5
    val urls = actions.map { action -> "$prefix/$id/$action" }
    println(urls)
}
```
{collapsible="true" collapsed-title="Example solution" id="kotlin-tour-lambdas-solution-1"}

### 练习 2 {collapsible="true" id="lambdas-exercise-2"}

编写一个函数，该函数接受一个 `Int` 值和一个操作（一个类型为 `() -> Unit` 的函数），然后重复执行给定次数的操作。
然后使用这个函数来打印 "Hello" 5 次。

|---|---|
```kotlin
fun repeatN(n: Int, action: () -> Unit) {
    // 在这里写下你的代码
}

fun main() {
    // 在这里写下你的代码
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-lambdas-exercise-2"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgE4wA4wEMAXAOQAo14MBJNIgGgwLCIEsJKMyBKDAWgB8GAKppWRXsAA6mDHID08jAHUs4mBgCeEFFgyRYGABYwcMgL4yZqTAFsCrNDwzTZCpavVadegxpNmaOYg9CBEBFgA5jBEAAoANsRIEFi2CCAAVgQAbgQh4BC2uKzxpgBqpgDO7GjpAIwAdACcDXUADCDmQA?_ga=2.250048134.1684546783.1699700997-2060274793.1694940376&_gl=1*wq2wgu*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc5MjE1My4xOS4wLjE2OTk3OTIxNTMuNjAuMC4w)

|---|---|
```kotlin
fun repeatN(n: Int, action: () -> Unit) {
    for (i in 1..n) {
        action()
    }
}

fun main() {
    repeatN(5) {
        println("Hello")
    }
}
```
{collapsible="true" collapsed-title="Example solution" id="kotlin-tour-lambdas-solution-2"}

## 下一步

[类](kotlin-tour-classes.md)
