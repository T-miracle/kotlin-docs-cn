[//]: # (title: 反射)

_反射_ 是一组语言和库的特性，允许你在运行时检查程序的结构。
函数和属性在 Kotlin 中是一等公民，能够对它们进行内省（例如，在运行时获取属性或函数的名称或类型）在使用函数式或响应式编程风格时至关重要。

> Kotlin/JS 提供对反射特性的有限支持。 [了解更多关于 Kotlin/JS 中的反射](js-reflection.md)。
>
{style="note"}

## JVM 依赖 {id=jvm-dependency}

在 JVM 平台上，Kotlin 编译器发行版包括一个用于使用反射特性的运行时组件，它是一个单独的工件，`kotlin-reflect.jar`。
这样做是为了减少不使用反射特性的应用程序的运行时库的大小。

要在 Gradle 或 Maven 项目中使用反射，请添加对 `kotlin-reflect` 的依赖：

* 在 Gradle 中：

    <tabs group="build-script">
    <tab title="Kotlin" group-key="kotlin">

    ```kotlin
    dependencies {
        implementation(kotlin("reflect"))
    }
    ```

    </tab>
    <tab title="Groovy" group-key="groovy">

    ```groovy
    dependencies {
        implementation "org.jetbrains.kotlin:kotlin-reflect:%kotlinVersion%"
    }
    ```

    </tab>
    </tabs>

* 在 Maven 中：

    ```xml
    <dependencies>
      <dependency>
          <groupId>org.jetbrains.kotlin</groupId>
          <artifactId>kotlin-reflect</artifactId>
      </dependency>
    </dependencies>
    ```

如果你不使用 Gradle 或 Maven，请确保你的项目类路径中有 `kotlin-reflect.jar`。
在其他受支持的情况下（使用命令行编译器或 Ant 的 IntelliJ IDEA 项目），它会默认添加。
在命令行编译器和 Ant 中，你可以使用 `-no-reflect` 编译器选项将 `kotlin-reflect.jar` 排除在类路径之外。

## 类引用 {id=class-references}

最基本的反射特性是获取 Kotlin 类的运行时引用。
要获取静态已知的 Kotlin 类的引用，可以使用 _类字面量_ 语法：

```kotlin
val c = MyClass::class
```

该引用是一个 [KClass](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-class/index.html) 类型的值。

> 在 JVM 上：Kotlin 类引用与 Java 类引用不同。
> 要获取 Java 类引用，请在 `KClass` 实例上使用 `.java` 属性。
>
{style="note"}

### 绑定类引用 {id=bound-class-references}

你可以使用对象作为接收者，通过相同的 `::class` 语法获取特定对象的类引用：

```kotlin
val widget: Widget = ...
assert(widget is GoodWidget) { "Bad widget: ${widget::class.qualifiedName}" }
```

你将获得对象的确切类引用，例如 `GoodWidget` 或 `BadWidget`，而不用考虑接收表达式的类型 (`Widget`)。

## 可调用引用 {id=可调用引用}

函数、属性和构造函数的引用可以被调用或用作[函数类型](lambdas.md#function-types)的实例。

所有可调用引用的通用超类型是[`KCallable<out R>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-callable/index.html)，其中 `R` 是返回值的类型。
对于属性，它是属性的类型；对于构造函数，它是构造的类型。

### 函数引用 {id=function-references}

当你声明一个具名函数如下所示时，你可以直接调用它 (`isOdd(5)`)：

```kotlin
fun isOdd(x: Int) = x % 2 != 0
```

或者，你可以将函数用作函数类型值，即传递给另一个函数。
为此，请使用 `::` 操作符：

```kotlin
fun isOdd(x: Int) = x % 2 != 0

fun main() {
//sampleStart
    val numbers = listOf(1, 2, 3)
    println(numbers.filter(::isOdd))
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

这里的 `::isOdd` 是一个函数类型 `(Int) -> Boolean` 的值。

函数引用属于 [`KFunction<out R>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-function/index.html)
的子类型之一，取决于参数的数量。例如，`KFunction3<T1, T2, T3, R>`。

当上下文中类型可以被推断出时，可以使用 `::` 处理重载函数。
例如：

```kotlin
fun main() {
//sampleStart
    fun isOdd(x: Int) = x % 2 != 0
    fun isOdd(s: String) = s == "brillig" || s == "slithy" || s == "tove"
    
    val numbers = listOf(1, 2, 3)
    println(numbers.filter(::isOdd)) // refers to isOdd(x: Int)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

或者，你可以通过将方法引用存储在一个显式指定类型的变量中来提供必要的上下文信息：

```kotlin
val predicate: (String) -> Boolean = ::isOdd   // refers to isOdd(x: String)
```

如果你需要使用类的成员或扩展函数，需要进行限定：`String::toCharArray`。

即使你用扩展函数引用初始化一个变量，推断的函数类型也不会包含接收者，而是会有一个额外的参数来接受接收者对象。
要获取包含接收者的函数类型，需要显式指定类型：

```kotlin
val isEmptyStringList: List<String>.() -> Boolean = List<String>::isEmpty
```

#### 示例：函数组合 {id=example-function-composition}

请思考下面的函数：

```kotlin
fun <A, B, C> compose(f: (B) -> C, g: (A) -> B): (A) -> C {
    return { x -> f(g(x)) }
}
```

它返回的是一个传递给它的两个函数的组合：`compose(f, g) = f(g(*))`。
你可以将这个函数应用于可调用引用：

```kotlin
fun <A, B, C> compose(f: (B) -> C, g: (A) -> B): (A) -> C {
    return { x -> f(g(x)) }
}

fun isOdd(x: Int) = x % 2 != 0

fun main() {
//sampleStart
    fun length(s: String) = s.length
    
    val oddLength = compose(::isOdd, ::length)
    val strings = listOf("a", "ab", "abc")
    
    println(strings.filter(oddLength))
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

### 属性引用 {id=property-references}

要在 Kotlin 中将属性作为一等对象访问，请使用 `::` 操作符：

```kotlin
val x = 1

fun main() {
    println(::x.get())
    println(::x.name) 
}
```

表达式 `::x` 计算为 `KProperty0<Int>` 类型的属性对象。
你可以使用 `get()` 读取其值或使用 `name` 属性获取属性名称。
更多信息，请参见 [`KProperty` 类的文档](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-property/index.html)。

对于可变属性，如 `var y = 1`，`::y` 返回一个 [`KMutableProperty0<Int>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-mutable-property/index.html)
类型的值，它具有 `set()` 方法：

```kotlin
var y = 1

fun main() {
    ::y.set(2)
    println(y)
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

属性引用可以在需要单个泛型参数的函数的地方使用：

```kotlin
fun main() {
//sampleStart
    val strs = listOf("a", "bc", "def")
    println(strs.map(String::length))
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

要访问类的成员属性，请按如下方式限定：

```kotlin
fun main() {
//sampleStart
    class A(val p: Int)
    val prop = A::p
    println(prop.get(A(1)))
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

对于扩展属性：

```kotlin
val String.lastChar: Char
    get() = this[length - 1]

fun main() {
    println(String::lastChar.get("abc"))
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

### 与 Java 反射的互操作性 {id=interoperability-with-java-reflection}

在 JVM 平台上，标准库包含了用于反射类的扩展，这些扩展提供了 Kotlin 反射对象与 Java
反射对象之间的映射（参见 `kotlin.reflect.jvm` 包）。
例如，要查找作为 Kotlin 属性 getter 的后台字段或 Java 方法，可以编写如下代码：

```kotlin
import kotlin.reflect.jvm.*
 
class A(val p: Int)
 
fun main() {
    println(A::p.javaGetter) // 打印 "public final int A.getP()"
    println(A::p.javaField)  // 打印 "private final int A.p"
}
```

要获取与 Java 类对应的 Kotlin 类，请使用 `.kotlin` 扩展属性：

```kotlin
fun getKClass(o: Any): KClass<Any> = o.javaClass.kotlin
```

### 构造函数引用 {id=constructor-references}

构造函数可以像方法和属性一样被引用。
你可以在程序期望一个接受与构造函数相同参数并返回适当类型对象的函数类型对象的地方使用它们。
构造函数通过使用 `::` 操作符并添加类名来引用。
思考以下期望一个无参数的函数参数并返回 `Foo` 类型的函数：

```kotlin
class Foo

fun function(factory: () -> Foo) {
    val x: Foo = factory()
}
```

使用 `::Foo`，这是 `Foo` 类的零参数构造函数，你可以这样调用它：

```kotlin
function(::Foo)
```

构造函数的可调用引用类型为
[`KFunction<out R>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-function/index.html)
的子类型之一，具体取决于参数的数量。

### 绑定函数和属性引用 {id=bound-function-and-property-references}

你可以引用特定对象的实例方法：

```kotlin
fun main() {
//sampleStart
    val numberRegex = "\\d+".toRegex()
    println(numberRegex.matches("29"))
     
    val isNumber = numberRegex::matches
    println(isNumber("29"))
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

与直接调用方法 `matches` 相比，示例使用了对它的引用。
这种引用绑定到它的接收者。
它可以直接调用（如上例所示），也可以在需要函数类型表达式的地方使用：

```kotlin
fun main() {
//sampleStart
    val numberRegex = "\\d+".toRegex()
    val strings = listOf("abc", "124", "a70")
    println(strings.filter(numberRegex::matches))
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

比较绑定引用和未绑定引用的类型。
绑定的可调用引用将其接收者“附加”到它上面，因此接收者的类型不再是一个参数：

```kotlin
val isNumber: (CharSequence) -> Boolean = numberRegex::matches

val matches: (Regex, CharSequence) -> Boolean = Regex::matches
```

属性引用也可以被绑定：

```kotlin
fun main() {
//sampleStart
    val prop = "abc"::length
    println(prop.get())
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

你不需要指定 `this` 作为接收者：`this::foo` 和 `::foo` 是等价的。

### 绑定构造函数引用 {id=bound-constructor-references}

可以通过提供外部类的实例来获取指向 [内部类](nested-classes.md#内部类)
构造函数的绑定可调用引用：

```kotlin
class Outer {
    inner class Inner
}

val o = Outer()
val boundInnerCtor = o::Inner
```
