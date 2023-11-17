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

### 源文件命名

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

为了解决这个问题，Kotlin JVM 编译器会创建包含顶级成员声明的包装类（所谓的“文件门面（file facades）”）。文件门面的内部名称派生自文件名。

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

### 函数名称
 
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

**当且仅当**在测试中，您可以使用用反引号括起来的带有空格的方法名称。
请注意，当前 Android 运行时不支持此类方法名称。在测试代码中也允许在方法名称中使用下划线。

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

### Colon

Put a space before `:` in the following cases:

* when it's used to separate a type and a supertype
* when delegating to a superclass constructor or a different constructor of the same class
* after the `object` keyword
    
Don't put a space before `:` when it separates a declaration and its type.
 
Always put a space after `:`.

```kotlin
abstract class Foo<out T : Any> : IFoo {
    abstract fun foo(a: Int): T
}

class FooImpl : Foo() {
    constructor(x: String) : this(x) { /*...*/ }
    
    val x = object : IFoo { /*...*/ } 
} 
```

### Class headers

Classes with a few primary constructor parameters can be written in a single line:

```kotlin
class Person(id: Int, name: String)
```

Classes with longer headers should be formatted so that each primary constructor parameter is in a separate line with indentation.
Also, the closing parenthesis should be on a new line. If you use inheritance, the superclass constructor call, or 
the list of implemented interfaces should be located on the same line as the parenthesis:

```kotlin
class Person(
    id: Int,
    name: String,
    surname: String
) : Human(id, name) { /*...*/ }
```

For multiple interfaces, the superclass constructor call should be located first and then each interface should
be located in a different line:

```kotlin
class Person(
    id: Int,
    name: String,
    surname: String
) : Human(id, name),
    KotlinMaker { /*...*/ }
```

For classes with a long supertype list, put a line break after the colon and align all supertype names horizontally:

```kotlin
class MyFavouriteVeryLongClassHolder :
    MyLongHolder<MyFavouriteVeryLongClass>(),
    SomeOtherInterface,
    AndAnotherOne {

    fun foo() { /*...*/ }
}
```

To clearly separate the class header and body when the class header is long, either put a blank line
following the class header (as in the example above), or put the opening curly brace on a separate line:

```kotlin
class MyFavouriteVeryLongClassHolder :
    MyLongHolder<MyFavouriteVeryLongClass>(),
    SomeOtherInterface,
    AndAnotherOne 
{
    fun foo() { /*...*/ }
}
```

Use regular indent (four spaces) for constructor parameters. This ensures that properties declared in the primary constructor have the same indentation as properties
declared in the body of a class.

### Modifiers order

If a declaration has multiple modifiers, always put them in the following order:

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
enum / annotation / fun // as a modifier in `fun interface` 
companion
inline / value
infix
operator
data
```

Place all annotations before modifiers:

```kotlin
@Named("Foo")
private val foo: Foo
```

Unless you're working on a library, omit redundant modifiers (for example, `public`).

### Annotations

Place annotations on separate lines before the declaration to which they are attached, and with the same indentation:

```kotlin
@Target(AnnotationTarget.PROPERTY)
annotation class JsonExclude
```

Annotations without arguments may be placed on the same line:

```kotlin
@JsonExclude @JvmField
var x: String
```

A single annotation without arguments may be placed on the same line as the corresponding declaration:

```kotlin
@Test fun foo() { /*...*/ }
```

### File annotations

File annotations are placed after the file comment (if any), before the `package` statement, 
and are separated from `package` with a blank line (to emphasize the fact that they target the file and not the package).

```kotlin
/** License, copyright and whatever */
@file:JvmName("FooBar")

package foo.bar
```

### Functions

If the function signature doesn't fit on a single line, use the following syntax:

```kotlin
fun longMethodName(
    argument: ArgumentType = defaultValue,
    argument2: AnotherArgumentType,
): ReturnType {
    // body
}
```

Use regular indent (four spaces) for function parameters. It helps ensure consistency with constructor parameters.

Prefer using an expression body for functions with the body consisting of a single expression.

```kotlin
fun foo(): Int {     // bad
    return 1 
}

