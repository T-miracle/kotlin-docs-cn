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
`data object` 的 `equals()` 函数确保所有 `data object` 类型的对象都被视为相等。
通常情况下，运行时只有一个 `data object` 实例（因为 `data object` 是单例的）。
但在一些特殊情况下（比如使用平台反射 `java.lang.reflect` 或某些 JVM 序列化库创建了另一个同类型的对象），
`equals()` 函数也会确保这些对象被视为相等。

> 确保你只通过**结构性比较** `data object`（使用 `==` 运算符），而不是通过**引用比较**（使用 `===` 运算符）。
> 这有助于避免在运行时存在多个 `data object` 实例时的陷阱。
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

生成的 `hashCode()` 函数的行为与 `equals()` 函数一致，因此所有运行时实例的 `data object` 都具有相同的哈希码。

#### 数据对象和数据类之间的区别

虽然 `data object` 和 `data class` 声明经常一起使用并且有一些相似之处，但有一些函数不会为 `data object` 生成：

* 没有 `copy()` 函数。由于 `data object` 声明旨在用作单例对象，因此不会生成 `copy()` 函数。
  单例模式限制了类的实例化只能有一个实例，允许创建实例的副本将违反这一原则。
* 没有 `componentN()` 函数。与 `data class` 不同，`data object` 没有任何数据属性。
  由于试图解构一个没有数据属性的对象是没有意义的，因此不会生成 `componentN()` 函数。

#### 使用数据对象处理密封层次结构

`data object` 声明对于封闭层次结构
（如 [sealed classes 或 sealed interfaces](sealed-classes.md)）
特别有用，因为它们允许你与可能定义在对象旁边的任何数据类保持对称。
在这个例子中，将 `EndOfFile` 声明为 `data object` 而不是普通的 `object` 意味着它将获得 `toString()` 函数，而不需要手动重写它：

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