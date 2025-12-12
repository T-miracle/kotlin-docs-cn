[//]: # (title: 注解)

Annotations are a means of attaching metadata to code. To declare an annotation, put the `annotation` modifier in front of a class:

```kotlin
annotation class Fancy
```

可以通过给注解类添加元注解来指定注解的附加属性：

* [`@Target`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.annotation/-target/index.html)
  指定哪些元素种类可以用该注解（例如类、函数、属性和表达式）；
* [`@Retention`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.annotation/-retention/index.html)
  可以指定注解信息是否保存到已编译的类文件中，以及是否在运行时通过反射可见（默认情况下，这两个属性都是 true）；
* [`@Repeatable`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.annotation/-repeatable/index.html)
  允许在单个元素上多次使用相同的注解；
* [`@MustBeDocumented`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.annotation/-must-be-documented/index.html)
  指定注解是公共 API 的一部分，并应包含在生成的 API 文档中显示的类或方法签名中。

```kotlin
@Target(AnnotationTarget.CLASS, AnnotationTarget.FUNCTION,
        AnnotationTarget.TYPE_PARAMETER, AnnotationTarget.VALUE_PARAMETER,
        AnnotationTarget.EXPRESSION)
@Retention(AnnotationRetention.SOURCE)
@MustBeDocumented
annotation class Fancy
```

## 用法 {id=usage}

```kotlin
@Fancy class Foo {
    @Fancy fun baz(@Fancy foo: Int): Int {
        return (@Fancy 1)
    }
}
```

如果需要为一个类的主构造函数添加注解，需在构造函数声明中使用 `constructor` 关键字，并在其前面添加注解：

```kotlin
class Foo @Inject constructor(dependency: MyDependency) { ... }
```

你还可以给属性访问器添加注解：

```kotlin
class Foo {
    var x: MyDependency? = null
        @Inject set
}
```

## 构造函数 {id=constructors}

注解可以有带参数的构造函数。

```kotlin
annotation class Special(val why: String)

@Special("example") class Foo {}
```

允许的参数类型有：

* 对应于 Java 基本类型的类型（例如 Int, Long 等）
* 字符串
* 类（`Foo::class`）
* 枚举
* 其他注解
* 上述类型的数组

注解参数不能有可空类型，因为 JVM 不支持将 `null` 存储为注解属性的值。

如果一个注解用作另一个注解的参数，它的名字前不用加 `@` 字符：

```kotlin
annotation class ReplaceWith(val expression: String)

annotation class Deprecated(
        val message: String,
        val replaceWith: ReplaceWith = ReplaceWith(""))

@Deprecated("This function is deprecated, use === instead", ReplaceWith("this === other"))
```

如果需要将一个类指定为注解的参数，请使用 Kotlin 类
([KClass](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-class/index.html))。
Kotlin 编译器会自动将其转换为 Java 类，以便 Java 代码可以正常访问这些注解和参数。

```kotlin
import kotlin.reflect.KClass

annotation class Ann(val arg1: KClass<*>, val arg2: KClass<out Any>)

@Ann(String::class, Int::class) class MyClass
```

## 实例化 {id=instantiation}

在 Java 中，注解类型是一种接口形式，因此你可以实现它并使用一个实例。
作为这种机制的替代，Kotlin 允许你在任意代码中调用注解类的构造函数，并类似地使用生成的实例。

```kotlin
annotation class InfoMarker(val info: String)

fun processInfo(marker: InfoMarker): Unit = TODO()

fun main(args: Array<String>) {
    if (args.isNotEmpty())
        processInfo(getAnnotationReflective(args))
    else
        processInfo(InfoMarker("default"))
}
```

在 [这个 KEEP](https://github.com/Kotlin/KEEP/blob/master/proposals/annotation-instantiation.md) 中了解更多关于注解类实例化的信息。

## Lambdas {id=lambdas}

注解也可以用于 lambdas。
它们将被应用到 lambda 的 `invoke()` 方法中，lambda 的主体会被生成到这个方法中。
这对于像 [Quasar](https://docs.paralleluniverse.co/quasar/) 这样的框架很有用，Quasar 使用注解进行并发控制。

```kotlin
annotation class Suspendable

val f = @Suspendable { Fiber.sleep(10) }
```

## 注解使用位置目标 {id=annotation-use-site-targets}

When you're annotating a property or a primary constructor parameter, there are multiple Java elements that are
generated from the corresponding Kotlin element, and therefore multiple possible locations for the annotation in
the generated Java bytecode. To specify how exactly the annotation should be generated, use the following syntax:

```kotlin
class Example(@field:Ann val foo,    // annotate only the Java field
              @get:Ann val bar,      // annotate only the Java getter
              @param:Ann val quux)   // annotate only the Java constructor parameter
```

同样的语法也可以用于注解整个文件。
为此，请在文件的顶层添加一个目标为 `file` 的注解，放在 `package` 声明之前，或者如果文件位于默认包中，则放在所有 `import` 声明之前：

```kotlin
@file:JvmName("Foo")

package org.jetbrains.demo
```

If you have multiple annotations with the same target, you can avoid repeating the target by adding brackets after the
target and putting all the annotations inside the brackets (except for the `all` meta-target):

```kotlin
class Example {
     @set:[Inject VisibleForTesting]
     var collaborator: Collaborator
}
```

支持使用注解的位置目标的完整列表如下：

  * `file`
  * `field`
  * `property` (annotations with this target are not visible to Java)
  * `get` (property getter)
  * `set` (property setter)
  * `all` (an experimental meta-target for properties, see [below](#all-meta-target) for its purpose and usage)
  * `receiver` (receiver parameter of an extension function or property)

    To annotate the receiver parameter of an extension function, use the following syntax:

    ```kotlin
    fun @receiver:Fancy String.myExtension() { ... }
    ```

  * `param` (constructor parameter)
  * `setparam` (property setter parameter)
  * `delegate` (the field storing the delegate instance for a delegated property)

### Defaults when no use-site targets are specified

如果未指定使用位置目标，系统会根据这个注解的 `@Target` 注解自动选择目标。
如果有多个适用目标，将优先选择以下列表中的第一个作为适用目标：

* `param`
* `property`
* `field`

Let's use the [`@Email` annotation from Jakarta Bean Validation](https://jakarta.ee/specifications/bean-validation/3.0/apidocs/jakarta/validation/constraints/email):

```java
@Target(value={METHOD,FIELD,ANNOTATION_TYPE,CONSTRUCTOR,PARAMETER,TYPE_USE})
public @interface Email { }
```

With this annotation, consider the following example:

```kotlin
data class User(val username: String,
                // @Email is equivalent to @param:Email
                @Email val email: String) {
    // @Email is equivalent to @field:Email
    @Email val secondaryEmail: String? = null
}
```

Kotlin 2.2.0 introduced an experimental defaulting rule which should
make propagating annotations to parameters, fields, and properties more predictable.

With the new rule, if there are multiple applicable targets, one or more is chosen as follows:

* If the constructor parameter target (`param`) is applicable, it is used.
* If the property target (`property`) is applicable, it is used.
* If the field target (`field`) is applicable while `property` isn't, `field` is used.

Using the same example:

```kotlin
data class User(val username: String,
                // @Email is now equivalent to @param:Email @field:Email
                @Email val email: String) {
    // @Email is still equivalent to @field:Email
    @Email val secondaryEmail: String? = null
}
```

If there are multiple targets, and none of `param`, `property`, or `field` are applicable, the annotation is invalid.

To enable the new defaulting rule, use the following line in your Gradle configuration:

```kotlin
// build.gradle.kts
kotlin {
    compilerOptions {
        freeCompilerArgs.add("-Xannotation-default-target=param-property")
    }
}
```

Whenever you'd like to use the old behavior, you can:

* In a specific case, specify the necessary target explicitly, for example, using `@param:Annotation` instead of `@Annotation`.
* For a whole project, use this flag in your Gradle build file:

    ```kotlin
    // build.gradle.kts
    kotlin {
        compilerOptions {
            freeCompilerArgs.add("-Xannotation-default-target=first-only")
        }
    }
    ```

### `all` meta-target

<primary-label ref="experimental-opt-in"/>

The `all` target makes it easier to apply the same annotation not only to the parameter and the property or field, but also to the corresponding getter and setter.

Specifically, the annotation marked with `all` is propagated, if applicable:

* To the constructor parameter (`param`) if the property is defined in the primary constructor.
* To the property itself (`property`).
* To the backing field (`field`) if the property has one.
* To the getter (`get`).
* To the setter parameter (`setparam`) if the property is defined as `var`.
* To the Java-only target `RECORD_COMPONENT` if the class has the `@JvmRecord` annotation.

Let's use the [`@Email` annotation from Jakarta Bean Validation](https://jakarta.ee/specifications/bean-validation/3.0/apidocs/jakarta/validation/constraints/email),
which is defined as follows:

```java
@Target(value={METHOD,FIELD,ANNOTATION_TYPE,CONSTRUCTOR,PARAMETER,TYPE_USE})
public @interface Email { }
```

In the example below, this `@Email` annotation is applied to all relevant targets:

```kotlin
data class User(
    val username: String,
    // Applies @Email to param, field, and get
    @all:Email val email: String,
    // Applies @Email to param, field, get, and setparam
    @all:Email var name: String,
) {
    // Applies @Email to field and getter (not param since it's not in the constructor)
    @all:Email val secondaryEmail: String? = null
}
```

You can use the `all` meta-target with any property, both inside and outside the primary constructor.

#### Limitations

The `all` target comes with some limitations:

* It does not propagate an annotation to types, potential extension receivers, or context receivers or parameters.
* It cannot be used with multiple annotations:
    ```kotlin
    @all:[A B] // forbidden, use @all:A @all:B
    val x: Int = 5
    ```
* It cannot be used with [delegated properties](delegated-properties.md).

#### How to enable

To enable the `all` meta-target in your project, use the following compiler option in the command line:

```Bash
-Xannotation-target-all
```

Or add it to the `compilerOptions {}` block of your Gradle build file:

```kotlin
// build.gradle.kts
kotlin {
    compilerOptions {
        freeCompilerArgs.add("-Xannotation-target-all")
    }
}
```

## Java 注解 {id=java-annotations}

Java 注解与 Kotlin 100% 兼容：

```kotlin
import org.junit.Test
import org.junit.Assert.*
import org.junit.Rule
import org.junit.rules.*

class Tests {
    // 应用 @Rule 注解到属性 getter
    @get:Rule val tempFolder = TemporaryFolder()

    @Test fun simple() {
        val f = tempFolder.newFile()
        assertEquals(42, getTheAnswer())
    }
}
```

由于用 Java 编写的注解的参数顺序未定义，因此你不能使用常规函数调用语法来传递参数。
相反，你需要使用具名参数语法：

```java
// Java
public @interface Ann {
    int intValue();
    String stringValue();
}
```

```kotlin
// Kotlin
@Ann(intValue = 1, stringValue = "abc") class C
```

就像在 Java 中一样，特殊情况是 `value` 参数；其值可以在没有显式名称的情况下指定：

```java
// Java
public @interface AnnWithValue {
    String value();
}
```

```kotlin
// Kotlin
@AnnWithValue("abc") class C
```

### 数组作为注解参数 {id=arrays-as-annotation-parameters}

如果 Java 中 `value` 参数是数组类型，那么它在 Kotlin 中变成了 `vararg` 参数：

``` java
// Java
public @interface AnnWithArrayValue {
    String[] value();
}
```

```kotlin
// Kotlin
@AnnWithArrayValue("abc", "foo", "bar") class C
```

对于其他数组类型的参数，你需要使用 数组字面量语法 或 `arrayOf(...)`：

``` java
// Java
public @interface AnnWithArrayMethod {
    String[] names();
}
```

```kotlin
@AnnWithArrayMethod(names = ["abc", "foo", "bar"])
class C
```

### 访问注解实例的属性 {id=accessing-properties-of-an-annotation-instance}

注解实例的值在 Kotlin 代码中作为属性暴露：

```java
// Java
public @interface Ann {
    int value();
}
```

```kotlin
// Kotlin
fun foo(ann: Ann) {
    val i = ann.value
}
```

### 禁止生成 JVM 1.8+ 注解目标的能力 {id=ability-to-not-generate-jvm-18-annotation-targets}

如果一个 Kotlin 注解在其 Kotlin 目标中包含 `TYPE`，则该注解在其 Java
注解目标列表中映射到 `java.lang.annotation.ElementType.TYPE_USE`。
这就像 Kotlin 目标 `TYPE_PARAMETER`映射到 Java 目标 `java.lang.annotation.ElementType.TYPE_PARAMETER` 一样。
这对于 API 级别低于 26 的 Android 客户端来说是一个问题，因为这些目标在 API 中不存在。

为了避免生成 `TYPE_USE` 和 `TYPE_PARAMETER` 注解目标，请使用新的编译器参数 `-Xno-new-java-annotation-targets`。

## 可重复注解 {id=repeatable-annotations}

就像 [在 Java 中](https://docs.oracle.com/javase/tutorial/java/annotations/repeating.html)
一样，Kotlin 也支持可重复注解，可以多次应用于单个代码元素。
要使你的注解可重复，请使用
[`@kotlin.annotation.Repeatable`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.annotation/-repeatable/)
元注解标记其声明。这将使其在 Kotlin 和 Java 中都可重复。Java 可重复注解在 Kotlin 端也得到了支持。

与 Java 使用的方案的主要区别是没有 _容器注解（containing annotation）_，Kotlin 编译器会自动生成一个预定义名称的容器注解。
对于下面的示例中的注解，编译器会生成容器注解 `@Tag.Container`：

```kotlin
@Repeatable
annotation class Tag(val name: String)

// 编译器生成 @Tag.Container 包含注解
```

你可以通过应用 [`@kotlin.jvm.JvmRepeatable`](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin.jvm/-jvm-repeatable/)
元注解，并将显式声明的容器注解类作为参数传递，用来自定义容器注解的名称：

```kotlin
@JvmRepeatable(Tags::class)
annotation class Tag(val name: String)

annotation class Tags(val value: Array<Tag>)
```

要通过反射提取 Kotlin 或 Java 可重复注解，请使用
[`KAnnotatedElement.findAnnotations()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect.full/find-annotations.html)
函数。

在 [这个 KEEP](https://github.com/Kotlin/KEEP/blob/master/proposals/repeatable-annotations.md)
中了解更多关于 Kotlin 可重复注解的信息。