fun foo() = 1        // good
```

### Expression bodies

If the function has an expression body whose first line doesn't fit on the same line as the declaration, put the `=` sign on the first line
and indent the expression body by four spaces.

```kotlin
fun f(x: String, y: String, z: String) =
    veryLongFunctionCallWithManyWords(andLongParametersToo(), x, y, z)
```

### Properties

For very simple read-only properties, consider one-line formatting:

```kotlin
val isEmpty: Boolean get() = size == 0
```

For more complex properties, always put `get` and `set` keywords on separate lines:

```kotlin
val foo: String
    get() { /*...*/ }
```

For properties with an initializer, if the initializer is long, add a line break after the `=` sign
and indent the initializer by four spaces:

```kotlin
private val defaultCharset: Charset? =
    EncodingRegistry.getInstance().getDefaultCharsetForPropertiesFiles(file)
```

### Control flow statements

If the condition of an `if` or `when` statement is multiline, always use curly braces around the body of the statement.
Indent each subsequent line of the condition by four spaces relative to the statement start. 
Put the closing parentheses of the condition together with the opening curly brace on a separate line:

```kotlin
if (!component.isSyncing &&
    !hasAnyKotlinRuntimeInScope(module)
) {
    return createKotlinNotConfiguredPanel(module)
}
```

This helps align the condition and statement bodies. 

Put the `else`, `catch`, `finally` keywords, as well as the `while` keyword of a `do-while` loop, on the same line as the 
preceding curly brace:

```kotlin
if (condition) {
    // body
} else {
    // else part
}

try {
    // body
} finally {
    // cleanup
}
```

In a `when` statement, if a branch is more than a single line, consider separating it from adjacent case blocks with a blank line:

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

Put short branches on the same line as the condition, without braces.

```kotlin
when (foo) {
    true -> bar() // good
    false -> { baz() } // bad
}
```

### Method calls

In long argument lists, put a line break after the opening parenthesis. Indent arguments by four spaces. 
Group multiple closely related arguments on the same line.

```kotlin
drawSquare(
    x = 10, y = 10,
    width = 100, height = 100,
    fill = true
)
```

Put spaces around the `=` sign separating the argument name and value.

### Wrap chained calls

When wrapping chained calls, put the `.` character or the `?.` operator on the next line, with a single indent:

```kotlin
val anchor = owner
    ?.firstChild!!
    .siblings(forward = true)
    .dropWhile { it is PsiComment || it is PsiWhiteSpace }
```

The first call in the chain should usually have a line break before it, but it's OK to omit it if the code makes more sense that way.

### Lambdas

In lambda expressions, spaces should be used around the curly braces, as well as around the arrow which separates the parameters
from the body. If a call takes a single lambda, pass it outside parentheses whenever possible.

```kotlin
list.filter { it > 10 }
```

If assigning a label for a lambda, do not put a space between the label and the opening curly brace:

```kotlin
fun foo() {
    ints.forEach lit@{
        // ...
    }
}
```

When declaring parameter names in a multiline lambda, put the names on the first line, followed by the arrow and the newline:

```kotlin
appendCommaSeparated(properties) { prop ->
    val propertyValue = prop.get(obj)  // ...
}
```

If the parameter list is too long to fit on a line, put the arrow on a separate line:

```kotlin
foo {
   context: Context,
   environment: Env
   ->
   context.configureEnv(environment)
}
```

### Trailing commas

A trailing comma is a comma symbol after the last item in a series of elements:

```kotlin
class Person(
    val firstName: String,
    val lastName: String,
    val age: Int, // trailing comma
)
```

Using trailing commas has several benefits:

* It makes version-control diffs cleaner – as all the focus is on the changed value.
* It makes it easy to add and reorder elements – there is no need to add or delete the comma if you manipulate elements.
* It simplifies code generation, for example, for object initializers. The last element can also have a comma.

Trailing commas are entirely optional – your code will still work without them. The Kotlin style guide encourages the use of trailing commas at the declaration site and leaves it at your discretion for the call site.

To enable trailing commas in the IntelliJ IDEA formatter, go to **Settings/Preferences | Editor | Code Style | Kotlin**, 
open the **Other** tab and select the **Use trailing comma** option.

#### Enumerations {collapsible="true"}

```kotlin
enum class Direction {
    NORTH,
    SOUTH,
    WEST,
    EAST, // trailing comma
}
```

#### Value arguments {collapsible="true"}

```kotlin
fun shift(x: Int, y: Int) { /*...*/ }
shift(
    25,
    20, // trailing comma
)
val colors = listOf(
    "red",
    "green",
    "blue", // trailing comma
)
```

#### Class properties and parameters {collapsible="true"}

```kotlin
class Customer(
    val name: String,
    val lastName: String, // trailing comma
)
class Customer(
    val name: String,
    lastName: String, // trailing comma
)
```

#### Function value parameters {collapsible="true"}

```kotlin
fun powerOf(
    number: Int, 
    exponent: Int, // trailing comma
) { /*...*/ }
constructor(
    x: Comparable<Number>,
    y: Iterable<Number>, // trailing comma
) {}
fun print(
    vararg quantity: Int,
    description: String, // trailing comma
) {}
```

#### Parameters with optional type (including setters) {collapsible="true"}

```kotlin
val sum: (Int, Int, Int) -> Int = fun(
    x,
    y,
    z, // trailing comma
): Int {
    return x + y + x
}
println(sum(8, 8, 8))
```

#### Indexing suffix {collapsible="true"}

```kotlin
class Surface {
    operator fun get(x: Int, y: Int) = 2 * x + 4 * y - 10
}
fun getZValue(mySurface: Surface, xValue: Int, yValue: Int) =
    mySurface[
        xValue,
        yValue, // trailing comma
    ]
