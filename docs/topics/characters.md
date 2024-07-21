[//]: # (title: 字符（Characters）)

字符由 `Char` 类型表示。
字符字面值用单引号括起来：`'1'`。

> 在 JVM 上，字符存储为基本类型：`char`，表示一个 16 位的 Unicode 字符。
>
{style="note"}

特殊字符以反斜杠 `\` 开始。
支持以下转义序列：

* `\t` – 制表符
* `\b` – 退格
* `\n` – 换行（LF）
* `\r` – 回车（CR）
* `\'` – 单引号
* `\"` – 双引号
* `\\` – 反斜杠
* `\$` – 美元符号

要编码其他任何字符，请使用 Unicode 转义序列语法：`'\uFF00'`。

```kotlin
fun main() {
//sampleStart
    val aChar: Char = 'a'
 
    println(aChar)
    println('\n') // 打印额外的换行符
    println('\uFF00')
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

如果字符变量的值是数字，你可以使用
[`digitToInt()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/digit-to-int.html) 函数将其明确转换为 `Int` 数字。

> 在 JVM 上，当需要一个可空引用时，字符会像 [数字](numbers.md#numbers-representation-on-the-jvm) 一样在 Java 类中装箱。
> 装箱操作不保持对象的同一性。
>
{style="note"}