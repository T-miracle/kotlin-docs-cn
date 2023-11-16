[//]: # (title: 控制流)

<p>
    <img src="icon-1-done.svg" width="20" alt="First step" /> <a href="kotlin-tour-hello-world.md">Hello world</a><br />
    <img src="icon-2-done.svg" width="20" alt="Second step" /> <a href="kotlin-tour-basic-types.md">基本类型</a><br />
    <img src="icon-3-done.svg" width="20" alt="Third step" /> <a href="kotlin-tour-collections.md">集合</a><br />
    <img src="icon-4.svg" width="20" alt="Fourth step" /> <strong>控制流</strong><br />
    <img src="icon-5-todo.svg" width="20" alt="Fifth step" /> <a href="kotlin-tour-functions.md">函数</a><br />
    <img src="icon-6-todo.svg" width="20" alt="Sixth step" /> <a href="kotlin-tour-classes.md">类</a><br />
    <img src="icon-7-todo.svg" width="20" alt="Final step" /> <a href="kotlin-tour-null-safety.md">空值安全</a>
</p>

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
    val d: Int
    val check = true

    if (check) {
        d = 1
    } else {
        d = 2
    }

    println(d)
    // 1
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-if"}
[打开训练场>>>](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGAbjgDYZTwYCSaALtbQ82AAWMMAGsMAXgxcATihiVeNPEgyEhI0aQqZaeqJIwBGJRgC%2BGGIwDOMMqf2GATKbOLdNAA4yCXRkShiUwB6YON3MxAAGhAuHBkAcxguAAVGHC4kCBksBBAAKxwGaPAILE88RhgZADVq6zwINDyjADoATlajAAYQMyA%3D%3D?_gl=1*17fmlk6*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4NzE2MC4xOC4wLjE2OTk3ODcxNjAuNjAuMC4w&_ga=2.190680363.1684546783.1699700997-2060274793.1694940376)

在 Kotlin 中没有三元运算符`condition ? then : else`。相反，`if`可以用作表达式。当将`if`用作表达式时，不需要使用花括号`{}`：

```kotlin
fun main() {
    val a = 1
    val b = 2

    println(if (a > b) a else b) // 返回值: 2
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-if-expression"}
[打开训练场>>>](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAYA6aVGtGAbjgDYY4YC8GAjDXYywCMOGAExVetAA4AnAgBcmRPEgyE2APgwDSbGEwDOMLaQD0JjACUYclNLT7WDZihjxR4tAF8QAGhBycaQBzawAFJhw5JAhpLAQQACscRl9wCCxJPCYYaQA1HP08CDR4rgA6AE4yrgAGEE8gA%3D?_gl=1*17fmlk6*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4NzE2MC4xOC4wLjE2OTk3ODcxNjAuNjAuMC4w&_ga=2.190680363.1684546783.1699700997-2060274793.1694940376)

### When

当有多个分支的条件表达式时，请使用`when`。
`when`既可以用作语句，也可以用作表达式。

以下是将`when`用作语句的示例：
* 将条件表达式放在括号`()`内，将要执行的操作放在花括号`{}`内。
* 在每个分支中使用`->`将每个条件与每个操作分开。

```kotlin
fun main() {
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
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-when-statement"}
[打开训练场>>>](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGAbjgDYYQBGAVhgLwbkgASMRowh9K1WgHcAFjEyE27UhUy01AenUYAwrLABrAM4YZMAC6yATiw4YYARxRNjZiLxABGMarU0%2BXkAwAWgA%2BDAAHSwIzRiI%2BAHk0GD5iCV8MTR09IxNZCxhrRTtHZwxXd0FhURA03z5KkT5gsMjo2MI%2BAHFLGHMCAHMU2o0tABEYJBwURjMMQzMcMxgsOTNh2iFDGGaIqLQYuJAAVTR9NAhJKhBSNVqAX1rM7t6zAfE0O5AAGhAFy37zAAFRiLJAQSxYBAgdg4BjfcAQLDhPCMAoANQKhjwEDQUI8ADoAJz4jwABhAdyAA%3D?_gl=1*17fmlk6*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4NzE2MC4xOC4wLjE2OTk3ODcxNjAuNjAuMC4w&_ga=2.190680363.1684546783.1699700997-2060274793.1694940376)

> 请注意，所有分支条件将按顺序检查，直到满足其中一个条件为止。
> 因此，只有第一个合适的分支会被执行。
>
{style="note"}

以下是将`when`用作表达式的示例。`when`的语法立即赋给一个变量：

```kotlin
fun main() {  
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
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-when-expression"}
[打开训练场>>>](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6mG1l11AbjgDYYQBGAVhgLwbkgAJGEyYR%2BdPlRpSMjFgCcYAZxRMALjwwB3ABYxMhdh1IUZEgPTmMASSStOGGAEcUzJXxABGfgBoMSmDV3RRV1DDUIDwg0GH5aCTp%2BbxAMAFoAPg8AeRi4szpLGzsjRxc3DyERMRA%2FAKCMENUNCI8AcUVAggBzPITEwWFRcQy2jrVu3r6MQoBlQODlJvDI%2FgBVNABrNAgtNHE8O22MAAdFejwIFHdINCg8ceiMPHclHHGlJDwYKHi%2B4QC0pk1pttrtJtQAL6%2FE7yAhqJhERrqYjQwrtGCdNA9PZoCE1EBqHDyLqBAAKTDeSAg8iwCBAHBwjHxkCwxzwTBg8gAapylBc0HTPAA6ACcQs8AAYQBCgA%3D?_gl=1*1tllg37*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4NzE2MC4xOC4wLjE2OTk3ODcxNjAuNjAuMC4w&_ga=2.148762039.1684546783.1699700997-2060274793.1694940376)

如果`when`被用作表达式，`else`分支是强制的，除非编译器可以检测到所有可能的情况都被分支条件覆盖。

前面的示例展示了`when`用于匹配变量的用法。当你需要检查一系列布尔表达式时，`when`也很有用：

```kotlin
fun main() {
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
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-when-expression-boolean"}
[打开训练场>>>](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGAbjgDYYAuMWADhgLwYCMADkrVaDZrADOYAE54OLPBEy8A7gAsYmCplq6A9HowBJJK3ZcAPBgAMGPBNbSUMADQYJMFg8ky5CpawQGOQgdDDSAJ4YkIxQISK6NGycGFa2ALQAfMGh4VExcSAJiQbGpsmW%2FLb2js5uHl4YPrLyipgsQSE4GABGeCzRELHxOolmKVZ8Gdldvf2Dw0WjJYYm45UATNUOLE6u7p7eMFIt%2Fu2dICo40lgjY0nmqRhbGFk5Vzd396UAyodNx18rQCHRyaggLBCdlMaCCHGkMDoihQDkgaCg%2FTadgcEhwCgkSDwMDiy10MEYHleMxA4MhIHuY2KAF9ivCCCxGERmn42sRiqUPrcqGgmSAXCAWNcAOaeAAKjDxSAgNwQIAAVjgGGLwBBOHhGOEAGrhCRtVV8AB0AE4LVMQEygA%3D%3D%3D?_gl=1*xksuc7*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4NzE2MC4xOC4wLjE2OTk3ODcxNjAuNjAuMC4w&_ga=2.153986616.1684546783.1699700997-2060274793.1694940376)

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
    for (number in 1..5) { 
        // number is the iterator and 1..5 is the range
        print(number)
    }
    // 12345
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-for-loop"}
[打开训练场>>>](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGSEAThoWilgEYxMEYCMAdPwCspYBmq1JAeikZWHLhjwBnDABcAFjCVquONYww40UPoKFLVm7Q2MBzGBMk0ADgwJqWbTg2JOMAL7%2BMnwATADMACxClJQBIAA0IGo4DA5qAAoANvr0DFgIIABWOABuOIngEFgueFlcAGpcyngQaIUCAJz8vAAMIAFAA%3D%3D%3D?_gl=1*92nz89*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4NzE2MC4xOC4xLjE2OTk3ODc0NTguNjAuMC4w&_ga=2.251963913.1684546783.1699700997-2060274793.1694940376)

集合也可以通过循环进行迭代：

```kotlin
fun main() {
    val cakes = listOf("carrot", "cheese", "chocolate")

    for (cake in cakes) {
        println("Yummy, it's a $cake cake!")
    }
    // Yummy, it's a carrot cake!
    // Yummy, it's a cheese cake!
    // Yummy, it's a chocolate cake!
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-for-collection-loop"}
[打开训练场>>>](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAYA6aVGtGAbjgDYZg4DWMAzhgLwZM8XAC4B5JIQrgcAJxkRhUgDSVwACxjcYy1WDURITHMO0hiVGnSQQZGQm04YCrDt1LBLdOgAcZBYUxEUgCaKFhYAJ4qeMIA5Dw4GAAkDjAunACEUuaYdAC%2BnhgA9EUYoeFRTnEJLnIK6TBZubQlZWGR0dUYiXqaXGmpTV6t5R1V8d2s%2BobGA65NVHkgSiDCsgDmMMIACkbC1jJYCCAAVjiMy%2BAQWN54TDAyAGoPXHgQaMcAjAB0AJzfnwADCA8kA?_gl=1*1skwo19*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4NzE2MC4xOC4xLjE2OTk3ODc0NTguNjAuMC4w&_ga=2.246858244.1684546783.1699700997-2060274793.1694940376)

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
    var cakesEaten = 0
    while (cakesEaten < 3) {
        println("Eat a cake")
        cakesEaten++
    }
    // Eat a cake
    // Eat a cake
    // Eat a cake
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-while-loop"}
[打开训练场>>>](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGAbjgE4Zg4DWMAzgKI4AuMTAF4MABmq0A7gAs8AGxgZCrDj36CMAHgwBmUhUy0jAB0YE%2BcouRC8%2BGHC3YxrxCUZoqutwQGofbjABfAIB6EIxbe0cOUPDIh09YiP4oxKo0QJAAGhA%2BJgBzGD4ABTl%2BJAhGLAQQACscBmzwCCxjeRhGADUOzjwINBqARgA6AE5hwdEQQKA?_gl=1*1skwo19*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4NzE2MC4xOC4xLjE2OTk3ODc0NTguNjAuMC4w&_ga=2.246858244.1684546783.1699700997-2060274793.1694940376)

在第二种用法中 (`do-while`)：
* 在括号 `()` 中声明你的 while 循环继续的条件表达式。
* 使用关键字 `do` 在花括号 `{}` 中定义你想要完成的操作。

```kotlin
fun main() {
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
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-while-do-loop"}
[打开训练场>>>](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGAbjgE4Zg4DWMAzgKI4AuMTAF4MABmq0GzVh04AhdjCgYR4zLQDuACzwAbGBkIyuvAZgA8GAMykK62rQAOjAn11FyIUxhwtFn4gkHGmMefkEAagigjABfGKgIMhiHZ1d3Qk8FDh8%2FDgCU2lDspSiY2IxtPQMjRXlFZUtQ00FA%2BwwAeg6Mb19jGK6e%2Flz%2B9sHevJgB7pKR%2FzGZxTn8hYxZvvnKWJAAGhA%2BJgBzGD4ABV1%2BJAhGLAQQACscBl3wCCxHasYANRhGTjwIGg7gBGAB0AE5QcDRCBYkA%3D%3D?_gl=1*46q296*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4NzE2MC4xOC4xLjE2OTk3ODc0NTguNjAuMC4w&_ga=2.191325482.1684546783.1699700997-2060274793.1694940376)

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
[打开训练场>>>](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6mG1AbjgDYYBGKALqxJgLwbkgCCfSpWrUADgCcCrekRGiFAekUYA6lNYwMATwgoJGSLAwALGBJjzqxSgF8QAGhCscEgOYxWABXo5WSCAksBBAAKxw6R3AILDE8enMANXMAZzxOEIBGADoATmzMgAYQWyA?_gl=1*1i69c2*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4NzE2MC4xOC4xLjE2OTk3ODc2NTcuNTEuMC4w&_ga=2.191325482.1684546783.1699700997-2060274793.1694940376)

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
[打开训练场>>>](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6mG1AbjgE4YAOeAXqzgMoA2eYMAZwwBeDAAZK1agHppGTgBcGCjPRhIcYBRHoEA5hgAWMNZKkt2XXvwEBqW2epNdaBdyLkQAFWNqA5EIQaNwAnhgAJBYcPHyCGALWMNKBSMxsHBjwhJ7EjmmWMTb2ec4Ebh7evjABGEGhEVFWsUIJscm1qY2Z2SC5VE7pTUUO%2Fcwu5T0%2BJtWBwWGRg4VxrfztEJ2D3Tkli4l2I1JjZe6TVTV1841LLYlrG5ZbvTsFe8Wjpa4nnlP%2Bs%2FULL2a8VuKXyGSy23eu2ab0OHwm3zOf0u0JswLaoK6EKeUMBwxK4y%2BlWm5zmDVRyxBHTBOEefThFP2eVkGAAomgoKp1JptC4DFUCccKj8YBgGKKAdE9ui0esaQA6DAACQgOhwIQAhBgAOqiww4Gii2kAd0MEG4osaWvgABFIQBfEAAGhASnoehgCgACtwcAokDosAgQAArA04Z3gCBYFgW%2BgANRMAjwQWDAEZ5QBOeVpsQge1AA?_gl=1*1i69c2*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4NzE2MC4xOC4xLjE2OTk3ODc2NTcuNTEuMC4w&_ga=2.191325482.1684546783.1699700997-2060274793.1694940376)

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
[打开训练场>>>](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6mG1A9DRgOoBOeALjBgJ4QpMaSwMACxhMYlAL4gANCFY4mAcxisACgBscrJBCZYEIAFY4AbjhngIWAA551ogGqiAznghoDARgB0ATm%2BeAAwgEkA%3D%3D?_gl=1*r09hco*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4NzE2MC4xOC4xLjE2OTk3ODc2NTcuNTEuMC4w&_ga=2.189173163.1684546783.1699700997-2060274793.1694940376)

|---|---|
```kotlin
fun main() {
    for (number in 1..100) {
        println(
            when {
                number % 15 == 0 -> "fizzbuzz"
                number % 3 == 0 -> "fizz"
                number % 5 == 0 -> "buzz"
                else -> number.toString()
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
[打开训练场>>>](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6mG1AbjgDYYDuEATlAM4YC8G9eHAC4B5JIXIgoBCBxwpWEgDQYJ%2FLBBQcCMJSpC4A5jgBe23apxoDKHAZ0hilatQD0LjAHVWeQTAwBPDVYMSFgMAAsYVh00AF8QRRBBHFY7QQAFehxBJDYsBBAAKxw6BPAILAAHPHoogDUorQg0AoBGADoATnbWgAYQWKA%3D%3D%3D?_gl=1*r09hco*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4NzE2MC4xOC4xLjE2OTk3ODc2NTcuNTEuMC4w&_ga=2.189173163.1684546783.1699700997-2060274793.1694940376)

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