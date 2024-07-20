[//]: # (title: 基本类型)

<p><img src="icon-1-done.svg" width="20" alt="First step" /> <a href="kotlin-tour-hello-world.md">Hello world</a><br />
    <img src="icon-2.svg" width="20" alt="Second step" /> <strong>基本类型</strong><br />
    <img src="icon-3-todo.svg" width="20" alt="Third step" /> <a href="kotlin-tour-collections.md">集合</a><br />
    <img src="icon-4-todo.svg" width="20" alt="Fourth step" /> <a href="kotlin-tour-control-flow.md">控制流</a><br />
    <img src="icon-5-todo.svg" width="20" alt="Fifth step" /> <a href="kotlin-tour-functions.md">函数</a><br />
    <img src="icon-6-todo.svg" width="20" alt="Sixth step" /> <a href="kotlin-tour-classes.md">类</a><br />
    <img src="icon-7-todo.svg" width="20" alt="Final step" /> <a href="kotlin-tour-null-safety.md">空值安全</a>
</p>

Kotlin 中的每个变量和数据结构都有一个数据类型。
数据类型很重要，因为它告诉编译器你可以对该变量或数据结构进行哪些操作。
换句话说，它具有哪些函数和属性。

在上一章中，Kotlin 能够在先前的示例中告诉 `customers` 具有类型：[`Int`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-int/)。
Kotlin 能够**推断**数据类型的能力被称为**类型推断**。
`customers` 被赋予一个整数值。
从这个值，Kotlin 推断出 `customers` 具有数字数据类型：`Int`。
因此，编译器知道您可以对 `customers` 进行算术操作：

```kotlin
fun main() {
    var customers = 10

    // 部分顾客离开队列
    customers = 8

    customers = customers + 3 // Example of addition: 11
    customers += 7            // Example of addition: 18
    customers -= 3            // Example of subtraction: 15
    customers *= 2            // Example of multiplication: 30
    customers /= 3            // Example of division: 10

    println(customers) // 10
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-basic-types-arithmetic"}

> `+=`、`-=`、`*=`、`/=` 和 `%=` 是增强赋值运算符。有关更多信息，请参阅 [增强赋值](operator-overloading.md#augmented-assignments)。


总的来说，Kotlin 有以下基本类型：

| **类别** | **基本类型**                           |
|--------|------------------------------------|
| 整型     | `Byte`, `Short`, `Int`, `Long`     |
| 无符号整数  | `UByte`, `UShort`, `UInt`, `ULong` |
| 浮点数    | `Float`, `Double`                  |
| 布尔值    | `Boolean`                          |
| 字符     | `Char`                             |
| 字符串    | `String`                           |

有关基本类型及其属性的更多信息，请参阅 [基本类型](basic-types.md)。

有了这些知识，您可以声明变量并稍后初始化它们。只要在第一次读取之前初始化变量，Kotlin 就能够管理这一点。

要声明一个未初始化的变量，请使用 `:` 指定其类型。

例如：

```kotlin
fun main() {
    // 声明变量但未初始化
    val d: Int
    // 变量已初始化
    d = 3

    // 变量显式类型化并初始化
    val e: String = "hello"

    // 变量可以被读取，因为它们已经被初始化了
    println(d) // 3
    println(e) // hello
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-basic-types-initialization"}

现在您已经了解如何声明基本类型，是时候学习有关 [集合](kotlin-tour-collections.md) 的知识了。

## 实践

### 练习 {collapsible="true"}

显式声明每个变量的正确类型：

|---|---|
```kotlin
fun main() {
    val a = 1000
    val b = "log message"
    val c = 3.14
    val d = 100_000_000_000_000
    val e = false
    val f = '\n'
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-basic-types-exercise"}

|---|---|
```kotlin
fun main() {
    val a: Int = 1000
    val b: String = "log message"
    val c: Double = 3.14
    val d: Long = 100_000_000_000
    val e: Boolean = false
    val f: Char = '\n'
}
```
{collapsible="true" collapsed-title="Example solution" id="kotlin-tour-basic-types-solution"}

## 下一步

[集合](kotlin-tour-collections.md)