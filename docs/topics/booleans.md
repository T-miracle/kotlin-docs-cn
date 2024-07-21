[//]: # (title: 布尔值（Booleans）)

`Boolean` 类型表示可以具有两个值的布尔对象：`true` 和 `false`。
`Boolean` 有一个声明为 `Boolean?` 的 [可空](null-safety.md) 对应类型。

> 在 JVM 上，布尔值以原始 `boolean` 类型存储，通常使用 8 位。
>
{style="note"}

布尔值的内置操作包括：

* `||` – 析取（逻辑 _OR_）
* `&&` – 合取（逻辑 _AND_）
* `!` – 否定（逻辑 _NOT_）

例如：

```kotlin
fun main() {
//sampleStart
    val myTrue: Boolean = true
    val myFalse: Boolean = false
    val boolNull: Boolean? = null

    println(myTrue || myFalse)
    // true
    println(myTrue && myFalse)
    // false
    println(!myTrue)
    // false
    println(boolNull)
    // null
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

`||` 和 `&&` 运算符是惰性求值的，这意味着：

* 如果第一个操作数为 `true`，`||` 运算符不会计算第二个操作数。
* 如果第一个操作数为 `false`，`&&` 运算符不会计算第二个操作数。

> 在 JVM 上，布尔对象的可空引用被封装在 Java 类中，就像 [数字](numbers.md#numbers-representation-on-the-jvm) 一样。
>
{style="note"}
