[//]: # (title: 控制流程)

<no-index/>

<tldr>
    <p><img src="icon-1-done.svg" width="20" alt="第一步" /> <a href="kotlin-tour-hello-world.md">Hello world</a><br />
        <img src="icon-2-done.svg" width="20" alt="第二步" /> <a href="kotlin-tour-basic-types.md">基本类型</a><br />
        <img src="icon-3-done.svg" width="20" alt="第三步" /> <a href="kotlin-tour-collections.md">集合</a><br />
        <img src="icon-4.svg" width="20" alt="第四步" /> <strong>控制流</strong><br />
        <img src="icon-5-todo.svg" width="20" alt="第五步" /> <a href="kotlin-tour-functions.md">函数</a><br />
        <img src="icon-6-todo.svg" width="20" alt="第六步" /> <a href="kotlin-tour-classes.md">类</a><br />
        <img src="icon-7-todo.svg" width="20" alt="最后一步" /> <a href="kotlin-tour-null-safety.md">空值安全</a></p>
</tldr>

像其他编程语言一样，Kotlin 也能够根据一段代码的计算结果是否为真来做出决策。
这些代码段被称为 **条件表达式**。
Kotlin 还能够创建并迭代循环。

## 条件表达式 {id=conditional-expressions}

Kotlin 提供了 `if` 和 `when` 来检查条件表达式。

> 如果你必须在 `if` 和 `when` 之间进行选择，我们建议使用 `when`，因为它：
> 
> * 使代码更易读。
> * 更容易添加另一个分支。
> * 减少代码中的错误。
> 
{style="note"}

### If {id=if}

要使用 `if`，请将条件表达式添加在圆括号 `()` 内，并将结果为真时要执行的操作放在花括号
`{}` 内：

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

Kotlin 中没有三元运算符 `condition ? then : else`。
取而代之，`if` 可以作为一个表达式使用。如果每个操作只有一行代码，花括号 `{}` 是可忽略不写的：

```kotlin
fun main() { 
//sampleStart
    val a = 1
    val b = 2

    println(if (a > b) a else b) // 返回值：2
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-if-expression"}

### When {id=when}

在有多个分支的条件表达式中使用 `when`。

使用 `when` 的步骤：

* 将要评估的值放在圆括号 `()` 内。
* 将分支放在花括号 `{}` 内。
* 在每个分支中使用 `->` 来分隔每个检查与检查成功时要执行的操作。

`when` 可以用作语句或表达式。
**语句**不会返回任何值，而是执行操作。

以下是将 `when` 用作语句的示例：

```kotlin
fun main() {
//sampleStart
    val obj = "Hello"

    when (obj) {
        // 检查 obj 是否等于 "1"
        "1" -> println("One")
        // 检查 obj 是否等于 "Hello"
        "Hello" -> println("Greeting")
        // 默认分支
        else -> println("Unknown")     
    }
    // Greeting
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-when-statement"}

> 请注意，所有分支条件都是按顺序检查的，直到其中一个条件被满足。
> 因此，只会执行第一个符合条件的分支。
>
{style="note"}

**表达式** 返回一个值，可以在代码中后续使用。

以下是将 `when` 用作表达式的示例。
`when` 表达式的结果立即赋值给一个变量，然后通过 `println()` 函数使用该变量：

```kotlin
fun main() {
//sampleStart    
    val obj = "Hello"    
    
    val result = when (obj) {
        // 如果 obj 等于 "1"，则将 result 设置为 "One"
        "1" -> "One"
        // 如果 obj 等于 "Hello"，则将 result 设置为 "Greeting"
        "Hello" -> "Greeting"
        // 如果没有满足前面的条件，将 result 设置为 "Unknown"
        else -> "Unknown"
    }
    println(result)
    // Greeting
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-when-expression"}

你已经看到的 `when` 示例都有一个主体：`obj`。但 `when` 也可以在没有主体的情况下使用。

这个示例使用 **没有** 主体的 `when` 表达式来检查一系列布尔表达式：

```kotlin
fun main() {
    val trafficLightState = "Red" // 这可以是 "Green"、"Yellow" 或 "Red"

    val trafficAction = when {
        trafficLightState == "Green" -> "Go"
        trafficLightState == "Yellow" -> "Slow down"
        trafficLightState == "Red" -> "Stop"
        else -> "Malfunction"
    }

    println(trafficAction)
    // Stop
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-when-expression-boolean"}

不过，你也可以写出相同的代码，但将 `trafficLightState` 作为主体：

```kotlin
fun main() {
    val trafficLightState = "Red" // 这可以是 "Green"、"Yellow" 或 "Red"

    val trafficAction = when (trafficLightState) {
        "Green" -> "Go"
        "Yellow" -> "Slow down"
        "Red" -> "Stop"
        else -> "Malfunction"
    }

    println(trafficAction)  
    // Stop
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-when-expression-boolean-subject"}

使用带主体的 `when` 使你的代码更易读和维护。
当使用带主体的 `when` 表达式时，它还帮助 Kotlin 检查所有可能的情况是否都被覆盖。
否则，如果不使用主体的 `when` 表达式，你需要提供一个 else 分支。

## 条件表达式练习 {id=conditional-expressions-practice}

### 练习 1 {id=exercise-1} {collapsible="true" collapsible="true" id="conditional-expressions-exercise-1"}

创建一个简单的游戏，如果投掷两个骰子结果相同，你就赢了。
使用 `if` 打印 `你赢了 :)`，如果骰子相同；否则，打印 `你输了 :(`。

> 在这个练习中，你需要导入一个包，以便使用 `Random.nextInt()` 函数来生成一个随机的 `Int`。
> 关于导入包的更多信息，请参见 [包和导入](packages.md)。
>
{style = "tip"}

<deflist collapsible="true">
    <def title="提示">
        使用 <a href="operator-overloading.md#equality-and-inequality-operators">相等运算符</a>（<code>==</code>）来比较骰子结果。
    </def>
</deflist>

|---|---|
```kotlin
import kotlin.random.Random

fun main() {
    val firstResult = Random.nextInt(6)
    val secondResult = Random.nextInt(6)
    // 在这里写下你的代码
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-control-flow-conditional-exercise-1"}

|---|---|
```kotlin
import kotlin.random.Random

fun main() {
    val firstResult = Random.nextInt(6)
    val secondResult = Random.nextInt(6)
    if (firstResult == secondResult)
        println("你赢了 :)")
    else
        println("你输了 :(")
}
```
{collapsible="true" collapsible="true" collapsed-title="示例解答" id="kotlin-tour-control-flow-conditional-solution-1"}

### 练习 2 {id=exercise-2} {collapsible="true" collapsible="true" id="conditional-expressions-exercise-2"}

使用 `when` 表达式，更新以下程序，使其在输入游戏控制器按钮名称时打印对应的操作。

| **按钮** | **操作** |
|--------|--------|
| A      | 是!     |
| B      | 不是!    |
| X      | 菜单     |
| Y      | 无      |
| 其他     | 没有这个按钮 |

|---|---|
```kotlin
fun main() {
    val button = "A"

    println(
        // 在这里写下你的代码
    )
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-control-flow-conditional-exercise-2"}

|---|---|
```kotlin
fun main() {
    val button = "A"
    
    println(
        when (button) {
            "A" -> "是!"
            "B" -> "不是!"
            "X" -> "菜单"
            "Y" -> "无"
            else -> "没有这个按钮"
        }
    )
}
```
{collapsible="true" collapsible="true" collapsed-title="示例解答" id="kotlin-tour-control-flow-conditional-solution-2"}

## 区间 {id=ranges}

在讨论循环之前，了解如何构造区间供循环遍历是很有用的。

在 Kotlin 中，创建区间最常见的方式是使用 `..` 运算符。例如，`1..4` 等价于 `1, 2, 3, 4`。

要声明一个不包括结束值的区间，使用 `..<` 运算符。例如，`1..<4` 等价于 `1, 2, 3`。

要声明一个逆序的区间，使用 `downTo`。例如，`4 downTo 1` 等价于 `4, 3, 2, 1`。

要声明一个增量不是 1 的区间，使用 `step` 和你想要的增量值。例如，`1..5 step 2` 等价于 `1, 3, 5`。

你也可以对 `Char` 类型的区间做相同的操作：

* `'a'..'d'` 等价于 `'a', 'b', 'c', 'd'`
* `'z' downTo 's' step 2` 等价于 `'z', 'x', 'v', 't'`

## 循环 {id=loops}

编程中最常见的两种循环结构是 `for` 和 `while`。
使用 `for` 遍历一系列值并执行操作。使用 `while` 在满足特定条件之前一直执行操作。

### For {id=for}

利用你对区间的新知识，你可以创建一个 `for` 循环，遍历从 1 到 5 的数字，并每次打印出数字。

将迭代器和区间放在圆括号 `()` 内，使用关键字 `in`。
在花括号 `{}` 内添加你要执行的操作：

```kotlin
fun main() {
//sampleStart
    for (number in 1..5) { 
        // number 是迭代器，1..5 是区间
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
    val cakes = listOf("胡萝卜", "芝士", "巧克力")

    for (cake in cakes) {
        println("好吃，这是一个 $cake 蛋糕！")
    }
    // 好吃，这是 胡萝卜 蛋糕！
    // 好吃，这是 芝士 蛋糕！
    // 好吃，这是 巧克力 蛋糕！
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-for-collection-loop"}

### While {id=while}

`while` 可以有两种使用方式：

* 当条件表达式为真时执行代码块。（`while`）
* 先执行代码块，然后检查条件表达式。（`do-while`）

在第一种用法中（`while`）：

* 在圆括号 `()` 内声明你的 `while` 循环的条件表达式，以决定循环是否继续。
* 在花括号 `{}` 内添加你要执行的操作。

> 以下示例使用了 [增量运算符](operator-overloading.md#increments-and-decrements) `++`
> 来递增 `cakesEaten` 变量的值。
>
{style="tip"}

```kotlin
fun main() {
//sampleStart
    var cakesEaten = 0
    while (cakesEaten < 3) {
        println("吃一块蛋糕")
        cakesEaten++
    }
    // 吃一块蛋糕
    // 吃一块蛋糕
    // 吃一块蛋糕
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-while-loop"}

在第二种用法中（`do-while`）：

* 在圆括号 `()` 内声明你的 `while` 循环的条件表达式，以决定循环是否继续。
* 使用关键字 `do` 定义你要执行的操作，在花括号 `{}` 内。

```kotlin
fun main() {
//sampleStart
    var cakesEaten = 0
    var cakesBaked = 0
    while (cakesEaten < 3) {
        println("吃一块蛋糕")
        cakesEaten++
    }
    do {
        println("烤一块蛋糕")
        cakesBaked++
    } while (cakesBaked < cakesEaten)
    // 吃一块蛋糕
    // 吃一块蛋糕
    // 吃一块蛋糕
    // 烤一块蛋糕
    // 烤一块蛋糕
    // 烤一块蛋糕
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-while-do-loop"}

有关条件表达式和循环的更多信息和示例，请参见 [条件和循环](control-flow.md)。

现在你已经掌握了 Kotlin 控制流程的基础知识，接下来是学习如何编写自己的 [函数](kotlin-tour-functions.md)。

## 循环练习 {id=loops-practice}

### 练习 1 {collapsible="true" collapsible="true" id="loops-exercise-1"}

你有一个程序，它会计数披萨片数，直到有一个完整的 8 片披萨。请以两种方式重构这个程序：

* 使用 `while` 循环。
* 使用 `do-while` 循环。

|---|---|
```kotlin
fun main() {
    var pizzaSlices = 0
    // 在这里开始重构
    pizzaSlices++
    println("披萨片只有 $pizzaSlices 片 :(")
    pizzaSlices++
    println("披萨片只有 $pizzaSlices 片 :(")
    pizzaSlices++
    println("披萨片只有 $pizzaSlices 片 :(")
    pizzaSlices++
    println("披萨片只有 $pizzaSlices 片 :(")
    pizzaSlices++
    println("披萨片只有 $pizzaSlices 片 :(")
    pizzaSlices++
    println("披萨片只有 $pizzaSlices 片 :(")
    pizzaSlices++
    println("披萨片只有 $pizzaSlices 片 :(")
    pizzaSlices++
    // 在这里结束重构
    println("披萨片有 $pizzaSlices 片。太棒了！我们有一整张披萨了！ :D")
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-control-flow-loops-exercise-1"}

|---|---|
```kotlin
fun main() {
    var pizzaSlices = 0
    while ( pizzaSlices < 7 ) {
        pizzaSlices++
        println("披萨片只有 $pizzaSlices 片 :(")
    }
    pizzaSlices++
    println("披萨片有 $pizzaSlices 片。太棒了！我们有一整张披萨了！ :D")
}
```
{collapsible="true" collapsible="true" collapsed-title="示例解答 1" id="kotlin-tour-control-flow-loops-exercise-1-solution-1"}

|---|---|
```kotlin
fun main() {
    var pizzaSlices = 0
    pizzaSlices++
    do {
        println("披萨片只有 $pizzaSlices 片 :(")
        pizzaSlices++
    } while ( pizzaSlices < 8 )
    println("披萨片有 $pizzaSlices 片。太棒了！我们有一整张披萨了！ :D")
}

```
{collapsible="true" collapsible="true" collapsed-title="示例解答 2" id="kotlin-tour-control-flow-loops-exercise-1-solution-2"}

### 练习 2 {collapsible="true" collapsible="true" id="loops-exercise-2"}

编写一个程序来模拟 [Fizz buzz](https://en.wikipedia.org/wiki/Fizz_buzz) 游戏。
你的任务是从 1 到 100 打印数字，任何能被 3 整除的数字用“fizz”替换，任何能被 5 整除的数字用“buzz”替换。
任何能被 3 和 5 同时整除的数字必须用“fizzbuzz”替换。

<deflist collapsible="true">
    <def title="提示 1">
        使用 <code>for</code> 循环来计数，使用 <code>when</code> 表达式来决定每一步要打印什么。
    </def>
</deflist>

<deflist collapsible="true">
    <def title="提示 2">
        使用取模运算符（ <code>%</code> ）来返回一个数除以另一个数后的余数。使用 <a href="operator-overloading.md#equality-and-inequality-operators">相等运算符</a> （ <code>==</code> ）来检查余数是否为零。
    </def>
</deflist>

|---|---|
```kotlin
fun main() {
    // 在这里写下你的代码
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-control-flow-loops-exercise-2"}

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
{collapsible="true" collapsible="true" collapsed-title="示例解答" id="kotlin-tour-control-flow-loops-solution-2"}

### 练习 3 {collapsible="true" collapsible="true" id="loops-exercise-3"}

你有一个单词列表。使用 `for` 和 `if` 只打印以字母 `l` 开头的单词。

<deflist collapsible="true">
    <def title="提示">
        对于 `String` 类型，使用 <a href="https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/starts-with.html"> <code>.startsWith()</code> </a> 函数。
    </def>
</deflist>

|---|---|
```kotlin
fun main() {
    val words = listOf("dinosaur", "limousine", "magazine", "language")
    // 在这里写下你的代码
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-control-flow-loops-exercise-3"}

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
{collapsible="true" collapsible="true" collapsed-title="示例解答" id="kotlin-tour-control-flow-loops-solution-3"}

## 下一步

[函数](kotlin-tour-functions.md)
