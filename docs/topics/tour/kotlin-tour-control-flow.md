[//]: # (title: 控制流)

<tldr>
    <p><img src="icon-1-done.svg" width="20" alt="First step" /> <a href="kotlin-tour-hello-world.md">Hello world</a><br />
        <img src="icon-2-done.svg" width="20" alt="Second step" /> <a href="kotlin-tour-basic-types.md">基本类型</a><br />
        <img src="icon-3-done.svg" width="20" alt="Third step" /> <a href="kotlin-tour-collections.md">集合</a><br />
        <img src="icon-4.svg" width="20" alt="Fourth step" /> <strong>控制流</strong><br />
        <img src="icon-5-todo.svg" width="20" alt="Fifth step" /> <a href="kotlin-tour-functions.md">函数</a><br />
        <img src="icon-6-todo.svg" width="20" alt="Sixth step" /> <a href="kotlin-tour-classes.md">类</a><br />
        <img src="icon-7-todo.svg" width="20" alt="Final step" /> <a href="kotlin-tour-null-safety.md">空值安全</a></p>
</tldr>

与其他编程语言一样，Kotlin 能够根据代码是否评估为真来做出决策。
这样的代码片段被称为**条件表达式**。
Kotlin 还能够创建和迭代循环。

## 条件表达式

Kotlin提供了`if`和`when`用于检查条件表达式。

> 如果你必须在`if`和`when`之间选择，我们建议使用`when`，因为它能够产生更健壮和安全的程序。
> 
{style="note"}

### If

要使用`if`，在括号`()`内添加条件表达式，如果结果为真，则在花括号`{}`内添加要执行的操作：

```kotlin
fun main() {
//sampleStart
    val d: Int
    val check = true

    if (check) {
        d = 1
    } else {
        d = 2
    }

    println(d)
    // 1
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-if"}

Kotlin 中没有三元运算符 `condition ? then : else`。相反，`if` 可以作为表达式使用。
如果每个操作只有一行代码，花括号 `{}` 是可以忽略的：

```kotlin
fun main() {
//sampleStart
    val a = 1
    val b = 2

    println(if (a > b) a else b) // 返回值: 2
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-if-expression"}

### When

当有多个分支的条件表达式时，请使用`when`。
`when`既可以用作语句，也可以用作表达式。

以下是将`when`用作语句的示例：
* 将条件表达式放在括号`()`内，将要执行的操作放在花括号`{}`内。
* 在每个分支中使用`->`将每个条件与每个操作分开。

```kotlin
fun main() {
//sampleStart
    val obj = "Hello"

    when (obj) {
        // 检查 obj 是否等于“1”
        "1" -> println("One")
        // 检查 obj 是否等于“Hello”
        "Hello" -> println("Greeting")
        // 默认声明
        else -> println("Unknown")     
    }
    // Greeting
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-when-statement"}

> 请注意，所有分支条件将按顺序检查，直到满足其中一个条件为止。
> 因此，只有第一个合适的分支会被执行。
>
{style="note"}

以下是将`when`用作表达式的示例。`when`的语法立即赋给一个变量：

```kotlin
fun main() {
//sampleStart    
    val obj = "Hello"    
    
    val result = when (obj) {
        // 如果 obj 等于“1”，则将结果设置为“One”
        "1" -> "One"
        // 如果 obj 等于“Hello”，则将结果设置为“Greeting”
        "Hello" -> "Greeting"
        // 如果不满足先前的条件，则将结果设置为“Unknown”
        else -> "Unknown"
    }
    println(result)
    // Greeting
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-when-expression"}

如果`when`被用作表达式，`else`分支是强制的，除非编译器可以检测到所有可能的情况都被分支条件覆盖。

前面的示例展示了`when`用于匹配变量的用法。当你需要检查一系列布尔表达式时，`when`也很有用：

```kotlin
fun main() {
//sampleStart
    val temp = 18

    val description = when {
        // 如果 temp < 0 为真，则将描述设置为“非常冷”
        temp < 0 -> "非常冷"
        // 如果 temp < 10 为 true，则将描述设置为“有点冷”
        temp < 10 -> "有点冷"
        // 如果 temp < 20 为 true，则将描述设置为“暖和”
        temp < 20 -> "暖和"
        // 如果先前条件不满足，则将描述设置为“热”
        else -> "热"             
    }
    println(description)
    // 暖和
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-when-expression-boolean"}

## Ranges（区间）

在讨论循环之前，了解如何构造循环迭代的区间是很有用的。

在 Kotlin 中创建区间的最常见方法是使用`..`运算符。例如，`1..4`相当于`1, 2, 3, 4`。

要声明不包括结束值的区间，请使用`..<`运算符。例如，`1..<4`相当于`1, 2, 3`。

要声明以相反顺序的区间，请使用`downTo`。例如，`4 downTo 1`相当于`4, 3, 2, 1`。

要声明以非1的步长递增的区间，请使用`step`和您想要的递增值。例如，`1..5 step 2`相当于`1, 3, 5`。

您也可以对`Char`区间执行相同的操作：
* `'a'..'d'`相当于`'a', 'b', 'c', 'd'`
* `'z' downTo 's' step 2`相当于`'z', 'x', 'v', 't'`

## Loops （循环）

编程中两种最常见的循环结构是`for`和`while`。使用`for`来迭代一系列的值并执行某个操作。使用`while`来持续执行某个操作，直到满足特定条件。

### For

利用你对区间的新认识，你可以创建一个`for`循环，迭代从1到5的数字，并在每次迭代时打印该数字。

将迭代器和区间放在括号`()`内，使用关键字`in`。在花括号`{}`内添加你想要完成的操作：

```kotlin
fun main() {
//sampleStart
    for (number in 1..5) { 
        // number is the iterator and 1..5 is the range
        print(number)
    }
    // 12345
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-for-loop"}

集合也可以通过循环进行迭代：

```kotlin
fun main() { 
//sampleStart
    val cakes = listOf("carrot", "cheese", "chocolate")

    for (cake in cakes) {
        println("Yummy, it's a $cake cake!")
    }
    // Yummy, it's a carrot cake!
    // Yummy, it's a cheese cake!
    // Yummy, it's a chocolate cake!
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-for-collection-loop"}

### While

`while`可以以两种方式使用：
* 在条件表达式为真时执行代码块。 (`while`)
* 先执行代码块，然后检查条件表达式。 (`do-while`)

在第一种用法中 (`while`)：
* 在括号 `()` 中声明你的 while 循环继续的条件表达式。
* 在花括号 `{}` 中添加你想要完成的操作。

> 以下示例使用 [递增运算符](operator-overloading.md#increments-and-decrements) `++` 来增加 `cakesEaten` 变量的值。
>
{style="note"}

```kotlin
fun main() {
//sampleStart
    var cakesEaten = 0
    while (cakesEaten < 3) {
        println("Eat a cake")
        cakesEaten++
    }
    // Eat a cake
    // Eat a cake
    // Eat a cake
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-while-loop"}

在第二种用法中 (`do-while`)：
* 在括号 `()` 中声明你的 while 循环继续的条件表达式。
* 使用关键字 `do` 在花括号 `{}` 中定义你想要完成的操作。

```kotlin
fun main() {
//sampleStart
    var cakesEaten = 0
    var cakesBaked = 0
    while (cakesEaten < 3) {
        println("Eat a cake")
        cakesEaten++
    }
    do {
        println("Bake a cake")
        cakesBaked++
    } while (cakesBaked < cakesEaten)
    // Eat a cake
    // Eat a cake
    // Eat a cake
    // Bake a cake
    // Bake a cake
    // Bake a cake
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-while-do-loop"}

有关条件表达式和循环的更多信息和示例，请参阅[条件和循环](control-flow.md)。

现在你已经了解了 Kotlin 控制流的基础知识，是时候学习如何编写你自己的[函数](kotlin-tour-functions.md)了。

## 实践

### 练习 1 {collapsible="true"}

使用`when`表达式，更新以下程序，以便在输入 GameBoy 按钮的名称时，将相应的操作打印到输出。

| **按钮** | **行为**                  |
|--------|-------------------------|
| A      | Yes                     |
| B      | No                      |
| X      | Menu                    |
| Y      | Nothing                 |
| Other  | There is no such button |

|---|---|
```kotlin
fun main() {
    val button = "A"

    println(
        // 在这里写下你的代码
    )
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-control-flow-exercise-1"}

|---|---|
```kotlin
fun main() {
    val button = "A"
    
    println(
        when (button) {
            "A" -> "Yes"
            "B" -> "No"
            "X" -> "Menu"
            "Y" -> "Nothing"
            else -> "There is no such button"
        }
    )
}
```
{collapsible="true" collapsed-title="Example solution"}

### 练习 2 {collapsible="true"}

你有一个程序，它计算披萨片数，直到有一个有8片的整个披萨。以两种方式重构此程序：
* 使用`while`循环。
* 使用`do-while`循环。

|---|---|
```kotlin
fun main() {
    var pizzaSlices = 0
    // 开始重构
    pizzaSlices++
    println("There's only $pizzaSlices slice/s of pizza :(")
    pizzaSlices++
    println("There's only $pizzaSlices slice/s of pizza :(")
    pizzaSlices++
    println("There's only $pizzaSlices slice/s of pizza :(")
    pizzaSlices++
    println("There's only $pizzaSlices slice/s of pizza :(")
    pizzaSlices++
    println("There's only $pizzaSlices slice/s of pizza :(")
    pizzaSlices++
    println("There's only $pizzaSlices slice/s of pizza :(")
    pizzaSlices++
    println("There's only $pizzaSlices slice/s of pizza :(")
    pizzaSlices++
    // 结束重构
    println("There are $pizzaSlices slices of pizza. Hooray! We have a whole pizza! :D")
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-control-flow-exercise-2"}

|---|---|
```kotlin
fun main() {
    var pizzaSlices = 0
    while ( pizzaSlices < 7 ) {
        pizzaSlices++
        println("There's only $pizzaSlices slice/s of pizza :(")
    }
    pizzaSlices++
    println("There are $pizzaSlices slices of pizza. Hooray! We have a whole pizza! :D")
}
```
{collapsible="true" collapsed-title="Example solution 1"}

|---|---|
```kotlin
fun main() {
    var pizzaSlices = 0
    pizzaSlices++
    do {
        println("There's only $pizzaSlices slice/s of pizza :(")
        pizzaSlices++
    } while ( pizzaSlices < 8 )
    println("There are $pizzaSlices slices of pizza. Hooray! We have a whole pizza! :D")
}

```
{collapsible="true" collapsed-title="Example solution 2"}

### 练习 3 {collapsible="true"}

编写一个模拟[Fizz Buzz](https://en.wikipedia.org/wiki/Fizz_buzz)游戏的程序。
你的任务是逐步打印从1到100的数字，将任何可以被三整除的数字替换为单词"fizz"，将任何可以被五整除的数字替换为单词"buzz"。
任何同时被3和5整除的数字必须替换为单词"fizzbuzz"。

<deflist collapsible="true">
    <def title="提示">
        使用<code>for</code>循环来计数数字，使用<code>when</code>表达式来决定在每一步打印什么。
    </def>
</deflist>

|---|---|
```kotlin
fun main() {
    // 在这里写下你的代码
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-control-flow-exercise-3"}

|---|---|
```kotlin
fun main() {
    for (number in 1..100) {
        println(
            when {
                number % 15 == 0 -> "fizzbuzz"
                number % 3 == 0 -> "fizz"
                number % 5 == 0 -> "buzz"
                else -> "$number"
            }
        )
    }
}
```
{collapsible="true" collapsed-title="Example solution"}

### 练习 4 {collapsible="true"}

你有一个单词列表。使用`for`和`if`，只打印以字母`l`开头的单词。

<deflist collapsible="true">
    <def title="提示">
        使用 <a href="https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/starts-with.html"><code>.startsWith()</code></a> 函数来处理
        <code>String</code> 类型。
    </def>
</deflist>

|---|---|
```kotlin
fun main() {
    val words = listOf("dinosaur", "limousine", "magazine", "language")
    // 在这里写下你的代码
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-control-flow-exercise-4"}

|---|---|
```kotlin
fun main() {
    val words = listOf("dinosaur", "limousine", "magazine", "language")
    for (w in words) {
        if (w.startsWith("l"))
            println(w)
    }
}
```
{collapsible="true" collapsed-title="Example solution"}

## 下一步

[函数](kotlin-tour-functions.md)
