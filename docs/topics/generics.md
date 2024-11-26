[//]: # (title: 泛型：in、out、where)

[//]: # (TODO 校验)

在 Kotlin 中，类可以具有类型形参，就像在 Java 中一样：

```kotlin
class Box<T>(t: T) {
    var value = t
}
```

要创建这样一个类的实例，只需提供类型实参：

```kotlin
val box: Box<Int> = Box<Int>(1)
```

但是如果参数可以被推断，例如从构造函数的参数中，那么你可以省略类型实参：

```kotlin
val box = Box(1) // 1 的类型是 Int，因此编译器会推断它是 Box<Int>
```

## 型变 {id=型变}

Java类型系统中最棘手的部分之一是通配符类型（参见[Java泛型FAQ](http://www.angelikalanger.com/GenericsFAQ/JavaGenericsFAQ.html)）。
而Kotlin则没有这种类型。相反，Kotlin 拥有声明处型变和类型投影。


### 型变与 Java 中的通配符 {id=variance-and-wildcards-in-java}

让我们思考一下为什么 Java 需要这些神秘的通配符。
首先，Java 中的泛型类型是**不可变**的，意思是 `List<String>` **不是** `List<Object>` 的子类型。
如果 `List` 不是**不可变**的，它就不会比 Java 的数组更有优势，因为下面的代码会编译成功但在运行时导致异常：

```java
// Java
List<String> strs = new ArrayList<String>();

// Java 在编译时报告类型不匹配。
List<Object> objs = strs;

// 如果没有这种限制会怎样？
// 我们将能够把一个 Integer 放入一个 String 列表中。
objs.add(1);

// 然后在运行时，Java 会抛出
// ClassCastException: Integer cannot be cast to String
String s = strs.get(0); 
```

Java 禁止这种情况以保证运行时的安全性。 但这也带来了影响。
例如，考虑 `Collection` 接口中的 `addAll()` 方法。
这个方法的签名是什么？根据直觉，你会这样写：

```java
// Java
interface Collection<E> ... {
    void addAll(Collection<E> items);
}
```

但是之后你将无法执行以下操作（这是完全安全的）：

```java
// Java

// The following would not compile with the naive declaration of addAll:
// Collection<String> is not a subtype of Collection<Object>
void copyAll(Collection<Object> to, Collection<String> from) {
    to.addAll(from);
}
```

这就是为什么`addAll()`的实际签名如下：

```java
// Java
interface Collection<E> ... {
    void addAll(Collection<? extends E> items);
}
```

`? extends E` 的 _通配符类型实参_ 表示该方法接受的是 `E` 的对象集合，_或者是_ `E` 的子类型，而不仅仅是 `E` 本身。这意味着你可以安全地从集合中读取 `E` 类型的元素（这些元素是 `E` 的子类的实例），但是无法写入其中，因为你不知道符合该未知子类型的对象是什么。
作为这种限制的回报，你得到了期望的行为：`Collection<String>` _是_ `Collection<? extends Object>` 的一个子类型。
换句话说，带有 _extends_ 约束（_上_界限）的通配符使得该类型变得 _协变_。

理解为什么这样能够工作的关键相当简单：
如果你只能从集合中**取出**东西，那么你可以从包含`String`的集合中读取`Object`。
反之亦然，如果你只能**放入**东西到集合中，那么你可以往包含`Object`的集合中放入`String`：
在 Java 中还有 `List<? super String>`，它可以接受 `String` 或任何其超类型。

后者被称为**逆变性（contravariance）**。在 `List<? super String>` 上，你只能调用那些接受 `String` 作为参数的方法（比如 `add(String)` 或 `set(int, String)`）。
如果你调用返回类型是 `T` 的 `List<T>` 中的某些方法，你将得到的结果不是 `String`，而是 `Object`。

Joshua Bloch 在他的书 [Effective Java, 3rd Edition](http://www.oracle.com/technetwork/java/effectivejava-136174.html)
中很好地解释了这个问题（第31条：“使用有界通配符来增加 API 的灵活性”）。
他把只**读取**的对象称为**生产者**，而把只**写入**的对象称为**消费者**。他建议：

>"为了最大限度的灵活性，对代表生产者或消费者的输入参数使用通配符类型。"

然后他提出了以下助记符：**PECS** 代表 **Producer-Extends, Consumer-Super（生产者用 Extends, 消费者用 Super）**。

> 如果你使用生产者对象，例如 [`List<? extends Foo>`，则不能在该对象上调用 `add()` 或 `set()` 方法](why-producer-cant-use-set-and-add-function.md)。
> 然而，这并不表示它是**不可变的**：
>
> The only thing guaranteed by wildcards (or other types of variance) is _type safety_. Immutability is a completely different story.
>
> 通配符（或其他类型的变体）唯一保证的是**类型安全**。不可变性是一个完全不同的问题。
>
{style="note"}

### 声明处型变 {id=declaration-site-variance}

假设有一个泛型接口 `Source<T>`，该接口没有任何以 `T` 为参数的方法，只有返回 `T` 类型值的方法：

```java
// Java
interface Source<T> {
    T nextT();
}
```

那么，将 `Source<String>` 的实例引用存储在类型为 `Source<Object>` 的变量中是完全安全的 —— 因为没有使用 `T` 作为参数的方法。
但是 Java 不知道这一点，仍然禁止这样做：

```java
// Java
void demo(Source<String> strs) {
    Source<Object> objects = strs; // !!! 在Java中不允许
    // ...
}
```

为了解决这个问题，您应该声明类型为 `Source<? extends Object>` 的变量。
尽管这样做是毫无意义的，因为您仍可以在这样的变量上调用与以前相同的所有方法，所以更复杂的类型并没有给我们带来任何价值。
然而，编译器并不知道这一点。

在 Kotlin 中，有一种向编译器解释这种情况的方法。这被称为**声明处型变**：
你可以标注 `Source` 的**类型形参** `T`，以确保它只能从 `Source<T>` 的成员中被**返回**（产生），而不能被消费。
为了实现这一点，使用 `out` 修饰符：

```kotlin
interface Source<out T> {
    fun nextT(): T
}

fun demo(strs: Source<String>) {
    val objects: Source<Any> = strs // 这是可以的，因为 T 是一个协变参数
    // ...
}
```

一般规则是这样的：
当类 `C` 的类型形参 `T` 被声明为 `out` 时，那么它就只能出现在 `C` 成员 **out（输出）** 的位置。
但回报是 `C<Base>（基类）` 可以安全地作为 `C<Derived>（派生类）` 的超类型。

换句话说，您可以说类 `C` 在参数 `T` 上是 [**协变**](type-covariant.md) 的，或者 `T` 是一个 [**协变**类型](type-covariant.md) 参数。
您可以将 `C` 视为 `T` 的 **生产者**，而不是 `T` 的 **消费者**。

[//]: # (TODO 继续校对)

`out` 修饰符被称为 **型变注解**，由于它是在类型形参声明处提供的，因此提供了 **声明处型变**。
这与 Java 的 **使用处型变** 形成对比，在那里，通配符在类型使用中使得类型是[协变](type-covariant.md)的。

除了 `out` 之外，Kotlin 还提供了一个互补的型变注解：`in`。它使得类型参数**逆变**，意味着它只能被消费而不能被产生。
一个很好的逆变类型的例子是 `Comparable`：

```kotlin
interface Comparable<in T> {
    operator fun compareTo(other: T): Int
}

fun demo(x: Comparable<Number>) {
    x.compareTo(1.0) // 1.0 具有类型 Double，它是 Number 的子类型
    // 因此，您可以将 x 分配给类型为 Comparable<Double> 的变量
    val y: Comparable<Double> = x // OK!
}
```

单词 **in** 和 **out** 似乎是不言自明的（因为它们在 C# 中已经成功使用了相当长的时间），因此上面提到的助记法实际上并不是真正需要的。
实际上，它可以以更高层次的抽象重新表达：

**[存在主义](https://en.wikipedia.org/wiki/Existentialism) 转换：消费者逆变，生产者协变！** :-)

## 类型投影 {id=type-projections}

### 使用处型变：类型投影 {id=use-site-variance-type-projections}

将类型形参 `T` 声明为 `out` 并在使用处避免子类型问题非常容易，但有些类实际上**不能**仅限于返回 `T`！一个很好的例子是 `Array`：

```kotlin
class Array<T>(val size: Int) {
    operator fun get(index: Int): T { ... }
    operator fun set(index: Int, value: T) { ... }
}
```

这个类既不能是协变也不能是逆变于 `T`。这带来了一定的不灵活性。考虑以下函数：

```kotlin
fun copy(from: Array<Any>, to: Array<Any>) {
    assert(from.size == to.size)
    for (i in from.indices)
        to[i] = from[i]
}
```

这个函数应该从一个数组复制项目到另一个数组。让我们尝试在实践中应用它：

```kotlin
val ints: Array<Int> = arrayOf(1, 2, 3)
val any = Array<Any>(3) { "" } 
copy(ints, any)
//   ^ 类型为 Array<Int>，但期望的是 Array<Any>
```

在这里，您遇到了同样熟悉的问题：`Array<T>` 在 `T` 上是**不变的**，因此 `Array<Int>` 和 `Array<Any>` 都不是对方的子类型。
为什么呢？
同样，这是因为 `copy` 可能有意外的行为，例如，它可能尝试将 `String` 写入 `from`，如果您实际上传递了一个 `Int` 数组，稍后会抛出 `ClassCastException`。

为了禁止 `copy` 函数对 `from` 进行**写操作**，您可以进行如下操作：

```kotlin
fun copy(from: Array<out Any>, to: Array<Any>) { ... }
```

这就是**类型投影**，这意味着 `from` 不是一个简单的数组，而是一个受限制的（**投影的**）数组。
您只能调用返回类型形参 `T` 的方法，这在这种情况下意味着您只能调用 `get()`。
这是我们对**使用处型变**的方法，它对应于 Java 的 `Array<? extends Object>`，但稍微简单一些。

您也可以使用 `in` 进行类型投影：

```kotlin
fun fill(dest: Array<in String>, value: String) { ... }
```

`Array<in String>` 对应于 Java 的 `Array<? super String>`。
这意味着您可以将 `CharSequence` 数组或 `Object` 数组传递给 `fill()` 函数。

### 星投影（`*`投影）{id=星投影}

有时候你想表达你对类型实参一无所知，但仍然希望以安全的方式使用它。
在这里，安全的方式是定义泛型类型的这种投影，使得该泛型类型的每个具体实例都是该投影的子类型。

Kotlin 提供了所谓的**星投影**语法：

- 对于 `Foo<out T : TUpper>`，其中 `T` 是具有上界 `TUpper` 的协变类型参数，`Foo<*>` 相当于 `Foo<out TUpper>`。
  这意味着当 `T` 是未知的时候，您可以安全地从 `Foo<*>` 中_读取_ `TUpper` 的值。
- 对于 `Foo<in T>`，其中 `T` 是逆变类型参数，`Foo<*>` 相当于 `Foo<in Nothing>`。
  这意味着在 `T` 未知的情况下，没有安全的方式可以_写入_ `Foo<*>`。
- 对于 `Foo<T : TUpper>`，其中 `T` 是具有上界 `TUpper` 的不变类型参数，`Foo<*>` 相当于对于读取值是 `Foo<out TUpper>`，对于写入值是 `Foo<in Nothing>`。

如果泛型类型有多个类型参数，则每个参数都可以独立投影。
例如，如果该类型声明为 `interface Function<in T, out U>`，则可以使用以下星投影：

* `Function<*, String>` 意味着 `Function<in Nothing, String>`。
* `Function<Int, *>` 意味着 `Function<Int, out Any?>`。
* `Function<*, *>` 意味着 `Function<in Nothing, out Any?>`。

> 星投影非常类似于 Java 的原始类型，但是更安全。
>
{style="note"}

## 泛型函数 {id=泛型函数}

不仅类可以拥有类型形参，函数也可以。类型形参被放置在函数名之前：

```kotlin
fun <T> singletonList(item: T): List<T> {
    // ...
}

fun <T> T.basicToString(): String { // 扩展函数
    // ...
}
```

要调用泛型函数，在调用点的函数名之后指定类型实参：

```kotlin
val l = singletonList<Int>(1)
```

如果可以从上下文中推断出类型实参，可以省略类型实参，因此以下示例同样有效：

```kotlin
val l = singletonList(1)
```

## 泛型约束 {id=泛型约束}

对于给定的类型形参，可以替代的所有可能类型的集合可以通过**泛型约束**进行限制。

### 上界 {id=上界}

最常见的约束类型是**上界**，它对应于 Java 的 `extends` 关键字：

```kotlin
fun <T : Comparable<T>> sort(list: List<T>) {  ... }
```

冒号后指定的类型是**上界**，表示只有 `Comparable<T>` 的子类型可以替代 `T`。例如：

```kotlin
sort(listOf(1, 2, 3)) // OK。Int 是 Comparable<Int> 的子类型
sort(listOf(HashMap<Int, String>())) // 错误：HashMap<Int, String> 不是 Comparable<HashMap<Int, String>> 的子类型
```

默认的上界（如果没有指定）是 `Any?`。在尖括号内只能指定一个上界。
如果相同的类型参数需要多个上界，则需要单独的 _where_ 子句：

```kotlin
fun <T> copyWhenGreater(list: List<T>, threshold: T): List<String>
    where T : CharSequence,
          T : Comparable<T> {
    return list.filter { it > threshold }.map { it.toString() }
}
```

传递的类型必须同时满足 **where** 子句的所有条件。在上面的例子中，`T` 类型必须同时实现 **CharSequence** 和 **Comparable**。

## 绝对非空类型 {id=definitely-non-nullable-types}

为了更容易与泛型 Java 类和接口进行交互，Kotlin 支持将泛型类型形参声明为**绝对非空类型**。

要将泛型类型 `T` 声明为绝对非空类型，请使用 `& Any` 声明类型，例如：`T & Any`。

绝对非空类型必须具有可为空的[上界](#上界)。

声明绝对非空类型的最常见用例是当你想要覆盖包含 `@NotNull` 作为参数的 Java 方法时。例如，考虑 `load()` 方法：

```java
import org.jetbrains.annotations.*;

public interface Game<T> {
    public T save(T x) {}
    @NotNull
    public T load(@NotNull T x) {}
}
```

要成功覆盖 Kotlin 中的 `load()` 方法，您需要将 `T1` 声明为绝对非空类型：

```kotlin
interface ArcadeGame<T1> : Game<T1> {
    override fun save(x: T1): T1
    // T1 绝对不可为空
    override fun load(x: T1 & Any): T1 & Any
}
```

在仅使用 Kotlin 的情况下，你不太可能需要显式声明绝对非空类型，因为 Kotlin 的类型推断会为你处理这个问题。

## 类型擦除 {id=类型擦除}

Kotlin 对泛型声明用法进行的类型安全检查是在编译时进行的。
在运行时，泛型类型的实例不包含有关其实际类型实参的任何信息。
这种类型信息被说成是**擦除**的。例如，`Foo<Bar>` 和 `Foo<Baz?>` 的实例在运行时被擦除为 `Foo<*>`。

### 泛型类型检查和转换 {id=泛型类型检查和转换}

由于类型擦除，没有一般的方法可以在运行时检查泛型类型的实例是否使用了特定的类型实参，而且编译器禁止这样的 `is` 检查，例如 `ints is List<Int>` 或 `list is T`（类型形参）。
但是，您可以将实例与星投影类型进行检查：

```kotlin
if (something is List<*>) {
    something.forEach { println(it) } // 项目的类型为 `Any?`
}
```

类似地，当您已经在静态（编译时）检查实例的类型实参时，可以进行涉及类型的非泛型部分的 `is` 检查或转换。
请注意，在这种情况下，尖括号被省略：

```kotlin
fun handleStrings(list: MutableList<String>) {
    if (list is ArrayList) {
        // `list` 智能转换为 `ArrayList<String>`
    }
}
```

相同的语法，但省略了类型实参，也可以用于不考虑类型实参的转换：`list as ArrayList`。

泛型函数调用的类型实参也只在编译时进行检查。在函数体内，类型形参不能用于类型检查，对类型形参的类型转换（`foo as T`）是未检查的。
唯一的例外是具有
[具体化的类型形参](https://kotlinlang.org/docs/inline-functions.html#reified-type-parameters)
的内联函数，在每个调用点都会内联其实际类型实参。这样可以实现类型形参的类型检查和转换。
然而，仍然适用于在检查或转换中使用的泛型类型的实例的上述限制。
例如，在类型检查 `arg is T` 中，如果 `arg` 本身是泛型类型的实例，其类型实参仍然会被擦除。

```kotlin
//sampleStart
inline fun <reified A, reified B> Pair<*, *>.asPairOf(): Pair<A, B>? {
    if (first !is A || second !is B) return null
    return first as A to second as B
}

val somePair: Pair<Any?, Any?> = "items" to listOf(1, 2, 3)


val stringToSomething = somePair.asPairOf<String, Any>()
val stringToInt = somePair.asPairOf<String, Int>()
val stringToList = somePair.asPairOf<String, List<*>>()
val stringToStringList = somePair.asPairOf<String, List<String>>() // 编译通过但破坏了类型安全！
// 展开示例以获取更多详细信息

//sampleEnd

fun main() {
    println("stringToSomething = " + stringToSomething)
    println("stringToInt = " + stringToInt)
    println("stringToList = " + stringToList)
    println("stringToStringList = " + stringToStringList)
    //println(stringToStringList?.second?.forEach() {it.length}) // 这会抛出ClassCastException，因为列表项不是String类型。
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

### 未经检查的强制类型转换 {id=未经检查的强制类型转换}

对于具有具体类型参数的泛型类型的类型转换，如 `foo as List<String>`，无法在运行时进行检查。  
这些未经检查的强制类型转换可在高级程序逻辑隐含类型安全的情况下使用，但编译器无法直接推断。请参阅下面的示例。

```kotlin
fun readDictionary(file: File): Map<String, *> = file.inputStream().use { 
    TODO("读取字符串到任意元素的映射。")
}

// 我们保存了一个带有`Int`的映射到这个文件
val intsFile = File("ints.dictionary")

// 警告：未经检查的强制类型转换：`Map<String, *>` 到 `Map<String, Int>`
val intsDictionary: Map<String, Int> = readDictionary(intsFile) as Map<String, Int>
```
在最后一行的强制类型转换会产生一个警告。编译器无法在运行时完全检查它，并且无法保证映射中的值为 `Int`。

为了避免未经检查的强制类型转换，你可以重新设计程序结构。
在上面的示例中，你可以使用 `DictionaryReader<T>` 和 `DictionaryWriter<T>` 接口，为不同类型提供类型安全的实现。
你可以引入合理的抽象，将未经检查的强制类型转换从调用点移动到实现细节。合理使用 [泛型型变](#型变) 也能有所帮助。

对于泛型函数，使用 [具体化的类型参数](inline-functions.md#reified-type-parameters)
使得像 `arg as T` 这样的强制类型转换得到检查，除非 `arg` 的类型具有 *自己的* 被擦除的类型参数。

通过在出现未经检查的强制类型转换的语句或声明上添加 `@Suppress("UNCHECKED_CAST")` 注解，可以抑制未经检查的强制类型转换警告：

```kotlin
inline fun <reified T> List<*>.asListOfType(): List<T>? =
    if (all { it is T })
        @Suppress("UNCHECKED_CAST")
        this as List<T> else
        null
```

> **在 JVM 上**: [数组类型](arrays.md) (`Array<Foo>`) 保留了关于其元素被擦除类型的信息，对数组类型的类型转换是部分检查的：
> 元素类型的可为 null 属性和实际类型参数仍然被擦除。例如，
> 如果 `foo` 是包含任何 `List<*>` 的数组，无论它是否可为 null，转换 `foo as Array<List<String>?>` 将成功。
>
{style="note"}

## 用于类型实参的下划线运算符 {id=用于类型实参的下划线运算符}

下划线运算符 `_` 可以用于类型实参。在其他类型被明确指定时，可以使用它来自动推断参数的类型：

```kotlin
abstract class SomeClass<T> {
    abstract fun execute(): T
}

class SomeImplementation : SomeClass<String>() {
    override fun execute(): String = "Test"
}

class OtherImplementation : SomeClass<Int>() {
    override fun execute(): Int = 42
}

object Runner {
    inline fun <reified S : SomeClass<T>, T> run(): T {
        return S::class.java.getDeclaredConstructor().newInstance().execute()
    }
}

fun main() {
    // T 被推断为 String，因为 SomeImplementation 派生自 SomeClass<String>
    val s = Runner.run<SomeImplementation, _>()
    assert(s == "Test")

    // T 被推断为 Int，因为 OtherImplementation 派生自 SomeClass<Int>
    val n = Runner.run<OtherImplementation, _>()
    assert(n == 42)
}
```
