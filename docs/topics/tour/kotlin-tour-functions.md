[//]: # (title: 函数)

<tldr>
    <p><img src="icon-1-done.svg" width="20" alt="第一步" /> <a href="kotlin-tour-hello-world.md">Hello world</a><br />
        <img src="icon-2-done.svg" width="20" alt="第二步" /> <a href="kotlin-tour-basic-types.md">基本类型</a><br />
        <img src="icon-3-done.svg" width="20" alt="第三步" /> <a href="kotlin-tour-collections.md">集合</a><br />
        <img src="icon-4-done.svg" width="20" alt="第四步" /> <a href="kotlin-tour-control-flow.md">控制流程</a><br />
        <img src="icon-5.svg" width="20" alt="第五步" /> <strong>函数</strong><br />
        <img src="icon-6-todo.svg" width="20" alt="第六步" /> <a href="kotlin-tour-classes.md">类</a><br />
        <img src="icon-7-todo.svg" width="20" alt="最后一步" /> <a href="kotlin-tour-null-safety.md">空值安全</a></p>
</tldr>

你可以使用 `fun` 关键字在 Kotlin 中声明你自己的函数 。

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

在 Kotlin 中：

* 函数 参数写在圆括号 `()` 内。
* 每个参数必须有一个类型，并且多个参数之间必须用逗号 `,` 分隔。
* 返回类型写在函数的圆括号 `()` 之后，用冒号 `:` 分隔。
* 函数的主体写在 花括号 `{}` 内。
* `return` 关键字用于从函数中退出或返回某些内容。

> 如果一个函数没有返回任何有用的东西，返回类型和 `return` 关键字可以省略。
> 了解更多请参见 [无返回值函数](#functions-without-return)。
>
{style="note"}

在以下示例中：

* `x` 和 `y` 是 函数 参数。
* `x` 和 `y` 的类型是 `Int`。
* 函数的返回类型是 `Int`。
* 该函数在调用时返回 `x` 和 `y` 的和。

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

> 我们在 [编码规范](coding-conventions.md#function-names) 中建议你以小写字母开头命名函数，并使用不带下划线的驼峰命名法。
> 
{style="note"}

## 具名参数 {id=named-arguments}

一般为了代码的简洁，你在调用函数时，并不用包含参数名称。
然而有时，包含参数名称确实会使你的代码更有可读性。这被称为使用 **具名参数**。
如果你确实包含了参数名称，那么你可以按任意顺序编写参数。

> 在以下示例中，使用了 [字符串模板](strings.md#string-templates) (`$`)
> 来访问参数值，将它们转换为 `String` 类型，然后将它们连接成一个字符串进行打印。
> 
{style="tip"}

```kotlin
fun printMessageWithPrefix(message: String, prefix: String) {
    println("[$prefix] $message")
}

fun main() {
    // 使用了具名参数，并交换了参数顺序
    printMessageWithPrefix(prefix = "Log", message = "Hello")
    // [Log] Hello
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-named-arguments-function"}

## 默认参数值 {id=default-parameter-values}

你可以为你的函数参数定义默认值。任何具有默认值的参数在调用你的函数时都可以省略。
要声明默认值，可以在类型后使用赋值运算符 `=`：

```kotlin
fun printMessageWithPrefix(message: String, prefix: String = "Info") {
    println("[$prefix] $message")
}

fun main() {
    // 调用函数时传入了两个参数
    printMessageWithPrefix("Hello", "Log") 
    // [Log] Hello
    
    // 调用函数时只传入了 message 参数
    printMessageWithPrefix("Hello")        
    // [Info] Hello
    
    printMessageWithPrefix(prefix = "Log", message = "Hello")
    // [Log] Hello
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-default-param-function"}

> 你可以跳过带有默认值的特定参数，而不是省略所有参数。
> 然而，在跳过第一个参数之后，你必须为所有后续参数命名。
>
{style="note"}

## 没有返回值的函数 {id=functions-without-return}

如果你的函数没有返回有用的值，那么它的返回类型就是 `Unit`。
`Unit` 是一个只有一个值的类型 —— `Unit`。你不需要在函数体中显式声明返回 `Unit`。
这意味着你不需要使用 `return` 关键字或声明返回类型：

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

## 单表达式函数 {id=single-expression-functions}

为了使代码更简洁，你可以使用单表达式函数。例如，`sum()` 函数可以这样简化：

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

你可以去掉花括号 `{}`，并使用赋值操作符 `=` 来声明函数体。
当使用赋值操作符 `=` 时，Kotlin 会使用类型推断，因此你也可以省略返回类型。这样 `sum()` 函数就变成了一行：

```kotlin
fun sum(x: Int, y: Int) = x + y

fun main() {
    println(sum(1, 2))
    // 3
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-simple-function-after"}

但是，如果你希望其他开发者能快速理解你的代码，即使使用赋值操作符 `=`，明确定义返回类型也是一个好主意。

> 如果使用 `{}` 花括号来声明函数体，则必须声明返回类型，除非返回类型是 `Unit`。
> 
{style="note"}

## 函数中的提前返回 {id=early-returns-in-functions}

要停止函数中代码的进一步处理，可以使用 `return` 关键字。
以下示例使用 `if` 来在条件表达式为真时提前返回函数：

```kotlin
// 已注册的用户名列表
val registeredUsernames = mutableListOf("john_doe", "jane_smith")

// 已注册的邮箱列表
val registeredEmails = mutableListOf("john@example.com", "jane@example.com")

fun registerUser(username: String, email: String): String {
    // 如果用户名已经被占用，则提前返回
    if (username in registeredUsernames) {
        return "用户名已被占用。请使用其他用户名。"
    }

    // 如果邮箱已经注册，则提前返回
    if (email in registeredEmails) {
        return "邮箱已注册。请使用其他邮箱。"
    }

    // 如果用户名和邮箱都没有被占用，则继续注册
    registeredUsernames.add(username)
    registeredEmails.add(email)

    return "用户注册成功: $username"
}

fun main() {
    println(registerUser("john_doe", "newjohn@example.com"))
    // 用户名已被占用。请使用其他用户名。
    println(registerUser("new_user", "newuser@example.com"))
    // 用户注册成功: new_user
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-function-early-return"}

## 函数练习 {id=functions-practice}

### 练习 1 {collapsible="true" id="functions-exercise-1"}

编写一个名为 `circleArea` 的函数，该函数以整数格式的圆半径作为参数，并输出该圆的面积。

> 在这个练习中，你需要导入一个包，以便通过 `PI` 访问圆周率的值。
> 有关导入包的更多信息，请参见 [包和导入](packages.md)。
>
{style = "tip"}

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
{collapsible="true" collapsed-title="示例解答" id="kotlin-tour-functions-solution-1"}

### 练习 2 {collapsible="true" id="functions-exercise-2"}

将前一个练习中的 `circleArea` 函数重写为单表达式函数。

|---|---|
```kotlin
import kotlin.math.PI

// 在这里写下你的代码

fun main() {
    println(circleArea(2))
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-functions-exercise-2"}

|---|---|
```kotlin
import kotlin.math.PI

fun circleArea(radius: Int): Double = PI * radius * radius

fun main() {
    println(circleArea(2)) // 12.566370614359172
}
```
{collapsible="true" collapsed-title="示例解答" id="kotlin-tour-functions-solution-2"}

### 练习 3 {collapsible="true" id="functions-exercise-3"}

你有一个函数，它将以小时、分钟和秒表示的时间间隔转换为秒。
在大多数情况下，你只需要传递一个或两个函数参数，而其余参数默认为 0。
通过使用默认参数值和具名参数来改进函数和调用代码，以使代码更易读。

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
{collapsible="true" collapsed-title="示例解答" id="kotlin-tour-functions-solution-3"}

## Lambda 表达式 {id=lambda-expressions}

Kotlin 允许你通过使用 Lambda 表达式编写更简洁的函数代码。

例如，以下 `uppercaseString()` 函数：

```kotlin
fun uppercaseString(text: String): String {
    return text.uppercase()
}
fun main() {
    println(uppercaseString("hello"))
    // HELLO
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-lambda-function-before"}

也可以写成 Lambda 表达式的形式：

```kotlin
fun main() {
    val upperCaseString = { text: String -> text.uppercase() }
    println(upperCaseString("hello"))
    // HELLO
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-lambda-variable"}

Lambda 表达式在初看时可能难以理解，所以我们来详细拆解一下。
Lambda 表达式写在花括号 `{}` 中。

在 Lambda 表达式中，你需要写：

* 参数列表，后面跟一个 `->`。
* `->` 后面的函数体。

在前面的示例中：

* `text` 是函数的参数。
* `text` 的类型是 `String`。
* 函数返回对 `text` 调用的 [`.uppercase()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/uppercase.html) 函数的结果。
* 整个 Lambda 表达式被赋值给 `upperCaseString` 变量，使用赋值操作符 `=`。
* Lambda 表达式通过将变量 `upperCaseString` 当作函数调用，并将字符串 `"hello"` 作为参数来调用。
* `println()` 函数打印结果。

> 如果你声明一个没有参数的 Lambda，则无需使用 `->`。例如：
> ```kotlin
> { println("Log message") }
> ```
>
{style="note"}

Lambda 表达式可以以多种方式使用。你可以：

* [将 Lambda 表达式作为参数传递给另一个函数](#pass-to-another-function)
* [从函数中返回 Lambda 表达式](#return-from-a-function)
* [单独调用 Lambda 表达式](#invoke-separately)

### 传递给另一个函数 {id=pass-to-another-function}

一个很好的例子是将 Lambda 表达式传递给函数，例如在集合上使用
[`.filter()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter.html) 函数：

```kotlin
fun main() {
    //sampleStart
    val numbers = listOf(1, -2, 3, -4, 5, -6)
    
    val positives = numbers.filter ({ x -> x > 0 })
    
    val isNegative = { x: Int -> x < 0 }
    val negatives = numbers.filter(isNegative)
    
    println(positives)
    // [1, 3, 5]
    println(negatives)
    // [-2, -4, -6]
    //sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-lambda-filter"}

`.filter()` 函数接受 Lambda 表达式作为谓词：

* `{ x -> x > 0 }` 处理列表中的每个元素，并仅返回正数。
* `{ x -> x < 0 }` 处理列表中的每个元素，并仅返回负数。

这个示例演示了两种将 Lambda 表达式传递给函数的方式：

* 对于正数，示例将 Lambda 表达式直接添加到 `.filter()` 函数中。
* 对于负数，示例将 Lambda 表达式赋值给 `isNegative` 变量。
然后，`isNegative` 变量作为函数参数传递给 `.filter()` 函数。
在这种情况下，你需要在 Lambda 表达式中指定函数参数（`x`）的类型。

> 如果 Lambda 表达式是唯一的函数参数，你可以省略函数的圆括号 `()`：
> 
> ```kotlin
> val positives = numbers.filter { x -> x > 0 }
> ```
> 
> 这是一种 [尾随 Lambda](#trailing-lambdas) 的示例，详细讨论请参见本章末尾。
>
{style = "note"}

另一个好的例子是使用 [`.map()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map.html)
函数来转换集合中的项目：

```kotlin
fun main() {
    //sampleStart
    val numbers = listOf(1, -2, 3, -4, 5, -6)
    val doubled = numbers.map { x -> x * 2 }
    
    val isTripled = { x: Int -> x * 3 }
    val tripled = numbers.map(isTripled)
    
    println(doubled)
    // [2, -4, 6, -8, 10, -12]
    println(tripled)
    // [3, -6, 9, -12, 15, -18]
    //sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-lambda-map"}

`.map()` 函数接受 Lambda 表达式作为转换函数：

* `{ x -> x * 2 }` 处理列表中的每个元素，并返回该元素乘以 2。
* `{ x -> x * 3 }` 处理列表中的每个元素，并返回该元素乘以 3。

### 函数类型 {id=function-types}

在你学习 从函数中返回 Lambda 表达式 之前，你首先需要理解什么是 **函数类型**。

你已经了解了基本类型，但函数本身也有一个类型。
Kotlin 的类型推断可以从参数类型推断函数的类型。
但是有时你可能需要显式指定函数类型。
编译器需要函数类型，以便让函数知道这个内容是允许的还是不允许的。

函数类型的语法包括：

* 每个参数的类型写在圆括号 `()` 内，并用逗号 `,` 分隔。
* 返回类型写在 `->` 后面。

例如：`(String) -> String` 或 `(Int, Int) -> Int`。

这是定义了 `upperCaseString()` 函数类型的 Lambda 表达式的样子：

```kotlin
val upperCaseString: (String) -> String = { text -> text.uppercase() }

fun main() {
    println(upperCaseString("hello"))
    // HELLO
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-lambda-function-type"}

如果你的 Lambda 表达式没有参数，那么圆括号 `()` 留空。例如：`() -> Unit`

> 你必须在 Lambda 表达式中或作为函数类型声明参数和返回类型。
> 否则，编译器将无法知道你的 Lambda 表达式的类型。
> 
> 例如，以下代码将无法工作：
> 
> `val upperCaseString = { str -> str.uppercase() }`
>
{style="note"}

### 从函数中返回 Lambda 表达式 {id=return-from-a-function}

Lambda 表达式可以从函数中返回。
为了让编译器理解返回的 Lambda 表达式的类型，你必须声明一个函数类型。

在以下示例中，`toSeconds()` 函数的函数类型是 `(Int) -> Int`，因为它总是一个返回接受
`Int` 类型参数并返回 `Int` 值的 Lambda 表达式。

这个示例使用 `when` 表达式来确定在调用 `toSeconds()` 时返回哪个 Lambda 表达式：

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

### 单独调用 {id=invoke-separately}

Lambda 表达式可以通过在花括号 `{}` 后添加圆括号 `()` 并在圆括号内包含任何参数来单独调用：

```kotlin
fun main() {
    //sampleStart
    println({ text: String -> text.uppercase() }("hello"))
    // HELLO
    //sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-lambda-standalone"}

### 尾随 Lambda 表达式 {id=trailing-lambdas}

正如你已经看到的那样，如果 Lambda 表达式是唯一的函数参数，你可以省略函数的圆括号 `()`。
如果 Lambda 表达式作为函数的最后一个参数传递，那么该表达式可以写在函数圆括号 `()` 之外。
在这两种情况下，这种语法被称为 **尾随 Lambda 表达式**。

例如，[`.fold()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.sequences/fold.html) 函数接受一个初始值和一个操作：

```kotlin
fun main() {
    //sampleStart
    // 初始值为零。 
    // 操作将初始值与列表中的每个项目进行累加。
    println(listOf(1, 2, 3).fold(0, { x, item -> x + item })) // 6

    // 或者，使用尾随 Lambda 表达式的形式
    println(listOf(1, 2, 3).fold(0) { x, item -> x + item })  // 6
    //sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-trailing-lambda"}

有关 Lambda 表达式的更多信息，请参见 [Lambda 表达式和匿名函数](lambdas.md#lambda-expressions-and-anonymous-functions)。

我们旅程的下一步是学习 Kotlin 中的 [类](kotlin-tour-classes.md)。

## Lambda 表达式练习

### 练习 1 {collapsible="true" id="lambdas-exercise-1"}

你有一个 web 服务支持的动作列表，一个请求的公共前缀，以及一个特定资源的 ID。  
要对 ID 为 5 的资源请求一个 `title` 动作，你需要创建以下 URL：`https://example.com/book-info/5/title`。  
使用 lambda 表达式从动作列表中创建 URL 列表。

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
{collapsible="true" collapsed-title="示例解答" id="kotlin-tour-lambdas-solution-1"}

### 练习 2 {collapsible="true" id="lambdas-exercise-2"}

编写一个函数，该函数接收一个 `Int` 值和一个动作（一个类型为 `() -> Unit` 的函数），然后重复执行该动作指定的次数。
然后使用这个函数打印 “Hello” 5 次。

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
{collapsible="true" collapsed-title="示例解答" id="kotlin-tour-lambdas-solution-2"}

## 下一步 {id=next-step}

[类](kotlin-tour-classes.md)
