[//]: # (title: 委托)

[委托模式](https://en.wikipedia.org/wiki/Delegation_pattern) 已被证明是实现继承的一个良好替代方案，Kotlin 原生支持它，并且不需要任何样板代码。

类 `Derived` 可以通过将其所有公共成员委托给指定对象来实现接口 `Base`：

```kotlin
interface Base {
    fun print()
}

class BaseImpl(val x: Int) : Base {
    override fun print() { print(x) }
}

class Derived(b: Base) : Base by b

fun main() {
    val b = BaseImpl(10)
    Derived(b).print()
}
```
{kotlin-runnable="true"}

在 `Derived` 类的超类型列表中使用 `by` 子句意味着 `b` 对象将会被内部存储在 `Derived` 对象中，编译器会自动生成所有转发到 `b` 的 `Base` 类方法。

## 重写通过委托实现的接口成员

[重写](inheritance.md#重写方法) 如你所预期：编译器将使用你的 `override` 实现，而不是委托对象中的实现。
如果你想在 `Derived` 中添加 `override fun printMessage() { print("abc") }`，那么在调用 `printMessage` 时，程序将打印 *abc* 而不是 *10*：

```kotlin
interface Base {
    fun printMessage()
    fun printMessageLine()
}

class BaseImpl(val x: Int) : Base {
    override fun printMessage() { print(x) }
    override fun printMessageLine() { println(x) }
}

class Derived(b: Base) : Base by b {
    override fun printMessage() { print("abc") }
}

fun main() {
    val b = BaseImpl(10)
    Derived(b).printMessage()
    Derived(b).printMessageLine()
}
```
{kotlin-runnable="true"}

但是，请注意，以这种方式重写的成员不会从委托对象的成员中被调用，委托对象只能访问其自身的接口成员实现：

```kotlin
interface Base {
    val message: String
    fun print()
}

class BaseImpl(val x: Int) : Base {
    override val message = "BaseImpl: x = $x"
    override fun print() { println(message) }
}

class Derived(b: Base) : Base by b {
    // 此属性不会从 b 的 `print` 实现中访问
    override val message = "Message of Derived"
}

fun main() {
    val b = BaseImpl(10)
    val derived = Derived(b)
    derived.print()
    println(derived.message)
}
```
{kotlin-runnable="true"}

了解更多关于 [委托属性](delegated-properties.md) 的信息。
