[//]: # (title: 在 Java 中调用 Kotlin)

在 Java 中可以轻松地调用 Kotlin 代码。比如，可以在 Java 方法中无缝创建和操作 Kotlin 类的实例。
然而，在将 Kotlin 代码集成到 Java 中时，需要注意 Java 和 Kotlin 之间的一些差异。
在本页中，我们将描述如何调整 Kotlin 代码与其 Java 客户端的互操作性。

## 属性 {id=properties}

Kotlin 属性会被编译为以下 Java 元素：

 * a getter method, with the name calculated by prepending the `get` prefix.
 * a setter method, with the name calculated by prepending the `set` prefix (only for `var` properties).
 * a private field, with the same name as the property name (only for properties with backing fields).

例如，`var firstName: String` 会编译为以下 Java 声明：

```java
private String firstName;

public String getFirstName() {
    return firstName;
}

public void setFirstName(String firstName) {
    this.firstName = firstName;
}
```

If the name of the property starts with `is`, a different name mapping rule is used: the name of the getter is
the same as the property name, and the name of the setter is obtained by replacing `is` with `set`.
For example, for a property `isOpen`, the getter is called `isOpen()` and the setter is called `setOpen()`.
This rule applies for properties of any type, not just `Boolean`.

## 包级函数 {id=package-level-functions}

在 `org.example` 包中的文件 `app.kt` 中声明的所有函数和属性，包括扩展函数，
都会被编译成一个名为 `org.example.AppKt` 的 Java 类的静态方法。

```kotlin
// app.kt
package org.example

class Util

fun getTime() { /*...*/ }

```

```java
// Java
new org.example.Util();
org.example.AppKt.getTime();
```

要为生成的 Java 类设置自定义名称，可以使用 `@JvmName` 注解：

```kotlin
@file:JvmName("DemoUtils")

package org.example

class Util

fun getTime() { /*...*/ }

```

```java
// Java
new org.example.Util();
org.example.DemoUtils.getTime();
```

如果多个文件具有相同的生成的 Java 类名称（相同的包和名称，或使用相同的
[`@JvmName`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvm-name/index.html) 注解），通常会报错。
但是，编译器可以生成一个单一的 Java Facade 类，该类具有指定的名称，并包含所有具有该名称的文件中的所有声明。
要启用生成这样的 Facade 类，可以在所有相关文件中使用
[`@JvmMultifileClass`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvm-multifile-class/index.html) 注解。

```kotlin
// oldutils.kt
@file:JvmName("Utils")
@file:JvmMultifileClass

package org.example

fun getTime() { /*...*/ }
```

```kotlin
// newutils.kt
@file:JvmName("Utils")
@file:JvmMultifileClass

package org.example

fun getDate() { /*...*/ }
```

```java
// Java
org.example.Utils.getTime();
org.example.Utils.getDate();
```

## 实例字段 {id=instance-fields}

If you need to expose a Kotlin property as a field in Java, annotate it with the [`@JvmField`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvm-field/index.html) annotation.
The field has the same visibility as the underlying property. You can annotate a property with `@JvmField` if it:
* has a backing field
* is not private
* does not have `open`, `override` or `const` modifiers
* is not a delegated property

```kotlin
class User(id: String) {
    @JvmField val ID = id
}
```

```java
// Java
class JavaClient {
    public String getID(User user) {
        return user.ID;
    }
}
```

[Late-Initialized](properties.md#late-initialized-properties-and-variables) properties are also exposed as fields. 
The visibility of the field is the same as the visibility of the `lateinit` property setter.

## 静态字段 {id=static-fields}

Kotlin properties declared in a named object or a companion object have static backing fields
either in that named object or in the class containing the companion object.

Usually these fields are private, but they can be exposed in one of the following ways:

- [`@JvmField`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvm-field/index.html) 注解
- `lateinit` 修饰符
- `const` 修饰符
 
为此类属性添加 `@JvmField` 注解会使其成为一个静态字段，且具有与属性本身相同的可见性。

```kotlin
class Key(val value: Int) {
    companion object {
        @JvmField
        val COMPARATOR: Comparator<Key> = compareBy<Key> { it.value }
    }
}
```

```java
// Java
Key.COMPARATOR.compare(key1, key2);
// Key 类中的 public static final 字段
```

在对象或伴生对象中的 [延迟初始化](properties.md#late-initialized-properties-and-variables) 属性具有静态的幕后字段，且其可见性与属性 setter 的可见性相同。

```kotlin
object Singleton {
    lateinit var provider: Provider
}
```

```java

// Java
Singleton.provider = new Provider();
// 在 Singleton 类中的 public static 非-final 字段
```

声明为 `const` 的属性（无论是在类中还是在顶层）都会在 Java 中转换为静态字段：

```kotlin
// file example.kt

object Obj {
    const val CONST = 1
}

class C {
    companion object {
        const val VERSION = 9
    }
}

const val MAX = 239
```

在 Java 中:

```java

int constant = Obj.CONST;
int max = ExampleKt.MAX;
int version = C.VERSION;
```

## 静态方法 {id=static-methods}

As mentioned above, Kotlin represents package-level functions as static methods.
Kotlin can also generate static methods for functions defined in named objects or companion objects if you annotate those
functions as [`@JvmStatic`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvm-static/index.html).
If you use this annotation, the compiler generates both a static method in the enclosing class of the object and
an instance method in the object itself. For example:

```kotlin
class C {
    companion object {
        @JvmStatic fun callStatic() {}
        fun callNonStatic() {}
    }
}
```

现在，`callStatic()` 在 Java 中是静态的，而 `callNonStatic()` 不是：

```java
C.callStatic(); // 正常工作
C.callNonStatic(); // 错误：不是静态方法
C.Companion.callStatic(); // 实例方法保持不变
C.Companion.callNonStatic(); // 这是唯一有效的方式
```

Similarly, for named objects:

```kotlin
object Obj {
    @JvmStatic fun callStatic() {}
    fun callNonStatic() {}
}
```

在 Java 中：

```java
Obj.callStatic(); // 正常工作
Obj.callNonStatic(); // 错误
Obj.INSTANCE.callNonStatic(); // 工作，通过单例实例调用
Obj.INSTANCE.callStatic(); // 也可以工作
```

从 Kotlin 1.3 开始，`@JvmStatic` 注解也适用于接口中伴生对象中定义的函数。  
这样的函数会被编译成接口中的静态方法。请注意，接口中的静态方法是在 Java 1.8 中引入的，因此确保使用相应的目标版本。

```kotlin
interface ChatBot {
    companion object {
        @JvmStatic fun greet(username: String) {
            println("Hello, $username")
        }
    }
}
```

You can also apply `@JvmStatic` annotation to the property of an object or a companion object making its getter and setter
methods static members in that object or the class containing the companion object.

## 接口中的默认方法 {id=default-methods-in-interfaces}

When targeting the JVM, Kotlin compiles functions declared in interfaces to [default methods](https://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html) unless [configured otherwise](#compatibility-modes-for-default-methods).
These are concrete methods in interfaces that Java classes can inherit directly, without reimplementation.

以下是一个带有默认方法的 Kotlin 接口示例：

```kotlin
interface Robot {
    fun move() { println("~walking~") }  // 在 Java 接口中将成为默认方法
    fun speak(): Unit
}
```

该默认实现对于实现该接口的 Java 类是可用的。

```java
// Java 实现
public class C3PO implements Robot {
    // Robot 中的 move() 实现隐式可用
    @Override
    public void speak() {
        System.out.println("I beg your pardon, sir");
    }
}
```

```java
C3PO c3po = new C3PO();
c3po.move(); // 来自 Robot 接口的默认实现
c3po.speak();
```

接口的实现类可以重写默认方法。

```java
//Java
public class BB8 implements Robot {
    // 自定义的默认方法实现
    @Override
    public void move() {
        System.out.println("~rolling~");
    }

    @Override
    public void speak() {
        System.out.println("Beep-beep");
    }
}
```

### Compatibility modes for default methods

Kotlin provides three modes for controlling how functions in interfaces are compiled to JVM default methods.
These modes determine whether the compiler generates compatibility bridges and static methods in `DefaultImpls` classes.

You can control this behavior using the `-jvm-default` compiler option:

> The `-jvm-default` compiler option replaces the deprecated `-Xjvm-default` option.
>
{style="note"}

了解更多关于兼容模式的信息：

#### enable {initial-collapse-state="collapsed" collapsible="true"}

Default behavior.
Generates default implementations in interfaces and includes compatibility bridges and `DefaultImpls` classes.
This mode maintains compatibility with older compiled Kotlin code.

#### no-compatibility {initial-collapse-state="collapsed" collapsible="true"}

Generates only default implementations in interfaces.
Skips compatibility bridges and `DefaultImpls` classes.
Use this mode for new codebases that don't interact with code that relies on `DefaultImpls` classes.
This can break binary compatibility with older Kotlin code.

> If interface delegation is used, all interface methods are delegated.
>
{style="note"}

#### disable {initial-collapse-state="collapsed" collapsible="true"}

Disables default implementations in interfaces.
Only compatibility bridges and `DefaultImpls` classes are generated.

## 可见性 {id=visibility}

Kotlin 的可见性修饰符在 Java 中的映射如下：

* `private` members are compiled to `private` members.
* `private` top-level declarations are compiled to `private` top-level declarations. Package-private accessors are also included,
if accessed from within a class. 
* `protected` remains `protected`. (Note that Java allows accessing protected members from other classes in the same package
and Kotlin doesn't, so Java classes will have broader access to the code.)
* `internal` declarations become `public` in Java. Members of `internal` classes go through name mangling, to make
it harder to accidentally use them from Java and to allow overloading for members with the same signature that don't see
each other according to Kotlin rules.
* `public` remains `public`.

## KClass {id=kclass}

有时你需要调用一个 Kotlin 方法，该方法的参数类型是 `KClass`。  
从 `Class` 到 `KClass` 没有自动转换，因此你需要通过调用等效的
`Class<T>.kotlin` 扩展属性来手动进行转换：

```kotlin
kotlin.jvm.JvmClassMappingKt.getKotlinClass(MainView.class)
```

## 使用 @JvmName 处理签名冲突 {id=handling-signature-clashes-with-jvmname}

Sometimes we have a named function in Kotlin, for which we need a different JVM name in bytecode.
The most prominent example happens due to *type erasure*:

```kotlin
fun List<String>.filterValid(): List<String>
fun List<Int>.filterValid(): List<Int>
```

这两个函数不能并排定义，因为它们的 JVM 签名是相同的：`filterValid(Ljava/util/List;)Ljava/util/List;`。  
如果我们确实希望它们在 Kotlin 中具有相同的名称，可以通过
[`@JvmName`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvm-name/index.html)
注解其中一个（或两个），并指定一个不同的名称作为参数：

```kotlin
fun List<String>.filterValid(): List<String>

@JvmName("filterValidInt")
fun List<Int>.filterValid(): List<Int>
```

From Kotlin, they are accessible by the same name `filterValid`, but from Java it is `filterValid` and `filterValidInt`.

The same trick applies when we need to have a property `x` along with a function `getX()`:

```kotlin
val x: Int
    @JvmName("getX_prop")
    get() = 15

fun getX() = 10
```

为了更改没有显式实现 getter 和 setter 的属性的生成访问器方法名称，
可以使用 `@get:JvmName` 和 `@set:JvmName`：

```kotlin
@get:JvmName("x")
@set:JvmName("changeX")
var x: Int = 23
```

## 重载生成 {id=overloads-generation}

Normally, if you write a Kotlin function with default parameter values, it is visible in Java only as a full
signature, with all parameters present. If you wish to expose multiple overloads to Java callers, you can use the
[`@JvmOverloads`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvm-overloads/index.html) annotation.

这个注解同样适用于构造函数、静态方法等。但是，它不能用于抽象方法，包括在接口中定义的方法。

```kotlin
class Circle @JvmOverloads constructor(centerX: Int, centerY: Int, radius: Double = 1.0) {
    @JvmOverloads fun draw(label: String, lineWidth: Int = 1, color: String = "red") { /*...*/ }
}
```

For every parameter with a default value, this generates one additional overload, which has this parameter and
all parameters to the right of it in the parameter list removed. In this example, the following is generated:

```java
// 构造函数：
Circle(int centerX, int centerY, double radius)
Circle(int centerX, int centerY)

// 方法：
void draw(String label, int lineWidth, String color) { }
void draw(String label, int lineWidth) { }
void draw(String label) { }
```

Note that, as described in [Secondary constructors](classes.md#secondary-constructors), if a class has default
values for all constructor parameters, a public constructor with no arguments is generated for it. This works even
if the `@JvmOverloads` annotation is not specified.

## 受检异常 {id=checked-exceptions}

Kotlin 没有受检异常。
因此，Kotlin 函数的 Java 签名通常不会声明抛出的异常。
例如，如果你在 Kotlin 中有一个如下的函数：

```kotlin
// example.kt
package demo

fun writeToFile() {
    /*...*/
    throw IOException()
}
```

并且你想从 Java 中调用它并捕获这个异常：

```java

// Java
try {
    demo.Example.writeToFile();
} catch (IOException e) { 
    // 错误：writeToFile() 没有在 throws 列表中声明 IOException
    // ...
}
```

你会收到来自 Java 编译器的错误消息，因为 `writeToFile()` 没有声明 `IOException`。
为了绕过这个问题，可以在 Kotlin 中使用 [`@Throws`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-throws/index.html)
注解：

```kotlin
@Throws(IOException::class)
fun writeToFile() {
    /*...*/
    throw IOException()
}
```

## 空安全 {id=null-safety}

当从 Java 调用 Kotlin 函数时，没有任何机制阻止我们将 `null` 作为一个非空参数传递。
因此，Kotlin 会为所有期望非空参数的公共函数生成运行时检查。
通过这种方式，我们可以在 Java 代码中立即得到 `NullPointerException`。

## 型变泛型 {id=variant-generics}

当 Kotlin 类使用 [声明处型变](generics.md#declaration-site-variance) 时，有两种方式可以从 Java 代码中看到它们的使用。
例如，假设你有以下类和两个使用它的函数：

```kotlin
class Box<out T>(val value: T)

interface Base
class Derived : Base

fun boxDerived(value: Derived): Box<Derived> = Box(value)
fun unboxBase(box: Box<Base>): Base = box.value
```

将这些函数翻译成 Java 的一种简单方法如下：

```java
Box<Derived> boxDerived(Derived value) { ... }
Base unboxBase(Box<Base> box) { ... }
```

问题在于，在 Kotlin 中你可以写 `unboxBase(boxDerived(Derived()))`，但在 Java 中这是不可能的，
因为在 Java 中，`Box` 类的参数 `T` 是 *不型变* 的，因此 `Box<Derived>` 不是 `Box<Base>` 的子类型。
为了在 Java 中实现这一点，你必须将 `unboxBase` 定义如下：

```java
Base unboxBase(Box<? extends Base> box) { ... }  
```

这个声明使用了 Java 的 *通配符类型* (`? extends Base`)，通过使用站点使用型变来模拟声明处型变，
因为这正是 Java 所提供的。

为了使 Kotlin API 在 Java 中工作，编译器会将协变定义的 `Box`（或者逆变定义的 `Foo`）在作为参数时生成 `Box<Super>` 为
`Box<? extends Super>`（或 `Foo<? super Bar>`）。
当它作为返回值时，则不会生成通配符，因为否则 Java 客户端必须处理它们（这与常见的 Java 编程风格不符）。
因此，我们示例中的函数实际上被翻译如下：

```java

// 返回类型 - 无通配符
Box<Derived> boxDerived(Derived value) { ... }
 
// 参数 - 通配符
Base unboxBase(Box<? extends Base> box) { ... }
```

> 当参数类型是 `final` 时，通常没有必要生成通配符，因此 `Box<String>` 始终是 `Box<String>`，无论它处于什么位置。
>
> {style="note"}

如果你需要在默认情况下没有生成的通配符，可以使用 `@JvmWildcard` 注解：

```kotlin
fun boxDerived(value: Derived): Box<@JvmWildcard Derived> = Box(value)
// 会被翻译成
// Box<? extends Derived> boxDerived(Derived value) { ... }
```

相反，如果你不需要生成通配符的地方，可以使用 `@JvmSuppressWildcards` 注解：

```kotlin
fun unboxBase(box: Box<@JvmSuppressWildcards Base>): Base = box.value
// 会被翻译成
// Base unboxBase(Box<Base> box) { ... }
```

>`@JvmSuppressWildcards` 不仅可以用于单个类型实参，还可以用于整个声明，比如函数或类，从而抑制其中所有通配符的生成。
>
{style="note"}

### `Nothing` 类型的翻译 {id=translation-of-type-nothing}
 
类型 [`Nothing`](exceptions.md#nothing-type) 是特殊的，因为它在 Java 中没有自然的对应类型。
实际上，所有 Java 引用类型，包括 `java.lang.Void`，都接受 `null` 作为值，而 `Nothing` 甚至不接受 `null`。
因此，这个类型在 Java 中无法准确表示。这就是为什么 Kotlin 在使用 `Nothing` 类型的参数时，会生成一个原始类型：

```kotlin
fun emptyList(): List<Nothing> = listOf()
// 会被翻译成
// List emptyList() { ... }
```

## Inline value classes

<primary-label ref="experimental-general"/>

If you want Java code to work smoothly with Kotlin's [inline value classes](inline-classes.md), you can use the
[`@JvmExposeBoxed`](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin.jvm/-jvm-expose-boxed/) annotation or the `-Xjvm-expose-boxed` compiler option. These approaches ensure Kotlin generates the 
necessary boxed representations for Java interoperability.

By default, Kotlin compiles inline value classes to use **unboxed representations**, which are often inaccessible from Java.
For example, you can't call the constructor for the `MyInt` class from Java:

```kotlin
@JvmInline
value class MyInt(val value: Int)
```

So the following Java code fails:

```java
MyInt input = new MyInt(5);
```

You can use the `@JvmExposeBoxed` annotation so that Kotlin generates a public constructor that you can call from Java directly.
You can apply the annotation at the following levels to ensure fine-grained control over what's exposed to Java:

* Class
* Constructor
* Function

Before using the `@JvmExposeBoxed` annotation in your code, you must opt in by using `@OptIn(ExperimentalStdlibApi::class)`.
For example:

```kotlin
@OptIn(ExperimentalStdlibApi::class)
@JvmExposeBoxed
@JvmInline
value class MyInt(val value: Int)

@OptIn(ExperimentalStdlibApi::class)
@JvmExposeBoxed
fun MyInt.timesTwoBoxed(): MyInt = MyInt(this.value * 2)
```

With these annotations, Kotlin generates a Java-accessible constructor for the `MyInt` class **and** a variant for the 
extension function that uses the boxed form of the value class. So the following Java code runs successfully:

```java
MyInt input = new MyInt(5);
MyInt output = ExampleKt.timesTwoBoxed(input);
```

To apply this behavior to all inline value classes and the functions that use them within a module, compile it with the `-Xjvm-expose-boxed` option. 
Compiling with this option has the same effect as if every declaration in the module has the `@JvmExposeBoxed` annotation.

### Inherited functions

The `@JvmExposeBoxed` annotation doesn't automatically generate boxed representations for inherited functions.
 
To generate the necessary representation for an inherited function, override it in the implementing or extending class:
 
```kotlin
interface IdTransformer {
    fun transformId(rawId: UInt): UInt = rawId
}

// Doesn't generate a boxed representation for the transformId() function
@OptIn(ExperimentalStdlibApi::class)
@JvmExposeBoxed
class LightweightTransformer : IdTransformer

// Generates a boxed representation for the transformId() function
@OptIn(ExperimentalStdlibApi::class)
@JvmExposeBoxed
class DefaultTransformer : IdTransformer {
    override fun transformId(rawId: UInt): UInt = super.transformId(rawId)
}
```

To learn how inheritance works in Kotlin and how to call superclass implementations using the `super` keyword, see [Inheritance](inheritance.md#calling-the-superclass-implementation).
