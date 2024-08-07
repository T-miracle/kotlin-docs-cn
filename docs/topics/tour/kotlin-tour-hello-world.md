[//]: # (title: Hello world)

<tldr>
    <p><img src="icon-1.svg" width="20" alt="First step" /> <strong>Hello world</strong><br />
        <img src="icon-2-todo.svg" width="20" alt="Second step" /> <a href="kotlin-tour-basic-types.md">基本类型</a><br />
        <img src="icon-3-todo.svg" width="20" alt="Third step" /> <a href="kotlin-tour-collections.md">集合</a><br />
        <img src="icon-4-todo.svg" width="20" alt="Fourth step" /> <a href="kotlin-tour-control-flow.md">控制流</a><br />
        <img src="icon-5-todo.svg" width="20" alt="Fifth step" /> <a href="kotlin-tour-functions.md">函数</a><br />
        <img src="icon-6-todo.svg" width="20" alt="Sixth step" /> <a href="kotlin-tour-classes.md">类</a><br />
        <img src="icon-7-todo.svg" width="20" alt="Final step" /> <a href="kotlin-tour-null-safety.md">空值安全</a></p>
</tldr>

这是一个打印“Hello, world!”的简单程序：

```kotlin
fun main() {
    println("Hello, world!")
    // Hello, world!
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="hello-world-kotlin"}

在 Kotlin 中：
* 使用 `fun` 声明函数
* `main()` 函数是程序的起点
* 函数体写在花括号 `{}` 中
* [`println()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.io/println.html) 和 [`print()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.io/print.html) 函数将它们的参数打印到标准输出

> 函数将在后面的章节中详细讨论。在此之前，所有的示例都使用 `main()` 函数。
> 
{style="note"}

## 变量

所有的程序都需要能够存储数据，而变量正是帮助你实现这一目标的工具。在 Kotlin 中，你可以声明：
* 使用 `val` 关键字声明只读变量
* 使用 `var` 关键字声明可变变量

要赋值，使用赋值运算符 `=`。

例如：

```kotlin
fun main() { 
    val popcorn = 5    // 有5盒爆米花
    val hotdog = 7     // 有7个热狗
    var customers = 10 // 有10位顾客在排队
    
    // 部分顾客离开队列
    customers = 8
    println(customers)
    // 8
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-variables"}

> 变量可以在 `main()` 函数之外在程序的开头声明。
> 以这种方式声明的变量被称为在 **顶层** 声明。


由于 `customers` 是可变变量，它的值可以在声明后重新赋值。

> 我们建议默认情况下将所有变量声明为只读 (`val`)。
> 只有在必要时才声明可变变量 (`var`)。
> 
{style="note"}

## 字符串模板

了解如何将变量的内容打印到标准输出是很有用的。您可以使用**字符串模板**来完成这个任务。
您可以使用模板表达式访问存储在变量和其他对象中的数据，并将它们转换为字符串。
字符串值是在双引号 `"` 中的字符序列。模板表达式总是以美元符号 `$` 开头。

要在模板表达式中评估一段代码，请在美元符号 `$` 后的花括号 `{}` 中放置代码。

例如：

```kotlin
fun main() {
    val customers = 10
    println("There are $customers customers")
    // There are 10 customers
    
    println("There are ${customers + 1} customers")
    // There are 11 customers
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-string-templates"}

有关更多信息，请参阅 [字符串模板](strings.md)。

您会注意到变量没有声明任何类型。Kotlin 自己已经推断出类型：`Int`。本教程将在 [下一章节](kotlin-tour-basic-types.md) 解释不同的 Kotlin 基本类型以及如何声明它们。

## 实践

### 练习 {collapsible="true"}

完成以下代码，使程序将 `"Mary is 20 years old"` 打印到标准输出：

|---|---|
```kotlin
fun main() {
    val name = "Mary"
    val age = 20
    // 在这里写下你的代码
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-hello-world-exercise"}

|---|---|
```kotlin
fun main() {
    val name = "Mary"
    val age = 20
    println("$name is $age years old")
}
```
{collapsible="true" collapsed-title="Example solution" id="kotlin-tour-hello-world-solution"}

## 下一步

[基本类型](kotlin-tour-basic-types.md)