[//]: # (title: 在 Kotlin 中调用 Java)

Kotlin 是以与 Java 的互操作性为设计目标的。现有的 Java 代码可以自然地从 Kotlin 中调用，Kotlin 代码也可以相对流畅地用于 Java。
在本节中，我们将描述从 Kotlin 调用 Java 代码的一些细节。

几乎所有的 Java 代码都可以毫无问题地使用：

```kotlin
import java.util.*

fun demo(source: List<Int>) {
    val list = ArrayList<Int>()
    // 'for' 循环适用于 Java 集合：
    for (item in source) {
        list.add(item)
    }
    // 操作符约定也同样适用：
    for (i in 0..source.size - 1) {
        list[i] = source[i] // 调用了 get 和 set 方法
    }
}
```

## Getter 和 setter {id=getters-and-setters}

遵循 Java getter 和 setter 约定的方法（无参数方法且以 `get`
开头的名称，以及单参数方法且以 `set` 开头的名称）在 Kotlin 中被表示为属性。
此类属性也称为 _合成属性_。  
`Boolean` 的访问器方法（getter 方法的名称以 `is` 开头，setter 方法的名称以 `set` 开头）
在 Kotlin 中表示为与 getter 方法同名的属性。

```kotlin
import java.util.Calendar

fun calendarDemo() {
    val calendar = Calendar.getInstance()
    if (calendar.firstDayOfWeek == Calendar.SUNDAY) { // 调用了 getFirstDayOfWeek()
        calendar.firstDayOfWeek = Calendar.MONDAY // 调用了 setFirstDayOfWeek()
    }
    if (!calendar.isLenient) { // 调用了 isLenient()
        calendar.isLenient = true // 调用了 setLenient()
    }
}
```

上面的 `calendar.firstDayOfWeek` 是合成属性的一个示例。

请注意，如果 Java 类只有一个 setter 方法，那么它在 Kotlin 中不会被表示为属性，因为 Kotlin 不支持仅有 setter 的属性。

## Java 合成属性引用 {id=java-synthetic-property-references}

> 此功能是 [实验性的](components-stability.md#stability-levels-explained)。它可能随时被移除或更改。  
> 我们建议仅将其用于评估目的。  
>
{style="warning"}

从 Kotlin 1.8.20 开始，你可以创建对 Java 合成属性的引用。请参考以下 Java 代码：

```java
public class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }
}
```

Kotlin 一直允许你使用 `person.age`，其中 `age` 是一个合成属性。
现在，你也可以创建对 `Person::age` 和 `person::age` 的引用。`name` 同样适用。

```kotlin
val persons = listOf(Person("Jack", 11), Person("Sofie", 12), Person("Peter", 11))
    persons
         // 调用对 Java 合成属性的引用：
        .sortedBy(Person::age)
         // 通过 Kotlin 属性语法调用 Java getter 方法：
        .forEach { person -> println(person.name) }
```

### 如何启用 Java 合成属性引用 {id=how-to-enable-java-synthetic-property-references collapsible="true"}

要启用此功能，请设置编译器选项 `-language-version 2.1`。
在 Gradle 项目中，你可以通过将以下内容添加到 `build.gradle(.kts)` 来实现：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
tasks
    .withType<org.jetbrains.kotlin.gradle.tasks.KotlinCompilationTask<*>>()
    .configureEach {
        compilerOptions
            .languageVersion
            .set(
                org.jetbrains.kotlin.gradle.dsl.KotlinVersion.KOTLIN_2_1
            )
    }
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
tasks
    .withType(org.jetbrains.kotlin.gradle.tasks.KotlinCompilationTask.class)
    .configureEach {
        compilerOptions.languageVersion
            = org.jetbrains.kotlin.gradle.dsl.KotlinVersion.KOTLIN_2_1
}
```

</tab>
</tabs>

> 在 Kotlin 1.9.0 之前，要启用此功能，必须设置编译器选项 `-language-version 1.9`。  
> 
{style="note"}

## 返回 void 的方法 {id=methods-returning-void}

如果一个 Java 方法返回 `void`，则在 Kotlin 中调用时会返回 `Unit`。  
如果有人不小心使用了该返回值，Kotlin 编译器将在调用点进行赋值，因为该值在预先是已知的（为 `Unit`）。

## 对于在 Kotlin 中为关键字的 Java 标识符进行转义 {id=escaping-for-java-identifiers-that-are-keywords-in-kotlin}

一些 Kotlin 关键字在 Java 中是有效的标识符：`in`、`object`、`is` 等。  
如果一个 Java 库使用 Kotlin 关键字作为方法名，你仍然可以通过使用反引号（`）字符来调用该方法：

```kotlin
foo.`is`(bar)
```

## 空安全和平台类型 {id=null-safety-and-platform-types}

在 Java 中，任何引用都可能为 `null`，这使得 Kotlin 对来自 Java 的对象严格的空安全要求变得不切实际。  
Java 声明的类型在 Kotlin 中以特定方式处理，称为 *平台类型*。
对于此类类型的空检查被放宽，因此它们的安全性保证与 Java 相同（详见 [下面](#mapped-types)）。

考虑以下示例：

```kotlin
val list = ArrayList<String>() // 非空（构造函数结果）
list.add("Item")
val size = list.size // 非空（基本类型 int）
val item = list[0] // 平台类型推断（普通 Java 对象）
```

当你在平台类型的变量上调用方法时，Kotlin 不会在编译时发出空值错误，
但该调用可能在运行时失败，因为会发生空指针异常或 Kotlin 生成的断言以防止空值传播：

```kotlin
item.substring(1) // 允许，如果 item == null 则抛出异常
```

平台类型是 *不可表述的*，这意味着你不能在语言中显式地书写它们。
当一个平台值被赋值给 Kotlin 变量时，你可以依赖类型推断（该变量将具有推断的平台类型，正如上面示例中的
`item`），或者你可以选择你期望的类型（允许 nullable 和 non-nullable 类型）：

```kotlin
val nullable: String? = item // 允许，总是有效
val notNull: String = item // 允许，可能在运行时失败
```

如果你选择了非空类型，编译器将在赋值时发出断言。这防止了 Kotlin 的非空变量持有 null。
当你将平台值传递给期望非空值的 Kotlin 函数时，编译器也会发出断言，其他情况亦然。
总体而言，编译器尽力防止 null 在程序中传播，尽管有时由于泛型，这种传播是无法完全消除的。

### 平台类型的表示法 {id=notation-for-platform-types}

如上所述，平台类型不能在程序中显式提及，因此语言中没有它们的语法。  
然而，编译器和 IDE 有时需要显示这些类型（例如，在错误消息或参数信息中），因此有一种助记符表示法：

* `T!` 表示 "`T` 或 `T?`"，
* `(Mutable)Collection<T>!` 表示 "Java 集合的 `T` 可能是可变的，也可能不是，可能是可空的，也可能不是"，
* `Array<(out) T>!` 表示 "Java 数组的 `T`（或 `T` 的子类型），可能是可空的，也可能不是"。

### 空值注解 {id=nullability-annotations}

具有空值注解的 Java 类型被表示为实际的可空或非空
Kotlin 类型，而不是平台类型。编译器支持几种类型的空值注解，包括：

  * [JetBrains](https://www.jetbrains.com/idea/help/nullable-and-notnull-annotations.html)
（来自 `org.jetbrains.annotations` 包的 `@Nullable` 和 `@NotNull`）
  * [JSpecify](https://jspecify.dev/)（`org.jspecify.nullness`）
  * Android（`com.android.annotations` 和 `android.support.annotations`）
  * JSR-305（`javax.annotation`，详细信息见下文）
  * FindBugs（`edu.umd.cs.findbugs.annotations`）
  * Eclipse（`org.eclipse.jdt.annotation`）
  * Lombok（`lombok.NonNull`）
  * RxJava 3（`io.reactivex.rxjava3.annotations`）

你可以设置编译器，是否根据特定类型的空值注解来报告空值不匹配问题。
使用编译器选项 `-Xnullability-annotations=@<package-name>:<report-level>`。
在参数中，指定完全限定的空值注解包和以下报告级别之一：
* `ignore` 以忽略空值不匹配
* `warn` 以报告警告
* `strict` 以报告错误。

请参阅[Kotlin 编译器源代码](https://github.com/JetBrains/kotlin/blob/master/core/compiler.common.jvm/src/org/jetbrains/kotlin/load/java/JvmAnnotationNames.kt)中支持的空值注解的完整列表。

### 注解类型实参和类型形参 {id=annotating-type-arguments-and-type-parameters}

你可以注解泛型类型的类型实参和类型形参，以提供它们的空值信息。

> 本节中的所有示例使用来自 `org.jetbrains.annotations` 包的 JetBrains 空值注解。
>
{style="note"}

#### 类型实参 {id=type-arguments}

考虑以下 Java 声明中的注解：

```java
@NotNull
Set<@NotNull String> toSet(@NotNull Collection<@NotNull String> elements) { ... }
```

它们在 Kotlin 中产生以下签名：

```kotlin
fun toSet(elements: (Mutable)Collection<String>) : (Mutable)Set<String> { ... }
```

当类型实参缺少 `@NotNull` 注解时，你将得到一个平台类型：

```kotlin
fun toSet(elements: (Mutable)Collection<String!>) : (Mutable)Set<String!> { ... }
```

Kotlin 还考虑了基类和接口的类型实参上的空值注解。例如，下面有两个 Java 类，其签名如下：

```java
public class Base<T> {}
```

```java
public class Derived extends Base<@Nullable String> {}
```

在 Kotlin 代码中，将 `Derived` 的实例传递到假定为 `Base<String>` 的地方会产生警告。

```kotlin
fun takeBaseOfNotNullStrings(x: Base<String>) {}

fun main() {
    takeBaseOfNotNullStrings(Derived()) // 警告：空值不匹配
}
```

`Derived` 的上限被设置为 `Base<String?>`，这与 `Base<String>` 不同。

了解更多关于[Kotlin中的 Java 泛型](java-interop.md#java-generics-in-kotlin)。

#### 类型形参 {id=type-parameters}

默认情况下，Kotlin 和 Java 中普通类型形参的空值性是未定义的。在 Java 中，你可以使用空值注解来指定它。
让我们为 `Base` 类的类型形参添加注解：

```java
public class Base<@NotNull T> {}
```

当从 `Base` 继承时，Kotlin 期望一个非空类型实参或类型形参。
因此，以下 Kotlin 代码会产生警告：

```kotlin
class Derived<K> : Base<K> {} // 警告：K 的空值性未定义
```

你可以通过指定上限 `K : Any` 来修复它。

Kotlin 还支持 Java 类型形参边界上的空值注解。让我们为 `Base` 添加边界：

```java
public class BaseWithBound<T extends @NotNull Number> {}
```

Kotlin 将其翻译如下：

```kotlin
class BaseWithBound<T : Number> {}
```

将可空类型作为类型实参或类型形参传递会产生警告。

为类型实参和类型形参添加注解适用于 Java 8 及更高版本。
此功能要求空值注解支持 `TYPE_USE` 目标（`org.jetbrains.annotations` 在 15 及更高版本中支持此目标）。
使用 `-Xtype-enhancement-improvements-strict-mode` 编译器选项可以报告使用与 Java 的空值注解不符的 Kotlin 代码中的错误。

> 注意：如果一个空值注解支持其他适用于类型的目标，除了 `TYPE_USE` 目标，则 `TYPE_USE` 优先。
> 例如，如果 `@Nullable` 同时具有 `TYPE_USE` 和 `METHOD` 目标，则 Java 方法
> 签名 `@Nullable String[] f()` 在 Kotlin 中变为 `fun f(): Array<String?>!`。
>
{style="note"}

### JSR-305 支持 {id=jsr-305-support}

[`@Nonnull`](https://www.javadoc.io/doc/com.google.code.findbugs/jsr305/latest/javax/annotation/Nonnull.html)
注解在 [JSR-305](https://jcp.org/en/jsr/detail?id=305) 中定义，用于表示 Java 类型的可空性。

如果 `@Nonnull(when = ...)` 的值为 `When.ALWAYS`，则标注的类型被视为非空类型；`When.MAYBE` 和
`When.NEVER` 表示可空类型；而 `When.UNKNOWN` 强制类型为 [平台类型](#null-safety-and-platform-types)。

一个库可以针对 JSR-305 注解进行编译，但不需要将注解工件（例如 `jsr305.jar`）
作为库消费者的编译依赖。Kotlin 编译器可以从没有注解存在于类路径上的库中读取 JSR-305 注解。

[自定义可空性限定符 (KEEP-79)](https://github.com/Kotlin/KEEP/blob/master/proposals/jsr-305-custom-nullability-qualifiers.md)
也得到了支持（见下文）。

#### 类型限定符别名 {id=type-qualifier-nicknames}

如果一个注解类型同时被标注为
[`@TypeQualifierNickname`](https://www.javadoc.io/doc/com.google.code.findbugs/jsr305/latest/javax/annotation/meta/TypeQualifierNickname.html)
和 JSR-305 的 `@Nonnull`（或其另一个别名，例如 `@CheckForNull`），则该注解类型本身用于获取精确的可空性，
并且具有与该可空性注解相同的含义：

```java
@TypeQualifierNickname
@Nonnull(when = When.ALWAYS)
@Retention(RetentionPolicy.RUNTIME)
public @interface MyNonnull {
}

@TypeQualifierNickname
@CheckForNull // 另一个类型限定符别名
@Retention(RetentionPolicy.RUNTIME)
public @interface MyNullable {
}

interface A {
    @MyNullable String foo(@MyNonnull String x);
    // 在 Kotlin（严格模式）：`fun foo(x: String): String?`

    String bar(List<@MyNonnull String> x);
    // 在 Kotlin（严格模式）：`fun bar(x: List<String>!): String!`
}
```

#### 类型限定符默认值 {id=type-qualifier-defaults}

[`@TypeQualifierDefault`](https://www.javadoc.io/doc/com.google.code.findbugs/jsr305/latest/javax/annotation/meta/TypeQualifierDefault.html)
允许引入注解，当应用时，在被注解元素的范围内定义默认的可空性。

这样的注解类型应该同时被标注为 `@Nonnull`（或其别名）和 `@TypeQualifierDefault(...)`，并且具有一个或多个 `ElementType` 值：

* `ElementType.METHOD` 用于方法的返回类型
* `ElementType.PARAMETER` 用于值参数
* `ElementType.FIELD` 用于字段
* `ElementType.TYPE_USE` 用于任何类型，包括类型参数的类型实参、上界和通配符类型

当类型本身没有被可空性注解标注时，会使用默认的可空性，默认值由最近的被标注为类型限定符默认注解的元素确定，该元素的
`ElementType` 与类型使用相匹配。

```java
@Nonnull
@TypeQualifierDefault({ElementType.METHOD, ElementType.PARAMETER})
public @interface NonNullApi {
}

@Nonnull(when = When.MAYBE)
@TypeQualifierDefault({ElementType.METHOD, ElementType.PARAMETER, ElementType.TYPE_USE})
public @interface NullableApi {
}

@NullableApi
interface A {
    String foo(String x); // fun foo(x: String?): String?

    @NotNullApi // 覆盖接口中的默认值
    String bar(String x, @Nullable String y); // fun bar(x: String, y: String?): String

    // 由于 `@NullableApi` 具有 `TYPE_USE` 元素类型，List<String> 类型实参被视为可空：
    String baz(List<String> x); // fun baz(List<String?>?): String?

    // `x` 参数的类型保持平台类型，因为有一个显式的
    // 标记为 UNKNOWN 的可空性注解：
    String qux(@Nonnull(when = When.UNKNOWN) String x); // fun baz(x: String!): String?
}
```

> 本示例中的类型仅在启用严格模式时生效；否则，平台类型保持不变。
> 请参见 [`@UnderMigration` 注解](#undermigration-annotation) 和 [编译器配置](#compiler-configuration) 部分。
>
{style="note"}

包级默认可空性也得到了支持：

```java
// FILE: test/package-info.java
@NonNullApi // 将包 'test' 中的所有类型声明为默认非空
package test;
```

#### @UnderMigration 注解 {id=undermigration-annotation}

`@UnderMigration` 注解（提供在单独的工件 `kotlin-annotations-jvm` 中）可以被库维护者用来定义可空性类型限定符的迁移状态。

`@UnderMigration(status = ...)` 中的状态值指定了编译器如何处理
Kotlin 中对标注类型的不当用法（例如，将标注为 `@MyNullable` 的类型值用作非空）：

* `MigrationStatus.STRICT` 使注解像任何普通可空性注解一样工作，即对不当用法报告错误，并影响标注声明中类型在 Kotlin 中的表现。
* `MigrationStatus.WARN`：不当用法会被报告为编译警告而不是错误，但标注声明中的类型保持为平台类型。
* `MigrationStatus.IGNORE` 使编译器完全忽略可空性注解。

库维护者可以将 `@UnderMigration` 状态添加到类型限定符别名和类型限定符默认值中：

```java
@Nonnull(when = When.ALWAYS)
@TypeQualifierDefault({ElementType.METHOD, ElementType.PARAMETER})
@UnderMigration(status = MigrationStatus.WARN)
public @interface NonNullApi {
}

// 类中的类型是非空的，但由于 `@NonNullApi` 被标注为 `@UnderMigration(status = MigrationStatus.WARN)`，所以只报告警告
@NonNullApi
public class Test {}
```

> 可空性注解的迁移状态不会被其类型限定符别名继承，但会应用于默认类型限定符中的用法。
>
{style="note"}

如果默认类型限定符使用了类型限定符别名，并且它们都标注为 `@UnderMigration`，则使用默认类型限定符中的状态。

#### Compiler configuration

The JSR-305 checks can be configured by adding the `-Xjsr305` compiler flag with the following options (and their combination):

* `-Xjsr305={strict|warn|ignore}` to set up the behavior for non-`@UnderMigration` annotations.
Custom nullability qualifiers, especially
`@TypeQualifierDefault`, are already spread among many well-known libraries, and users may need to migrate smoothly when
updating to the Kotlin version containing JSR-305 support. Since Kotlin 1.1.60, this flag only affects non-`@UnderMigration` annotations.

* `-Xjsr305=under-migration:{strict|warn|ignore}` to override the behavior for the `@UnderMigration` annotations.
Users may have different view on the migration status for the libraries:
they may want to have errors while the official migration status is `WARN`, or vice versa,
they may wish to postpone errors reporting for some until they complete their migration.

* `-Xjsr305=@<fq.name>:{strict|warn|ignore}` to override the behavior for a single annotation, where `<fq.name>`
is the fully qualified class name of the annotation. May appear several times for different annotations. This is useful
for managing the migration state for a particular library.

The `strict`, `warn` and `ignore` values have the same meaning as those of `MigrationStatus`,
and only the `strict` mode affects the types in the annotated declarations as they are seen in Kotlin.

> Note: the built-in JSR-305 annotations [`@Nonnull`](https://www.javadoc.io/doc/com.google.code.findbugs/jsr305/latest/javax/annotation/Nonnull.html),
>[`@Nullable`](https://www.javadoc.io/doc/com.google.code.findbugs/jsr305/3.0.1/javax/annotation/Nullable.html) and
>[`@CheckForNull`](https://www.javadoc.io/doc/com.google.code.findbugs/jsr305/latest/javax/annotation/CheckForNull.html) are always enabled and
>affect the types of the annotated declarations in Kotlin, regardless of compiler configuration with the `-Xjsr305` flag.
>
{style="note"}

For example, adding `-Xjsr305=ignore -Xjsr305=under-migration:ignore -Xjsr305=@org.library.MyNullable:warn` to the
compiler arguments makes the compiler generate warnings for inappropriate usages of types annotated by
`@org.library.MyNullable` and ignore all other JSR-305 annotations.

The default behavior is the same to `-Xjsr305=warn`. The
`strict` value should be considered experimental (more checks may be added to it in the future).

## Mapped types

Kotlin treats some Java types specifically. Such types are not loaded from Java "as is", but are _mapped_ to corresponding Kotlin types.
The mapping only matters at compile time, the runtime representation remains unchanged.
 Java's primitive types are mapped to corresponding Kotlin types (keeping [platform types](#null-safety-and-platform-types) in mind):

| **Java type** | **Kotlin type**  |
|---------------|------------------|
| `byte`        | `kotlin.Byte`    |
| `short`       | `kotlin.Short`   |
| `int`         | `kotlin.Int`     |
| `long`        | `kotlin.Long`    |
| `char`        | `kotlin.Char`    |
| `float`       | `kotlin.Float`   |
| `double`      | `kotlin.Double`  |
| `boolean`     | `kotlin.Boolean` |

Some non-primitive built-in classes are also mapped:

| **Java type** | **Kotlin type**  |
|---------------|------------------|
| `java.lang.Object`       | `kotlin.Any!`    |
| `java.lang.Cloneable`    | `kotlin.Cloneable!`    |
| `java.lang.Comparable`   | `kotlin.Comparable!`    |
| `java.lang.Enum`         | `kotlin.Enum!`    |
| `java.lang.annotation.Annotation`   | `kotlin.Annotation!`    |
| `java.lang.CharSequence` | `kotlin.CharSequence!`   |
| `java.lang.String`       | `kotlin.String!`   |
| `java.lang.Number`       | `kotlin.Number!`     |
| `java.lang.Throwable`    | `kotlin.Throwable!`    |

Java's boxed primitive types are mapped to nullable Kotlin types:

| **Java type**           | **Kotlin type**  |
|-------------------------|------------------|
| `java.lang.Byte`        | `kotlin.Byte?`   |
| `java.lang.Short`       | `kotlin.Short?`  |
| `java.lang.Integer`     | `kotlin.Int?`    |
| `java.lang.Long`        | `kotlin.Long?`   |
| `java.lang.Character`   | `kotlin.Char?`   |
| `java.lang.Float`       | `kotlin.Float?`  |
| `java.lang.Double`      | `kotlin.Double?`  |
| `java.lang.Boolean`     | `kotlin.Boolean?` |

Note that a boxed primitive type used as a type parameter is mapped to a platform type:
for example, `List<java.lang.Integer>` becomes a `List<Int!>` in Kotlin.

Collection types may be read-only or mutable in Kotlin, so Java's collections are mapped as follows
(all Kotlin types in this table reside in the package `kotlin.collections`):

| **Java type** | **Kotlin read-only type**  | **Kotlin mutable type** | **Loaded platform type** |
|---------------|----------------------------|-------------------------|--------------------------|
| `Iterator<T>`        | `Iterator<T>`        | `MutableIterator<T>`            | `(Mutable)Iterator<T>!`            |
| `Iterable<T>`        | `Iterable<T>`        | `MutableIterable<T>`            | `(Mutable)Iterable<T>!`            |
| `Collection<T>`      | `Collection<T>`      | `MutableCollection<T>`          | `(Mutable)Collection<T>!`          |
| `Set<T>`             | `Set<T>`             | `MutableSet<T>`                 | `(Mutable)Set<T>!`                 |
| `List<T>`            | `List<T>`            | `MutableList<T>`                | `(Mutable)List<T>!`                |
| `ListIterator<T>`    | `ListIterator<T>`    | `MutableListIterator<T>`        | `(Mutable)ListIterator<T>!`        |
| `Map<K, V>`          | `Map<K, V>`          | `MutableMap<K, V>`              | `(Mutable)Map<K, V>!`              |
| `Map.Entry<K, V>`    | `Map.Entry<K, V>`    | `MutableMap.MutableEntry<K,V>` | `(Mutable)Map.(Mutable)Entry<K, V>!` |

Java's arrays are mapped as mentioned [below](java-interop.md#java-arrays):

| **Java type** | **Kotlin type**                |
|---------------|--------------------------------|
| `int[]`       | `kotlin.IntArray!`             |
| `String[]`    | `kotlin.Array<(out) String!>!` |

>The static members of these Java types are not directly accessible on the [companion objects](object-declarations.md#companion-objects)
>of the Kotlin types. To call them, use the full qualified names of the Java types, e.g. `java.lang.Integer.toHexString(foo)`.
>
{style="note"}

## Java generics in Kotlin

Kotlin's generics are a little different from Java's (see [Generics](generics.md)).
When importing Java types to Kotlin, the following conversions are done:

* Java's wildcards are converted into type projections:
  * `Foo<? extends Bar>` becomes `Foo<out Bar!>!`
  * `Foo<? super Bar>` becomes `Foo<in Bar!>!`

* Java's raw types are converted into star projections:
  * `List` becomes `List<*>!` that is `List<out Any?>!`

Like Java's, Kotlin's generics are not retained at runtime: objects do not carry information about actual type arguments
passed to their constructors. For example, `ArrayList<Integer>()` is indistinguishable from `ArrayList<Character>()`.
This makes it impossible to perform `is`-checks that take generics into account.
Kotlin only allows `is`-checks for star-projected generic types:

```kotlin
if (a is List<Int>) // Error: cannot check if it is really a List of Ints
// but
if (a is List<*>) // OK: no guarantees about the contents of the list
```

## Java arrays

Arrays in Kotlin are invariant, unlike Java. This means that Kotlin won't let you assign an `Array<String>` to an `Array<Any>`,
which prevents a possible runtime failure. Passing an array of a subclass as an array of superclass to a Kotlin method is also prohibited,
but for Java methods this is allowed through [platform types](#null-safety-and-platform-types) of the form `Array<(out) String>!`.

Arrays are used with primitive datatypes on the Java platform to avoid the cost of boxing/unboxing operations.
As Kotlin hides those implementation details, a workaround is required to interface with Java code.
There are specialized classes for every type of primitive array (`IntArray`, `DoubleArray`, `CharArray`, and so on) to handle this case.
They are not related to the `Array` class and are compiled down to Java's primitive arrays for maximum performance.

Suppose there is a Java method that accepts an int array of indices:

``` java
public class JavaArrayExample {
    public void removeIndices(int[] indices) {
        // code here...
    }
}
```

To pass an array of primitive values, you can do the following in Kotlin:

```kotlin
val javaObj = JavaArrayExample()
val array = intArrayOf(0, 1, 2, 3)
javaObj.removeIndices(array)  // passes int[] to method
```

When compiling to the JVM bytecode, the compiler optimizes access to arrays so that there's no overhead introduced:

```kotlin
val array = arrayOf(1, 2, 3, 4)
array[1] = array[1] * 2 // no actual calls to get() and set() generated
for (x in array) { // no iterator created
    print(x)
}
```

Even when you navigate with an index, it does not introduce any overhead:

```kotlin
for (i in array.indices) { // no iterator created
    array[i] += 2
}
```

Finally, `in`-checks have no overhead either:

```kotlin
if (i in array.indices) { // same as (i >= 0 && i < array.size)
    print(array[i])
}
```

## Java varargs

Java classes sometimes use a method declaration for the indices with a variable number of arguments (varargs):

``` java
public class JavaArrayExample {

    public void removeIndicesVarArg(int... indices) {
        // code here...
    }
}
```

In that case you need to use the spread operator `*` to pass the `IntArray`:

```kotlin
val javaObj = JavaArrayExample()
val array = intArrayOf(0, 1, 2, 3)
javaObj.removeIndicesVarArg(*array)
```

## Operators

Since Java has no way of marking methods for which it makes sense to use the operator syntax, Kotlin allows using any
Java methods with the right name and signature as operator overloads and other conventions (`invoke()` etc.)
Calling Java methods using the infix call syntax is not allowed.

## Checked exceptions

In Kotlin, all [exceptions are unchecked](exceptions.md), meaning that the compiler does not force you to catch any of them.
So, when you call a Java method that declares a checked exception, Kotlin does not force you to do anything:

```kotlin
fun render(list: List<*>, to: Appendable) {
    for (item in list) {
        to.append(item.toString()) // Java would require us to catch IOException here
    }
}
```

## Object methods

When Java types are imported into Kotlin, all the references of the type `java.lang.Object` are turned into `Any`.
Since `Any` is not platform-specific, it only declares `toString()`, `hashCode()` and `equals()` as its members,
so to make other members of `java.lang.Object` available, Kotlin uses [extension functions](extensions.md).

### wait()/notify()

Methods `wait()` and `notify()` are not available on references of type `Any`. Their usage is generally discouraged in
favor of `java.util.concurrent`. If you really need to call these methods, you can cast to `java.lang.Object`:

```kotlin
(foo as java.lang.Object).wait()
```

### getClass()

To retrieve the Java class of an object, use the `java` extension property on a [class reference](reflection.md#class-references):

```kotlin
val fooClass = foo::class.java
```

The code above uses a [bound class reference](reflection.md#bound-class-references). You can also use the `javaClass` extension property:

```kotlin
val fooClass = foo.javaClass
```

### clone()

To override `clone()`, your class needs to extend `kotlin.Cloneable`:

```kotlin
class Example : Cloneable {
    override fun clone(): Any { ... }
}
```

Don't forget about [Effective Java, 3rd Edition](https://www.oracle.com/technetwork/java/effectivejava-136174.html),
Item 13: *Override clone judiciously*.

### finalize()

To override `finalize()`, all you need to do is simply declare it, without using the `override` keyword:

```kotlin
class C {
    protected fun finalize() {
        // finalization logic
    }
}
```

According to Java's rules, `finalize()` must not be `private`.

## Inheritance from Java classes

At most one Java class (and as many Java interfaces as you like) can be a supertype for a class in Kotlin.

## Accessing static members

Static members of Java classes form "companion objects" for these classes. You can't pass such a "companion object"
around as a value but can access the members explicitly, for example:

```kotlin
if (Character.isLetter(a)) { ... }
```

To access static members of a Java type that is [mapped](#mapped-types) to a Kotlin type, use the full qualified name of
the Java type: `java.lang.Integer.bitCount(foo)`.

## Java reflection

Java reflection works on Kotlin classes and vice versa. As mentioned above, you can use `instance::class.java`,
`ClassName::class.java` or `instance.javaClass` to enter Java reflection through `java.lang.Class`.
Do not use `ClassName.javaClass` for this purpose because it refers to `ClassName`'s companion object class,
which is the same as `ClassName.Companion::class.java` and not `ClassName::class.java`.

For each primitive type, there are two different Java classes, and Kotlin provides ways to get both. For
example, `Int::class.java` will return the class instance representing the primitive type itself,
corresponding to `Integer.TYPE` in Java. To get the class of the corresponding wrapper type, use
`Int::class.javaObjectType`, which is equivalent of Java's `Integer.class`.

Other supported cases include acquiring a Java getter/setter method or a backing field for a Kotlin property, a `KProperty` for a Java field, a Java method or constructor for a `KFunction` and vice versa.


## SAM conversions

Kotlin supports SAM conversions for both Java and [Kotlin interfaces](fun-interfaces.md). 
This support for Java means that Kotlin function literals can be automatically converted
into implementations of Java interfaces with a single non-default method, as long as the parameter types of the interface
method match the parameter types of the Kotlin function.

You can use this for creating instances of SAM interfaces:

```kotlin
val runnable = Runnable { println("This runs in a runnable") }
```

...and in method calls:

```kotlin
val executor = ThreadPoolExecutor()
// Java signature: void execute(Runnable command)
executor.execute { println("This runs in a thread pool") }
```

If the Java class has multiple methods taking functional interfaces, you can choose the one you need to call by
using an adapter function that converts a lambda to a specific SAM type. Those adapter functions are also generated
by the compiler when needed:

```kotlin
executor.execute(Runnable { println("This runs in a thread pool") })
```

> SAM conversions only work for interfaces, not for abstract classes, even if those also have just a single
abstract method.
>
{style="note"}

## Using JNI with Kotlin

To declare a function that is implemented in native (C or C++) code, you need to mark it with the `external` modifier:

```kotlin
external fun foo(x: Int): Double
```

The rest of the procedure works in exactly the same way as in Java.

You can also mark property getters and setters as `external`:

```kotlin
var myProperty: String
    external get
    external set
```

Behind the scenes, this will create two functions `getMyProperty` and `setMyProperty`, both marked as `external`.

## Using Lombok-generated declarations in Kotlin

You can use Java's Lombok-generated declarations in Kotlin code.
If you need to generate and use these declarations in the same mixed Java/Kotlin module,
you can learn how to do this on the [Lombok compiler plugin's page](lombok.md).
If you call such declarations from another module, then you don't need to use this plugin to compile that module.
