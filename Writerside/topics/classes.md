[//]: # (title: 类)

在 Kotlin 中，使用关键字 `class` 来声明类：

```kotlin
class Person { /*...*/ }
```

类声明包括类名、类头（指定其类型参数、主构造函数和其他一些内容）以及由花括号括起来的类体。
类的头和类体都是可选的；
如果类没有类体，则可以省略花括号。

```kotlin
class Empty
```

## 构造函数 {id=构造函数}

在 Kotlin 中，类有一个**主构造函数**，可能还有一个或多个**次构造函数**。
主构造函数在类头中声明，他跟在类名和可选类型参数之后。

```kotlin
class Person constructor(firstName: String) { /*...*/ }
```

如果主构造函数没有任何注解或可见性修饰符，则可以省略 `constructor` 关键字：

```kotlin
class Person(firstName: String) { /*...*/ }
```

主构造函数在类头中初始化类实例及其属性。类头不能包含任何可运行的代码。
如果想在对象创建期间运行一些代码，请使用类体内的**初始化块**。
初始化块使用 `init` 关键字后跟大括号声明。在大括号内你可以写任何想要运行的代码。

在实例初始化期间，初始化块按照它们在类体中出现的顺序执行，与属性初始化器交替进行：

```kotlin
//sampleStart
class InitOrderDemo(name: String) {
    val firstProperty = "First property: $name".also(::println)
    
    init {
        println("First initializer block that prints $name")
    }
    
    val secondProperty = "Second property: ${name.length}".also(::println)
    
    init {
        println("Second initializer block that prints ${name.length}")
    }
}
//sampleEnd

fun main() {
    InitOrderDemo("hello")
}
```
{kotlin-runnable="true"}

主构造函数的参数可以在初始化块中使用，也可以在类体中声明的属性初始化器中使用：

```kotlin
class Customer(name: String) {
    val customerKey = name.uppercase()
}
```

Kotlin 提供了一种简洁的语法，用于声明属性并从主构造函数进行初始化：

```kotlin
class Person(val firstName: String, val lastName: String, var age: Int)
```

这样的声明还可以包含类属性的默认值：

```kotlin
class Person(val firstName: String, val lastName: String, var isEmployed: Boolean = true)
```

在声明类属性时，你可以使用[尾随逗号](coding-conventions.md#尾随逗号)：

```kotlin
class Person(
    val firstName: String,
    val lastName: String,
    var age: Int, // 尾随逗号
) { /*...*/ }
```

与普通属性类似，主构造函数中声明的属性可以是可变的 (`var`) 或只读的 (`val`)。

如果构造函数有注解或可见性修饰符，则需要 `constructor` 关键字，并且修饰符放在其前面：

```kotlin
class Customer public @Inject constructor(name: String) { /*...*/ }
```

了解更多关于[可见性修饰符](visibility-modifiers.md#构造函数)的信息。

### 次构造函数 {id=次构造函数}

一个类还可以声明**次构造函数**，以 `constructor` 为前缀：

```kotlin
class Person(val pets: MutableList<Pet> = mutableListOf())

class Pet {
    constructor(owner: Person) {
        owner.pets.add(this) // 将此宠物添加到其所有者的宠物列表中
    }
}
```

如果一个类有一个主构造函数，那么每个次构造函数都需要向主构造函数进行委托。
这可以直接委托，也可以通过其他次构造函数间接委托。
委托给同一类的另一个构造函数可以使用 `this` 关键字来实现：

```kotlin
class Person(val name: String) {
    val children: MutableList<Person> = mutableListOf()
    constructor(name: String, parent: Person) : this(name) {
        parent.children.add(this)
    }
}
```

初始化块中的代码实际上成为主构造函数的一部分。
当使用次构造函数时，委托给主构造函数会在访问次构造函数的第一条语句时发生。
因此，初始化块和属性初始化器中的代码会在执行次构造函数体之前被执行。
这确保了在实例化对象时，无论使用主构造函数还是次构造函数，所有相关的初始化代码都会在构造函数体执行之前完成。

即使类没有主构造函数，委托仍然会隐式发生，并且初始化块仍然会被执行：

```kotlin
//sampleStart
class Constructors {
    init {
        println("Init block")
    }

    constructor(i: Int) {
        println("Constructor $i")
    }
}
//sampleEnd

fun main() {
    Constructors(1)
}
```
{kotlin-runnable="true"}

如果一个非抽象类没有声明任何构造函数（主构造函数或次构造函数），它将具有一个生成的无参数主构造函数。构造函数的可见性将是 public。

如果不希望你的类有一个公共构造函数，请声明一个没有默认可见性的空主构造函数：

```kotlin
class DontCreateMe private constructor() { /*...*/ }
```

> 在 JVM 上，如果所有主构造函数参数都有默认值，编译器将生成一个额外的无参数构造函数，该构造函数将使用默认值。
> 这样可以更容易地将 Kotlin 与通过无参数构造函数创建类实例的库（例如 Jackson 或 JPA）一起使用。
>
> ```kotlin
> class Customer(val customerName: String = "")
> ```
>
{style="note"}

## 创建类的实例 {id=创建类的实例}

要创建类的实例，请调用构造函数，就像调用普通函数一样：

```kotlin
val invoice = Invoice()

val customer = Customer("Joe Smith")
```

> Kotlin 没有 `new` 关键字。
>
{style="note"}

有关创建嵌套、内部和匿名内部类的实例的过程，请参阅 [嵌套类](nested-classes.md)。

## 类成员 {id=类成员}

类可以包含：

* [构造函数和初始化块](classes.md#构造函数)
* [函数](functions.md)
* [属性](properties.md)
* [嵌套类和内部类](nested-classes.md)
* [对象声明](object-declarations.md)

## 继承 {id=继承}

类可以相互继承，形成继承层次结构。
这意味着一个类可以从另一个类继承属性和方法，形成一种层次化的关系。
在继承结构中，子类可以继承父类的特征，并且可以添加或修改一些新的特征。
这种继承机制有助于代码的重用和组织，使得类之间可以建立更为有序和结构化的关系。

[了解 Kotlin 中的继承](inheritance.md)。

## 抽象类 {id=抽象类}

我们可以声明一个类为`abstract`，同时也可以将类中的一些或全部成员标记为抽象。
抽象成员意味着在这个类中没有具体的实现。
这里有一个特别的地方是，对于抽象类或函数，不需要使用`open`关键字进行标注。

```kotlin
abstract class Polygon {
    abstract fun draw()
}

class Rectangle : Polygon() {
    override fun draw() {
        // 画矩形
    }
}
```

你可以用抽象的方法覆盖非抽象的 `open` 成员。

```kotlin
open class Polygon {
    open fun draw() {
        // 一些默认的多边形绘制方法
    }
}

abstract class WildShape : Polygon() {
    // 继承 WildShape 的类需要提供自己的
    // draw 方法，而不是使用 Polygon 上的默认方法
    abstract override fun draw()
}
```

## 伴生对象 {id=伴生对象}

如果你需要编写一个函数，该函数可以在没有类实例的情况下调用，但需要访问类的内部（例如工厂方法），你可以将其编写为该类内部的 [对象声明](object-declarations.md) 的成员。

更具体地说，如果你在类内部声明了一个[伴生对象](object-declarations.md#伴生对象)，你可以只使用类名作为限定符访问其成员。
