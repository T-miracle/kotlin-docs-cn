[//]: # (title: 基本语法)

这是一个包含基本语法元素及示例的集合。每个部分末尾都有一个链接，指向相关主题的详细描述。

你也可以通过 JetBrains Academy 的免费
[Kotlin Core track](https://hyperskill.org/tracks?category=4&utm_source=jbkotlin_hs&utm_medium=referral&utm_campaign=kotlinlang-docs&utm_content=button_1&utm_term=22.03.23)
学习所有 Kotlin 的基本知识。

## 包定义和导入

源文件顶部应包含包规范。

```kotlin
package my.demo

import kotlin.text.*

// ...
```

不需要匹配目录和包：源文件可以随意放置在文件系统中。

请参阅 [包](packages.md)。

## 程序入口点

Kotlin 应用的入口是 `main` 函数。

```kotlin
fun main() {
    println("Hello world!")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-hello-world"}

`main` 的另一种形式接受可变数量的 `String` 参数。

```kotlin
fun main(args: Array<String>) {
    println(args.contentToString())
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

## 打印到标准输出

`print` 将其参数打印到标准输出。

```kotlin
fun main() {
//sampleStart
    print("Hello ")
    print("world!")
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-print"}

`println` 打印其参数并添加换行，使得下一个打印的内容出现在下一行。

```kotlin
fun main() {
//sampleStart
    println("Hello world!")
    println(42)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-println"}

## 函数

一个具有两个 `Int` 参数和 `Int` 返回类型的函数。

```kotlin
//sampleStart
fun sum(a: Int, b: Int): Int {
    return a + b
}
//sampleEnd

fun main() {
    print("sum of 3 and 5 is ")
    println(sum(3, 5))
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-return-int"}

函数体可以是一个表达式。
其返回类型是自动推断出来的。

```kotlin
//sampleStart
fun sum(a: Int, b: Int) = a + b
//sampleEnd

fun main() {
    println("sum of 19 and 23 is ${sum(19, 23)}")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-function-expression"}

一个不返回任何有意义值的函数（返回类型为 `Unit`）。

```kotlin
//sampleStart
fun printSum(a: Int, b: Int): Unit {
    println("sum of $a and $b is ${a + b}")
}
//sampleEnd

fun main() {
    printSum(-1, 8)
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-return-unit"}

`Unit` 返回类型可以省略。

```kotlin
//sampleStart
fun printSum(a: Int, b: Int) {
    println("sum of $a and $b is ${a + b}")
}
//sampleEnd

fun main() {
    printSum(-1, 8)
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-function-omit-unit"}

请参阅 [函数](functions.md)。

## 变量 {id=变量}

使用关键字 `val` 定义只读本地变量。
它们只能被赋值一次。

```kotlin
fun main() {
//sampleStart
    val a: Int = 1  // 立即分配
    val b = 2   // 推断出“Int”类型
    val c: Int  // 未提供初始值设定项时所需的类型
    c = 3       // 延期分配
//sampleEnd
    println("a = $a, b = $b, c = $c")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-val"}

可重新分配的变量使用关键字 `var`。

```kotlin
fun main() {
//sampleStart
    var x = 5 // 推断出“Int”类型
    x += 1
//sampleEnd
    println("x = $x")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-var"}

你可以在顶层声明变量。

```kotlin
//sampleStart
val PI = 3.14
var x = 0

fun incrementX() { 
    x += 1 
}
//sampleEnd

fun main() {
    println("x = $x; PI = $PI")
    incrementX()
    println("incrementX()")
    println("x = $x; PI = $PI")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-variable-top-level"}

请参阅 [属性](properties.md)。

## 创建类和实例

要定义一个类，使用关键字 `class`。

```kotlin
class Shape
```

类的属性可以在其声明或主体中列出。

```kotlin
class Rectangle(var height: Double, var length: Double) {
    var perimeter = (height + length) * 2 
}
```

默认构造函数，其中参数在类声明中列出，将自动可用。

```kotlin
class Rectangle(var height: Double, var length: Double) {
    var perimeter = (height + length) * 2 
}
fun main() {
//sampleStart
    val rectangle = Rectangle(5.0, 2.0)
    println("The perimeter is ${rectangle.perimeter}")
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-class-constructor"}

类之间的继承由冒号（`:`）声明。
类默认为 final；
要使类可继承，将其标记为 `open`。

```kotlin
open class Shape

class Rectangle(var height: Double, var length: Double): Shape() {
    var perimeter = (height + length) * 2 
}
```

详情请参阅 [类](classes.md) 和 [对象和实例](object-declarations.md)。

## 注释

与大多数现代语言一样，Kotlin 支持单行（或 _行尾_）和多行（_块_）注释。

```kotlin
// 这是一个行尾注释。

/* 这是一个跨多行的
    块注释。 */
```

Kotlin 中的块注释可以嵌套。

```kotlin
/* 评论从这里开始
/* 包含嵌套注释 *&#8288;/     
并在此结束。 */
```

查看 [记录 Kotlin 代码](kotlin-doc.md) 以获取有关文档注释语法的信息。

## 字符串模板

```kotlin
fun main() {
//sampleStart
    var a = 1
    // 模板中的简单名称：
    val s1 = "a is $a" 
    
    a = 2
    // 模板中的任意表达式：
    val s2 = "${s1.replace("is", "was")}, but now is $a"
//sampleEnd
    println(s2)
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-string-templates"}

查看 [字符串模板](strings.md#字符串模板) 以获取详细信息。

## 条件表达式

```kotlin
//sampleStart
fun maxOf(a: Int, b: Int): Int {
    if (a > b) {
        return a
    } else {
        return b
    }
}
//sampleEnd

fun main() {
    println("max of 0 and 42 is ${maxOf(0, 42)}")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-conditional-expressions"}

在 Kotlin 中，`if` 也可以作为一个表达式使用。

```kotlin
//sampleStart
fun maxOf(a: Int, b: Int) = if (a > b) a else b
//sampleEnd

fun main() {
    println("max of 0 and 42 is ${maxOf(0, 42)}")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-if-expression"}

查看 [`if`-表达式](control-flow.md#if表达式)。

## for 循环

```kotlin
fun main() {
//sampleStart
    val items = listOf("apple", "banana", "kiwifruit")
    for (item in items) {
        println(item)
    }
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-for-loop"}

或

```kotlin
fun main() {
//sampleStart
    val items = listOf("apple", "banana", "kiwifruit")
    for (index in items.indices) {
        println("item at $index is ${items[index]}")
    }
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-for-loop-indices"}

查看 [for 循环](control-flow.md#for循环)。

## while 循环

```kotlin
fun main() {
//sampleStart
    val items = listOf("apple", "banana", "kiwifruit")
    var index = 0
    while (index < items.size) {
        println("item at $index is ${items[index]}")
        index++
    }
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-while-loop"}

请参阅 [while 循环](control-flow.md#while循环)。

## when 表达式

```kotlin
//sampleStart
fun describe(obj: Any): String =
    when (obj) {
        1          -> "One"
        "Hello"    -> "Greeting"
        is Long    -> "Long"
        !is String -> "Not a string"
        else       -> "Unknown"
    }
//sampleEnd

fun main() {
    println(describe(1))
    println(describe("Hello"))
    println(describe(1000L))
    println(describe(2))
    println(describe("other"))
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-when-expression"}

请参阅 [when 表达式](control-flow.md#when表达式)。

## Ranges（区间）

使用 `in` 运算符检查数字是否在指定区间内。

```kotlin
fun main() {
//sampleStart
    val x = 10
    val y = 9
    if (x in 1..y+1) {
        println("fits in range")
    }
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-range-in"}

检查数字是否超出区间。

```kotlin
fun main() {
//sampleStart
    val list = listOf("a", "b", "c")
    
    if (-1 !in 0..list.lastIndex) {
        println("-1 is out of range")
    }
    if (list.size !in list.indices) {
        println("list size is out of valid list indices range, too")
    }
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-out-of-range"}

迭代一个区间。

```kotlin
fun main() {
//sampleStart
    for (x in 1..5) {
        print(x)
    }
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-iterate-range"}

或者迭代一个数列。

```kotlin
fun main() {
//sampleStart
    for (x in 1..10 step 2) {
        print(x)
    }
    println()
    for (x in 9 downTo 0 step 3) {
        print(x)
    }
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-iterate-progression"}

请参阅 [区间和数列](ranges.md)。

## 集合

迭代集合。

```kotlin
fun main() {
    val items = listOf("apple", "banana", "kiwifruit")
//sampleStart
    for (item in items) {
        println(item)
    }
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-iterate-collection"}

使用 `in` 运算符检查集合是否包含一个对象。

```kotlin
fun main() {
    val items = setOf("apple", "banana", "kiwifruit")
//sampleStart
    when {
        "orange" in items -> println("juicy")
        "apple" in items -> println("apple is fine too")
    }
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-collection-in"}

使用 lambda 表达式来过滤和映射集合：

```kotlin
fun main() {
//sampleStart
    val fruits = listOf("banana", "avocado", "apple", "kiwifruit")
    fruits
      .filter { it.startsWith("a") }
      .sortedBy { it }
      .map { it.uppercase() }
      .forEach { println(it) }
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-collection-filter-map"}

请参阅 [集合概述](collections-overview.md)。

## 可空值和空值检查

当可能存在 `null` 值时，引用必须显式标记为可空。可空类型的名称末尾带有 `?`。

如果 `str` 不包含整数，则返回 `null`：

```kotlin
fun parseInt(str: String): Int? {
    // ...
}
```

使用返回可空值的函数：

```kotlin
fun parseInt(str: String): Int? {
    return str.toIntOrNull()
}

//sampleStart
fun printProduct(arg1: String, arg2: String) {
    val x = parseInt(arg1)
    val y = parseInt(arg2)

    // 使用 `x * y` 会导致错误，因为它们可能包含 null 值。
    if (x != null && y != null) {
        // 在空检查后，`x` 和 `y` 会自动转换为非空类型。
        println(x * y)
    }
    else {
        println("'$arg1' or '$arg2' is not a number")
    }    
}
//sampleEnd

fun main() {
    printProduct("6", "7")
    printProduct("a", "7")
    printProduct("a", "b")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-function-nullable-value"}

或

```kotlin
fun parseInt(str: String): Int? {
    return str.toIntOrNull()
}

fun printProduct(arg1: String, arg2: String) {
    val x = parseInt(arg1)
    val y = parseInt(arg2)
    
//sampleStart
    // ...
    if (x == null) {
        println("Wrong number format in arg1: '$arg1'")
        return
    }
    if (y == null) {
        println("Wrong number format in arg2: '$arg2'")
        return
    }

    // 在空检查后，`x` 和 `y` 会自动转换为非空类型。
    println(x * y)
//sampleEnd
}

fun main() {
    printProduct("6", "7")
    printProduct("a", "7")
    printProduct("99", "b")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-function-null-check"}

请参阅 [空值安全](null-safety.md)。

## 类型检查和自动转换

`is` 运算符用于检查表达式是否是某种类型的实例。
如果对不可变局部变量或属性进行特定类型的检查，就无需显式进行转换：

```kotlin
//sampleStart
fun getStringLength(obj: Any): Int? {
    if (obj is String) {
        // 在此分支中，`obj` 会自动转换为 `String`。
        return obj.length
    }

    // 在类型检查分支外部，`obj` 仍然是 `Any` 类型。
    return null
}
//sampleEnd

fun main() {
    fun printLength(obj: Any) {
        println("Getting the length of '$obj'. Result: ${getStringLength(obj) ?: "Error: The object is not a string"} ")
    }
    printLength("Incomprehensibilities")
    printLength(1000)
    printLength(listOf(Any()))
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-is-operator"}

或

```kotlin
//sampleStart
fun getStringLength(obj: Any): Int? {
    if (obj !is String) return null

    // 在此分支中，`obj` 会自动转换为 `String`。
    return obj.length
}
//sampleEnd

fun main() {
    fun printLength(obj: Any) {
        println("Getting the length of '$obj'. Result: ${getStringLength(obj) ?: "Error: The object is not a string"} ")
    }
    printLength("Incomprehensibilities")
    printLength(1000)
    printLength(listOf(Any()))
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-is-operator-expression"}

甚至可以：

```kotlin
//sampleStart
fun getStringLength(obj: Any): Int? {
    // 在 `&&` 的右侧，`obj` 会自动转换为 `String`。
    if (obj is String && obj.length > 0) {
        return obj.length
    }

    return null
}
//sampleEnd

fun main() {
    fun printLength(obj: Any) {
        println("Getting the length of '$obj'. Result: ${getStringLength(obj) ?: "Error: The object is not a string"} ")
    }
    printLength("Incomprehensibilities")
    printLength("")
    printLength(1000)
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-is-operator-logic"}

请参阅 [类](classes.md) 和 [类型转换](typecasts.md)。

