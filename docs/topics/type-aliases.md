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

## Nested type aliases

<primary-label ref="beta"/>

In Kotlin, you can define type aliases inside other declarations, as long as they
don't capture type parameters from their outer class:

```kotlin
class Dijkstra {
    typealias VisitedNodes = Set<Node>

    private fun step(visited: VisitedNodes, ...) = ...
}
```

Capturing means that the type alias refers to a type parameter defined in the outer class:

```kotlin
class Graph<Node> {
    // Incorrect because captures Node
    typealias Path = List<Node>
}
```

To fix this issue, declare the type parameter directly in the type alias:

```kotlin
class Graph<Node> {
    // Correct because Node is a type alias parameter
    typealias Path<Node> = List<Node>
}
```

Nested type aliases allow for cleaner, more maintainable code by improving encapsulation, reducing package-level clutter,
and simplifying internal implementations.

### Rules for nested type aliases

Nested type aliases follow specific rules to ensure clear and consistent behavior:

* Nested type aliases must follow all existing type alias rules.
* In terms of visibility, the alias can't expose more than its referenced types allow.
* Their scope is the same as [nested classes](nested-classes.md). You can define them inside classes,
  and they hide any parent type aliases with the same name as they don't override.
* Nested type aliases can be marked as `internal` or `private` to limit their visibility.
* Nested type aliases are not supported in Kotlin Multiplatform's [`expect/actual` declarations](https://kotlinlang.org/docs/multiplatform/multiplatform-expect-actual.html).

### How to enable nested type aliases

To enable nested type aliases in your project, use the following compiler option in the command line:

```bash
-Xnested-type-aliases
```

Or add it to the `compilerOptions {}` block of your Gradle build file:

```kotlin
// build.gradle.kts
kotlin {
    compilerOptions {
        freeCompilerArgs.add("-Xnested-type-aliases")
    }
}
```
