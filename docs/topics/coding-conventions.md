# 编码约定

通用且易于遵循的编码规范对于任何编程语言都至关重要。
在这里，我们提供了使用 Kotlin 的项目的代码风格和代码组织指南。

## 在 IDE 中配置风格

Kotlin 的两个最流行的 IDE - [IntelliJ IDEA](https://www.jetbrains.com/idea/) 和[Android Studio](https://developer.android.com/studio/)
提供了强大的代码风格支持。
您可以配置它们以使您的代码自动符合给定的代码风格。

### 应用代码风格指南

1. 转到 **Settings/Preferences | Editor | Code Style | Kotlin（设置/首选项 | 编辑器 | 代码样式 | Kotlin）**。
2. 点击 **Set from...（设置自...）**。
3. 选择 **Kotlin style guide（Kotlin 样式指南）**。

### 验证您的代码是否遵循代码风格

1. 转到 **Settings/Preferences | Editor | Inspections | General（设置/首选项 | 编辑器 | 检查 | 常规）**。
2. 打开 **Incorrect formatting（格式设置不正确）** 检查。
   额外的检查，验证代码风格指南中描述的其他问题（如命名约定）默认已启用。

## 源代码组织

### 目录结构

在纯 Kotlin 项目中，推荐的目录结构遵循包结构，省略了共同的根包。
例如，如果项目中的所有代码都在 `org.example.kotlin` 包及其子包中，那么带有 `org.example.kotlin` 包的文件应直接放在源目录下，而
`org.example.kotlin.network.socket` 中的文件应该位于源目录的 `network/socket` 子目录下。

> 在 JVM 上：在与 Java 一起使用 Kotlin 的项目中，Kotlin 源文件应存放在与 Java 源文件相同的源目录中，并遵循相同的目录结构：
> 每个文件应存储在与每个 package 语句相对应的目录中。
>
{style="note"}

### 源文件命名 {id=源文件命名}

如果一个 Kotlin 文件包含一个单一的类或接口（可能带有相关的顶级声明），其名称应与类的名称相同，附加 `.kt` 扩展名。这适用于所有类型的类和接口。
如果一个文件包含多个类或仅包含顶级声明，请选择一个描述文件内容的名称，并相应地命名文件。
使用[大驼峰命名法](https://en.wikipedia.org/wiki/Camel_case)（首字母大写，也称为帕斯卡命名法），例如，`ProcessDeclarations.kt`。

文件名应描述文件中代码的功能。因此，应避免在文件名中使用无意义的词汇，如 `Util`。

#### 多平台项目

在多平台项目中，具有平台特定源代码集中顶级声明的文件应具有与源代码集名称相关联的后缀。例如：

* **jvm**Main/kotlin/Platform.**jvm**.kt
* **android**Main/kotlin/Platform.**android**.kt
* **ios**Main/kotlin/Platform.**ios**.kt

至于公共源代码集，具有顶级声明的文件不应具有后缀。例如，`commonMain/kotlin/Platform.kt`。

##### 技术细节 {collapsible="true"}

由于 JVM 的限制，我们建议在多平台项目中遵循这种文件命名方案：不允许顶级成员（函数、属性）。

为了解决这个问题，Kotlin JVM 编译器会创建包含顶级成员声明的包装类（所谓的“文件门面（file facades）”）。文件门面的内部名称<tooltip term="派生">派生</tooltip>自文件名。

反过来，JVM 不允许具有相同 完全限定名（FQN，fully qualified name） 的多个类。这可能导致 Kotlin 项目无法编译到 JVM：

```none
root
|- commonMain/kotlin/myPackage/Platform.kt // 包含 'fun count() { }'
|- jvmMain/kotlin/myPackage/Platform.kt // 包含 'fun multiply() { }'
```

在这里，`Platform.kt` 文件都在相同的包中，因此 Kotlin JVM 编译器会生成两个文件门面，它们的 FQN 都是 `myPackage.PlatformKt`。
这会导致 "Duplicate JVM classes（重复的 JVM 类）" 错误。

最简单的避免方法是根据上述准则之一重命名其中一个文件。这种命名方案有助于避免冲突同时保持代码可读性。

> 有两种情况，这些建议可能看起来多余，但我们仍然建议遵循它们：
>
> * 非 JVM 平台不会出现重复文件门面的问题。然而，这种命名方案有助于保持文件命名的一致性。
> * 在 JVM 上，如果源文件没有顶级声明，将不会生成文件门面，因此不会出现命名冲突。
>
>   但是，这种命名方案有助于避免在简单的重构或添加操作中包含顶级函数并导致相同的 "Duplicate JVM classes" 错误的情况。


### 源文件组织

我们鼓励将多个声明（类、顶层函数或属性）放置在同一个 Kotlin 源文件中，只要这些声明在语义上彼此密切相关，并且文件大小保持合理（不超过几百行）。

特别是在为一个类定义适用于该类所有客户端的扩展函数时，请将它们放在与该类本身相同的文件中。
在为仅对特定客户端有意义的扩展函数时，将它们放在该客户端代码的旁边。
避免仅为容纳某个类的所有扩展而创建文件。

### 类布局

类的内容应按以下顺序排列：

1. 属性声明和初始化块
2. 辅助构造函数
3. 方法声明
4. 伴生对象

不要按字母顺序或可见性对方法声明进行排序，也不要将常规方法与扩展方法分开。
相反，将相关的内容放在一起，以便从上到下阅读类的人能够理解发生的逻辑。
选择一种顺序（首先是更高级别的内容，或相反），并坚持遵循它。

将嵌套类放在使用这些类的代码旁边。如果这些类旨在被外部使用且在类内部未被引用，请将它们放在伴生对象之后的末尾。

### 接口实现布局

在实现接口时，将实现的成员保持与接口成员相同的顺序（必要时，与用于实现的额外私有方法交错）。

### 重载布局

始终将重载放在类中彼此相邻的位置。

## 命名规则

在 Kotlin 中，包和类的命名规则非常简单：

* 包的名称始终为小写，并且不使用下划线（`org.example.project`）。
  通常不鼓励使用多个单词的名称，但如果确实需要使用多个单词，可以将它们简单拼接在一起，或者使用驼峰式命名（`org.example.myProject`）。

* 类和对象的名称以大写字母开头，使用驼峰式命名：

```kotlin
open class DeclarationProcessor { /*...*/ }

object EmptyDeclarationProcessor : DeclarationProcessor() { /*...*/ }
```

### 函数名称 {id=函数名称}
 
函数、属性和局部变量的名称以小写字母开头，使用驼峰式大小写且不带下划线：

```kotlin
fun processDeclarations() { /*...*/ }
var declarationCount = 1
```

例外：用于创建类实例的工厂函数可以与抽象返回类型同名：

```kotlin
interface Foo { /*...*/ }

class FooImpl : Foo { /*...*/ }

fun Foo(): Foo { return FooImpl() }
```

### 测试方法的命名

在测试中（**仅**在测试中），你可以使用用反引号括起来的带空格的方法名。
注意，这样的方法名仅在 Android 运行时 API 级别 30 及以上版本支持。
测试代码中的方法名也允许使用下划线。

```kotlin
class MyTestCase {
     @Test fun `ensure everything works`() { /*...*/ }
     
     @Test fun ensureEverythingWorks_onAndroid() { /*...*/ }
}
```

### 属性命名

常量的名称（使用`const`标记的属性，或者没有自定义的`get`函数并且包含深度不可变数据的顶级/对象`val`属性）应该使用大写下划线分隔的命名风格（[尖叫蛇命名](https://en.wikipedia.org/wiki/Snake_case)）：

```kotlin
const val MAX_COUNT = 8
val USER_NAME_FIELD = "UserName"
```

持有具有行为或可变数据的对象的顶级或对象属性应该使用驼峰命名法：

```kotlin
val mutableCollection: MutableSet<String> = HashSet()
```

持有对单例对象引用的属性可以使用与`object`声明相同的命名风格：

```kotlin
val PersonComparator: Comparator<Person> = /*...*/
```

对于枚举常量，可以使用大写下划线分隔的名称（[尖叫蛇命名](https://en.wikipedia.org/wiki/Snake_case)）（`enum class Color { RED, GREEN }`）或者大写驼峰命名法，具体取决于用途。

### 幕后属性的命名

如果一个类有两个在概念上相同，但一个是公共 API 的一部分，另一个是实现细节的属性，请使用下划线作为私有属性名称的前缀：

```kotlin
class C {
    private val _elementList = mutableListOf<Element>()

    val elementList: List<Element>
         get() = _elementList
}
```

### 选择良好的命名

类的名称通常是一个名词或一个解释类是什么的名词短语：`List`（列表），`PersonReader`（人员阅读器）。

方法的名称通常是一个动词或一个解释方法做什么的动词短语：`close`（关闭），`readPersons`（读取人员）。
名称还应该表明方法是否改变对象或返回一个新对象。例如，`sort`（排序）正在就地对集合进行排序，而 `sorted`（已排序）返回集合的已排序副本。

名称应该清楚地表明实体的目的，因此最好避免在名称中使用无意义的词汇（`Manager`（管理器），`Wrapper`（包装器））。

在声明名称中使用首字母缩写时，如果它由两个字母组成，则将其大写（`IOStream`）；如果它更长，则仅大写第一个字母（`XmlFormatter`，`HttpInputStream`）。

## 格式化

### 缩进

使用四个空格进行缩进，不要使用制表符。

对于花括号，将开放的花括号放在构造开始的行的末尾，将闭合的花括号放在与开放构造横向对齐的单独行上。

```kotlin
if (elements != null) {
    for (element in elements) {
        // ...
    }
}
```

> 在 Kotlin 中，分号是可选的，因此换行是有意义的。
> 语言设计假定采用 Java 风格的花括号，如果尝试使用不同的格式化风格，可能会遇到意外的行为。
>
{style="note"}

### 水平空白

* 在二元运算符周围加上空格（`a + b`）。例外：在“范围运算符（range to）”（`0..i`）周围不加空格。

* 不要在一元运算符周围加上空格（`a++`）。

* 在控制流关键字（`if`、`when`、`for`和`while`）与相应的开括号之间加上空格。

* 在主构造函数声明、方法声明或方法调用中，不要在开括号前加上空格。

```kotlin
class A(val x: Int)

fun foo(x: Int) { ... }

fun bar() {
    foo(1)
}
```

* 在 `(`、`[` 之后和 `]`、`)` 之前不加空格。

* 在 `.` 或 `?.` 周围不加空格：`foo.bar().filter { it > 2 }.joinToString()`，`foo?.bar()`

* 在 `//` 后加上空格：`// 这是一条注释`

* 在用于指定类型参数的尖括号周围不加空格：`class Map<K, V> { ... }`

* 在 `::` 周围不加空格：`Foo::class`，`String::length`

* 在标记可空类型的 `?` 前不加空格：`String?`

作为一般规则，避免任何形式的水平对齐。将标识符重命名为不同长度的名称不应该影响声明或者任何用法的格式。

### 冒号

在以下情况下，在 `:` 前面加上空格：

* 用于分隔类型和超类型时
* 在委托到超类构造函数或同一类的不同构造函数时
* 在 `object` 关键字后

在声明和其类型之间用冒号时，不要在 `:` 前面加空格。

始终在 `:` 后面加上空格。

```kotlin
abstract class Foo<out T : Any> : IFoo {
    abstract fun foo(a: Int): T
}

class FooImpl : Foo() {
    constructor(x: String) : this(x) { /*...*/ }
    
    val x = object : IFoo { /*...*/ } 
} 
```

### 类头部

具有少量主构造函数参数的类可以写在单行上：

```kotlin
class Person(id: Int, name: String)
```

具有较长头部的类应该进行格式化，使每个主构造函数参数都在单独的一行，并进行缩进。
此外，闭括号应该在新的一行上。如果使用继承，超类构造函数调用或实现的接口列表应该位于与括号相同的行上：

```kotlin
class Person(
    id: Int,
    name: String,
    surname: String
) : Human(id, name) { /*...*/ }
```

对于多个接口，应该放置超类构造函数调用在第一位，然后每个接口应该位于不同的行：

```kotlin
class Person(
    id: Int,
    name: String,
    surname: String
) : Human(id, name),
    KotlinMaker { /*...*/ }
```

对于具有长<tooltip term="超类型列表">超类型列表</tooltip>的类，在冒号后进行换行，并水平对齐所有超类的名称：

```kotlin
class MyFavouriteVeryLongClassHolder :
    MyLongHolder<MyFavouriteVeryLongClass>(),
    SomeOtherInterface,
    AndAnotherOne {

    fun foo() { /*...*/ }
}
```

为了在类头部较长时清晰地分隔类头和类体，可以在类头部后面加上一个空行（如上面的示例）或将开花括号放在单独的一行：

```kotlin
class MyFavouriteVeryLongClassHolder :
    MyLongHolder<MyFavouriteVeryLongClass>(),
    SomeOtherInterface,
    AndAnotherOne 
{
    fun foo() { /*...*/ }
}
```

对于构造函数参数，请使用常规缩进（四个空格）。这确保在主构造函数中声明的属性与类体中声明的属性具有相同的缩进。

### 修饰符顺序

如果一个声明有多个修饰符，请按照以下顺序放置它们：

```kotlin
public / protected / private / internal
expect / actual
final / open / abstract / sealed / const
external
override
lateinit
tailrec
vararg
suspend
inner
enum / annotation / fun // 作为 `fun interface` 中的修饰符
companion
inline / value
infix
operator
data
```

将所有注解放在修饰符之前：

```kotlin
@Named("Foo")
private val foo: Foo
```

除非你在开发一个库，否则省略冗余的修饰符（例如，`public`）。

### 注解

将注解放在它们所附加的声明之前的单独行上，并且使用相同的缩进：

```kotlin
@Target(AnnotationTarget.PROPERTY)
annotation class JsonExclude
```

没有参数的注解可以放在同一行上：

```kotlin
@JsonExclude @JvmField
var x: String
```

没有参数的单个注解可以与相应的声明放在同一行上：

```kotlin
@Test fun foo() { /*...*/ }
```

### 文件注解

文件注解放在文件注释（如果有的话）之后，在 `package` 语句之前，与 `package` 之间用一个空行分隔（以强调它们针对的是文件而不是包）。

```kotlin
/** 许可、版权等等 */
@file:JvmName("FooBar")

package foo.bar
```

### 函数

如果函数签名不适合放在一行上，请使用以下语法：

```kotlin
fun longMethodName(
    argument: ArgumentType = defaultValue,
    argument2: AnotherArgumentType,
): ReturnType {
    // 主体
}
```

对于函数参数，请使用常规缩进（四个空格）。这有助于确保与构造函数参数的一致性。

对于函数体只包含单个表达式的函数，最好使用表达式体。

```kotlin
fun foo(): Int {     // 坏的写法
    return 1 
}

fun foo() = 1        // 好的写法
```

### 表达式体

如果函数有一个表达式体，其第一行不能放在与声明相同的行上，请将 `=` 符号放在第一行，并将表达式体缩进四个空格。

```kotlin
fun f(x: String, y: String, z: String) =
    veryLongFunctionCallWithManyWords(andLongParametersToo(), x, y, z)
```

### 属性

对于非常简单的只读属性，请考虑单行格式化：

```kotlin
val isEmpty: Boolean get() = size == 0
```

对于更复杂的属性，请始终将 `get` 和 `set` 关键字放在不同的行上：

```kotlin
val foo: String
    get() { /*...*/ }
```

对于带有初始化程序的属性，如果初始化程序很长，请在 `=` 符号之后添加一个换行，并将初始化程序缩进四个空格：

```kotlin
private val defaultCharset: Charset? =
    EncodingRegistry.getInstance().getDefaultCharsetForPropertiesFiles(file)
```

### 控制流语句

如果 `if` 或 `when` 语句的条件跨足多行，请始终在语句体周围使用花括号。
相对于语句的开始，将条件的每一行缩进四个空格。
将条件的闭括号与开花括号放在单独的一行上：

```kotlin
if (!component.isSyncing &&
    !hasAnyKotlinRuntimeInScope(module)
) {
    return createKotlinNotConfiguredPanel(module)
}
```

这有助于对齐条件和语句体。

将 `else`、`catch`、`finally` 关键字，以及 `do-while` 循环的 `while` 关键字放在前面花括号的同一行：

```kotlin
if (condition) {
    // 主体
} else {
    // else 部分
}

try {
    // 主体
} finally {
    // 清理
}
```

在 `when` 语句中，如果一个分支超过一行，请考虑在它与相邻的 case 块之间加上一个空行：

```kotlin
private fun parsePropertyValue(propName: String, token: Token) {
    when (token) {
        is Token.ValueToken ->
            callback.visitValue(propName, token.value)

        Token.LBRACE -> { // ...
        }
    }
}
```

将短的分支放在与条件相同的行上，不使用花括号。

```kotlin
when (foo) {
    true -> bar() // 好的写法
    false -> { baz() } // 坏的写法
}
```

### 方法调用

在较长的参数列表中，在开括号后进行换行。通过四个空格缩进参数。
将多个密切相关的参数组合在同一行上。

```kotlin
drawSquare(
    x = 10, y = 10,
    width = 100, height = 100,
    fill = true
)
```

在分隔参数名和值的 `=` 符号周围加上空格。

### 包装链式调用

在包装链式调用时，将 `.` 字符或 `?.` 操作符放在下一行，缩进单倍空格：

```kotlin
val anchor = owner
    ?.firstChild!!
    .siblings(forward = true)
    .dropWhile { it is PsiComment || it is PsiWhiteSpace }
```

链中的第一个调用通常应该在其前面有一个换行，但如果代码以这种方式更有意义，也可以省略。

### Lambda 表达式 {id=lambdas}

在 Lambda 表达式中，应在花括号周围以及将 参数 与 代码体 分隔的箭头周围使用空格。如果调用接受一个 Lambda，请尽可能将其放在括号外传递。

```kotlin
list.filter { it > 10 }
```

如果为 Lambda 分配一个标签，请不要在标签和开放花括号之间加上空格：

```kotlin
fun foo() {
    ints.forEach lit@{
        // ...
    }
}
```

在多行 Lambda 中声明参数名称时，请将名称放在第一行，然后是箭头和换行符：

```kotlin
appendCommaSeparated(properties) { prop ->
    val propertyValue = prop.get(obj)  // ...
}
```

如果参数列表太长无法放在一行上，请将箭头放在单独的一行上：

```kotlin
foo {
   context: Context,
   environment: Env
   ->
   context.configureEnv(environment)
}
```

### 尾随逗号 {id=尾随逗号}

尾随逗号是一系列元素中最后一个元素之后的逗号符号：

```kotlin
class Person(
    val firstName: String,
    val lastName: String,
    val age: Int, // 尾随逗号
)
```

使用尾随逗号有几个好处：

* 使版本控制的差异更清晰 - 因为所有的焦点都在更改的值上。
* 使添加和重新排序元素变得简单 - 如果操作元素，就无需添加或删除逗号。
* 简化代码生成，例如，对于对象初始化器。最后一个元素也可以有一个逗号。

尾随逗号是完全可选的 - 没有它们，你的代码仍然可以正常工作。Kotlin 风格指南鼓励在声明处使用尾逗号，并将其作为在调用处的自由裁量。

要在 IntelliJ IDEA 格式化器中启用尾逗号，请转到
**Settings/Preferences | Editor | Code Style | Kotlin（设置/首选项 | 编辑器 | 代码样式 | Kotlin）**，
打开 **Other（其他）** 选项卡，并选择 **Use trailing comma（使用尾随逗号）** 选项。

#### 枚举 {collapsible="true"}

```kotlin
enum class Direction {
    NORTH,
    SOUTH,
    WEST,
    EAST, // 尾随逗号
}
```

#### 值参数 {collapsible="true"}

```kotlin
fun shift(x: Int, y: Int) { /*...*/ }
shift(
    25,
    20, // 尾随逗号
)
val colors = listOf(
    "red",
    "green",
    "blue", // 尾随逗号
)
```

#### 类属性和参数 {collapsible="true"}

```kotlin
class Customer(
    val name: String,
    val lastName: String, // 尾随逗号
)
class Customer(
    val name: String,
    lastName: String, // 尾随逗号
)
```

#### 函数值参数 {collapsible="true"}

```kotlin
fun powerOf(
    number: Int, 
    exponent: Int, // 尾随逗号
) { /*...*/ }
constructor(
    x: Comparable<Number>,
    y: Iterable<Number>, // 尾随逗号
) {}
fun print(
    vararg quantity: Int,
    description: String, // 尾随逗号
) {}
```

#### 具有可选类型的参数（包括设置器） {collapsible="true"}

```kotlin
val sum: (Int, Int, Int) -> Int = fun(
    x,
    y,
    z, // 尾随逗号
): Int {
    return x + y + x
}
println(sum(8, 8, 8))
```

#### 索引后缀 {collapsible="true"}

```kotlin
class Surface {
    operator fun get(x: Int, y: Int) = 2 * x + 4 * y - 10
}
fun getZValue(mySurface: Surface, xValue: Int, yValue: Int) =
    mySurface[
        xValue,
        yValue, // 尾随逗号
    ]
```

#### lambda 中的参数 {collapsible="true" id="lambda中的参数"}

```kotlin
fun main() {
    val x = {
            x: Comparable<Number>,
            y: Iterable<Number>, // 尾随逗号
        ->
        println("1")
    }
    println(x)
}
```

#### `when` 入口 {collapsible="true"}

```kotlin
fun isReferenceApplicable(myReference: KClass<*>) = when (myReference) {
    Comparable::class,
    Iterable::class,
    String::class, // 尾随逗号
        -> true
    else -> false
}
```

#### 集合字面量（在注解中） {collapsible="true"}

```kotlin
annotation class ApplicableFor(val services: Array<String>)
@ApplicableFor([
    "serializer",
    "balancer",
    "database",
    "inMemoryCache", // 尾随逗号
])
fun run() {}
```

#### 类型实参 {collapsible="true"}

```kotlin
fun <T1, T2> foo() {}
fun main() {
    foo<
            Comparable<Number>,
            Iterable<Number>, // 尾随逗号
            >()
}
```

#### 类型形参 {collapsible="true"}

```kotlin
class MyMap<
        MyKey,
        MyValue, // trailing comma
        > {}
```

#### 解构声明 {collapsible="true"}

```kotlin
data class Car(val manufacturer: String, val model: String, val year: Int)
val myCar = Car("Tesla", "Y", 2019)
val (
    manufacturer,
    model,
    year, // 尾随逗号
) = myCar
val cars = listOf<Car>()
fun printMeanValue() {
    var meanValue: Int = 0
    for ((
        _,
        _,
        year, // 尾随逗号
    ) in cars) {
        meanValue += year
    }
    println(meanValue/cars.size)
}
printMeanValue()
```

## 文档注释

对于较长的文档注释，请将起始符 `/**` 放在独立的一行，并在后续每一行以星号开头：

```kotlin
/**
 * 这是文档注释
 * 在多行上。
 */
```

简短注释可以放在单独的一行：

```kotlin
/** 这是一个简短的文档注释。 */
```

通常避免使用 `@param` 和 `@return` 标签。
相反，将参数和返回值的描述直接融入文档注释中，并在提及参数的地方添加参数链接。
仅在需要提供无法融入主文本流的详细描述时使用 `@param` 和 `@return`。

```kotlin
// 避免这样做：

/**
 * 返回给定数字的绝对值。
 * @param number 要返回其绝对值的数字。
 * @return The 绝对值。
 */
fun abs(number: Int): Int { /*...*/ }

// 改为这样做：

/**
 * 返回给定 [number] 的绝对值。
 */
fun abs(number: Int): Int { /*...*/ }
```

## 避免冗余结构

通常情况下，如果 Kotlin 中的某种语法结构是可选的并且由 IDE 标识为冗余的，应该在代码中省略它。
不要在代码中保留不必要的语法元素仅仅是为了“清晰”。

### Unit 返回类型

如果函数返回 Unit，应省略返回类型：

```kotlin
fun foo() { // `": Unit"` 在这里被省略。

}
```

### 分号

尽可能地省略分号。

### 字符串模板

在将简单变量插入字符串模板时，不要使用花括号。仅在表达式较长时使用花括号。

```kotlin
println("$name has ${children.size} children")
```

## 语言特性的惯用方式

### 不可变性 {id=不可变性}

更倾向于使用不可变数据而非可变数据。如果在初始化后不会修改，始终将局部变量和属性声明为 `val` 而不是 `var`。

总是使用不可变集合接口（`Collection`，`List`，`Set`，`Map`）声明不会被修改的集合。
在使用工厂函数创建集合实例时，尽量使用返回不可变集合类型的函数：

```kotlin
// 坏的写法：使用可变集合类型来获取不会改变的值
fun validateValue(actualValue: String, allowedValues: HashSet<String>) { ... }

// 好的写法：使用不可变集合类型代替
fun validateValue(actualValue: String, allowedValues: Set<String>) { ... }

// 坏的写法： arrayListOf() 返回 ArrayList<T>，这是一个可变集合类型
val allowedValues = arrayListOf("a", "b", "c")

// 好的写法：listOf() 返回 List<T>
val allowedValues = listOf("a", "b", "c")
```

### 默认参数值

更倾向于使用带有默认参数值的函数声明，而不是声明重载函数。

```kotlin
// 坏的写法
fun foo() = foo("a")
fun foo(a: String) { /*...*/ }

// 好的写法
fun foo(a: String = "a") { /*...*/ }
```

### 类型别名

如果在代码库中多次使用功能型类型或具有类型形参的类型，请更倾向于为其定义一个类型别名：

```kotlin
typealias MouseClickHandler = (Any, MouseEvent) -> Unit
typealias PersonIndex = Map<String, Person>
```
如果使用私有或内部类型别名以避免名称冲突，更倾向于使用 [包和导入](packages.md) 中提到的 `import ... as ...`。

### Lambda 参数 {id="lambda参数"}

在简短且不嵌套的 Lambda 中，建议使用 `it` 约定而不是显式声明参数。在带有参数的嵌套 Lambda 中，始终显式声明参数。

### Lambda 中的返回 {id="lambda中的返回"}

避免在 Lambda 中使用多个带标签的返回。考虑重构 Lambda 以使其具有单一的退出点。如果这不可行或不够清晰，请考虑将 Lambda 转换为匿名函数。

不要对 Lambda 中的最后一条语句使用带标签的返回。

### 具名函数

当方法接受多个相同原始类型的参数，或者是 `Boolean` 类型的参数时，请使用具名函数语法，除非从上下文中完全清晰地了解所有参数的含义。

```kotlin
drawSquare(x = 10, y = 10, width = 100, height = 100, fill = true)
```

### 条件语句

优先使用 `try`、`if` 和 `when` 的表达式形式。

```kotlin
return if (x) foo() else bar()
```

```kotlin
return when(x) {
    0 -> "zero"
    else -> "nonzero"
}
```

上述内容优于：

```kotlin
if (x)
    return foo()
else
    return bar()
```

```kotlin
when(x) {
    0 -> return "zero"
    else -> return "nonzero"
}    
```

### if 与 when 的选择

在二元条件中，推荐使用 `if` 而不是 `when`。
例如，使用以下 `if` 语法：

```kotlin
if (x == null) ... else ...
```

而不是以下 `when` 语法：

```kotlin
when (x) {
    null -> // ...
    else -> // ...
}
```

如果有三个或更多选项，请优先使用 `when`。

### 在条件语句中使用可空布尔值

如果需要在条件语句中使用可空的 `Boolean`，请使用 `if (value == true)` 或 `if (value == false)` 进行检查。

### 循环

优先使用高阶函数（`filter`、`map` 等）而不是循环。
例外情况：`forEach`（优先使用普通的 `for` 循环，除非 `forEach` 的接收者可空或 `forEach` 作为较长调用链的一部分使用）。

在选择使用多个高阶函数和循环之间时，了解每种情况下执行的操作开销，并考虑性能方面的考虑。

### 区间上的循环

使用 `..<` 运算符循环遍历开放区间：

```kotlin
for (i in 0..n - 1) { /*...*/ }  // 坏的写法
for (i in 0..<n) { /*...*/ }  // 好的写法
```

### 字符串 {id=字符串}

优先使用字符串模板而不是字符串连接。

优先使用多行字符串而不是将 `\n` 转义序列嵌入常规字符串文字中。

为保持多行字符串的缩进，当生成的字符串不需要任何内部缩进时，请使用 `trimIndent`，或者当需要内部缩进时，请使用 `trimMargin`：

```kotlin
fun main() {
//sampleStart
   println("""
    不是
    修剪过的
    文本
    """
   )

   println("""
    修剪过的
    文本
    """.trimIndent()
   )

   println()

   val a = """修剪到边距文本：
          |if(a > 1) {
          |    return a
          |}""".trimMargin()

   println(a)
//sampleEnd
}
```
{kotlin-runnable="true"}

了解 [Java 和 Kotlin 多行字符串](java-to-kotlin-idioms-strings.md#use-multiline-strings) 之间的区别。

### 函数与属性的选择

在某些情况下，没有参数的函数可能与只读属性可互换。尽管语义相似，但在何时更喜欢其中一个时存在一些风格约定。

当底层算法：

* 不会抛出异常
* 计算廉价（或在第一次运行时缓存）
* 在对象状态未更改的情况下在调用之间返回相同结果时

优先使用属性而不是函数。

### 扩展函数

你可以自由地使用扩展函数。每当有一个主要作用于对象的函数时，考虑将其作为接收该对象的扩展函数。
为了最小化 API 污染，根据情况限制扩展函数的可见性。
必要时使用局部扩展函数、成员扩展函数，或者具有私有可见性的顶层扩展函数。

### 中缀函数

只有在函数作用于两个扮演相似角色的对象时才将函数声明为 `infix`。良好的例子包括：`and`、`to`、`zip`。不好的例子是：`add`。

如果方法会改变接收者对象，请勿将其声明为 `infix`。

### 工厂函数

如果为类声明工厂函数，请避免将其命名为与类本身相同的名称。
最好使用一个独特的名称，清楚表明工厂函数的行为为何特殊。
仅当真的没有特殊语义时，才可以使用与类相同的名称。

```kotlin
class Point(val x: Double, val y: Double) {
    companion object {
        fun fromPolar(angle: Double, radius: Double) = Point(...)
    }
}
```

如果你有一个对象有多个重载的构造函数，并且它们不调用不同的超类构造函数，也不能简化为带有默认参数值的单个构造函数，建议用工厂函数替换重载的构造函数。

### 平台类型

返回平台类型表达式的公共函数/方法必须明确声明其 Kotlin 类型：

```kotlin
fun apiCall(): String = MyJavaApi.getProperty("name")
```

任何使用平台类型表达式初始化的属性（包级别或类级别）都必须明确声明其 Kotlin 类型：

```kotlin
class Person {
    val name: String = MyJavaApi.getProperty("name")
}
```

使用平台类型表达式初始化的局部值可能具有类型声明，也可能没有类型声明：

```kotlin
fun main() {
    val name = MyJavaApi.getProperty("name")
    println(name)
}
```

### 作用域函数 apply/with/run/also/let

Kotlin 提供了一组函数，用于在给定对象的上下文中执行一块代码：`let`、`run`、`with`、`apply` 和 `also`。
关于在特定情况下选择正确的作用域函数的指导，请参考[作用域函数](scope-functions.md)。

## 库的编码约定

在编写库时，建议遵循一组额外的规则，以确保 API 的稳定性：

* 始终明确指定成员的可见性（以避免意外将声明暴露为公共 API）
* 始终明确指定函数返回类型和属性类型（以避免在实现更改时意外更改返回类型）
* 为所有公共成员提供[KDoc](kotlin-doc.md)注释，除了不需要任何新文档的覆盖（以支持为库生成文档）

在[库创建者指南](https://kotlinlang.org/docs/jvm-api-guidelines-introduction.html)中了解有关在为库编写 API 时考虑的最佳实践和想法。
