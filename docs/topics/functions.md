[//]: # (title: 函数)

Kotlin **函数** 使用 `fun` 关键字声明：

```kotlin
fun double(x: Int): Int {
    return 2 * x
}
```

## 函数用法

函数使用标准的方式调用：

```kotlin
val result = double(2)
```

调用成员函数使用点符号：

```kotlin
Stream().read() // 创建 Stream 类的实例并调用 read() 函数
```

### 参数

函数参数使用 Pascal 表示法定义 - **name**: **type**。参数用逗号分隔，并且每个参数必须显式指定类型：

```kotlin
fun powerOf(number: Int, exponent: Int): Int { /*...*/ }
```

在声明函数参数时，可以使用[尾随逗号](coding-conventions.md#trailing-commas)：

```kotlin
fun powerOf(
    number: Int,
    exponent: Int, // 尾随逗号
) { /*...*/ }
```

### 默认参数 {id=default-arguments}

函数参数可以有默认值，当跳过相应的实参时，将使用默认值。这减少了重载的数量：

```kotlin
fun read(
    b: ByteArray,
    off: Int = 0,
    len: Int = b.size,
) { /*...*/ }
```

默认值通过在类型后附加 `=` 来设置。

重写函数时总是使用基函数的默认参数值。当重写一个具有默认参数值的函数时，必须在签名中省略默认参数值：

```kotlin
open class A {
    open fun foo(i: Int = 10) { /*...*/ }
}

class B : A() {
    override fun foo(i: Int) { /*...*/ }  // 不允许使用默认值
}
```

如果默认参数出现在没有默认值的参数之前，只有通过[具名参数](#named-arguments)调用函数时才能使用默认值：

```kotlin
fun foo(
    bar: Int = 0,
    baz: Int,
) { /*...*/ }

foo(baz = 1) // 使用默认值 bar = 0
```

如果默认参数之后的最后一个参数是一个[lambda](lambdas.md#lambda-expression-syntax)，可以将其作为具名函数传递，或者[在括号外传递](lambdas.md#passing-trailing-lambdas)：

```kotlin
fun foo(
    bar: Int = 0,
    baz: Int = 1,
    qux: () -> Unit,
) { /*...*/ }

foo(1) { println("hello") }     // 使用默认值 baz = 1
foo(qux = { println("hello") }) // 使用默认值 bar = 0 和 baz = 1
foo { println("hello") }        // 使用默认值 bar = 0 和 baz = 1
```

### 具名参数 {id=named-arguments}

调用函数时，可以给一个或多个参数命名。当一个函数有很多参数且难以将值与参数关联时，这尤其有用，特别是当参数是布尔值或 `null` 值时。

在函数调用中使用具名函数时，可以随意更改参数的顺序。如果想使用它们的默认值，可以直接省略这些参数。

考虑具有四个默认值参数的 `reformat()` 函数。

```kotlin
fun reformat(
    str: String,
    normalizeCase: Boolean = true,
    upperCaseFirstLetter: Boolean = true,
    divideByCamelHumps: Boolean = false,
    wordSeparator: Char = ' ',
) { /*...*/ }
```

调用此函数时，不必为所有参数命名：

```kotlin
reformat(
    "String!",
    false,
    upperCaseFirstLetter = false,
    divideByCamelHumps = true,
    '_'
)
```

可以跳过所有带默认值的参数：

```kotlin
reformat("This is a long String!")
```

也可以跳过特定的带默认值的参数，而不是全部省略。然而，在第一个跳过的参数之后，必须为所有后续参数命名：

```kotlin
reformat("This is a short String!", upperCaseFirstLetter = false, wordSeparator = '_')
```

可以使用 `展开` 运算符为[可变数量的参数 (`vararg`)](#可变参数数量varargs)传递具名函数：

```kotlin
fun foo(vararg strings: String) { /*...*/ }

foo(strings = *arrayOf("a", "b", "c"))
```

> 在 JVM 上调用 Java 函数时，不能使用具名函数语法，因为 Java 字节码不总是保留函数参数的名称。
>
{style="note"}

### 返回 `Unit` 的函数 {id=返回Unit的函数}

如果函数不返回有用的值，其返回类型为 `Unit`。`Unit` 是只有一个值的类型——`Unit`。这个值不必显式返回：

```kotlin
fun printHello(name: String?): Unit {
    if (name != null)
        println("Hello $name")
    else
        println("Hi there!")
    // `return Unit` 或 `return` 是可选的
}
```

`Unit` 返回类型的声明也是可选的。上述代码等价于：

```kotlin
fun printHello(name: String?) { ... }
```

### 单表达式函数 {id=single-expression-functions}

当函数体只包含一个表达式时，可以省略花括号，并在 `=` 符号之后指定函数体：

```kotlin
fun double(x: Int): Int = x * 2
```

当返回类型可以被编译器推断时，[显式声明返回类型](#显式返回类型)是可选的：

```kotlin
fun double(x: Int) = x * 2
```

### 显式返回类型 {id=显式返回类型}

对于具有块体的函数，必须始终显式指定返回类型，除非它们返回 `Unit`，[在这种情况下，指定返回类型是可选的](#返回Unit的函数)。

Kotlin 不会为具有块体的函数推断返回类型，因为此类函数的控制流可能很复杂，返回类型对于读者（有时甚至对于编译器）来说可能并不明显。

### 可变数量的参数（varargs） {id=可变参数数量varargs}

你可以用 `vararg` 修饰符标记函数的一个参数（通常是最后一个）：

```kotlin
fun <T> asList(vararg ts: T): List<T> {
    val result = ArrayList<T>()
    for (t in ts) // ts 是一个数组
        result.add(t)
    return result
}
```

在这种情况下，你可以向函数传递可变数量的参数：

```kotlin
val list = asList(1, 2, 3)
```

在函数内部，类型为 `T` 的 `vararg` 参数可以作为 `T` 类型的数组使用，如上例所示，其中 `ts` 变量的类型为 `Array<out T>`。

只有一个参数可以标记为 `vararg`。
如果 `vararg` 参数不是列表中的最后一个参数，则可以使用具名函数语法传递后续参数的值，或者如果参数具有函数类型，则可以通过在括号外传递 lambda 来实现。

调用 `vararg` 函数时，你可以单独传递参数，例如 `asList(1, 2, 3)`。
如果已经有一个数组并且想将其内容传递给函数，请使用*展开*操作符（在数组前加 `*`）：

```kotlin
val a = arrayOf(1, 2, 3)
val list = asList(-1, 0, *a, 4)
```

如果想将一个[原始类型数组](arrays.md#原始类型数组)传递给 `vararg`，需要使用 `toTypedArray()` 函数将其转换为常规（类型化）数组：

```kotlin
val a = intArrayOf(1, 2, 3) // IntArray 是原始类型数组
val list = asList(-1, 0, *a.toTypedArray(), 4)
```

### 中缀表示符号（Infix Notation）

用 `infix` 关键字标记的函数也可以使用中缀符号调用（省略点号和调用的括号）。中缀函数必须满足以下要求：

* 必须是成员函数或[扩展函数](extensions.md)。
* 必须只有一个参数。
* 该参数不能[接受可变数量的参数](#可变参数数量varargs)且不能有[默认值](#default-arguments)。

```kotlin
infix fun Int.shl(x: Int): Int { ... }

// 使用中缀符号调用函数
1 shl 2

// 等同于
1.shl(2)
```

> 中缀函数调用的优先级低于算术运算符、类型转换和 `rangeTo` 操作符。以下表达式是等效的：
> * `1 shl 2 + 3` 等同于 `1 shl (2 + 3)`
> * `0 until n * 2` 等同于 `0 until (n * 2)`
> * `xs union ys as Set<*>` 等同于 `xs union (ys as Set<*>)`
>
> 另一方面，中缀函数调用的优先级高于布尔运算符 `&&` 和 `||`、`is` 和 `in` 检查，以及其他一些运算符。这些表达式也等效：
> * `a && b xor c` 等同于 `a && (b xor c)`
> * `a xor b in c` 等同于 `(a xor b) in c`
>
{style="note"}

注意中缀函数总是要求指定接收者和参数。当你使用中缀符号调用当前接收者的函数时，需要显式使用 `this`。这是为了确保解析无歧义。

```kotlin
class MyStringCollection {
    infix fun add(s: String) { /*...*/ }
    
    fun build() {
        this add "abc"   // 正确
        add("abc")       // 正确
        //add "abc"        // 错误：必须指定接收者
    }
}
```

## 函数作用域 {id=function-scope}

Kotlin 中的函数可以在文件的顶层声明，这意味着你不需要像在 Java、C# 和 Scala 等语言中那样创建一个类来容纳函数（Scala 3 起提供顶层定义
【[Scala 3 顶层定义](https://docs.scala-lang.org/scala3/book/taste-toplevel-definitions.html#inner-main)】）。
除了顶层函数，Kotlin 函数还可以在本地声明为成员函数和扩展函数。

### 局部函数 {id=局部函数}

Kotlin 支持局部函数，即在其他函数内部定义的函数：

```kotlin
fun dfs(graph: Graph) {
    fun dfs(current: Vertex, visited: MutableSet<Vertex>) {
        if (!visited.add(current)) return
        for (v in current.neighbors)
            dfs(v, visited)
    }

    dfs(graph.vertices[0], HashSet())
}
```

局部函数可以访问外部函数的局部变量（闭包）。在上面的例子中，`visited` 可以是一个局部变量：

```kotlin
fun dfs(graph: Graph) {
    val visited = HashSet<Vertex>()
    fun dfs(current: Vertex) {
        if (!visited.add(current)) return
        for (v in current.neighbors)
            dfs(v)
    }

    dfs(graph.vertices[0])
}
```

### 成员函数 {id=成员函数}

成员函数是在类或对象内部定义的函数：

```kotlin
class Sample {
    fun foo() { print("Foo") }
}
```

成员函数使用点符号进行调用：

```kotlin
Sample().foo() // 创建 Sample 类的实例并调用 foo
```

有关类和覆盖成员的更多信息，请参阅[类](classes.md)和[继承](classes.md#继承)。

## 泛型函数

函数可以具有泛型参数，使用尖括号在函数名之前指定：

```kotlin
fun <T> singletonList(item: T): List<T> { /*...*/ }
```

有关泛型函数的更多信息，请参阅[泛型](generics.md)。

## 尾递归函数 {id=尾递归函数}

Kotlin支持一种称为[尾递归](https://en.wikipedia.org/wiki/Tail_call)的函数式编程风格。
对于一些通常使用循环的算法，您可以使用递归函数代替，而不会出现堆栈溢出的风险。
当函数标记为`tailrec`修饰符并满足所需的形式条件时，编译器会优化递归，留下一个快速而高效的基于循环的版本：

```kotlin
val eps = 1E-10 // "足够好"，可以是10^-15

tailrec fun findFixPoint(x: Double = 1.0): Double =
    if (Math.abs(x - Math.cos(x)) < eps) x else findFixPoint(Math.cos(x))
```

这段代码计算了余弦的`fixpoint`，这是一个数学常量。
它简单地重复调用`Math.cos`，从`1.0`开始，直到结果不再改变，对于指定的`eps`精度，得到的结果是`0.7390851332151611`。
生成的代码相当于这种更传统的风格：

```kotlin
val eps = 1E-10 // "足够好"，可以是10^-15

private fun findFixPoint(): Double {
    var x = 1.0
    while (true) {
        val y = Math.cos(x)
        if (Math.abs(x - y) < eps) return x
        x = Math.cos(x)
    }
}
```

要成为`tailrec`修饰符的候选项，函数必须在执行的最后操作中调用自身。
在递归调用之后不能使用尾递归，不能在`try`/`catch`/`finally`块内使用，也不能在开放函数中使用。
目前，Kotlin支持JVM和Kotlin/Native的尾递归。

**另请参阅**：
- [内联函数](inline-functions.md)
- [扩展函数](extensions.md)
- [高阶函数和Lambda](lambdas.md)

