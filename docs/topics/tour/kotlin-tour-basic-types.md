[//]: # (title: 基本类型)

<no-index/>

<tldr>
    <p><img src="icon-1-done.svg" width="20" alt="First step" /> <a href="kotlin-tour-hello-world.md">Hello world</a><br />
        <img src="icon-2.svg" width="20" alt="Second step" /> <strong>Basic types</strong><br />
        <img src="icon-3-todo.svg" width="20" alt="Third step" /> <a href="kotlin-tour-collections.md">Collections</a><br />
        <img src="icon-4-todo.svg" width="20" alt="Fourth step" /> <a href="kotlin-tour-control-flow.md">Control flow</a><br />
        <img src="icon-5-todo.svg" width="20" alt="Fifth step" /> <a href="kotlin-tour-functions.md">Functions</a><br />
        <img src="icon-6-todo.svg" width="20" alt="Sixth step" /> <a href="kotlin-tour-classes.md">Classes</a><br />
        <img src="icon-7-todo.svg" width="20" alt="Final step" /> <a href="kotlin-tour-null-safety.md">Null safety</a></p>
</tldr>

在 Kotlin 中，每个变量和数据结构都有类型。
类型很重要，因为它告诉编译器你可以对该变量或数据结构做什么操作，也就是它有哪些函数和属性。

在上一章中的例子中，Kotlin 能够推断出 `customers` 变量的类型，即 [`Int`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-int/)。
Kotlin 的这种推断类型的能力被称为 **类型推断**。
`customers` 被赋予一个整数值。因此，Kotlin 推断出 `customers` 是数值类型 `Int`。
因此，编译器知道你可以对 `customers` 执行算术运算：

```kotlin
fun main() {
//sampleStart
    var customers = 10

    // 一些顾客离开队列
    customers = 8

    customers = customers + 3 // 加法示例：11
    customers += 7            // 加法示例：18
    customers -= 3            // 减法示例：15
    customers *= 2            // 乘法示例：30
    customers /= 3            // 除法示例：10

    println(customers) // 输出 10
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-basic-types-arithmetic"}

> `+=`、`-=`、`*=`、`/=` 和 `%=` 是扩展赋值运算符。想了解更多信息，请参阅 [扩展赋值](operator-overloading.md#augmented-assignments)。
> 
{style="tip"}

总的来说，Kotlin 具有以下基本类型：

| **类别**  | **基本类型**                        | **示例代码**                                                     |
|---------|---------------------------------|--------------------------------------------------------------|
| 整数类型    | `Byte`、`Short`、`Int`、`Long`     | `val year: Int = 2020`                                       |
| 无符号整数类型 | `UByte`、`UShort`、`UInt`、`ULong` | `val score: UInt = 100u`                                     |
| 浮点数类型   | `Float`、`Double`                | `val currentTemp: Float = 24.5f`，`val price: Double = 19.99` |
| 布尔类型    | `Boolean`                       | `val isEnabled: Boolean = true`                              |
| 字符类型    | `Char`                          | `val separator: Char = ','`                                  |
| 字符串类型   | `String`                        | `val message: String = "Hello, world!"`                      |

想要了解更多关于基本类型及其属性的信息，请参阅 [基本类型](basic-types.md)。

有了这些知识，你可以声明变量并在稍后初始化它们。
只要在第一次读取之前进行初始化，Kotlin 就可以管理这些变量。

要在不初始化的情况下声明变量，需要使用 `:` 指定其类型。例如：

```kotlin
fun main() {
//sampleStart
    // 未初始化的变量声明
    val d: Int
    // 变量初始化
    d = 3

    // 显式指定类型并初始化的变量
    val e: String = "hello"

    // 变量已经初始化，因此可以读取
    println(d) // 输出 3
    println(e) // 输出 hello
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-basic-types-initialization"}

如果在读取变量之前没有初始化它，将会出现错误：

```kotlin
fun main() {
//sampleStart
    // 未初始化的变量声明
    val d: Int
    
    // 触发错误
    println(d)
    // 报错：Variable 'd' must be initialized
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-basic-types-no-initialization" validate="false"}

现在你已经了解了如何声明基本类型，是时候学习关于 [集合](kotlin-tour-collections.md) 的知识了。

## 实践 {id=practice}

### 练习 {id="exercise" collapsible="true" collapsible="true"}

显式声明每个变量的正确类型：

|---|---|
```kotlin
fun main() {
    val a: Int = 1000 
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
    val d: Long = 100_000_000_000_000
    val e: Boolean = false
    val f: Char = '\n'
}
```
{collapsible="true" collapsible="true" collapsed-title="Example solution" id="kotlin-tour-basic-types-solution"}

## 下一步 {id=next-step}

[集合](kotlin-tour-collections.md)

