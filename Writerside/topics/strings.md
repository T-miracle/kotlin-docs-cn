[//]: # (title: 字符串（Strings）)

Kotlin 中的字符串由类型 [`String`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-string/) 表示。
一般而言，字符串值是双引号 (`"`) 中的字符序列：

```kotlin
val str = "abcd 123"
```

字符串的元素是通过索引操作 `s[i]` 可以访问的字符。
你可以使用 `for` 循环迭代这些字符：

```kotlin
fun main() {
val str = "abcd"
//sampleStart
for (c in str) {
    println(c)
}
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

字符串是不可变的。一旦初始化了一个字符串，就无法更改其值或为其赋新值。
所有转换字符串的操作都会将结果返回到一个新的 `String` 对象中，原始字符串保持不变：

```kotlin
fun main() {
//sampleStart
    val str = "abcd"
    println(str.uppercase()) // 创建并打印一个新的 String 对象
    println(str) // 原始字符串保持不变
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

要连接字符串，使用 `+` 运算符。这也适用于将字符串与其他类型的值连接起来，只要表达式中的第一个元素是字符串：

```kotlin
fun main() {
//sampleStart
val s = "abc" + 1
println(s + "def")
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

> 在大多数情况下，使用 [字符串模板](#字符串模板) 或 [多行字符串](#多行字符串) 更可取，而不是字符串连接。
> 
{style="note"}

## 字符串字面量

Kotlin 有两种类型的字符串字面量：

* [转义字符串](#转义字符串)
* [多行字符串](#多行字符串)

### 转义字符串 {id=转义字符串}

_转义字符串_ 可以包含转义字符。  
以下是转义字符串的示例：

```kotlin
val s = "Hello, world!\n"
```

转义使用传统的方式，即使用反斜杠 (`\`)。  
请参阅 [字符](characters.md) 页面获取支持的转义序列列表。

### 多行字符串 {id=多行字符串}

_多行字符串_ 可以包含换行和任意文本。它由三个双引号 (`"""`) 括起，不包含转义，可以包含换行和任何其他字符：

```kotlin
val text = """
    for (c in "foo")
        print(c)
"""
```

要从多行字符串中删除前导空格，请使用 [`trimMargin()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/trim-margin.html) 函数：

```kotlin
val text = """
    |Tell me and I forget.
    |Teach me and I remember.
    |Involve me and I learn.
    |(Benjamin Franklin)
    """.trimMargin()
```

默认情况下，管道符号 `|` 被用作边距前缀，但你可以选择另一个字符并将其作为参数传递，比如 `trimMargin(">")`。

## 字符串模板 {id=字符串模板}

字符串字面量可能包含 _模板表达式_ —— 代码片段，会求值并将结果连接到字符串中。
模板表达式以美元符号 (`$`) 开始，可以是一个名称：

```kotlin
fun main() {
//sampleStart
    val i = 10
    println("i = $i") // 打印 “i = 10”
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

或花括号中的表达式：

```kotlin
fun main() {
//sampleStart
    val s = "abc"
    println("$s.length is ${s.length}") // 打印 “abc.length is 3”
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

你可以在多行字符串和转义字符串中都使用模板。
在多行字符串（不支持反斜杠转义）中插入美元符号 `$`，在任何允许作为
[标识符](https://kotlinlang.org/docs/reference/grammar.html#identifiers) 起始符号的地方使用以下语法：

```kotlin
val price = """
${'$'}_9.99
"""
```

## 字符串格式化

> 使用 `String.format()` 函数进行字符串格式化仅在 Kotlin/JVM 中可用。
>
{style="note"}

为了按照特定要求格式化字符串，请使用 [`String.format()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/format.html) 函数。

`String.format()` 函数接受一个格式字符串和一个或多个参数。
格式字符串中包含每个剩余参数的一个占位符（`%`），后跟 [格式说明符](https://docs.oracle.com/javase/8/docs/api/java/util/Formatter.html#summary)。
格式说明符是相应参数的格式化指令。
在输出中，每个参数都填充其对应的占位符以定义的格式：

```Kotlin
fun main() {
    // 添加零使长度为七的格式
    val integerNumber = String.format("%07d", 31416)
    println(integerNumber)
    // 0031416

    // 带有四位小数和符号的格式
    val floatNumber = String.format("%+.4f", 3.141592)
    println(floatNumber)
    // +3.1416

    // 使用大写字母表示两个占位符的格式
    val helloString = String.format("%S %S", "hello", "world")
    println(helloString)
    // HELLO WORLD
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

`String.format()` 函数提供了与字符串模板类似的功能。然而，`String.format()` 函数更为灵活，因为它提供了更多的格式选项。

此外，您可以将格式字符串赋值给一个变量。当格式字符串发生变化时，这在依赖用户区域设置的本地化情况下可能会很有用。

在使用 `String.format()` 函数时要小心，因为很容易使参数的数量或位置与相应的占位符不匹配。