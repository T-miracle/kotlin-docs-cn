[//]: # (title: 可见性修饰符)

类、对象、接口、构造函数和方法，以及属性及其设置器（setters），都可以拥有**可见性修饰符**。
获取器（getters）的可见性始终与它们所属的属性相同。

在 Kotlin 中有四个可见性修饰符：`private`、`protected`、`internal` 和 `public`。
默认可见性是 `public`。

在本页面上，你将学习这些修饰符如何适用于不同类型的声明范围。

## 包 {id=包}

函数、属性、类、对象和接口可以在包的“顶层”直接声明：

```kotlin
// 文件名: example.kt
package foo

fun baz() { ... }
class Bar { ... }
```

* 如果你没有使用可见性修饰符，默认情况下将使用 `public`。这就意味着你的声明将在任何地方都可见。
* 如果你将声明标记为 `private`，它只会在包含该声明的文件内可见。
* 如果将其标记为 `internal`，它将在同一[模块](#模块)中的任何地方可见。
* 请注意，`protected` 修饰符不适用于顶层声明。

> 要从另一个包中使用可见的顶层声明，你应该进行[导入](packages.md#导入)。
>
{style="note"}

示例：

```kotlin
// 文件名: example.kt
package foo

private fun foo() { ... } // 在 example.kt 中可见

public var bar: Int = 5 // 属性在任何地方都可见
    private set         // 设置器仅在 example.kt 中可见
    
internal val baz = 6    // 在同一模块中可见
```

## 类成员 {id=类成员}

对于在类内声明的成员：

* `private` 表示该成员仅在该类内可见（包括其所有成员）。
* `protected` 表示该成员具有与标记为 `private` 相同的可见性，但它还在子类中可见。
* `internal` 表示在**此模块内**看到声明类的任何客户端都会看到其 `internal` 成员。
* `public` 表示看到声明类的任何客户端都会看到其 `public` 成员。

> 在 Kotlin 中，外部类看不到其内部类的私有成员。
>
{style="note"}

如果你重新定义了一个 `protected` 或者 `internal` 成员，而没有明确指定它的可见性，那么重新定义的成员将会具有与原始成员相同的可见性。

示例：

```kotlin
open class Outer {
    private val a = 1
    protected open val b = 2
    internal open val c = 3
    val d = 4  // 默认情况下是 public
    
    protected class Nested {
        public val e: Int = 5
    }
}

class Subclass : Outer() {
    // a 不可见
    // b, c 和 d 可见
    // Nested 和 e 可见

    override val b = 5   // 'b' 是 protected
    override val c = 7   // 'c' 是 internal
}

class Unrelated(o: Outer) {
    // o.a, o.b 不可见
    // o.c 和 o.d 可见（同一模块）
    // Outer.Nested 不可见，Nested::e 也不可见
}
```

### 构造函数 {id=构造函数}

使用以下语法指定类的主构造函数的可见性：

> 你需要添加显式的 `constructor` 关键字。
>
{style="note"}

```kotlin
class C private constructor(a: Int) { ... }
```

在这里，构造函数是私有的。
默认情况下，所有构造函数都是公开的 (`public`)。
这实际上意味着它们可以在类可见的任何地方被访问到（这也就是说，`internal` 类的构造函数只有在同一模块内才能被看到）。

对于密封类（sealed classes），构造函数默认是 `protected` 的。有关更多信息，请参见 [密封类](sealed-classes.md#constructors)。

### 局部声明 {id=局部声明}

局部变量、函数和类不能具有可见性修饰符。

## 模块 {id=模块}

`internal` 这个可见性修饰符表示，被标记的成员（通常是函数、类等）只能在同一个模块内可见。
具体来说，模块是一组在编译时一起处理的 Kotlin 文件，例如：

* IntelliJ IDEA 的模块。
* Maven 项目。
* Gradle 的源代码集（但 `test` 源代码集只能访问 `main` 的内部声明）。
* 使用 `<kotlinc>` Ant 任务的文件集。