```

#### Parameters in lambdas {collapsible="true"}

```kotlin
fun main() {
    val x = {
            x: Comparable<Number>,
            y: Iterable<Number>, // trailing comma
        ->
        println("1")
    }
    println(x)
}
```

#### `when` entry {collapsible="true"}

```kotlin
fun isReferenceApplicable(myReference: KClass<*>) = when (myReference) {
    Comparable::class,
    Iterable::class,
    String::class, // trailing comma
        -> true
    else -> false
}
```

#### Collection literals (in annotations) {collapsible="true"}

```kotlin
annotation class ApplicableFor(val services: Array<String>)
@ApplicableFor([
    "serializer",
    "balancer",
    "database",
    "inMemoryCache", // trailing comma
])
fun run() {}
```

#### Type arguments {collapsible="true"}

```kotlin
fun <T1, T2> foo() {}
fun main() {
    foo<
            Comparable<Number>,
            Iterable<Number>, // trailing comma
            >()
}
```

#### Type parameters {collapsible="true"}

```kotlin
class MyMap<
        MyKey,
        MyValue, // trailing comma
        > {}
```

#### Destructuring declarations {collapsible="true"}

```kotlin
data class Car(val manufacturer: String, val model: String, val year: Int)
val myCar = Car("Tesla", "Y", 2019)
val (
    manufacturer,
    model,
    year, // trailing comma
) = myCar
val cars = listOf<Car>()
fun printMeanValue() {
    var meanValue: Int = 0
    for ((
        _,
        _,
        year, // trailing comma
    ) in cars) {
        meanValue += year
    }
    println(meanValue/cars.size)
}
printMeanValue()
```

## Documentation comments

For longer documentation comments, place the opening `/**` on a separate line and begin each subsequent line
with an asterisk:

```kotlin
/**
 * This is a documentation comment
 * on multiple lines.
 */
```

Short comments can be placed on a single line:

```kotlin
/** This is a short documentation comment. */
```

Generally, avoid using `@param` and `@return` tags. Instead, incorporate the description of parameters and return values
directly into the documentation comment, and add links to parameters wherever they are mentioned. Use `@param` and
`@return` only when a lengthy description is required which doesn't fit into the flow of the main text.

```kotlin
// Avoid doing this:

/**
 * Returns the absolute value of the given number.
 * @param number The number to return the absolute value for.
 * @return The absolute value.
 */
fun abs(number: Int): Int { /*...*/ }

// Do this instead:

/**
 * Returns the absolute value of the given [number].
 */
