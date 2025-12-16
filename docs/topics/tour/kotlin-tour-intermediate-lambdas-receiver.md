[//]: # (title: 中级：带接收者的 Lambda 表达式)

<no-index/>

<tldr>
    <p><img src="icon-1-done.svg" width="20" alt="第一步" /> <a href="kotlin-tour-intermediate-extension-functions.md">扩展函数</a><br />
        <img src="icon-2-done.svg" width="20" alt="第二步" /> <a href="kotlin-tour-intermediate-scope-functions.md">作用域函数</a><br />
        <img src="icon-3.svg" width="20" alt="第三步" /> <strong>带接收者的 Lambda 表达式</strong><br />
        <img src="icon-4-todo.svg" width="20" alt="第四步" /> <a href="kotlin-tour-intermediate-classes-interfaces.md">类和接口</a><br />
        <img src="icon-5-todo.svg" width="20" alt="第五步" /> <a href="kotlin-tour-intermediate-objects.md">对象</a><br />
        <img src="icon-6-todo.svg" width="20" alt="第六步" /> <a href="kotlin-tour-intermediate-open-special-classes.md">开放类和特殊类</a><br />
        <img src="icon-7-todo.svg" width="20" alt="第七步" /> <a href="kotlin-tour-intermediate-properties.md">属性</a><br />
        <img src="icon-8-todo.svg" width="20" alt="第八步" /> <a href="kotlin-tour-intermediate-null-safety.md">空安全</a><br />
        <img src="icon-9-todo.svg" width="20" alt="第九步" /> <a href="kotlin-tour-intermediate-libraries-and-apis.md">库和 API</a></p>
</tldr>

在本章中，你将学习如何在另一类函数 —— Lambda 表达式中使用接收者，以及它们如何帮助你创建领域特定语言（DSL）。

## 带接收者的 Lambda 表达式 {id=lambda-expressions-with-receiver}

在入门导览中，你学习了如何使用 [Lambda 表达式](kotlin-tour-functions.md#lambda-expressions)。Lambda 表达式也可以拥有接收者。
在这种情况下，Lambda 表达式可以访问接收者的任何成员函数或属性，而无需每次都显式指定接收者。
省略这些额外引用后，代码更易读且易于维护。

> 带接收者的 Lambda 表达式也称为 带接收者的函数字面量（function literals with receiver）。
>
{style="tip"}

在定义函数类型时，带接收者的 Lambda 表达式的语法有所不同。首先，写出你希望扩展的接收者类型。
然后加上一个 `.`，接着完成函数类型的剩余定义。例如：

```kotlin
MutableList<Int>.() -> Unit
```

该函数类型包含：

* `MutableList<Int>` 作为接收者。
* 括号 `()` 内没有函数参数。
* 无返回值：`Unit`。

请看下面这个在画布上绘制图形的示例：

```kotlin
class Canvas {
    fun drawCircle() = println("🟠 Drawing a circle")
    fun drawSquare() = println("🟥 Drawing a square")
}

// 带接收者的 Lambda 表达式定义
fun render(block: Canvas.() -> Unit): Canvas {
    val canvas = Canvas()
    // 使用带接收者的 Lambda 表达式
    canvas.block()
    return canvas
}

fun main() {
    render {
        drawCircle()
        // 🟠 Drawing a circle
        drawSquare()
        // 🟥 Drawing a square
    }
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-intermediate-tour-lambda-expression-with-receiver"}

在这个示例中：

* `Canvas` 类有两个函数，用于模拟绘制圆形或方形。
* `render()` 函数接收一个 `block` 参数，并返回一个 `Canvas` 类的实例。
* `block` 参数是一个带接收者的 Lambda 表达式，其中 `Canvas` 类作为接收者。
* `render()` 函数创建一个 `Canvas` 实例，并在该实例上调用 `block()` Lambda 表达式，将其作为接收者。
* `main()` 函数调用 `render()` 函数，并传入一个 Lambda 表达式作为 `block` 参数。
* 在传入 `render()` 的 Lambda 表达式内部，程序直接调用了 `Canvas` 实例的 `drawCircle()` 和 `drawSquare()` 函数。

由于 `drawCircle()` 和 `drawSquare()` 在带接收者的 Lambda 表达式中调用，它们可以直接被调用，就像在 `Canvas` 类内部一样。

带接收者的 Lambda 表达式在创建领域特定语言（DSL）时非常有用。
由于可以直接访问接收者的成员函数和属性而无需显式引用接收者，代码变得更加简洁。

为了演示这一点，我们来看一个配置菜单项的示例。
首先定义一个 `MenuItem` 类，以及一个包含添加菜单项函数 `item()` 和存储所有菜单项的列表 `items` 的 `Menu` 类：

```kotlin
class MenuItem(val name: String)

class Menu(val name: String) {
    val items = mutableListOf<MenuItem>()

    fun item(name: String) {
        items.add(MenuItem(name))
    }
}
```

我们可以将带接收者的 Lambda 表达式作为函数参数（`init`）传入 `menu()` 函数，用于构建菜单，作为起点：

```kotlin
fun menu(name: String, init: Menu.() -> Unit): Menu {
    // 创建 Menu 类的实例
    val menu = Menu(name)
    // 在该类实例上调用带接收者的 Lambda 表达式 init()
    menu.init()
    return menu
}
```

现在，你可以使用这个 DSL 来配置菜单，并创建一个 `printMenu()` 函数，将菜单结构打印到控制台：

```kotlin
class MenuItem(val name: String)

class Menu(val name: String) {
    val items = mutableListOf<MenuItem>()

    fun item(name: String) {
        items.add(MenuItem(name))
    }
}

fun menu(name: String, init: Menu.() -> Unit): Menu {
    val menu = Menu(name)
    menu.init()
    return menu
}

//sampleStart
fun printMenu(menu: Menu) {
    println("Menu: ${menu.name}")
    menu.items.forEach { println("  Item: ${it.name}") }
}

// 使用 DSL
fun main() {
    // 创建菜单
    val mainMenu = menu("Main Menu") {
        // 将项目添加到菜单
        item("Home")
        item("Settings")
        item("Exit")
    }

    // 打印菜单
    printMenu(mainMenu)
    // Menu: Main Menu
    //   Item: Home
    //   Item: Settings
    //   Item: Exit
}
//sampleEnd
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-intermediate-tour-lambda-expression-with-receiver-dsl"}

如你所见，使用带接收者的 Lambda 表达式可以大大简化创建菜单所需的代码。
Lambda 表达式不仅适用于对象的初始化和创建，也适用于配置。
它们在构建 API、UI 框架以及配置生成器的 DSL 时非常常用，能够生成简洁的代码，让你更专注于底层代码结构和逻辑。

Kotlin 生态系统中有许多这种设计模式的示例，例如标准库中的
[`buildList()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/build-list.html) 和
[`buildString()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/build-string.html) 函数。

> 带接收者的 Lambda 表达式可以与 Kotlin 的 **类型安全构建器（type-safe builders）** 结合使用，使 DSL 能在编译时而非运行时检测类型问题。
> 想了解更多，请参阅 [类型安全构建器](type-safe-builders.md)。
>
{style="tip"}

## 练习 {id=practice}

### 练习 1 {initial-collapse-state="collapsed" collapsible="true" id="lambda-receivers-exercise-1"}

你有一个 `fetchData()` 函数，它接受一个带接收者的 Lambda 表达式。
请更新 Lambda 表达式，使用 `append()` 函数，使代码输出为：`Data received - Processed`。

|---|---|
```kotlin
fun fetchData(callback: StringBuilder.() -> Unit) {
    val builder = StringBuilder("Data received")
    builder.callback()
}

fun main() {
    fetchData {
        // 在此处编写你的代码
        // Data received - Processed
    }
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-lambda-receivers-exercise-1"}

|---|---|
```kotlin
fun fetchData(callback: StringBuilder.() -> Unit) {
    val builder = StringBuilder("Data received")
    builder.callback()
}

fun main() {
    fetchData {
        append(" - Processed")
        println(this.toString())
        // Data received - Processed
    }
}
```
{initial-collapse-state="collapsed" collapsible="true" collapsed-title="Example solution" id="kotlin-tour-lambda-receivers-solution-1"}

### 练习 2 {initial-collapse-state="collapsed" collapsible="true" id="lambda-receivers-exercise-2"}

你有一个 `Button` 类，以及 `ButtonEvent` 和 `Position` 数据类。
编写代码触发 `Button` 类的 `onEvent()` 成员函数，使其触发双击事件，并打印 `"Double click!"`。

```kotlin
class Button {
    fun onEvent(action: ButtonEvent.() -> Unit) {
        // 模拟双击事件（不是右键点击）
        val event = ButtonEvent(isRightClick = false, amount = 2, position = Position(100, 200))
        event.action() // 触发事件回调
    }
}

data class ButtonEvent(
    val isRightClick: Boolean,
    val amount: Int,
    val position: Position
)

data class Position(
    val x: Int,
    val y: Int
)

fun main() {
    val button = Button()

    button.onEvent {
        // 在这里写下你的代码
        // Double click!
    }
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-lambda-receivers-exercise-2"}

|---|---|
```kotlin
class Button {
    fun onEvent(action: ButtonEvent.() -> Unit) {
        // 模拟双击事件（不是右键点击）
        val event = ButtonEvent(isRightClick = false, amount = 2, position = Position(100, 200))
        event.action() // 触发事件回调
    }
}

data class ButtonEvent(
    val isRightClick: Boolean,
    val amount: Int,
    val position: Position
)

data class Position(
    val x: Int,
    val y: Int
)

fun main() {
    val button = Button()
    
    button.onEvent {
        if (!isRightClick && amount == 2) {
            println("Double click!")
            // Double click!
        }
    }
}
```
{initial-collapse-state="collapsed" collapsible="true" collapsed-title="Example solution" id="kotlin-tour-lambda-receivers-solution-2"}

### 练习 3 {initial-collapse-state="collapsed" collapsible="true" id="lambda-receivers-exercise-3"}

编写一个函数，创建一个整数列表的副本，其中每个元素都加 1。使用提供的函数骨架扩展 `List<Int>` 并定义 `incremented` 函数。

```kotlin
fun List<Int>.incremented(): List<Int> {
    val originalList = this
    return buildList {
        // 在这里编写你的代码
    }
}

fun main() {
    val originalList = listOf(1, 2, 3)
    val newList = originalList.incremented()
    println(newList)
    // [2, 3, 4]
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-lambda-receivers-exercise-3"}

|---|---|
```kotlin
fun List<Int>.incremented(): List<Int> {
    val originalList = this
    return buildList {
        for (n in originalList) add(n + 1)
    }
}

fun main() {
    val originalList = listOf(1, 2, 3)
    val newList = originalList.incremented()
    println(newList)
    // [2, 3, 4]
}
```
{initial-collapse-state="collapsed" collapsible="true" collapsed-title="Example solution" id="kotlin-tour-lambda-receivers-solution-3"}

## 下一步

[中级：类和接口](kotlin-tour-intermediate-classes-interfaces.md)
