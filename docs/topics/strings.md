[//]: # (title: 字符串（Strings）)

Kotlin 中的字符串由类型 [`String`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-string/) 表示。

> 在 JVM 上，`String` 类型的对象使用 UTF-16 编码，每个字符大约占用 2 字节。
>
{style="note"}

通常，字符串值是双引号（`"`）中的字符序列：

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
   
    // 创建并打印一个新的 String 对象
    println(str.uppercase())
    // ABCD
   
    // 原始字符串保持不变
    println(str) 
    // abcd
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
    // abc1def    
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

> 在大多数情况下，使用 [字符串模板](#string-templates) 或 [多行字符串](#多行字符串) 更可取，而不是字符串连接。
> 
{style="note"}

## 字符串字面量

Kotlin 有两种类型的字符串字面量：

* [转义字符串](#escaped-strings)
* [多行字符串](#多行字符串)

### 转义字符串 {id=escaped-strings}

_转义字符串_ 可以包含转义字符。  
以下是转义字符串的示例：

```kotlin
val s = "Hello, world!\n"
```

转义使用传统的方式，即使用反斜杠 (`\`)。  
请参阅 [字符](characters.md) 页面获取支持的转义序列列表。

### 多行字符串 {id=多行字符串}

_多行字符串_ 可以包含换行符和任意文本。
它由三个引号（`"""`）界定，不需要转义，可以包含换行符和任何其他字符：

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

## 字符串模板 {id=string-templates}

字符串字面量可以包含 **模板表达式** —— 这些是会被计算并将结果拼接到字符串中的代码片段。
处理模板表达式时，Kotlin 会自动调用表达式结果的 `.toString()` 函数将其转换为字符串。
模板表达式以美元符号 (`$`) 开头，并由以下两种形式之一组成：

```kotlin
fun main() {
//sampleStart
    val i = 10
    println("i = $i") 
    // i = 10
    
    val letters = listOf("a","b","c","d","e")
    println("Letters: $letters") 
    // Letters: [a, b, c, d, e]

//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

或花括号中的表达式：

```kotlin
fun main() {
//sampleStart
    val s = "abc"
    println("$s 的长度是 ${s.length}") 
    // abc 的长度是 3
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

你可以在多行字符串和转义字符串中使用模板。
然而，多行字符串不支持反斜杠转义。  
要在多行字符串中插入美元符号 `$`，并且在任何符号前面都允许作为 [标识符](https://kotlinlang.org/docs/reference/grammar.html#identifiers) 开头的字符，
可以使用以下语法：

```kotlin
val price = """
${'$'}_9.99
"""
```

> 为了避免字符串中的 `${'$'}` 序列，你可以使用实验性的 [多重美元符号字符串插值特性](#multi-dollar-string-interpolation)。
>
{style="note"}

### 多美元符号字符串插值 {id=multi-dollar-string-interpolation}

> 多美元符号字符串插值是[实验性](https://kotlinlang.org/docs/components-stability.html#stability-levels-explained)的，
> 且需要显式开启（详见下文）。
> 
> 它可能随时发生变化。我们非常欢迎您在[YouTrack](https://youtrack.jetbrains.com/issue/KT-2425)上提供反馈。
>
> {style="warning"}

多美元符号字符串插值允许你指定需要多少个连续的美元符号来触发插值。  
插值是将变量或表达式直接嵌入到字符串中的过程。

虽然你可以为单行字符串[转义字面量](#escaped-strings)，  
但 Kotlin 中的多行字符串不支持反斜杠转义。  
要将美元符号（`$`）作为字面量字符包含在内，你必须使用`${'$'}`构造来防止字符串插值。  
这种方法可能会使代码更难以阅读，特别是当字符串中包含多个美元符号时。

多美元符号字符串插值通过允许你在单行和多行字符串中将美元符号视为字面量字符，简化了这一过程。  
例如：

```kotlin
val KClass<*>.jsonSchema : String
    get() = $$"""
    {
      "$schema": "https://json-schema.org/draft/2020-12/schema",
      "$id": "https://example.com/product.schema.json",
      "$dynamicAnchor": "meta"
      "title": "$${simpleName ?: qualifiedName ?: "unknown"}",
      "type": "object"
    }
    """
```

这里，`$$` 前缀指定需要两个连续的美元符号来触发字符串插值。  
单个美元符号保持为字面量字符。

你可以调整触发插值所需的美元符号数量。  
例如，使用三个连续的美元符号（`$$$`）可以让 `$` 和 `$$` 保持为字面量，
同时启用 `$$$` 进行插值：

```kotlin
val productName = "carrot"
val requestedData =
    $$$"""{
      "currency": "$",
      "enteredAmount": "42.45 $$",
      "$$serviceField": "none",
      "product": "$$$productName"
    }
    """

println(requestedData)
//{
//    "currency": "$",
//    "enteredAmount": "42.45 $$",
//    "$$serviceField": "none",
//    "product": "carrot"
//}
```

在这里，`$$$` 前缀允许字符串包含 `$` 和 `$$`，而无需使用 `${'$'}` 构造进行转义。

要启用此功能，可以在命令行中使用以下编译器选项：

```bash
kotlinc -Xmulti-dollar-interpolation main.kt
```

或者，更新你 Gradle 构建文件中的 `compilerOptions {}` 块：

```kotlin
// build.gradle.kts
kotlin {
    compilerOptions {
        freeCompilerArgs.add("-Xmulti-dollar-interpolation")
    }
}
```

此功能不会影响现有的使用单美元符号字符串插值的代码。  
你可以继续像之前一样使用单个 `$`，
并在需要处理字符串中的字面量美元符号时，应用多美元符号插值。

## 字符串格式化 {id=string-formatting}

> `String.format()` 函数的字符串格式化仅在 Kotlin/JVM 中可用。
>
{style="note"}

要按照您的特定要求格式化字符串，请使用 [`String.format()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/format.html) 函数。

`String.format()` 函数接受一个格式化字符串和一个或多个参数。
格式化字符串包含一个占位符（由 `%` 表示）用于指定参数，后面跟着格式说明符。
格式说明符是对相应参数的格式化指令，包括标志、宽度、精度和转换类型。格式说明符共同决定了输出的格式。
常见的格式说明符包括 ` %d ` 用于整数，` %f ` 用于浮点数，和 ` %s ` 用于字符串。
你还可以使用 `argument_index$` 语法在格式化字符串中以不同的格式多次引用相同的参数。

> 要详细了解格式说明符及其完整列表，请参见 [Java 的 Formatter 文档](https://docs.oracle.com/javase/8/docs/api/java/util/Formatter.html#summary)。
>
{style="note"}

让我们来看一个例子：

```kotlin
fun main() { 
//sampleStart
    // 格式化一个整数，添加前导零以达到七个字符的长度
    val integerNumber = String.format("%07d", 31416)
    println(integerNumber)
    // 0031416

    // 格式化一个浮点数，以 + 符号显示，并保留四位小数
    val floatNumber = String.format("%+.4f", 3.141592)
    println(floatNumber)
    // +3.1416

    // 格式化两个字符串为大写，每个占用一个占位符
    val helloString = String.format("%S %S", "hello", "world")
    println(helloString)
    // HELLO WORLD
    
    // 格式化一个负数以括号括起来，然后使用 `argument_index$` 以不同的格式（没有括号）重复相同的数字
    val negativeNumberInParentheses = String.format("%(d means %1\$d", -31416)
    println(negativeNumberInParentheses)
    //(31416) means -31416
//sampleEnd    
}
```
{interpolate-variables="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

`String.format()` 函数提供了与字符串模板类似的功能。然而，`String.format()` 函数更加通用，因为提供了更多的格式化选项。

此外，您可以将格式字符串从变量赋值。这在格式字符串发生变化时非常有用，例如在依赖用户区域的本地化情况下。

使用 `String.format()` 函数时要小心，因为很容易导致参数的数量或位置与对应的占位符不匹配。