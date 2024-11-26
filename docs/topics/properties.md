[//]: # (title: 属性)

## 声明属性 {id=声明属性}

在 Kotlin 类中，属性可以分为可变（读写）和只读两种类型。可变属性使用 `var` 关键字声明，而只读属性则使用 `val` 关键字声明。

```kotlin
class Address {
    var name: String = "Holmes, Sherlock"
    var street: String = "Baker"
    var city: String = "London"
    var state: String? = null
    var zip: String = "123456"
}
```

要使用属性，只需通过其名称引用即可：

```kotlin
fun copyAddress(address: Address): Address {
    val result = Address() // 在 Kotlin 中没有 'new' 关键字
    result.name = address.name // 调用访问器
    result.street = address.street
    // ...
    return result
}
```

## Getters 和 Setters {id=getters和setters}

声明属性的完整语法如下：

```kotlin
var <propertyName>[: <属性类型>] [= <属性初始化值>]
    [<getter>]
    [<setter>]
```

属性的初始化程序、getter 和 setter 都是可选的。
如果属性的类型可以从初始化程序或 getter 的返回类型中推断出来，那么在属性声明中可以省略类型信息，如下所示：

```kotlin
var initialized = 1 // 类型为 Int，具有默认的 getter 和 setter
// var allByDefault // 错误：需要显式初始化程序，但具有默认的 getter 和 setter
```

只读属性声明的完整语法与可变属性的语法有两点不同：以 `val` 关键字开头而不是 `var`，并且不允许包含 setter 方法：

```kotlin
val simple: Int? // 类型为 Int，具有默认的 getter，必须在构造函数中初始化
val inferredType = 1 // 类型为 Int，并且具有默认的 getter
```

您可以为属性定义自定义访问器。如果定义了自定义 getter，每次访问属性时都会调用它（这样你可以实现一个计算属性）。
下面是一个自定义 getter 的示例：

```kotlin
//sampleStart
class Rectangle(val width: Int, val height: Int) {
    val area: Int // 属性类型是可选的，因为它可以从 getter 的返回类型中推断出来
        get() = this.width * this.height
}
//sampleEnd
fun main() {
    val rectangle = Rectangle(3, 4)
    println("Width=${rectangle.width}, height=${rectangle.height}, area=${rectangle.area}")
}
```
{kotlin-runnable="true"}

如果可以从 getter 推断出来，可以省略属性类型：

```kotlin
val area get() = this.width * this.height
```

如果定义了自定义 setter，每次为属性赋值（除了初始化）时都会调用它。自定义 setter 如下：

```kotlin
var stringRepresentation: String
    get() = this.toString()
    set(value) {
        setDataFromString(value) // 解析字符串并将值分配给其他属性
    }
```

按照惯例，setter 参数的名称为 `value`，但如果喜欢，可以选择其他名称。

如果需要为访问器添加注释或更改其可见性，但又不想更改默认实现，可以定义访问器而不定义其主体：

```kotlin
var setterVisibility: String = "abc"
    private set // setter 是私有的，并且具有默认的实现

var setterWithAnnotation: Any? = null
    @Inject set // 用 Inject 注释 setter
```

### 幕后字段 {id=幕后字段}

> 译者注：
> 
> 在Kotlin中，属性可以有一个关联的字段，这个字段用于存储属性的实际值。
> 这个字段通常是在属性的 getter 和 setter 中使用，而在外部是不可见的，因此称为“幕后字段”。
>
{style=info}

在 Kotlin 中，字段仅作为属性的一部分，用于在内存中保存属性的值。
字段不能直接声明。
然而，当属性需要一个幕后字段时，Kotlin 会自动提供。
这个幕后字段可以在访问器中使用 `field` 标识符引用：

```kotlin
var counter = 0 // 初始化程序直接分配给了幕后属性
    set(value) {
        if (value >= 0)
            // 正确用法
            field = value // 使用 'field' 代替 'counter' 本身
            // 错误用法
            // counter = value // 错误：堆栈溢出，使用实际名称 'counter' 将使 setter 递归调用
    }
```

`field` 标识符只能在属性的访问器中使用。

如果属性使用至少一个访问器的默认实现，或者如果自定义访问器通过 `field` 标识符引用它，将会为该属性生成一个幕后字段。

例如，在以下情况下将不会有幕后字段：

```kotlin
val isEmpty: Boolean
    get() = this.size == 0
```

### 幕后属性 {id=幕后属性}

> 译者注：
> 
> 幕后属性是指具有自定义 getter 或 setter 的属性。幕后属性使用自定义的逻辑来获取或设置值，但在外部看起来就像是普通的属性。
>
{style=info}

如果你想要做一些不符合这个[**隐式幕后字段**](implicit-backing-field.md)方案的事情，你总是可以退而使用一个**幕后属性**：

```kotlin
private var _table: Map<String, Int>? = null
public val table: Map<String, Int>
    get() {
        if (_table == null) {
            _table = HashMap() // 类型参数会被推断
        }
        return _table ?: throw AssertionError("Set to null by another thread")
    }
```

> 在 JVM 上：对具有默认 getter 和 setter 的私有属性的访问进行了优化，以避免函数调用开销。
>
{style="note"}

## 编译时常量 {id=编译时常量}

如果一个只读属性的值在编译时已知，请使用 `const` 修饰符将其标记为**编译时常量**。这样的属性需要满足以下要求：

* 必须是顶层属性，或者是 [`object` 声明](object-declarations.md#object-declarations-overview) 的成员或 **[伴生对象](object-declarations.md#companion-objects)** 的成员。
* 必须用 `String` 类型或原始类型的值进行初始化。
* 不能有自定义的 getter。

编译器会将代码中的内联常量替换为其实际值，但这并不会移除定义的常量本身，因此仍然可以使用[反射](reflection.md)与之交互。

这样的属性也可以在注解中使用：

```kotlin
const val SUBSYSTEM_DEPRECATED: String = "This subsystem is deprecated"

@Deprecated(SUBSYSTEM_DEPRECATED) fun foo() { ... }
```

实际编译成：

```Java
@NotNull
public static final String SUBSYSTEM_DEPRECATED = "This subsystem is deprecated";

@Deprecated(
  message = "This subsystem is deprecated"
)
public static final void foo() { ... }
```

可以发现，`@Deprecated` 中的 `message` 被替换成了实际值，常量 `SUBSYSTEM_DEPRECATED` 也没有被删除。

## 延迟初始化属性和变量 {id=late-initialized-properties-and-variables}

通常，声明为非空类型的属性在构造函数中必须初始化。然而，有时情况并不方便。
例如，属性可能通过依赖注入进行初始化，或者在单元测试的设置方法中初始化。
在这些情况下，你无法在构造函数中提供非空初始化程序，但仍然希望在类体内引用属性时避免空检查。

为处理这类情况，可以使用 `lateinit` 修饰符标记属性：

```kotlin
public class MyTest {
    lateinit var subject: TestSubject

    @SetUp fun setup() {
        subject = TestSubject()
    }

    @Test fun test() {
        subject.method()  // 直接引用
    }
}
```

此修饰符可用于在类体中声明的 `var` 属性（不在主构造函数中，且属性没有自定义的 getter 或 setter），以及顶层属性和局部变量。
属性或变量的类型必须是非空的，且不能是原始类型。

在初始化之前访问 `lateinit` 属性会引发一个特殊的异常，清晰地标识正在访问的属性以及它尚未被初始化的事实。

### 检查 `lateinit var` 是否已初始化 {id=checking-whether-a-lateinit-var-is-initialized}

要检查 `lateinit var` 是否已经初始化，可以在对[该属性的引用](reflection.md#property-references)上使用 `.isInitialized`：

```kotlin
if (foo::bar.isInitialized) {
    println(foo.bar)
}
```

此检查仅适用于在词法上可访问的属性，并符合以下声明之一时： 
- 在同一类型中 
- 在外部类型之一中 
- 在同一文件的顶层

## 覆盖属性 {id=覆盖属性}

参见 [覆盖属性](inheritance.md#重写属性)

## 委托属性 {id=委托属性}

最常见的一类属性就是简单地从幕后字段中读取（也可能是写入），但使用自定义的 getter 和 setter 允许你实现属性的任何行为。
介于第一种的简单性和第二种的多样性之间，存在一些属性可以做什么的通用模式。
一些例子包括：延迟加载的值、通过给定键从映射中读取值、访问数据库，以及在访问时通知监听器。

这些常见行为可以使用[委托属性](delegated-properties.md)作为库来实现。
