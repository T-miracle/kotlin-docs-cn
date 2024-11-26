[//]: # (title: 高阶函数和 lambda 表达式)

Kotlin 函数是[一等公民](https://en.wikipedia.org/wiki/First-class_function)，这意味着它们可以存储在变量和数据结构中，并且可以作为参数传递给其他[高阶函数](#higher-order-functions)，也可以从其他高阶函数中返回。
你可以对函数执行任何操作，就像对其他非函数值进行的操作一样。

为了方便这一点，Kotlin 作为一种静态类型的编程语言，使用一系列[函数类型](#function-types)来表示函数，并提供一套专门的语言构造，比如[lambda 表达式](#lambda-expressions-and-anonymous-functions)。

## 高阶函数 {id=higher-order-functions}

高阶函数是一种接受函数作为参数或返回函数的函数。

一个非常好的高阶函数例子是集合操作中的[函数式编程风格的`fold`](https://en.wikipedia.org/wiki/Fold_(higher-order_function))。
`Fold`函数接受一个初始值和一个组合函数，然后通过遍历集合中的每个元素，将初始值与当前元素进行组合，并不断更新这个初始值，直到遍历完所有元素，最终返回累加的结果。

```kotlin
fun <T, R> Collection<T>.fold(
    initial: R, 
    combine: (acc: R, nextElement: T) -> R
): R {
    var accumulator: R = initial
    for (element: T in this) {
        accumulator = combine(accumulator, element)
    }
    return accumulator
}
```

[//]: # (TODO 上次校对位置)

在上面的代码中，`combine` 参数具有[函数类型](#function-types) `(R, T) -> R`，因此它接受一个函数，该函数接受类型为 `R` 和 `T` 的两个参数，并返回类型为 `R` 的值。
它在`for`循环内[被调用](#invoking-a-function-type-instance)，然后将返回值分配给`accumulator`。

要调用 `fold`，你需要将一个[函数类型的实例](#instantiating-a-function-type)作为参数传递给它，lambda 表达式（[下文将更详细地描述](#lambda-expressions-and-anonymous-functions)）在高阶函数调用点广泛使用：

```kotlin
fun main() {
    //sampleStart
    val items = listOf(1, 2, 3, 4, 5)
    
    // Lambda 是用花括号括起来的代码块。
    items.fold(0, { 
        // 当 lambda 有参数时，它们放在前面，后跟 '->'
        acc: Int, i: Int -> 
        print("acc = $acc, i = $i, ") 
        val result = acc + i
        println("result = $result")
        // lambda 中的最后一个表达式被认为是返回值：
        result
    })
    
    // 如果 lambda 的参数可以推断出类型，则 lambda 中的参数类型是可选的：
    val joinedToString = items.fold("Elements:", { acc, i -> acc + " " + i })
    
    // 函数引用也可以用于高阶函数调用：
    val product = items.fold(1, Int::times)
    //sampleEnd
    println("joinedToString = $joinedToString")
    println("product = $product")
}
```
{kotlin-runnable="true"}

## 函数类型 {id=function-types}

Kotlin 使用函数类型，比如 `(Int) -> String`，来声明处理函数的声明：`val onClick: () -> Unit = ...`。

这些类型有一种特殊的表示法，对应于函数的签名 - 它们的参数和返回值：

- 所有函数类型都有一个带括号的参数类型列表和一个返回类型：`(A, B) -> C` 表示一个代表接受两个类型为 `A` 和 `B` 的参数并返回类型为 `C` 的值的函数类型。
  参数类型列表可以为空，比如 `() -> A`。[`Unit` 返回类型](functions.md#返回Unit的函数)不能省略。

- 函数类型可以选择性地有一个额外的 **接收者** 类型，在表示法中的点之前指定：类型 `A.(B) -> C` 表示可以在接收者对象 `A` 上调用带有参数 `B` 并返回值 `C` 的函数。
  [具有接收者的函数字面值](#function-literals-with-receiver)经常与这些类型一起使用。

- [挂起函数](coroutines-basics.md#extract-function-refactoring)属于一种特殊类型的函数类型，其表示法中有一个 **suspend** 修饰符，比如 `suspend () -> Unit` 或 `suspend A.(B) -> C`。

函数类型表示法还可以选择性地包括函数参数的名称：`(x: Int, y: Int) -> Point`。这些名称可用于记录参数的含义。

要指定函数类型为[可空类型](null-safety.md#nullable-types-and-non-nullable-types)，使用括号如下：`((Int, Int) -> Int)?`。

函数类型也可以使用括号进行组合：`(Int) -> ((Int) -> Unit)`。

> 箭头表示法是右结合的，`(Int) -> (Int) -> Unit` 等同于前面的例子，但不等同于 `((Int) -> (Int)) -> Unit`。
>
{style="note"}

你还可以使用 [类型别名](type-aliases.md) 给函数类型取一个别名：

```kotlin
typealias ClickHandler = (Button, ClickEvent) -> Unit
```

### 实例化函数类型 {id=instantiating-a-function-type}

有几种方法可以获得函数类型的实例：

* 在函数字面值中使用代码块，有以下几种形式之一：
  * [lambda 表达式](#lambda-expressions-and-anonymous-functions)：`{ a, b -> a + b }`,
  * [匿名函数](#anonymous-functions)：`fun(s: String): Int { return s.toIntOrNull() ?: 0 }`

  [具有接收者的函数字面值](#function-literals-with-receiver)可以作为具有接收者的函数类型的值使用。

* 使用对现有声明的可调用引用：
  * 顶层、局部、成员或扩展[函数](reflection.md#function-references)：`::isOdd`、`String::toInt`,
  * 顶层、成员或扩展[属性](reflection.md#property-references)：`List<Int>::size`,
  * [构造函数](reflection.md#constructor-references)：`::Regex`

  这些包括指向特定实例的成员的[绑定可调用引用](reflection.md#bound-function-and-property-references)：`foo::toString`。

* 使用自定义类的实例来实现函数类型作为接口：

```kotlin
class IntTransformer: (Int) -> Int {
    override operator fun invoke(x: Int): Int = TODO()
}

val intFunction: (Int) -> Int = IntTransformer()
```

如果有足够的信息，编译器可以推断出变量的函数类型：

```kotlin
val a = { i: Int -> i + 1 } // 推断出的类型是 (Int) -> Int
```

具有接收者和不具有接收者的函数类型的*非字面*值是可互换的，因此接收者可以代替第一个参数，反之亦然。
例如，类型为 `(A, B) -> C` 的值可以在期望类型为 `A.(B) -> C` 的位置传递或赋值，反之亦然：

```kotlin
fun main() {
    //sampleStart
    val repeatFun: String.(Int) -> String = { times -> this.repeat(times) }
    val twoParameters: (String, Int) -> String = repeatFun // OK
    
    fun runTransformation(f: (String, Int) -> String): String {
        return f("hello", 3)
    }
    val result = runTransformation(repeatFun) // OK
    //sampleEnd
    println("result = $result")
}
```
{kotlin-runnable="true"}

> 默认情况下推断不具有接收者的函数类型，即使变量被初始化为扩展函数的引用也是如此。
> 要更改这一点，请明确指定变量类型。
>
{style="note"}

### 调用函数类型实例 {id=invoking-a-function-type-instance}

可以使用其 [`invoke(...)` 运算符](operator-overloading.md#invoke-operator) 来调用函数类型的值：`f.invoke(x)` 或者简写为 `f(x)`。

如果该值具有接收者类型，则应将接收者对象作为第一个参数传递。
调用具有接收者类型的函数类型值的另一种方法是在接收者对象之前添加它，就像该值是一个[扩展函数](extensions.md)一样：`1.foo(2)`。

示例：

```kotlin
fun main() {
    //sampleStart
    val stringPlus: (String, String) -> String = String::plus
    val intPlus: Int.(Int) -> Int = Int::plus
    
    println(stringPlus.invoke("<-", "->"))
    println(stringPlus("Hello, ", "world!"))
    
    println(intPlus.invoke(1, 1))
    println(intPlus(1, 2))
    println(2.intPlus(3)) // extension-like call
    //sampleEnd
}
```
{kotlin-runnable="true"}

### 内联函数 {id=inline-functions}

有时候，为了高阶函数提供灵活的控制流，使用[内联函数](inline-functions.md)是很有益的。

## Lambda 表达式和匿名函数 {id=lambda-expressions-and-anonymous-functions}

Lambda 表达式和匿名函数都是 *函数字面值*。函数字面值是不声明但立即作为表达式传递的函数。考虑以下示例：

```kotlin
max(strings, { a, b -> a.length < b.length })
```

函数 `max` 是一个高阶函数，因为它将一个函数值作为其第二个参数。
这第二个参数是一个表达式，它本身就是一个函数，称为函数字面值，相当于以下命名函数：

```kotlin
fun compare(a: String, b: String): Boolean = a.length < b.length
```

### Lambda 表达式语法 {id=lambda-expression-syntax}

Lambda 表达式的完整语法形式如下：

```kotlin
val sum: (Int, Int) -> Int = { x: Int, y: Int -> x + y }
```

* Lambda 表达式始终用花括号括起来。
* 完整语法形式中的参数声明位于花括号内，并且具有可选的类型注解。
* 箭头 `->` 后面是函数体。
* 如果 lambda 的推断返回类型不是 `Unit`，那么 lambda 主体内最后的（或可能是单个的）表达式被视为返回值。

如果省略所有可选的注解，剩下的形式如下：

```kotlin
val sum = { x: Int, y: Int -> x + y }
```

### 传递尾随 Lambda {id=passing-trailing-lambdas}

根据 Kotlin 的约定，如果函数的最后一个参数是一个函数，那么作为相应参数传递的 lambda 表达式可以放在括号外面：

```kotlin
val product = items.fold(1) { acc, e -> acc * e }
```

这样的语法也被称为 *尾随 lambda*。

如果 lambda 是该调用中的唯一参数，那么括号可以完全省略：

```kotlin
run { println("...") }
```

### `it`: 单个参数的隐式名称 {id=it-implicit-name-of-a-single-parameter}

一个 lambda 表达式只有一个参数是非常常见的。

如果编译器可以在没有任何参数的情况下解析签名，那么参数就不需要声明，`->` 可以省略。该参数将隐式地以名称 `it` 声明：

```kotlin
ints.filter { it > 0 } // 这个字面量的类型是 '(it: Int) -> Boolean'
```

### 从 lambda 表达式返回值 {id=returning-a-value-from-a-lambda-expression}

你可以使用 [qualified return](returns.md#返回到标签) 语法来显式地从 lambda 返回值。
否则，最后一个表达式的值将隐式返回。

因此，以下两个代码片段是等价的：

```kotlin
ints.filter {
    val shouldFilter = it > 0
    shouldFilter
}

ints.filter {
    val shouldFilter = it > 0
    return@filter shouldFilter
}
```

这个约定，以及 [将 lambda 表达式放在括号外传递](#passing-trailing-lambdas) ，使得可以编写[LINQ-style](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/linq/)代码：

```kotlin
strings.filter { it.length == 5 }.sortedBy { it }.map { it.uppercase() }
```

### 下划线用于未使用的变量 {id=underscore-for-unused-variables}

如果 lambda 参数未使用，你可以用下划线代替其名称：

```kotlin
map.forEach { (_, value) -> println("$value!") }
```

### lambda 中的解构 {id=destructuring-in-lambdas}

lambda 中的解构被描述为 [解构声明](destructuring-declarations.md#destructuring-in-lambdas) 的一部分。

### 匿名函数 {id=anonymous-functions}

上面的 lambda 表达式语法缺少一样东西 - 指定函数返回类型的能力。在大多数情况下，这是不必要的，因为返回类型可以自动推断。
但是，如果你确实需要明确指定它，可以使用另一种语法：**匿名函数**。

```kotlin
fun(x: Int, y: Int): Int = x + y
```

匿名函数看起来非常像常规函数声明，只是省略了它的名称。它的主体可以是一个表达式（如上所示）或一个块：

```kotlin
fun(x: Int, y: Int): Int {
    return x + y
}
```

参数和返回类型的指定方式与常规函数相同，只是如果它们可以从上下文中推断出，则参数类型可以省略：

```kotlin
ints.filter(fun(item) = item > 0)
```

对于匿名函数，返回类型的推断与常规函数一样工作：对于具有表达式主体的匿名函数，返回类型会自动推断，但对于具有块主体的匿名函数，必须明确指定返回类型（或假定为 `Unit`）。

> 当将匿名函数作为参数传递时，请将它们放在括号内。允许你将函数放在括号外的简写语法仅适用于 lambda 表达式。
>
{style="note"}

lambda 表达式和匿名函数之间的另一个区别是[非局部返回](inline-functions.md#non-local-returns)的行为。
没有标签的 `return` 语句总是从使用 `fun` 关键字声明的函数中返回。
这意味着 lambda 表达式中的 `return` 将从包含它的函数返回，而匿名函数中的 `return` 将从匿名函数本身返回。

### 闭包 {id=closures}

lambda 表达式或匿名函数（以及 [局部函数](functions.md#局部函数) 和 [对象表达式](object-declarations.md#object-expressions)）可以访问其*闭包*，其中包括外部作用域中声明的变量。在闭包中捕获的变量可以在 lambda 中进行修改：

```kotlin
var sum = 0
ints.filter { it > 0 }.forEach {
    sum += it
}
print(sum)
```

### 带接收者的函数字面值 {id=function-literals-with-receiver}

带接收者的 [函数类型](#function-types)，如 `A.(B) -> C`，可以使用特殊形式的函数字面值——带接收者的函数字面值来实例化。

如上所述，Kotlin 提供了在提供 **接收者对象** 的同时调用函数类型实例的能力。

在函数字面值的主体内，传递给调用的接收者对象成为 **隐式** 的 `this`，因此您可以在函数体内访问该接收者对象的成员，而无需任何额外的限定符，或者使用 [`this` 表达式](this-expressions.md) 访问接收者对象。

这种行为类似于 [扩展函数](extensions.md) 的行为，后者也允许您在函数体内访问接收者对象的成员。

下面是一个带接收者的函数字面值及其类型的示例，其中在接收者对象上调用 `plus`：

```kotlin
val sum: Int.(Int) -> Int = { other -> plus(other) }
```

匿名函数语法允许您直接指定函数字面值的接收者类型。
如果需要声明带接收者的函数类型的变量，然后稍后使用它，这可能很有用。

```kotlin
val sum = fun Int.(other: Int): Int = this + other
```

当接收者类型可以从上下文中推断出时，lambda 表达式可以用作带接收者的函数字面值。
它们的使用中最重要的例子之一是 [类型安全的构建器](type-safe-builders.md)：

```kotlin
class HTML {
    fun body() { ... }
}

fun html(init: HTML.() -> Unit): HTML {
    val html = HTML()  // 创建接收者对象
    html.init()        // 将接收者对象传递给 lambda
    return html
}

html {       // 带接收者的 lambda 从这里开始
    body()   // 在接收者对象上调用方法
}
```