fun abs(number: Int): Int { /*...*/ }
```

## Avoid redundant constructs

In general, if a certain syntactic construction in Kotlin is optional and highlighted by the IDE
as redundant, you should omit it in your code. Do not leave unnecessary syntactic elements in code
just "for clarity".

### Unit return type

If a function returns Unit, the return type should be omitted:

```kotlin
fun foo() { // ": Unit" is omitted here

}
```

### Semicolons

Omit semicolons whenever possible.

### String templates

Don't use curly braces when inserting a simple variable into a string template. Use curly braces only for longer expressions.

```kotlin
println("$name has ${children.size} children")
```

## Idiomatic use of language features

### Immutability

Prefer using immutable data to mutable. Always declare local variables and properties as `val` rather than `var` if
they are not modified after initialization.

Always use immutable collection interfaces (`Collection`, `List`, `Set`, `Map`) to declare collections which are not
mutated. When using factory functions to create collection instances, always use functions that return immutable
collection types when possible:

```kotlin
// Bad: use of a mutable collection type for value which will not be mutated
fun validateValue(actualValue: String, allowedValues: HashSet<String>) { ... }

// Good: immutable collection type used instead
fun validateValue(actualValue: String, allowedValues: Set<String>) { ... }

// Bad: arrayListOf() returns ArrayList<T>, which is a mutable collection type
val allowedValues = arrayListOf("a", "b", "c")

// Good: listOf() returns List<T>
val allowedValues = listOf("a", "b", "c")
```

### Default parameter values

Prefer declaring functions with default parameter values to declaring overloaded functions.

```kotlin
// Bad
fun foo() = foo("a")
fun foo(a: String) { /*...*/ }

