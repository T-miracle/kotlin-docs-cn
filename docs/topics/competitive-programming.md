[//]: # (title: 用于竞争性编程的 Kotlin)

这个教程旨在面向那些之前没有使用过 Kotlin 的竞技程序员以及之前没有参加过竞技编程活动的 Kotlin 开发者。它假定具备相应的编程技能。

[竞技编程](https://en.wikipedia.org/wiki/Competitive_programming) 是一种智力竞技，参赛者编写程序以在严格的约束条件下解决精确规定的算法问题。
问题的难度各不相同，可以是简单的问题，任何软件开发人员都可以解决，只需编写很少的代码即可得到正确的解决方案；
也可以是复杂的问题，需要了解特殊的算法、数据结构以及大量的实践经验。
尽管 Kotlin 并非专为竞技编程而设计，但它巧妙地适用于这个领域，减少了程序员在编写和阅读代码时通常需要的样板代码，几乎达到了动态类型脚本语言提供的水平，同时具有静态类型语言的工具和性能。

请参阅 [使用 Kotlin/JVM 入门](jvm-get-started.md) 以了解如何配置 Kotlin 的开发环境。
在竞技编程中，通常会创建一个项目，每个问题的解决方案都写在一个单独的源文件中。

## 简单示例: 可达数字问题

让我们看一个具体的例子。

[Codeforces](https://codeforces.com/)
第 555 场比赛于 4 月 26 日为第 3 组举办，这意味着它有适合任何开发者尝试的问题。
您可以使用[此链接](https://codeforces.com/contest/1157)查看问题。
这个集合中最简单的问题是
[问题 A: 可达数字](https://codeforces.com/contest/1157/problem/A)。
它要求实现问题陈述中描述的一个简单算法。

我们将通过创建一个带有任意名称的 Kotlin 源文件来解决它。`A.kt` 就挺好。
首先，您需要实现问题陈述中指定的一个函数，如下所示：

我们用这样的方式表示一个函数 f(x)：我们将 1 加到 x，然后，在得到的数字中仍然有至少一个尾随零的情况下，我们就移除该零。

Kotlin 是一种务实而没有强烈偏见的语言，支持命令式和函数式编程风格，而不会将开发者推向其中的任何一种。
您可以使用 Kotlin 的功能，如[尾递归](functions.md#尾递归函数)，以函数式风格实现函数 `f`：

```kotlin
tailrec fun removeZeroes(x: Int): Int =
    if (x % 10 == 0) removeZeroes(x / 10) else x

fun f(x: Int) = removeZeroes(x + 1)
```

或者，您可以使用传统的 [while 循环](control-flow.md) 和在 Kotlin 中用 [var](basic-syntax.md#variables) 表示的可变变量，编写函数 `f` 的命令式实现：

```kotlin
fun f(x: Int): Int {
    var cur = x + 1
    while (cur % 10 == 0) cur /= 10
    return cur
}
```

在 Kotlin 中，由于广泛使用类型推断，许多地方都可以选择使用类型，但是每个声明在编译时仍然有一个明确定义的静态类型。

现在，唯一需要做的是编写主函数，该函数读取输入并实现问题陈述要求的算法的其余部分，即计算在将函数 `f` 重复应用于给定标准输入中的初始数字 `n` 时产生的不同整数的数量。

默认情况下，Kotlin 在 JVM 上运行，并直接访问一个丰富而高效的集合库，具有通用集合和数据结构，如动态大小数组（`ArrayList`）、基于哈希的映射和集合（`HashMap`/`HashSet`）、基于树的有序映射和集合（`TreeMap`/`TreeSet`）。
通过使用整数的哈希集来跟踪在应用函数 `f` 时已经达到的值，可以编写如下所示的直观命令式版本的问题解决方案：

<tabs group="kotlin-versions">
<tab title="Kotlin 1.6.0 及更高版本" group-key="kotlin-1-6">

```kotlin
fun main() {
    var n = readln().toInt() // 从输入中读取整数
    val reached = HashSet<Int>() // 可变哈希集
    while (reached.add(n)) n = f(n) // 迭代函数 f
    println(reached.size) // 打印输出的答案
}
```

在竞技编程中，无需处理格式错误的输入情况。输入格式总是在竞技编程中精确指定的，并且实际输入不能偏离问题陈述中的输入规范。
这就是为什么可以使用 Kotlin 的 [`readln()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.io/readln.html) 函数的原因。
它断言输入字符串存在，否则会抛出异常。
同样，[`String.toInt()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/to-int.html) 函数在输入字符串不是整数时会抛出异常。

</tab>
<tab title="早期版本" group-key="kotlin-1-5">

```kotlin
fun main() {
    var n = readLine()!!.toInt() // 从输入中读取整数
    val reached = HashSet<Int>() // 可变哈希集 
    while (reached.add(n)) n = f(n) // 迭代函数 f
    println(reached.size) // 打印输出的答案
}
```

请注意，在 [readLine()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.io/read-line.html) 函数调用之后使用了 Kotlin 的
[null 断言运算符](null-safety.md#not-null-assertion-operator) `!!`。
Kotlin 的 `readLine()` 函数被定义为返回可空类型 `String?`，在输入结束时返回 `null`，这明确要求开发者处理缺少输入的情况。

在竞技编程中，无需处理格式错误的输入情况。
在竞技编程中，输入格式总是被精确指定的，实际输入不能偏离问题陈述中的输入规范。
这就是 null 断言运算符 `!!` 的本质 — 它断言输入字符串存在，否则会抛出异常。同样，
[String.toInt()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/to-int.html) 也是如此。

</tab>
</tabs>

所有在线竞技编程活动都允许使用预先编写的代码，因此您可以定义自己的实用程序函数库，这些函数库专为竞技编程而设计，以使您的实际解决方案代码更易于阅读和编写。
然后，您将使用此代码作为解决方案的模板。
例如，您可以定义以下用于在竞技编程中读取输入的辅助函数：

<tabs group="kotlin-versions">
<tab title="Kotlin 1.6.0 及更高版本" group-key="kotlin-1-6">

```kotlin
private fun readStr() = readln() // 字符串行
private fun readInt() = readStr().toInt() // 单整数
// 与您在解决方案中使用的其他类型类似
```

</tab>
<tab title="早期版本" group-key="kotlin-1-5">

```kotlin
private fun readStr() = readLine()!! // 字符串行
private fun readInt() = readStr().toInt() // 单整数
// 与您在解决方案中使用的其他类型类似
```

</tab>
</tabs>

请注意这里使用了 `private` [可见性修饰符](visibility-modifiers.md)。
尽管可见性修饰符的概念对于竞技编程来说并不相关，但它允许您在相同包中基于相同模板放置多个解决方案文件，而不会因相同包中的冲突公共声明而产生错误。

## 函数操作符示例: 长数字问题

对于更复杂的问题，Kotlin 丰富的集合函数操作库非常方便，可以最小化样板代码，并将代码转化为线性自上而下和从左到右的流畅数据转换流水线。
例如，[问题 B: 长数字](https://codeforces.com/contest/1157/problem/B) 可以使用简单的贪心算法来实现，而且可以使用这种风格编写，而不需要单个可变变量：

<tabs group="kotlin-versions">
<tab title="Kotlin 1.6.0 及更高版本" group-key="kotlin-1-6">

```kotlin
fun main() {
    // 读取输入
    val n = readln().toInt()
    val s = readln()
    val fl = readln().split(" ").map { it.toInt() }
    // 定义局部函数 f
    fun f(c: Char) = '0' + fl[c - '1']
    // 贪婪查找第一个和最后一个索引
    val i = s.indexOfFirst { c -> f(c) > c }
        .takeIf { it >= 0 } ?: s.length
    val j = s.withIndex().indexOfFirst { (j, c) -> j > i && f(c) < c }
        .takeIf { it >= 0 } ?: s.length
    // 撰写并写下答案
    val ans =
        s.substring(0, i) +
        s.substring(i, j).map { c -> f(c) }.joinToString("") +
        s.substring(j)
    println(ans)
}
```

</tab>
<tab title="早期版本" group-key="kotlin-1-5">

```kotlin
fun main() {
    // 读取输入
    val n = readLine()!!.toInt()
    val s = readLine()!!
    val fl = readLine()!!.split(" ").map { it.toInt() }
    // 定义局部函数 f
    fun f(c: Char) = '0' + fl[c - '1']
    // 贪婪查找第一个和最后一个索引
    val i = s.indexOfFirst { c -> f(c) > c }
        .takeIf { it >= 0 } ?: s.length
    val j = s.withIndex().indexOfFirst { (j, c) -> j > i && f(c) < c }
        .takeIf { it >= 0 } ?: s.length
    // 撰写并写下答案
    val ans =
        s.substring(0, i) +
        s.substring(i, j).map { c -> f(c) }.joinToString("") + 
        s.substring(j)
    println(ans)
}
```

</tab>
</tabs>

在这段密集的代码中，除了集合转换之外，您还可以看到一些方便的 Kotlin 功能，如局部函数和
[elvis operator](null-safety.md#elvis-operator) `?:`，它们允许表达类似 "如果值为正则取其值，否则使用长度" 的
[idioms](idioms.md) 以简洁可读的表达方式，例如 `.takeIf { it >= 0 } ?: s.length`。
然而，在 Kotlin 中，创建额外的可变变量并以命令式风格表达相同的代码也是完全可以的。

为了使竞赛性编程任务中的输入读取更为简洁，您可以使用以下辅助输入读取函数列表：

<tabs group="kotlin-versions">
<tab title="Kotlin 1.6.0 及更高版本" group-key="kotlin-1-6">

```kotlin
private fun readStr() = readln() // 字符串行
private fun readInt() = readStr().toInt() // 单整数
private fun readStrings() = readStr().split(" ") // 字符串列表
private fun readInts() = readStrings().map { it.toInt() } // 整数列表
```

</tab>
<tab title="早期版本" group-key="kotlin-1-5">

```kotlin
private fun readStr() = readLine()!! // 字符串行
private fun readInt() = readStr().toInt() // 单整数
private fun readStrings() = readStr().split(" ") // 字符串列表
private fun readInts() = readStrings().map { it.toInt() } // 整数列表
```

</tab>
</tabs>

有了这些辅助函数，用于读取输入的代码部分变得更简单，紧密地按照问题陈述中的输入规范逐行进行：

```kotlin
// 读取输入
val n = readInt()
val s = readStr()
val fl = readInts()
```

请注意，在竞赛性编程中，通常习惯给变量起比工业编程实践中更短的名字，因为代码只需编写一次，之后不再进行维护。
然而，这些名称通常仍然是助记的 — `a` 用于数组，`i`、`j` 等用于索引，`r` 和 `c` 用于表中的行和列号，`x` 和 `y` 用于坐标等等。
在输入数据中使用与问题陈述中给出的相同名称更容易。
然而，更复杂的问题需要更多的代码，因此需要使用更长、自说明的变量和函数名称。

## 更多提示和技巧

竞赛性编程问题通常有以下类型的输入：

输入的第一行包含两个整数 `n` 和 `k`

在 Kotlin 中，可以使用以下语句简洁地解析此行，使用整数列表进行 [解构声明](destructuring-declarations.md)：

```kotlin
val (n, k) = readInts()
```

使用 JVM 的 `java.util.Scanner` 类来解析结构较少的输入格式可能会很诱人。
Kotlin 被设计为与 JVM 库良好互操作，因此在 Kotlin 中使用它们会感觉相当自然。
然而，请注意，`java.util.Scanner` 的速度非常慢。
实际上，它的速度如此之慢，以至于使用它解析 10<sup>5</sup> 或更多个整数可能不符合典型的 2 秒时间限制，而简单的 Kotlin 的
`split(" ").map { it.toInt() }` 可以处理。

在 Kotlin 中通常使用 [println(...)](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.io/println.html) 函数以及使用
Kotlin 的 [字符串模板](strings.md#string-templates) 来直接编写输出。
然而，当输出包含大约 10<sup>5</sup> 行或更多时，必须小心。
发出如此多的 `println` 调用太慢，因为在 Kotlin 中，输出在每行后自动刷新。
从数组或列表写入多行的更快方法是使用 [joinToString()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/join-to-string.html) 函数，以 `"\n"` 作为分隔符，如下所示：

```kotlin
println(a.joinToString("\n")) // 单独行的数组/列表的每个元素
```

## 学习 Kotlin

Kotlin 很容易学习，尤其是对于那些已经了解 Java 的人来说。
对于软件开发人员，可以直接在网站的参考部分找到 Kotlin 基本语法的简短介绍，从 [basic syntax](basic-syntax.md) 开始。

IDEA 内置了 [Java-to-Kotlin 转换器](https://www.jetbrains.com/help/idea/converting-a-java-file-to-kotlin-file.html)。
熟悉 Java 的人可以使用它来学习相应的 Kotlin 语法构造，但它并不完美，因此熟悉 Kotlin 并学习 [Kotlin 习惯用语](idioms.md) 仍然是值得的。

学习 Kotlin 语法和 Kotlin 标准库 API 的一个很好的资源是 [Kotlin Koans](koans.md)。
