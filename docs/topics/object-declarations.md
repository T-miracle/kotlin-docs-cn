[//]: # (title: 对象表达式与对象声明)

有时你需要创建一个对某个类进行轻微修改的对象，而不需要显式地为它声明一个新的子类。
Kotlin 可以通过 **对象表达式** 和 **对象声明** 来处理这种情况。

## 对象表达式 {id=对象表达式}

**对象表达式** 会创建匿名类的对象，也就是说，这些类不是通过 `class` 声明显式声明的。
这些类对于一次性使用非常有用。
你可以从头定义它们，继承现有类，或实现接口。
匿名类的实例也被称为 **匿名对象**，因为它们是通过表达式定义的，而不是通过名称定义的。

### 从头创建匿名对象

对象表达式以 `object` 关键字开始。

如果你只需要一个没有任何复杂超类型的对象，可以在 `object` 之后的花括号内编写它的成员：

```kotlin
fun main() {
//sampleStart
    val helloWorld = object {
        val hello = "Hello"
        val world = "World"
        // 对象表达式继承自 Any，因此 `toString()` 需要 `override`
        override fun toString() = "$hello $world"
    }

    print(helloWorld)
//sampleEnd
}
```
{kotlin-runnable="true"}


### 继承匿名对象的超类型

要创建一个继承某种类型（或多种类型）的匿名类对象，可以在 `object` 后面加上类型和冒号 (`:`)。
然后像 [继承](inheritance.md) 该类一样实现或重写该类的成员：

```kotlin
window.addMouseListener(object : MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) { /*...*/ }

    override fun mouseEntered(e: MouseEvent) { /*...*/ }
})
```

如果超类型有构造函数，请传递适当的构造函数参数。
可以在冒号后以逗号分隔的列表形式指定多个超类型：

```kotlin
open class A(x: Int) {
    public open val y: Int = x
}

interface B { /*...*/ }

val ab: A = object : A(1), B {
    override val y = 15
}
```

### 将匿名对象用作返回值和变量类型

当匿名对象被用作局部或 [私有](visibility-modifiers.md#包) 声明（方法或属性）类型时，所有它的成员都可以通过此方法或属性访问，前提是不 [内联](inline-functions.md)：

```kotlin
class C {
    private fun getObject() = object {
        val x: String = "x"
    }

    fun printX() {
        println(getObject().x)
    }
}
```

如果该方法或属性是公共或私有内联的，其实际类型如下：
* 如果匿名对象没有声明超类型，则为 `Any`
* 如果匿名对象有一个声明的超类型，则为该声明的超类型
* 如果有多个声明的超类型，则为显式声明的类型

在所有这些情况下，添加到匿名对象中的成员是不可访问的。如果重写的成员在方法或属性的实际类型中声明，则它们是可访问的：

```kotlin
interface A {
    fun funFromA() {}
}
interface B

class C {
    // 返回类型是 Any；x 不可访问
    fun getObject() = object {
        val x: String = "x"
    }

    // 返回类型是 A；x 不可访问
    fun getObjectA() = object: A {
        override fun funFromA() {}
        val x: String = "x"
    }

    // 返回类型是 B；funFromA() 和 x 不可访问
    fun getObjectB(): B = object: A, B { // 需要显式的返回类型
        override fun funFromA() {}
        val x: String = "x"
    }
}
```

### 访问匿名对象中的变量

对象表达式中的代码可以访问其外部作用域中的变量：

```kotlin
fun countClicks(window: JComponent) {
    var clickCount = 0
    var enterCount = 0

    window.addMouseListener(object : MouseAdapter() {
        override fun mouseClicked(e: MouseEvent) {
            clickCount++
        }

        override fun mouseEntered(e: MouseEvent) {
            enterCount++
        }
    })
    // ...
}
```

## 对象声明
{id="object-declarations-overview"}

[单例](https://en.wikipedia.org/wiki/Singleton_pattern) 模式在多种情况下都很有用，Kotlin 使得声明单例变得非常容易：

```kotlin
object DataProviderManager {
    fun registerDataProvider(provider: DataProvider) {
        // ...
    }

    val allDataProviders: Collection<DataProvider>
        get() = // ...
}
```

这称为 **对象声明**，它总是有一个在 `object` 关键字后面的名称。就像变量声明一样，对象声明不是表达式，不能用在赋值语句的右边。

对象声明的初始化是线程安全的，并在第一次访问时完成。

要引用该对象，直接使用它的名称：

```kotlin
DataProviderManager.registerDataProvider(...)
```

此类对象可以具有超类型：

```kotlin
object DefaultListener : MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) { ... }

    override fun mouseEntered(e: MouseEvent) { ... }
}
```

> 对象声明不能是局部的（即，不能直接嵌套在方法中），但可以嵌套在其他对象声明或非内部类中。
>
{style="note"}

### 数据对象

在 Kotlin 中打印普通的 `object` 声明时，其字符串表示包含其名称和对象的哈希值：

```kotlin
object MyObject

fun main() {
    println(MyObject) // MyObject@1f32e575
}
```

Just like [data classes](data-classes.md), you can mark an `object` declaration with the `data` modifier. 
This instructs the compiler to generate a number of functions for your object:

* `toString()` 返回数据对象的名称
* `equals()`/`hashCode()` 对

  > 你不能为 `data object` 提供自定义的 `equals` 或 `hashCode` 实现。
  >
  {style="note"}

数据对象的 `toString()` 函数返回对象的名称：
```kotlin
data object MyDataObject {
    val x: Int = 3
}

fun main() {
    println(MyDataObject) // MyDataObject
}
```

The `equals()` function for a `data object` ensures that all objects that have the type of your `data object` are considered equal.
In most cases, you will only have a single instance of your data object at runtime (after all, a `data object` declares a singleton).
However, in the edge case where another object of the same type is generated at runtime (for example, by using platform 
reflection with `java.lang.reflect` or a JVM serialization library that uses this API under the hood), this ensures that 
the objects are treated as being equal.

> Make sure that you only compare `data objects` structurally (using the `==` operator) and never by reference (using the `===` operator).
> This helps you to avoid pitfalls when more than one instance of a data object exists at runtime.
>
{style="warning"}

```kotlin
import java.lang.reflect.Constructor

data object MySingleton

fun main() {
    val evilTwin = createInstanceViaReflection()

    println(MySingleton) // MySingleton
    println(evilTwin) // MySingleton

    // 即使库强制创建了 MySingleton 的第二个实例，其 `equals` 方法也会返回 true：
    println(MySingleton == evilTwin) // true

    // 不要通过 === 比较数据对象。
    println(MySingleton === evilTwin) // false
}

fun createInstanceViaReflection(): MySingleton {
    // Kotlin 反射不允许实例化数据对象。
    // 这将通过强制方式创建一个新的 MySingleton 实例（即 Java 平台反射）
    // 不要自己这样做！
    return (MySingleton.javaClass.declaredConstructors[0].apply { isAccessible = true } as Constructor<MySingleton>).newInstance()
}
```

The generated `hashCode()` function has behavior that is consistent with the `equals()` function, so that all runtime 
instances of a `data object` have the same hash code.

#### 数据对象和数据类之间的区别

While `data object` and `data class` declarations are often used together and have some similarities, there are some 
functions that are not generated for a `data object`:

* No `copy()` function. Because a `data object` declaration is intended to be used as singleton objects, no `copy()` 
  function is generated. The singleton pattern restricts the instantiation of a class to a single instance, which would 
  be violated by allowing copies of the instance to be created.
* No `componentN()` function. Unlike a `data class`, a `data object` does not have any data properties. 
  Since attempting to destructure such an object without data properties would not make sense, no `componentN()` functions are generated.

#### 使用数据对象处理密封层次结构

Data object declarations are particularly useful for sealed hierarchies like 
[sealed classes or sealed interfaces](sealed-classes.md), since they allow you to maintain symmetry with any data classes 
you may have defined alongside the object. In this example, declaring `EndOfFile` as a `data object` instead of a plain `object` 
means that it will get the `toString()` function without the need to override it manually:

```kotlin
sealed interface ReadResult
data class Number(val number: Int) : ReadResult
data class Text(val text: String) : ReadResult
data object EndOfFile : ReadResult

fun main() {
  println(Number(7)) // Number(number=7)
  println(EndOfFile) // EndOfFile
}
```
{kotlin-runnable="true" id="data-objects-sealed-hierarchies"}

### 伴生对象 {id=companion-objects}

类内部的对象声明可以使用 `companion` 关键字标记：

```kotlin
class MyClass {
    companion object Factory {
        fun create(): MyClass = MyClass()
    }
}
```

伴生对象的成员可以通过使用类名作为限定符来简单调用：

```kotlin
val instance = MyClass.create()
```

如果省略了伴生对象的名称，那么将使用 `Companion` 作为名称：

```kotlin
class MyClass {
    companion object { }
}

val x = MyClass.Companion
```

类成员可以访问相应伴生对象的私有成员。

一个类的名称（不作为另一个名称的限定符）被用作对类的伴生对象的引用（无论是否命名）：

```kotlin
class MyClass1 {
    companion object Named { }
}

val x = MyClass1

class MyClass2 {
    companion object { }
}

val y = MyClass2
```

请注意，尽管伴生对象的成员在其他语言中看起来像静态成员，但在运行时，这些成员仍然是真实对象的实例成员，可以实现接口，例如：

```kotlin
interface Factory<T> {
    fun create(): T
}

class MyClass {
    companion object : Factory<MyClass> {
        override fun create(): MyClass = MyClass()
    }
}

val f: Factory<MyClass> = MyClass
```

然而，在 JVM 上，如果使用 `@JvmStatic` 注解，可以将伴生对象的成员生成为真正的静态方法和字段。
有关详细信息，请参阅[Java 互操作性](java-to-kotlin-interop.md#static-fields)部分。

### 对象表达式和声明之间的语义差异

对象表达式和对象声明之间有一个重要的语义差异：

* 对象表达式在使用它们的地方 **立即** 执行（和初始化）。
* 对象声明在 **首次访问时** 延迟初始化。
* 伴生对象在与之对应的类加载（解析）时初始化，这符合 Java 静态初始化器的语义。