// Good
fun foo(a: String = "a") { /*...*/ }
```

### Type aliases

If you have a functional type or a type with type parameters which is used multiple times in a codebase, prefer defining
a type alias for it:

```kotlin
typealias MouseClickHandler = (Any, MouseEvent) -> Unit
typealias PersonIndex = Map<String, Person>
```
If you use a private or internal type alias for avoiding name collision, prefer the `import ... as ...` mentioned in 
[Packages and Imports](packages.md).

### Lambda parameters

In lambdas which are short and not nested, it's recommended to use the `it` convention instead of declaring the parameter
explicitly. In nested lambdas with parameters, always declare parameters explicitly.

### Returns in a lambda

Avoid using multiple labeled returns in a lambda. Consider restructuring the lambda so that it will have a single exit point.
If that's not possible or not clear enough, consider converting the lambda into an anonymous function.

Do not use a labeled return for the last statement in a lambda.

### Named arguments

Use the named argument syntax when a method takes multiple parameters of the same primitive type, or for parameters of `Boolean` type,
unless the meaning of all parameters is absolutely clear from context.

```kotlin
drawSquare(x = 10, y = 10, width = 100, height = 100, fill = true)
```

### Conditional statements

Prefer using the expression form of `try`, `if`, and `when`.

```kotlin
return if (x) foo() else bar()
```

```kotlin
return when(x) {
    0 -> "zero"
    else -> "nonzero"
}
```

The above is preferable to:

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

### if versus when

Prefer using `if` for binary conditions instead of `when`. 
For example, use this syntax with `if`:

```kotlin
if (x == null) ... else ...
```

instead of this one with `when`:

```kotlin
when (x) {
    null -> // ...
    else -> // ...
}
```

Prefer using `when` if there are three or more options.

### Nullable Boolean values in conditions

If you need to use a nullable `Boolean` in a conditional statement, use `if (value == true)` or `if (value == false)` checks.

### Loops

Prefer using higher-order functions (`filter`, `map` etc.) to loops. Exception: `forEach` (prefer using a regular `for` loop instead,
unless the receiver of `forEach` is nullable or `forEach` is used as part of a longer call chain).

When making a choice between a complex expression using multiple higher-order functions and a loop, understand the cost
of the operations being performed in each case and keep performance considerations in mind. 

### Loops on ranges

Use the `..<` operator to loop over an open-ended range:

```kotlin
for (i in 0..n - 1) { /*...*/ }  // bad
for (i in 0..<n) { /*...*/ }  // good
```

### Strings

Prefer string templates to string concatenation.

Prefer multiline strings to embedding `\n` escape sequences into regular string literals.

To maintain indentation in multiline strings, use `trimIndent` when the resulting string does not require any internal
indentation, or `trimMargin` when internal indentation is required:

```kotlin
fun main() {
   println("""
    Not
    trimmed
    text
    """
   )

   println("""
    Trimmed
    text
    """.trimIndent()
   )

   println()

   val a = """Trimmed to margin text:
          |if(a > 1) {
          |    return a
          |}""".trimMargin()

   println(a)
}
```
{kotlin-runnable="true"}
[打开训练场>>>](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNADgE4EAuANkeSB15jRgHIQm1Xk0ZYsMKMJpMYADyE8aXTpyXFK0hszaEV3XhgAqYiVKUZZC6Rn0gAdKLxYAkmlhomJaRqpLtnrq%2B0gBuOCwYOBgAvLaqHCbOZpYQ2Dj0AOYElvJM8DaGNAA%2BeEiEUQB8GACMpBQWhRhFhvQwTCj0mDgFhUUAvnaOYgCy6VlEwf6MgUQ4k30gADQgTGNtAAosOExIEPRYCCAAVjhhS%2BAQWLR4LDD0AGp3AM54EGiH1fYAnPYATAAMID6QA%3D%3D%3D?_gl=1*1c0z4i0*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDE0NDQ1MS4zMi4wLjE3MDAxNDQ0NTEuNjAuMC4w&_ga=2.69185428.408218914.1700050452-2060274793.1694940376)

Learn the difference between [Java and Kotlin multiline strings](java-to-kotlin-idioms-strings.md#use-multiline-strings).

### Functions vs properties

In some cases, functions with no arguments might be interchangeable with read-only properties. 
Although the semantics are similar, there are some stylistic conventions on when to prefer one to another.

Prefer a property over a function when the underlying algorithm:

* does not throw
* is cheap to calculate (or cached on the first run)
* returns the same result over invocations if the object state hasn't changed

### Extension functions

Use extension functions liberally. Every time you have a function that works primarily on an object, consider making it
an extension function accepting that object as a receiver. To minimize API pollution, restrict the visibility of
extension functions as much as it makes sense. As necessary, use local extension functions, member extension functions,
or top-level extension functions with private visibility.

### Infix functions

Declare a function as `infix` only when it works on two objects which play a similar role. Good examples: `and`, `to`, `zip`.
Bad example: `add`.

Do not declare a method as `infix` if it mutates the receiver object.

### Factory functions

If you declare a factory function for a class, avoid giving it the same name as the class itself. Prefer using a distinct name,
making it clear why the behavior of the factory function is special. Only if there is really no special semantics,
you can use the same name as the class.

```kotlin
class Point(val x: Double, val y: Double) {
    companion object {
        fun fromPolar(angle: Double, radius: Double) = Point(...)
    }
}
```

If you have an object with multiple overloaded constructors that don't call different superclass constructors and
can't be reduced to a single constructor with default argument values, prefer to replace the overloaded constructors with
factory functions.

### Platform types

A public function/method returning an expression of a platform type must declare its Kotlin type explicitly:

```kotlin
fun apiCall(): String = MyJavaApi.getProperty("name")
```

Any property (package-level or class-level) initialized with an expression of a platform type must declare its Kotlin type explicitly:

```kotlin
class Person {
    val name: String = MyJavaApi.getProperty("name")
}
```

A local value initialized with an expression of a platform type may or may not have a type declaration:

```kotlin
fun main() {
    val name = MyJavaApi.getProperty("name")
    println(name)
}
```

### Scope functions apply/with/run/also/let

Kotlin provides a set of functions to execute a block of code in the context of a given object: `let`, `run`, `with`, `apply`, and `also`.
For the guidance on choosing the right scope function for your case, refer to [Scope Functions](scope-functions.md).

## Coding conventions for libraries

When writing libraries, it's recommended to follow an additional set of rules to ensure API stability:

 * Always explicitly specify member visibility (to avoid accidentally exposing declarations as public API)
 * Always explicitly specify function return types and property types (to avoid accidentally changing the return type
   when the implementation changes)
 * Provide [KDoc](kotlin-doc.md) comments for all public members, except for overrides that do not require any new documentation
   (to support generating documentation for the library)

Learn more about best practices and ideas to consider when writing an API for your library in [library creators' guidelines](https://kotlinlang.org/docs/jvm-api-guidelines-introduction.html).
