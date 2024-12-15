[//]: # (title: 条件和循环)

## If 表达式 {id=if-expression}

在 Kotlin 中，`if` 是一个表达式：它返回一个值。
因此，Kotlin 没有三目运算符 (`condition ? then : else`)，因为普通的 `if` 在这个角色中已经表现得很好了。

```kotlin
fun main() {
    val a = 2
    val b = 3

    //sampleStart
    var max = a
    if (a < b) max = b

    // 跟 else 一起
    if (a > b) {
      max = a
    } else {
      max = b
    }

    // 作为表达式
    max = if (a > b) a else b

    // 您还可以在表达式中使用 “else if”：
    val maxLimit = 1
    val maxOrLimit = if (maxLimit > a) maxLimit else if (a > b) a else b
  
    println("max is $max")
    // max is 3
    println("maxOrLimit is $maxOrLimit")
    // maxOrLimit is 3
    //sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="if-else-if-kotlin"}

`if` 表达式的分支可以是代码块。在这种情况下，代码块的最后一个表达式是该块的值：

```kotlin
val max = if (a > b) {
    print("Choose a")
    a
} else {
    print("Choose b")
    b
}
```

如果你将 `if` 用作表达式，例如用于 返回它的值 或 赋值给变量，`else` 分支是必需的。

## `when` 表达式和语句 {id=when-expressions-and-statements}

`when` 是一个条件表达式，根据多个可能的值或条件执行代码。
它类似于 Java、C 及类似语言中的 `switch` 语句。例如：

```kotlin
fun main() {
    //sampleStart
    val x = 2
    when (x) {
        1 -> print("x == 1")
        2 -> print("x == 2")
        else -> print("x is neither 1 nor 2")
    }
    // x == 2
    //sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-conditions-when-statement"}

`when` 会按顺序将其参数与所有分支进行匹配，直到某个分支的条件达到满足。

您可以以几种不同的方式使用 `when`。首先，您可以将 `when` 作为 **表达式** 或 **语句** 使用。  
作为表达式，`when` 返回一个值，以供稍后在代码中使用。作为语句，`when` 完成一个动作，但不返回任何进一步有用的内容：

<table>
   <tr>
       <td>表达式</td>
       <td>语句</td>
   </tr>
   <tr>
<td>

```kotlin
// 返回一个字符串并将其赋值给 text 变量
val text = when (x) {
    1 -> "x == 1"
    2 -> "x == 2"
    else -> "x is neither 1 nor 2"
}
```

</td>
<td>

```kotlin
// 不返回任何内容，但会触发
// 打印语句
when (x) {
    1 -> print("x == 1")
    2 -> print("x == 2")
    else -> print("x is neither 1 nor 2")
}
```

</td>
</tr>
</table>

其次，您可以选择带有或不带有主体的 `when`。无论您是否使用主体，您的表达式或语句的行为都是相同的。
我们建议在可能的情况下使用带有主体的 `when`，因为它能使您的代码更易于阅读和维护，并清楚地显示您正在检查的内容。

<table>
   <tr>
       <td>带主体 <code>x</code></td>
       <td>不带主体</td>
   </tr>
   <tr>
<td>

```kotlin
when(x) { ... }
```

</td>
<td>

```kotlin
when { ... }
```

</td>
</tr>
</table>

根据您使用 `when` 的方式，是否需要覆盖所有可能的情况在不同情况下有不同的要求。

如果您将 `when` 用作语句，则不必覆盖所有可能的情况。
在此示例中，某些情况没有覆盖，因此不会发生任何事情。但不会出现错误：

```kotlin
fun main() {
    //sampleStart
    val x = 3
    when (x) {
        // 并未覆盖所有情况
        1 -> print("x == 1")
        2 -> print("x == 2")
    }
    //sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-when-statement"}

在 `when` 语句中，单个分支的值会被忽略。
就像使用 `if` 一样，每个分支可以是一个代码块，它的值是该块中最后一个表达式的值。

如果您将 `when` 用作表达式，则必须覆盖所有可能的情况。
换句话说，它必须是 _完全穷尽的_。
第一个匹配分支的值将成为整个表达式的值。如果没有覆盖所有情况，编译器会抛出错误。

如果您的 `when` 表达式有主体，您可以使用 `else` 分支来确保覆盖所有可能的情况，但这并不是强制性的。
例如，如果您的主体是 `Boolean`、[`enum` 类](enum-classes.md)、[`sealed` 类](sealed-classes.md)
或它们的可空类型，您可以在不使用 `else` 分支的情况下覆盖所有情况：

```kotlin
enum class Bit {
    ZERO, ONE
}

val numericValue = when (getRandomBit()) {
  // 不需要 else 分支，因为所有情况都已覆盖
    Bit.ZERO -> 0
    Bit.ONE -> 1
}
```

如果您的 `when` 表达式 **没有** 主体，则 **必须** 有一个 `else` 分支，否则编译器会抛出错误。
当没有其他分支条件满足时，`else` 分支将被执行：

```kotlin
when {
    a > b -> "a is greater than b"
    a < b -> "a is less than b"
    else -> "a is equal to b"
}
```

`when` 表达式和语句提供了不同的方式来简化代码、处理多个条件并执行类型检查。

您可以通过在一行中使用逗号组合多个条件，定义多个情况的共同行为：

```kotlin
when (x) {
    0, 1 -> print("x == 0 or x == 1")
    else -> print("otherwise")
}
```

您可以使用任意表达式（不仅限于常量）作为分支条件：

```kotlin
when (x) {
    s.toInt() -> print("s encodes x")
    else -> print("s does not encode x")
}
```

您还可以通过 `in` 或 `!in` 关键字检查一个值是否包含在某个 [区间](ranges.md) 或集合中：

```kotlin
when (x) {
    in 1..10 -> print("x is in the range")
    in validNumbers -> print("x is valid")
    !in 10..20 -> print("x is outside the range")
    else -> print("none of the above")
}
```

此外，您可以通过 `is` 或 `!is` 关键字检查一个值是否是特定类型。
需要注意的是，由于 [智能类型转换](typecasts.md#smart-casts)，您可以在不进行额外检查的情况下访问该类型的成员函数和属性。

```kotlin
fun hasPrefix(x: Any) = when(x) {
    is String -> x.startsWith("prefix")
    else -> false
}
```

您可以将 `when` 作为 `if`-`else` 链的替代方案。
如果没有主体，分支条件就是布尔表达式。第一个条件为 `true` 的分支会执行：

```kotlin
when {
    x.isOdd() -> print("x is odd")
    y.isEven() -> print("y is even")
    else -> print("x+y is odd")
}
```

您可以通过以下语法将主体捕获到一个变量中：

```kotlin
fun Request.getBody() =
    when (val response = executeRequest()) {
        is Success -> response.body
        is HttpError -> throw HttpException(response.status)
    }
```

作为主体引入的变量的作用域仅限于 `when` 表达式或语句的主体部分。

### when 表达式中的守卫条件 {id=guard-conditions-in-when-expressions}

> 守卫条件是一个[实验性特性](components-stability.md#stability-levels-explained)，可能会随时发生变化。
> 我们欢迎你在[YouTrack](https://youtrack.jetbrains.com/issue/KT-71140/Guard-conditions-in-when-expressions-feedback)上提供反馈。
>
{style="warning"}

守卫条件允许你在 `when` 表达式的分支中包含多个条件，使得复杂的控制流更加明确和简洁。  
你可以在带有主体的 `when` 表达式或语句中使用守卫条件。

要在分支中包含守卫条件，将其放在主要条件之后，并用 `if` 分隔：

```kotlin
sealed interface Animal {
    data class Cat(val mouseHunter: Boolean) : Animal
    data class Dog(val breed: String) : Animal
}

fun feedAnimal(animal: Animal) {
    when (animal) {
        // 只有主要条件的分支。当 `Animal` 是 `Dog` 时调用 `feedDog()`
        is Animal.Dog -> feedDog()
        // 同时有主要条件和守卫条件的分支。当 `Animal` 是 `Cat` 且不是 `mouseHunter` 时调用 `feedCat()`
        is Animal.Cat if !animal.mouseHunter -> feedCat()
        // 如果上述条件都不匹配，则打印 "Unknown animal"
        else -> println("Unknown animal")
    }
}
```

在一个单一的 `when` 表达式中，你可以将带有守卫条件和不带守卫条件的分支结合起来。  
带有守卫条件的分支只有在主条件和守卫条件都为 `true` 时才会执行。  
如果主条件不匹配，守卫条件则不会被求值。

如果你在没有 `else` 分支的 `when` 语句中使用守卫条件，并且没有任何条件匹配，则不会执行任何分支。

否则，如果你在没有 `else` 分支的 `when` 表达式中使用守卫条件，编译器要求你声明所有可能的情况，以避免运行时错误。

此外，守卫条件还支持 `else if`：

```kotlin
when (animal) {
    // 检查 `animal` 是否是 `Dog`
    is Animal.Dog -> feedDog()
    // 守卫条件检查 `animal` 是否是 `Cat` 且不是 `mouseHunter`
    is Animal.Cat if !animal.mouseHunter -> feedCat()
    // 如果上述条件都不匹配且 animal.eatsPlants 为 true，调用 giveLettuce()
    else if animal.eatsPlants -> giveLettuce()
    // 如果上述条件都不匹配，打印 "Unknown animal"
    else -> println("Unknown animal")
}
```

你可以在单个分支中使用布尔运算符 `&&`（与）或 `||`（或）来组合多个守卫条件。  
使用圆括号来包围布尔表达式，以[避免混淆](coding-conventions.md#guard-conditions-in-when-expression)：

```kotlin
when (animal) {
    is Animal.Cat if (!animal.mouseHunter && animal.hungry) -> feedCat()
}
```

你可以在任何带有主体的 `when` 表达式或语句中使用守卫条件，除了在使用逗号分隔的多个条件的情况下。  
例如，`0, 1 -> print("x == 0 or x == 1")`。

> 要在命令行界面（CLI）中启用守卫条件，请运行以下命令：
>
> `kotlinc -Xwhen-guards main.kt`
>
> 要在 Gradle 中启用守卫条件，请将以下行添加到 `build.gradle.kts` 文件：
>
> `kotlin.compilerOptions.freeCompilerArgs.add("-Xwhen-guards")`
>
{style="note"}

## For 循环 {id=for循环}

`for` 循环用于迭代任何提供迭代器的对象。在类似 C# 的语言中，这类似于 `foreach` 循环。`for` 的语法如下：

```kotlin
for (item in collection) print(item)
```

`for` 的主体可以是一个代码块。

```kotlin
for (item: Int in ints) {
    // ...
}
```

如上所述，`for` 循环迭代任何提供迭代器的对象。这意味着：

* 具有一个成员或扩展函数 `iterator()`，它返回一个 `Iterator<>`，该迭代器：
  * 有一个成员或扩展函数 `next()`
  * 有一个成员或扩展函数 `hasNext()` 返回 `Boolean`。

这三个函数都需要标记为 `operator`。

要迭代一系列数字，使用 [区间表达式](ranges.md)：

```kotlin
fun main() {
//sampleStart
    for (i in 1..3) {
        print(i)
    }
    for (i in 6 downTo 0 step 2) {
        print(i)
    }
    // 1236420
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

`for` 循环遍历区间或数组会被编译为基于索引的循环，而不会创建迭代器对象。

如果你想通过索引遍历数组或列表，可以这样做：

```kotlin
fun main() {
    val array = arrayOf("a", "b", "c")
//sampleStart
    for (i in array.indices) {
        print(array[i])
    }
    // abc
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

或者，你可以使用 `withIndex` 库函数：

```kotlin
fun main() {
    val array = arrayOf("a", "b", "c")
//sampleStart
    for ((index, value) in array.withIndex()) {
        println("the element at $index is $value")
    }
    // the element at 0 is a
    // the element at 1 is b
    // the element at 2 is c
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

## While 循环 {id=while-loops}

`while` 和 `do-while` 循环会在条件满足时不断地处理它们的主体。它们之间的区别在于条件检查的时间：

* `while` 在每次处理主体之前先检查条件，如果条件满足，则处理主体，然后返回进行条件检查。
* `do-while` 先处理主体，再检查条件。如果条件满足，循环会重复。因此，`do-while` 的主体至少会执行一次，无论条件是否满足。

```kotlin
while (x > 0) {
    x--
}

do {
    val y = retrieveData()
} while (y != null) // y 在这里可见！
```

## 循环中的 Break 和 Continue {id=break-and-continue-in-loops}

Kotlin 支持在循环中使用传统的 `break` 和 `continue` 操作符。详见[返回和跳转](returns.md)。
