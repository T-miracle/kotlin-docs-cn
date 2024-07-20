[//]: # (title: 布尔值（Booleans）)

类型 `Boolean` 表示可以有两个值的布尔对象：`true` 和 `false`。

`Boolean` 有一个可空的对应类型 `Boolean?`，它可以取值为 `null`。

布尔类型的内置操作包括：

* `||` – 析取（逻辑 _OR_）
* `&&` – 合取（逻辑 _AND_）
* `!` – 否定（逻辑 _NOT_）

`||` 和 `&&` 以惰性方式（一旦前面的表达式不满足条件，后面的表达式不会执行）工作。

```kotlin
fun main() {
//sampleStart
    val myTrue: Boolean = true
    val myFalse: Boolean = false
    val boolNull: Boolean? = null
    
    println(myTrue || myFalse)
    println(myTrue && myFalse)
    println(!myTrue)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

> **在JVM中**: 可空引用的布尔对象与 [数字](numbers.md#JVM平台上的数字表示) 类似地进行装箱。
>
{style="note"}