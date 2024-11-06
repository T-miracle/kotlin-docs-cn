[//]: # (title: 在 Java 中调用 Kotlin)

在 Java 中可以轻松地调用 Kotlin 代码。比如，可以在 Java 方法中无缝创建和操作 Kotlin 类的实例。
然而，在将 Kotlin 代码集成到 Java 中时，需要注意 Java 和 Kotlin 之间的一些差异。
在本页中，我们将描述如何调整 Kotlin 代码与其 Java 客户端的互操作性。

## 属性 {id=properties}

Kotlin 属性会被编译为以下 Java 元素：

* 一个 getter 方法，名称通过在属性名前添加 `get` 前缀来计算
* 一个 setter 方法，名称通过在属性名前添加 `set` 前缀来计算（仅适用于 `var` 属性）
* 一个私有字段，名称与属性名称相同（仅适用于具有幕后字段的属性）

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

如果属性名称以 `is` 开头，将使用不同的名称映射规则：getter 的名称将与属性名称相同，setter 的名称则是将 `is` 替换为 `set` 得到的名称。
例如，对于属性 `isOpen`，getter 会被命名为 `isOpen()`，setter 会被命名为 `setOpen()`。
该规则适用于任何类型的属性，而不仅仅是 `Boolean` 类型。

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

如果需要将 Kotlin 属性暴露为 Java 中的字段，可以使用 [`@JvmField`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvm-field/index.html) 注解。
该字段将具有与底层属性相同的可见性。只有满足以下条件时，可以为属性添加 `@JvmField` 注解：
* 属性有幕后字段
* 属性不是私有的
* 属性没有 `open`、`override` 或 `const` 修饰符
* 属性不是委托属性

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

[延迟初始化](properties.md#延迟初始化属性和变量) 属性也会作为字段暴露。  
该字段的可见性将与 `lateinit` 属性 setter 的可见性相同。

## 静态字段 {id=static-fields}

在命名对象或伴生对象中声明的 Kotlin 属性，将具有静态的幕后字段，这些字段要么在该命名对象中，要么在包含伴生对象的类中。

通常这些字段是私有的，但可以通过以下方式暴露：

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

在对象或伴生对象中的 [延迟初始化](properties.md#延迟初始化属性和变量) 属性具有静态的幕后字段，且其可见性与属性 setter 的可见性相同。

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

如上所述，Kotlin 将包级函数表示为静态方法。 
如果为这些函数添加 [`@JvmStatic`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvm-static/index.html) 注解，
Kotlin 还可以为在命名对象或伴生对象中定义的函数生成静态方法。
使用此注解时，编译器会在对象的外部类中生成一个静态方法，并在对象本身中生成一个实例方法。例如：

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

命名对象同样适用：

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

`@JvmStatic` 注解还可以应用于对象或伴生对象的属性，使其
getter 和 setter 方法成为该对象或包含伴生对象的类中的静态成员。

## 接口中的默认方法 {id=default-methods-in-interfaces}

> 默认方法仅适用于目标 JVM 1.8 及以上版本。
>
{style="note"}

从 JDK 1.8 开始，Java 接口可以包含 [默认方法](https://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html)。
为了使 Kotlin 接口中的所有非抽象成员在实现它们的 Java 类中成为默认方法，可以使用 `-Xjvm-default=all` 编译器选项编译 Kotlin 代码。

以下是一个带有默认方法的 Kotlin 接口示例：

```kotlin
// 使用 -Xjvm-default=all 编译

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

> 在 Kotlin 1.4 之前，要生成默认方法，可以在这些方法上使用 `@JvmDefault` 注解。
> 在 1.4 及以上版本中，使用 `-Xjvm-default=all` 编译选项通常相当于为接口的所有非抽象方法添加
> `@JvmDefault` 注解，并使用 `-Xjvm-default=enable` 进行编译。
> 然而，在某些情况下，它们的行为有所不同。
> 关于 Kotlin 1.4 中默认方法生成变化的详细信息，可以参考 Kotlin 博客中的
> [这篇文章](https://blog.jetbrains.com/kotlin/2020/07/kotlin-1-4-m3-generating-default-methods-in-interfaces/)。
>
{style="note"}

### Compatibility modes for default methods

If there are clients that use your Kotlin interfaces compiled without the `-Xjvm-default=all` option, then they may
be binary-incompatible with the code compiled with this option. To avoid breaking the compatibility with such clients, 
use the `-Xjvm-default=all` mode and mark interfaces with the [`@JvmDefaultWithCompatibility`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvm-default-with-compatibility/) annotation. 
This allows you to add this annotation to all interfaces in the public API once, and you won't need to use any annotations for new non-public code.

> Starting from Kotlin 1.6.20, you can compile modules in the default mode (the `-Xjvm-default=disable` compiler option) against 
> modules compiled with the `-Xjvm-default=all` or `-Xjvm-default=all-compatibility` modes.
>
{style="note"}

Learn more about compatibility modes:

#### disable {collapsible="true"}

Default behavior. Do not generate JVM default methods and prohibit `@JvmDefault` annotation usage.

#### all {collapsible="true"}

Generate JVM default methods for all interface declarations with bodies in the module. Do not generate [`DefaultImpls`](https://blog.jetbrains.com/kotlin/2020/07/kotlin-1-4-m3-generating-default-methods-in-interfaces/) stubs 
for interface declarations with bodies, which are generated by default in the `disable` mode. 

If interface inherits a method with body from an interface compiled in the `disable` mode and doesn't override it, 
then a `DefaultImpls` stub will be generated for it.

__Breaks binary compatibility__ if some client code relies on the presence of `DefaultImpls` classes.

> If interface delegation is used, all interface methods are delegated. The only exception are methods annotated with the deprecated `@JvmDefault` annotation.
>
{style="note"}

#### all-compatibility {collapsible="true"}

In addition to the `all` mode, generate compatibility stubs in the [`DefaultImpls`](https://blog.jetbrains.com/kotlin/2020/07/kotlin-1-4-m3-generating-default-methods-in-interfaces/)
classes. Compatibility stubs could be useful for 
library and runtime authors to keep backward binary compatibility for existing clients compiled against previous library versions. 
`all` and `all-compatibility` modes are changing the library ABI surface that clients will use after the recompilation of the library. 
In that sense, clients might be incompatible with previous library versions. 
This usually means that you need a proper library versioning, for example, major version increase in SemVer.

The compiler generates all the members of `DefaultImpls` with the `@Deprecated` annotation: you shouldn't use these 
members in Java code, because the compiler generates them only for compatibility purposes.

In case of inheritance from a Kotlin interface compiled in `all` or `all-compatibility` modes,
`DefaultImpls` compatibility stubs will invoke the default method of the interface with standard JVM runtime resolution semantics.

Perform additional compatibility checks for classes inheriting generic interfaces where in some cases additional implicit method 
with specialized signatures was generated in the `disable` mode:
unlike in the `disable` mode, the compiler will report an error if you don't override such method explicitly and don't annotate the class 
with `@JvmDefaultWithoutCompatibility` (see [this YouTrack issue](https://youtrack.jetbrains.com/issue/KT-39603) for more details).

## Visibility

The Kotlin visibility modifiers map to Java in the following way:

* `private` members are compiled to `private` members
* `private` top-level declarations are compiled to `private` top-level declarations. Package-private accessors are also included,
if accessed from within a class. 
* `protected` remains `protected` (note that Java allows accessing protected members from other classes in the same package
and Kotlin doesn't, so Java classes will have broader access to the code)
* `internal` declarations become `public` in Java. Members of `internal` classes go through name mangling, to make
it harder to accidentally use them from Java and to allow overloading for members with the same signature that don't see
each other according to Kotlin rules
* `public` remains `public`

## KClass

Sometimes you need to call a Kotlin method with a parameter of type `KClass`.
There is no automatic conversion from `Class` to `KClass`, so you have to do it manually by invoking the equivalent of
the `Class<T>.kotlin` extension property:

```kotlin
kotlin.jvm.JvmClassMappingKt.getKotlinClass(MainView.class)
```

## Handling signature clashes with @JvmName

Sometimes we have a named function in Kotlin, for which we need a different JVM name in the bytecode.
The most prominent example happens due to *type erasure*:

```kotlin
fun List<String>.filterValid(): List<String>
fun List<Int>.filterValid(): List<Int>
```

These two functions can not be defined side-by-side, because their JVM signatures are the same: `filterValid(Ljava/util/List;)Ljava/util/List;`.
If we really want them to have the same name in Kotlin, we can annotate one (or both) of them with
[`@JvmName`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvm-name/index.html) and specify a different name
as an argument:

```kotlin
fun List<String>.filterValid(): List<String>

@JvmName("filterValidInt")
fun List<Int>.filterValid(): List<Int>
```

From Kotlin they will be accessible by the same name `filterValid`, but from Java it will be `filterValid` and `filterValidInt`.

The same trick applies when we need to have a property `x` alongside with a function `getX()`:

```kotlin
val x: Int
    @JvmName("getX_prop")
    get() = 15

fun getX() = 10
```

To change the names of generated accessor methods for properties without explicitly implemented getters and setters,
you can use `@get:JvmName` and `@set:JvmName`:

```kotlin
@get:JvmName("x")
@set:JvmName("changeX")
var x: Int = 23
```

## Overloads generation

Normally, if you write a Kotlin function with default parameter values, it will be visible in Java only as a full
signature, with all parameters present. If you wish to expose multiple overloads to Java callers, you can use the
[`@JvmOverloads`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvm-overloads/index.html) annotation.

The annotation also works for constructors, static methods, and so on. It can't be used on abstract methods, including
methods defined in interfaces.

```kotlin
class Circle @JvmOverloads constructor(centerX: Int, centerY: Int, radius: Double = 1.0) {
    @JvmOverloads fun draw(label: String, lineWidth: Int = 1, color: String = "red") { /*...*/ }
}
```

For every parameter with a default value, this will generate one additional overload, which has this parameter and
all parameters to the right of it in the parameter list removed. In this example, the following will be
generated:

```java
// Constructors:
Circle(int centerX, int centerY, double radius)
Circle(int centerX, int centerY)

// Methods
void draw(String label, int lineWidth, String color) { }
void draw(String label, int lineWidth) { }
void draw(String label) { }
```

Note that, as described in [Secondary constructors](classes.md#secondary-constructors), if a class has default
values for all constructor parameters, a public constructor with no arguments will be generated for it. This works even
if the `@JvmOverloads` annotation is not specified.

## Checked exceptions

Kotlin does not have checked exceptions.
So, normally the Java signatures of Kotlin functions do not declare exceptions thrown.
Thus, if you have a function in Kotlin like this:

```kotlin
// example.kt
package demo

fun writeToFile() {
    /*...*/
    throw IOException()
}
```

And you want to call it from Java and catch the exception:

```java

// Java
try {
    demo.Example.writeToFile();
} catch (IOException e) { 
    // error: writeToFile() does not declare IOException in the throws list
    // ...
}
```

You get an error message from the Java compiler, because `writeToFile()` does not declare `IOException`.
To work around this problem, use the [`@Throws`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-throws/index.html)
annotation in Kotlin:

```kotlin
@Throws(IOException::class)
fun writeToFile() {
    /*...*/
    throw IOException()
}
```

## Null-safety

When calling Kotlin functions from Java, nobody prevents us from passing `null` as a non-nullable parameter.
That's why Kotlin generates runtime checks for all public functions that expect non-nulls.
This way we get a `NullPointerException` in the Java code immediately.

## Variant generics

When Kotlin classes make use of [declaration-site variance](generics.md#declaration-site-variance), there are two 
options of how their usages are seen from the Java code. For example, imagine you have the following class and two functions that use it:

```kotlin
class Box<out T>(val value: T)

interface Base
class Derived : Base

fun boxDerived(value: Derived): Box<Derived> = Box(value)
fun unboxBase(box: Box<Base>): Base = box.value
```

A naive way of translating these functions into Java would be this:

```java
Box<Derived> boxDerived(Derived value) { ... }
Base unboxBase(Box<Base> box) { ... }
```

The problem is that in Kotlin you can write `unboxBase(boxDerived(Derived()))` but in Java that would be impossible
because in Java the class `Box` is *invariant* in its parameter `T`, and thus `Box<Derived>` is not a subtype of `Box<Base>`. 
To make this work in Java, you would have to define `unboxBase` as follows:

```java
Base unboxBase(Box<? extends Base> box) { ... }  
```

This declaration uses Java's *wildcards types* (`? extends Base`) to emulate declaration-site variance through use-site 
variance, because it is all Java has.

To make Kotlin APIs work in Java, the compiler generates `Box<Super>` as `Box<? extends Super>` for covariantly defined `Box` 
(or `Foo<? super Bar>` for contravariantly defined `Foo`) when it appears *as a parameter*. When it's a return value,
wildcards are not generated, because otherwise Java clients will have to deal with them (and it's against the common 
Java coding style). Therefore, the functions from our example are actually translated as follows:

```java

// return type - no wildcards
Box<Derived> boxDerived(Derived value) { ... }
 
// parameter - wildcards 
Base unboxBase(Box<? extends Base> box) { ... }
```

> When the argument type is final, there's usually no point in generating the wildcard, so `Box<String>` is always `Box<String>`,
>no matter what position it takes.
>
{style="note"}

If you need wildcards where they are not generated by default, use the `@JvmWildcard` annotation:

```kotlin
fun boxDerived(value: Derived): Box<@JvmWildcard Derived> = Box(value)
// is translated to 
// Box<? extends Derived> boxDerived(Derived value) { ... }
```

In the opposite case, if you don't need wildcards where they are generated, use `@JvmSuppressWildcards`:

```kotlin
fun unboxBase(box: Box<@JvmSuppressWildcards Base>): Base = box.value
// is translated to 
// Base unboxBase(Box<Base> box) { ... }
```

>`@JvmSuppressWildcards` can be used not only on individual type arguments, but on entire declarations, such as functions
>or classes, causing all wildcards inside them to be suppressed.
>
{style="note"}

### Translation of type `Nothing`
 
The type [`Nothing`](exceptions.md#nothing-type) is special, because it has no natural counterpart in Java. Indeed, every Java reference type, including
`java.lang.Void`, accepts `null` as a value, and `Nothing` doesn't accept even that. So, this type cannot be accurately
represented in the Java world. This is why Kotlin generates a raw type where an argument of type `Nothing` is used:

```kotlin
fun emptyList(): List<Nothing> = listOf()
// is translated to
// List emptyList() { ... }
```
