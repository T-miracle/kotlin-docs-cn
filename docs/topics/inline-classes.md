[//]: # (title: 内联值类)

有时，将一个值包装在一个类中以创建更特定于领域的类型是很实用的。
然而，这会引入运行时开销，因为需要额外的堆分配。
此外，如果包装的类型是原始类型，性能影响会更加显著，因为原始类型通常在运行时经过大量优化，而它们的包装类则没有得到任何特殊处理。

为了解决这些问题，Kotlin 引入了一种特殊的类别，称为**内联类**。
内联类是[value-based classes](https://github.com/Kotlin/KEEP/blob/master/notes/value-classes.md)的一个子集。
它们没有身份，只能保存值。

要声明内联类，在类名之前使用 `value` 修饰符：

```kotlin
value class Password(private val s: String)
```

若要在 JVM 后端声明内联类，只需在类声明前使用 `value` 修饰符并添加 `@JvmInline` 注解：

```kotlin
// 适用于 JVM 后端
@JvmInline
value class Password(private val s: String)
```

内联类必须有一个在主构造函数中初始化的属性。
在运行时，内联类的实例将使用此单个属性表示（有关运行时表示的详细信息，请参见下文 [表示](#表示)）：

```kotlin
// 实际上不会实例化 'Password' 类
// 在运行时 'securePassword' 只包含 'String'
val securePassword = Password("不要在生产环境中尝试这个")
```

这是内联类的主要特性，它灵感来源于 `inline` 这个名称：
类的数据被**内联**到该类使用的地方（类似于[内联函数](inline-functions.md)的内容被内联到该函数调用的地方）。

[// TODO]: (校对至此处)

## 成员

内联类支持常规类的一些功能。特别是，它们可以声明属性和函数，具有 `init` 块和[次构造函数](classes.md#secondary-constructors)：

```kotlin
@JvmInline
value class Person(private val fullName: String) {
    init {
        require(fullName.isNotEmpty()) {
            "全名不能为空"
        }
    }

    constructor(firstName: String, lastName: String) : this("$firstName $lastName") {
        require(lastName.isNotBlank()) {
            "姓氏不能为空"
        }
    }

    val length: Int
        get() = fullName.length

    fun greet() {
        println("你好，$fullName")
    }
}

fun main() {
    val name1 = Person("Kotlin", "Mascot")
    val name2 = Person("Kodee")
    name1.greet() // `greet()` 函数被调用为静态方法
    println(name2.length) // 属性 getter 被调用为静态方法
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.9"}

内联类的属性不能有[幕后字段](properties.md#幕后字段)。它们只能有简单的可计算属性（没有 `lateinit`/委托属性）。

## 继承

内联类允许从接口继承：

```kotlin
interface Printable {
    fun prettyPrint(): String
}

@JvmInline
value class Name(val s: String) : Printable {
    override fun prettyPrint(): String = "Let's $s!"
}

fun main() {
    val name = Name("Kotlin")
    println(name.prettyPrint()) // 仍然作为静态方法调用
}
```

内联类被禁止参与类层次结构。这意味着内联类不能扩展其他类，并且始终是 `final`。

## 表示 {id=表示}

在生成的代码中，Kotlin 编译器为每个内联类保留一个*包装器*。
内联类实例在运行时可以表示为包装器或基础类型。
这类似于 `Int` 可以被[表示](numbers.md#numbers-representation-on-the-jvm)为基本类型 `int` 或包装器 `Integer` 的方式。

Kotlin 编译器将优先使用基础类型而不是包装器，以生成性能最佳且经过优化的代码。
但是，有时需要保留包装器。
一般而言，当内联类作为另一种类型使用时，它们会被装箱。

```kotlin
interface I

@JvmInline
value class Foo(val i: Int) : I

fun asInline(f: Foo) {}
fun <T> asGeneric(x: T) {}
fun asInterface(i: I) {}
fun asNullable(i: Foo?) {}

fun <T> id(x: T): T = x

fun main() {
    val f = Foo(42) 
    
    asInline(f)    // 未装箱：作为 Foo 本身使用
    asGeneric(f)   // 装箱：作为泛型类型 T 使用
    asInterface(f) // 装箱：作为类型 I 使用
    asNullable(f)  // 装箱：作为 Foo? 使用，与 Foo 不同
    
    // 在下面的示例中，'f' 首先在传递给 'id' 时装箱，然后在从 'id' 返回时取消装箱
    // 最终，'c' 包含未装箱的表示（只是 '42'），与 'f' 一致
    val c = id(f)  
}
```

由于内联类可能既表示基础值又表示包装器，[引用相等性](equality.md#referential-equality)对它们来说毫无意义，因此被禁止。

内联类还可以具有泛型类型参数作为基础类型。在这种情况下，编译器将其映射为`Any?`，或者通常是类型参数的上界。

```kotlin
@JvmInline
value class UserId<T>(val value: T)

fun compute(s: UserId<String>) {} // 编译器生成 fun compute-<hashcode>(s: Any?)
```

### 重命名

由于内联类被编译为其基础类型，这可能导致各种晦涩的错误，例如意外的平台签名冲突：

```kotlin
@JvmInline
value class UInt(val x: Int)

// 在 JVM 上表示为 'public final void compute(int x)'
fun compute(x: Int) { }

// 在 JVM 上也表示为 'public final void compute(int x)'！
fun compute(x: UInt) { }
```

为了缓解这类问题，使用内联类的函数会通过在函数名后添加一些稳定的哈希码来进行*重命名*。
因此，`fun compute(x: UInt)` 将被表示为 `public final void compute-<hashcode>(int x)`，从而解决了冲突问题。

### 从 Java 代码调用

你可以从 Java 代码调用接受内联类的函数。为此，你应该手动禁用重命名：在函数声明之前添加 `@JvmName` 注解：

```kotlin
@JvmInline
value class UInt(val x: Int)

fun compute(x: Int) { }

@JvmName("computeUInt")
fun compute(x: UInt) { }
```

## 内联类 vs 类型别名

乍一看，内联类似乎与[type aliases](type-aliases.md)非常相似。事实上，两者都似乎引入了一个新类型，并且在运行时都将被表示为基础类型。

然而，关键的区别在于类型别名与其基础类型（以及具有相同基础类型的其他类型别名）是*赋值兼容*的，而内联类则不是。

换句话说，内联类引入了一个真正的 _新_ 类型，而类型别名只是为现有类型引入了一个替代名称（别名）：

```kotlin
typealias NameTypeAlias = String

@JvmInline
value class NameInlineClass(val s: String)

fun acceptString(s: String) {}
fun acceptNameTypeAlias(n: NameTypeAlias) {}
fun acceptNameInlineClass(p: NameInlineClass) {}

fun main() {
    val nameAlias: NameTypeAlias = ""
    val nameInlineClass: NameInlineClass = NameInlineClass("")
    val string: String = ""

    acceptString(nameAlias) // OK：传递别名而不是基础类型
    acceptString(nameInlineClass) // 不OK：不能传递内联类而不是基础类型

    // 反之亦然：
    acceptNameTypeAlias(string) // OK：传递基础类型而不是别名
    acceptNameInlineClass(string) // 不OK：不能传递基础类型而不是内联类
}
```

## 内联类和委托

通过接口进行内联类的内联值的委托实现是允许的：

```kotlin
interface MyInterface {
    fun bar()
    fun foo() = "foo"
}

@JvmInline
value class MyInterfaceWrapper(val myInterface: MyInterface) : MyInterface by myInterface

fun main() {
    val my = MyInterfaceWrapper(object : MyInterface {
        override fun bar() {
            // 体
        }
    })
    println(my.foo()) // 输出 "foo"
}
```
