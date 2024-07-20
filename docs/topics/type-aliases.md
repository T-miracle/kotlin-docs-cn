[//]: # (title: 类型别名)

类型别名为现有类型提供了替代名称。
如果类型名称太长，你可以引入一个更短的不同名称，并使用新名称代替。

这对缩短长泛型类型很有用。
例如，缩短集合类型经常是一种诱人的选择：

```kotlin
typealias NodeSet = Set<Network.Node>

typealias FileTable<K> = MutableMap<K, MutableList<File>>
```

您可以为函数类型提供不同的别名：

```kotlin
typealias MyHandler = (Int, String, Any) -> Unit

typealias Predicate<T> = (T) -> Boolean
```

您可以为内部类和嵌套类使用新名称：

```kotlin
class A {
    inner class Inner
}
class B {
    inner class Inner
}

typealias AInner = A.Inner
typealias BInner = B.Inner
```

类型别名不引入新类型。
它们等同于相应的基础类型。
当你添加 `typealias Predicate<T>` 并在代码中使用 `Predicate<Int>` 时，Kotlin 编译器总是将其展开为 `(Int) -> Boolean`。
因此，你可以在需要一般函数类型的任何地方传递你的类型的变量，反之亦然：

```kotlin
typealias Predicate<T> = (T) -> Boolean

fun foo(p: Predicate<Int>) = p(42)

fun main() {
    val f: (Int) -> Boolean = { it > 0 }
    println(foo(f)) // 打印 "true"

    val p: Predicate<Int> = { it > 0 }
    println(listOf(1, -2).filter(p)) // 打印 "[1]"
}
```
{kotlin-runnable="true"}

