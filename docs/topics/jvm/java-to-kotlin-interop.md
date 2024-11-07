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

### 默认方法的兼容模式 {id=compatibility-modes-for-default-methods}

如果有客户端使用的是没有启用 `-Xjvm-default=all` 选项编译的 Kotlin 接口，那么它们可能与使用此选项编译的代码在二进制上不兼容。
为了避免破坏与这些客户端的兼容性，可以使用 `-Xjvm-default=all` 模式，并使用
[`@JvmDefaultWithCompatibility`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvm-default-with-compatibility/)
注解标记接口。 这样，您只需要在公共 API 中对所有接口添加一次此注解，对于新的非公共代码，则不需要使用任何注解。

> 从 Kotlin 1.6.20 开始，您可以将模块以默认模式（`-Xjvm-default=disable` 编译选项）编译，
> 并与使用 `-Xjvm-default=all` 或 `-Xjvm-default=all-compatibility` 模式编译的模块兼容。
>
{style="note"}

了解更多关于兼容模式的信息：

#### disable {collapsible="true" id=disable}

默认行为。 不生成 JVM 默认方法，并禁止使用 `@JvmDefault` 注解。

#### all {collapsible="true" id=all}

为模块中所有带有方法体的接口声明生成 JVM 默认方法。不会为带有方法体的接口声明生成
[`DefaultImpls`](https://blog.jetbrains.com/kotlin/2020/07/kotlin-1-4-m3-generating-default-methods-in-interfaces/)
存根，默认情况下在 `disable` 模式下会生成这些存根。

如果接口继承了在 `disable` 模式下编译的接口中的方法体，并且没有重写它，那么将会为该方法生成一个 `DefaultImpls` 存根。

如果某些客户端代码依赖于 `DefaultImpls` 类的存在，__会破坏二进制兼容性__。

> 如果使用了接口委托，则所有接口方法都会被委托。唯一的例外是那些使用已废弃的 `@JvmDefault` 注解的方法。
>
{style="note"}

#### all-compatibility {collapsible="true" id=all-compatibility}

在 `all` 模式的基础上，还会在
[`DefaultImpls`](https://blog.jetbrains.com/kotlin/2020/07/kotlin-1-4-m3-generating-default-methods-in-interfaces/)
类中生成兼容性存根。兼容性存根对于库和运行时的开发者来说可能很有用，以保持向后兼容，支持那些编译时依赖于先前库版本的现有客户端代码
。`all` 和 `all-compatibility` 模式会改变库的 ABI 接口，这将影响库重新编译后的客户端。
从这个角度来看，客户端可能会与先前的库版本不兼容。这通常意味着你需要进行适当的库版本控制，例如在 SemVer 中增加主版本号。

编译器会为 `DefaultImpls` 中的所有成员加上 `@Deprecated` 注解：
你不应该在 Java 代码中使用这些成员，因为编译器只会为兼容性目的生成它们。

如果继承自以 `all` 或 `all-compatibility` 模式编译的 Kotlin 接口，
`DefaultImpls` 兼容性存根将会调用接口的默认方法，并使用标准的 JVM 运行时解析语义。

对继承泛型接口的类进行额外的兼容性检查，在某些情况下，`disable` 模式下会生成具有特定签名的额外隐式方法：
与 `disable` 模式不同，如果你没有显式重写这些方法且没有用 `@JvmDefaultWithoutCompatibility`
注解标记类，编译器会报告错误（更多详情请参见 [这个 YouTrack 问题](https://youtrack.jetbrains.com/issue/KT-39603)）。

## 可见性 {id=visibility}

Kotlin 的可见性修饰符在 Java 中的映射如下：

* `private` 成员会编译为 `private` 成员
* `private` 顶层声明会编译为 `private` 顶层声明。如果从类内访问，还包括包私有访问器。
* `protected` 保持为 `protected`（请注意，Java 允许从同一包中的其他类访问受保护的成员，而
Kotlin 不允许，因此 Java 类对代码的访问范围会更广）
* `internal` 声明会变成 Java 中的 `public`。
`internal` 类的成员会进行名称重整，避免在 Java 中被意外使用，并允许对具有相同签名但在 Kotlin 规则下不可见的成员进行重载。
* `public` 保持为 `public`

## KClass {id=kclass}

有时你需要调用一个 Kotlin 方法，该方法的参数类型是 `KClass`。  
从 `Class` 到 `KClass` 没有自动转换，因此你需要通过调用等效的
`Class<T>.kotlin` 扩展属性来手动进行转换：

```kotlin
kotlin.jvm.JvmClassMappingKt.getKotlinClass(MainView.class)
```

## 使用 @JvmName 处理签名冲突 {id=handling-signature-clashes-with-jvmname}

有时我们在 Kotlin 中有一个命名函数，需要在字节码中使用不同的 JVM 名称。  
最常见的例子发生在 *类型擦除* 的情况下：

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

从 Kotlin 中，它们将通过相同的名称 `filterValid` 访问，但从 Java 中，它们将分别是 `filterValid` 和 `filterValidInt`。

当我们需要同时拥有一个属性 `x` 和一个函数 `getX()` 时，同样的技巧也适用：

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

通常，如果你编写一个带有默认参数值的 Kotlin 函数，它在 Java 中只会作为一个完整的签名显示，其中所有参数都被包含。
如果你希望向 Java 调用者暴露多个重载函数，可以使用
[`@JvmOverloads`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvm-overloads/index.html) 注解。

这个注解同样适用于构造函数、静态方法等。但是，它不能用于抽象方法，包括在接口中定义的方法。

```kotlin
class Circle @JvmOverloads constructor(centerX: Int, centerY: Int, radius: Double = 1.0) {
    @JvmOverloads fun draw(label: String, lineWidth: Int = 1, color: String = "red") { /*...*/ }
}
```

对于每个具有默认值的参数，这将生成一个额外的重载，该重载会移除该参数以及参数列表中该参数右侧的所有参数。在这个例子中，将会生成以下内容：

```java
// 构造函数：
Circle(int centerX, int centerY, double radius)
Circle(int centerX, int centerY)

// 方法：
void draw(String label, int lineWidth, String color) { }
void draw(String label, int lineWidth) { }
void draw(String label) { }
```

请注意，如 [次级构造函数](classes.md#secondary-constructors) 中所描述，如果一个类的所有构造函数参数都有默认值，
则会为该类生成一个没有参数的公共构造函数。即使没有指定 `@JvmOverloads` 注解，这也能正常工作。

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
