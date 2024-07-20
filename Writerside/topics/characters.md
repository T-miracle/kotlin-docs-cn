[//]: # (title: 字符（Characters）)

字符由类型 `Char` 表示。字符字面值放在单引号中：`'1'`。

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

> **在 JVM 上**：与 [数字](numbers.md#JVM平台上的数字表示) 一样，当需要可空引用时，字符会被装箱。
> 装箱操作不保留身份标识（唯一性）。
>
{style="note"}