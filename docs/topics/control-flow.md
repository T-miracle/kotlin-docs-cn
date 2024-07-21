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

    //sampleEnd
    println("max is $max")
    println("maxOrLimit is $maxOrLimit")
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

## When 表达式 {id=when-expression}

`when` 定义了一个带有多个分支的条件表达式。它类似于 C 语言中的 `switch` 语句。
它的简单形式如下：

```kotlin
when (x) {
    1 -> print("x == 1")
    2 -> print("x == 2")
    else -> {
        print("x is neither 1 nor 2")
    }
}
```

`when` 会按顺序将其参数与所有分支进行匹配，直到某个分支的条件达到满足。

`when` 可以作为表达式或语句使用。如果它作为表达式使用，则第一个匹配分支的值成为整体表达式的值。
如果它作为语句使用，则忽略各个分支的值。
与 `if` 一样，每个分支都可以是一个代码块，其值是代码块中的最后一个表达式的值。

如果没有其他分支的条件得到满足，将执行 `else` 分支。

如果 `when` 作为一个 **表达式** 使用，`else` 分支是必需存在的，除非编译器能够证明所有可能的情况都通过分支条件覆盖。
例如，使用 [`enum` 类](enum-classes.md) 条目和 [`sealed` 类](sealed-classes.md) 的子类型。

```kotlin
enum class Bit {
    ZERO, ONE
}

val numericValue = when (getRandomBit()) {
    Bit.ZERO -> 0
    Bit.ONE -> 1
    // 不需要“else”，因为涵盖了所有情况
}
```

在 `when` _语句_ 中，以下情况下 `else` 分支是必需存在的：
* `when` 的主语（subject，译注：指 `when` 所判断的表达式）是 `Boolean`、[`enum`](enum-classes.md) 或 [`sealed`](sealed-classes.md) 类型，或它们的可空对应类型。
* `when` 的分支没有覆盖此主语的所有可能情况。

```kotlin
enum class Color {
    RED, GREEN, BLUE
}

when (getColor()) {  
    Color.RED -> println("red")
    Color.GREEN -> println("green")   
    Color.BLUE -> println("blue")
    // 不需要“else”，因为涵盖了所有情况
}

when (getColor()) {
    Color.RED -> println("red") // GREEN 和 BLUE 没有分支
    else -> println("not red") // “其他”为必填项
}
```


为了使多个情况定义共同的行为，请将它们的条件用逗号在一行中组合：

```kotlin
when (x) {
    0, 1 -> print("x == 0 or x == 1")
    else -> print("otherwise")
}
```

你可以使用任意表达式（不仅限于常量）作为分支条件。

```kotlin
when (x) {
    s.toInt() -> print("s encodes x")
    else -> print("s does not encode x")
}
```

你还可以检查一个值是否在（`in`）或不在（`!in`）[区间](ranges.md)或集合中：

```kotlin
when (x) {
    in 1..10 -> print("x is in the range")
    in validNumbers -> print("x is valid")
    !in 10..20 -> print("x is outside the range")
    else -> print("none of the above")
}
```

另一种选择是检查一个值是否 是（`is`） 或 不是（`!is`） 特定类型。请注意，
由于[智能转换](typecasts.md#smart-casts)，你可以访问该类型的方法和属性而无需进行额外的检查。

```kotlin
fun hasPrefix(x: Any) = when(x) {
    is String -> x.startsWith("prefix")
    else -> false
}
```

`when` 也可以用作替代 `if`-`else` `if` 链的方式。
如果没有提供参数，分支条件就是布尔表达式，当其条件为真时执行相应的分支：

```kotlin
when {
    x.isOdd() -> print("x is odd")
    y.isEven() -> print("y is even")
    else -> print("x+y is odd")
}
```

你可以使用以下语法将 **when** 的主语捕获到一个变量中：

```kotlin
fun Request.getBody() =
    when (val response = executeRequest()) {
        is Success -> response.body
        is HttpError -> throw HttpException(response.status)
    }
```

**when** 表达式中引入的变量的作用域限制在 **when** 的主体内。

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
        println(i)
    }
    for (i in 6 downTo 0 step 2) {
        println(i)
    }
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
        println(array[i])
    }
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
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

## While 循环 {id=while循环}

`while` 和 `do-while` 循环在其条件满足的情况下持续执行其主体。
它们之间的区别在于条件检查的时间：
* `while` 先检查条件，如果条件满足，则执行主体，然后返回到条件检查。
* `do-while` 先执行主体，然后再检查条件。如果条件满足，循环重复。因此，`do-while` 的主体至少执行一次，不管条件如何。

```kotlin
while (x > 0) {
    x--
}

do {
    val y = retrieveData()
} while (y != null) // y is visible here!
```

## 循环中的 Break 和 Continue

Kotlin 支持在循环中使用传统的 `break` 和 `continue` 操作符。详见[返回和跳转](returns.md)。
