[//]: # (title: 嵌套类和内部类)

类可以嵌套在其他类中：

```kotlin
class Outer {
    private val bar: Int = 1
    class Nested {
        fun foo() = 2
    }
}

val demo = Outer.Nested().foo() // == 2
```

你也可以在嵌套中使用接口。所有类和接口的组合都是可能的：你可以将接口嵌套在类中，类嵌套在接口中，以及接口嵌套在接口中。

```kotlin
interface OuterInterface {
    class InnerClass
    interface InnerInterface
}

class OuterClass {
    class InnerClass
    interface InnerInterface
}
```

## 内部类 {id=内部类}

被标记为 `inner` 的嵌套类可以访问其外部类的成员。内部类携带对外部类对象的引用：

```kotlin
class Outer {
    private val bar: Int = 1
    inner class Inner {
        fun foo() = bar
    }
}

val demo = Outer().Inner().foo() // == 1
```

查看 [合格的 `this` 表达式](this-expressions.md) 了解有关内部类中 `this` 的消歧义的详细信息。

## 匿名内部类

使用 [对象表达式](object-declarations.md#object-expressions) 创建匿名内部类的实例：

```kotlin
window.addMouseListener(object : MouseAdapter() {

    override fun mouseClicked(e: MouseEvent) { ... }

    override fun mouseEntered(e: MouseEvent) { ... }
})
```

> 在 JVM 上，如果对象是函数式 Java 接口的实例（即带有单个抽象方法的 Java 接口），则可以使用带有接口类型前缀的 lambda 表达式来创建它：
>
> ```kotlin
> val listener = ActionListener { println("clicked") }
> ```
>
{style="note"}

