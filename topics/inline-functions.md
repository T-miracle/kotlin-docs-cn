[//]: # (title: 内联函数)

使用[高阶函数](lambdas.md)会带来一定的运行时损耗：每个函数都是一个对象，并且它捕获一个闭包。
闭包可以在函数体内访问外部变量的作用域。
函数对象和类的内存分配以及虚拟调用都引入了运行时开销。

但是，在许多情况下，这种开销可以通过内联 Lambda 表达式来消除。
下面显示的函数就是这种情况的良好示例。
`lock()` 函数可以很容易地在调用点内联。
考虑以下情况：

```kotlin
lock(l) { foo() }
```

编译器可以直接生成以下代码，而不是为参数创建一个函数对象并生成调用：

```kotlin
l.lock()
try {
    foo()
} finally {
    l.unlock()
}
```

要使编译器这样做，请使用 `inline` 修饰符标记 `lock()` 函数：

```kotlin
inline fun <T> lock(lock: Lock, body: () -> T): T { ... }
```

`inline` 修饰符影响函数本身及传递给它的 Lambda：所有这些都会被内联到调用点。

内联可能导致生成的代码增长。
然而，如果以合理的方式进行（避免内联大函数），它会在性能上有所回报，特别是在循环内的“多态（megamorphic）”调用点。

## `noinline` {id=noinline}

如果你不希望传递给内联函数的所有 Lambda 都被内联，请使用 `noinline` 修饰符标记一些函数参数：

```kotlin
inline fun foo(inlined: () -> Unit, noinline notInlined: () -> Unit) { ... }
```

可内联的 Lambda 只能在内联函数内部调用或作为可内联参数传递。`noinline` 的 Lambda 可以以任何方式操作，包括存储在字段中或传递。

> 如果一个内联函数没有可内联的函数参数，也没有[具化类型参数](#reified-type-parameters)，
> 编译器会发出警告，因为内联这样的函数不太可能有益（如果你确定需要内联，
> 可以使用 `@Suppress("NOTHING_TO_INLINE")` 注释来抑制警告）。
>
{style="note"}

## 非局部返回 {id=non-local-returns}

在 Kotlin 中，你只能使用普通的、未限定的 `return` 来退出一个命名函数或匿名函数。
要退出 lambda，请使用一个[标签](returns.md#返回到标签)。
在 lambda 内部禁止使用裸 `return`，因为 lambda 无法使包围它的函数 `return`：

```kotlin
fun ordinaryFunction(block: () -> Unit) {
    println("hi!")
}
//sampleStart
fun foo() {
    ordinaryFunction {
        return // ERROR: 无法让 `foo` 返回这里
    }
}
//sampleEnd
fun main() {
    foo()
}
```
{kotlin-runnable="true" validate="false"}

但是，如果传递给 lambda 的函数是内联的，`return` 也可以被内联。因此，这是允许的：

```kotlin
inline fun inlined(block: () -> Unit) {
    println("hi!")
}
//sampleStart
fun foo() {
    inlined {
        return // OK：lambda 是内联的
    }
}
//sampleEnd
fun main() {
    foo()
}
```
{kotlin-runnable="true"}

这种返回（位于 lambda 中，但退出包围它的函数）称为**非局部**返回。这种结构通常出现在内联函数常常包含的循环中：

```kotlin
fun hasZeros(ints: List<Int>): Boolean {
    ints.forEach {
        if (it == 0) return true // 从 hasZeros 返回
    }
    return false
}
```

请注意，一些内联函数可能不是直接从函数体中调用传递给它们的 Lambda，而是从另一个执行上下文（例如本地对象或嵌套函数）中调用。
在这种情况下，Lambda 中的非局部控制流也是不允许的。
要表示内联函数的 Lambda 参数不能使用非局部返回，请使用 `crossinline` 修饰符标记 Lambda 参数：

```kotlin
inline fun f(crossinline body: () -> Unit) {
    val f = object: Runnable {
        override fun run() = body()
    }
    // ...
}
```

> `break` 和 `continue` 在内联 Lambda 中尚不可用，但我们计划支持它们。
>
{style="note"}

## 具化类型参数 {id=reified-type-parameters}

有时候你需要访问作为参数传递的类型：

```kotlin
fun <T> TreeNode.findParentOfType(clazz: Class<T>): T? {
    var p = parent
    while (p != null && !clazz.isInstance(p)) {
        p = p.parent
    }
    @Suppress("UNCHECKED_CAST")
    return p as T?
}
```

在这里，你遍历一棵树并使用反射来检查节点是否具有某种类型。这样是可以的，但调用点不是很优雅：

```kotlin
treeNode.findParentOfType(MyTreeNode::class.java)
```

更好的解决方案是简单地将类型传递给此函数。你可以这样调用它：

```kotlin
treeNode.findParentOfType<MyTreeNode>()
```

为了实现这一点，内联函数支持**具化类型参数**，所以你可以这样写：

```kotlin
inline fun <reified T> TreeNode.findParentOfType(): T? {
    var p = parent
    while (p != null && p !is T) {
        p = p.parent
    }
    return p as T?
}
```

上面的代码使用 `reified` 修饰符限定类型参数，使其在函数内部可访问，几乎就像它是一个普通的类一样。
由于函数是内联的，不需要反射，现在可以使用正常的操作符，如 `!is` 和 `as`。
你也可以像上面所示那样调用该函数：`myTree.findParentOfType<MyTreeNodeType>()`。

尽管在许多情况下可能不需要反射，但你仍然可以使用具化类型参数进行反射：

```kotlin
inline fun <reified T> membersOf() = T::class.members

fun main(s: Array<String>) {
    println(membersOf<StringBuilder>().joinToString("\n"))
}
```

普通函数（未标记为内联的）不能具有具化参数。没有运行时表示的类型（例如，非具化类型参数或像 `Nothing` 这样的虚构类型）不能用作具化类型参数的实参。

## 内联属性 {id=inline-properties}

`inline` 修饰符可以用于没有[幕后字段](properties.md#幕后字段)的属性访问器。你可以注解单个属性访问器：

```kotlin
val foo: Foo
    inline get() = Foo()

var bar: Bar
    get() = ...
    inline set(v) { ... }
```

你也可以注解整个属性，这会将它的两个访问器都标记为 `inline`：

```kotlin
inline var bar: Bar
    get() = ...
    set(v) { ... }
```

在调用点，内联访问器会像普通的内联函数一样被内联。

## 公共 API 内联函数的限制 {id=restrictions-for-public-api-inline-functions}

当一个内联函数是 `public` 或 `protected`，但不是 `private` 或 `internal`
声明的一部分时，它被视为一个[模块](visibility-modifiers.md#模块)的公共 API。
它可以在其他模块中被调用，并且在这些调用点内联。

这带来了某些二进制不兼容的风险，当声明内联函数的模块发生变化而调用模块没有重新编译时，可能会引发不兼容问题。

为了消除这种风险，公共 API 内联函数不允许在其主体中使用非公共 API 声明，即 `private` 和 `internal` 声明及其部分。

一个 `internal` 声明可以用 `@PublishedApi` 注解，这样就允许在公共 API 内联函数中使用它。当一个 `internal` 
内联函数被标记为 `@PublishedApi` 时，它的主体也会被检查，就像它是 `public` 一样。