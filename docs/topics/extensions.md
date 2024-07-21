[//]: # (title: 扩展)

Kotlin 提供了一种扩展类或接口功能的能力，而无需继承自该类或使用设计模式，比如 **装饰者（Decorator）**。
这是通过称为 **扩展（extensions）** 的特殊声明完成的。

例如，你可以为无法修改的第三方库中的类或接口编写新的函数。
这些函数可以像原始类的方法一样常规调用。
这个机制被称为 **扩展函数（extension function）**。
还有 **扩展属性（extension properties）** 允许你为现有类定义新属性。

## 扩展函数 {id=extension-functions}

要声明扩展函数，请在其名称前加上**接收者类型（receiver type）**，该类型指的是被扩展的类型。
以下示例向 `MutableList<Int>` 添加了一个名为 `swap` 的函数：

```kotlin
fun MutableList<Int>.swap(index1: Int, index2: Int) {
    val tmp = this[index1] // 'this' 对应该列表
    this[index1] = this[index2]
    this[index2] = tmp
}
```

在扩展函数内部，关键字 `this` 对应于接收者对象（即在点号之前传递的对象）。现在，你可以在任何 `MutableList<Int>` 上调用这个函数：

```kotlin
val list = mutableListOf(1, 2, 3)
list.swap(0, 2) // 'swap()' 内部的 'this' 将持有 'list' 的值
```

这个函数对于任何 `MutableList<T>` 都是有意义的，你可以使它成为通用的：

```kotlin
fun <T> MutableList<T>.swap(index1: Int, index2: Int) {
    val tmp = this[index1] // 'this' 对应于列表
    this[index1] = this[index2]
    this[index2] = tmp
}
```

你需要在函数名称之前声明泛型类型参数，以便在接收者类型表达式中使用。有关泛型的更多信息，请参见[泛型函数](generics.md)。

## 扩展是**静态**解析的

扩展实际上不会修改它们所扩展的类。通过定义扩展，你并没有在类内部添加新成员，只是让该类型的变量能够使用点符号调用新的函数。

需要注意的是，扩展函数是[**静态**分派](static-dispatchpic.md)的。这意味着在编译时就已经根据接收者类型确定调用哪个扩展函数。例如：

```kotlin
fun main() {
//sampleStart
    open class Shape
    class Rectangle: Shape()
    
    fun Shape.getName() = "Shape"
    fun Rectangle.getName() = "Rectangle"
    
    fun printClassName(s: Shape) {
        println(s.getName())
    }
    
    printClassName(Rectangle())
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

这个例子会打印出 **Shape**，因为被调用的扩展函数仅取决于参数 `s` 的声明类型，即 `Shape` 类。

如果一个类同时拥有一个成员函数和一个具有相同接收者类型、相同名称以及适用相同参数的扩展函数，那么**成员函数始终优先**被执行。例如：

```kotlin
fun main() {
//sampleStart
    class Example {
        fun printFunctionType() { println("Class method") }
    }
    
    fun Example.printFunctionType() { println("Extension function") }
    
    Example().printFunctionType()
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

这段代码会打印出 **Class method**。

然而对于扩展函数来说，重载具有相同名称但不同签名的成员函数是完全没有问题的：

```kotlin
fun main() {
//sampleStart
    class Example {
        fun printFunctionType() { println("Class method") }
    }
    
    fun Example.printFunctionType(i: Int) { println("Extension function #$i") }
    
    Example().printFunctionType(1)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

## 可空接收者 {id=可空接收者}

请注意，扩展函数可以使用可空的接收者类型进行定义。这意味着即使对象变量的值为 `null`，也可以在其上调用这些扩展函数。
如果接收者为 `null`，那么 `this` 也为 `null`。
因此，在使用可空接收者类型定义扩展时，我们建议在函数体内执行 `this == null` 检查以避免编译器错误。

在 Kotlin 中，你可以调用 `toString()` 而无需检查 `null`，因为这个检查已经在扩展函数内部发生了：

```kotlin
fun Any?.toString(): String {
    if (this == null) return "null"
    // 在空检查之后，'this' 自动转换为非空类型，因此下面的 toString() 解析为 Any 类的成员函数
    return toString()
}
```

## 扩展属性 {id=扩展属性}

与扩展函数类似，Kotlin 同样支持扩展属性：

```kotlin
val <T> List<T>.lastIndex: Int
    get() = size - 1
```

> 由于扩展实际上不会向类中插入成员，因此扩展属性没有有效的方式来拥有[幕后字段](properties.md#幕后字段)。
> 这就是为什么**扩展属性不允许使用初始化器**的原因。
> 它们的行为只能通过显式提供的 getter/setter 来定义。
>
{style="note"}

例子：

```kotlin
val House.number = 1 // 错误：扩展属性不允许使用初始值设定项
```

## 伴生对象扩展

如果一个类定义了[伴生对象](object-declarations.md#伴生对象)，你还可以为伴生对象定义扩展函数和属性。
与伴生对象的常规成员一样，它们可以只使用类名作为限定符来调用：

```kotlin
class MyClass {
    companion object { }  // 将被称为 "Companion"
}

fun MyClass.Companion.printCompanion() { println("companion") }

fun main() {
    MyClass.printCompanion()
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

## 扩展的作用域

在大多数情况下，你会在顶层直接在包下定义扩展：

```kotlin
package org.example.declarations

fun List<String>.getLongestString() { /*...*/}
```

要在声明扩展的包之外使用它，请在调用处导入：

```kotlin
package org.example.usage

import org.example.declarations.getLongestString

fun main() {
    val list = listOf("red", "green", "blue")
    list.getLongestString()
}
```

请参阅[导入](packages.md#导入)获取更多信息。

## 将扩展声明为成员

你可以在一个类内部为另一个类声明扩展。
在这样的扩展内部，有多个**隐式接收者（implicit receivers）** - 可以无需限定符访问其成员的对象。
在扩展中声明的类的实例被称为**分发接收者（dispatch receiver）**，扩展方法的接收者类型的实例被称为**扩展接收者（extension receiver）**。

```kotlin
class Host(val hostname: String) {
    // 构造函数
    fun printHostname() { print(hostname) }  // 方法

}

class Connection(val host: Host, val port: Int) {
    // 构造函数
    fun printPort() { print(port) }  // 方法

    // Host类的扩展函数
    fun Host.printConnectionString() {
        printHostname()   // 调用Host类的printHostname()方法
        print(":")
        printPort()   // 调用Connection类的printPort()方法
    }

    fun connect() {
        /*...*/
        host.printConnectionString()   // 调用扩展函数
    }
}

fun main() {
    Connection(Host("kotl.in"), 443).connect()   // 创建Connection实例并调用connect()方法

    // Host("kotl.in").printConnectionString()  // 错误，扩展函数在Connection外部不可用
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

在分发接收者和扩展接收者的成员之间发生名称冲突时，扩展接收者具有优先权。
要引用分发接收者的成员，您可以使用[限定的 `this` 语法](this-expressions.md#qualified-this)。

```kotlin
class Connection {
    fun Host.getConnectionString() {
        toString()         // 调用 Host.toString()
        this@Connection.toString()  // 调用 Connection.toString()
    }
}
```

声明为成员的扩展可以使用`open`关键字声明，并允许在子类中进行重写。
这意味着对于调度接收者类型，这些函数的分派是虚拟的，但对于扩展接收者类型，分派是静态的。

```kotlin
open class Base { }

class Derived : Base() { }

open class BaseCaller {
    open fun Base.printFunctionInfo() {
        println("Base extension function in BaseCaller")
    }

    open fun Derived.printFunctionInfo() {
        println("Derived extension function in BaseCaller")
    }

    fun call(b: Base) {
        b.printFunctionInfo()   // 调用扩展函数
    }
}

class DerivedCaller: BaseCaller() {
    override fun Base.printFunctionInfo() {
        println("Base extension function in DerivedCaller")
    }

    override fun Derived.printFunctionInfo() {
        println("Derived extension function in DerivedCaller")
    }
}

fun main() {
    BaseCaller().call(Base())   // "Base extension function in BaseCaller"
    DerivedCaller().call(Base())  // "Base extension function in DerivedCaller" - 分发接收者在虚拟上解析
    DerivedCaller().call(Derived())  // "Base extension function in DerivedCaller" - 扩展接收者在静态上解析
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

## 关于可见性的注意事项

扩展函数和在相同作用域声明的普通函数一样，使用相同的[可见性修饰符](visibility-modifiers.md)。
例如：

* 在文件的顶层声明的扩展可以访问文件中其他的 `private` 顶层声明。
* 如果扩展在其接收者类型之外声明，它将无法访问接收者的 `private` 或 `protected` 成员。
