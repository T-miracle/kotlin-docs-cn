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

### 如何启用 Java 合成属性引用 {id=how-to-enable-java-synthetic-property-references collapsible="true" collapsible="true"}

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
  * [JSpecify](https://jspecify.dev/)（`org.jspecify.annotations`）
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

对类型实参和类型形参的注解需要 Java 8 或更高版本目标平台。
该功能要求可空性注解支持 `TYPE_USE` 目标（`org.jetbrains.annotations` 在 15 及以上版本支持此特性）。

> 如果一个可空性注解除了支持 `TYPE_USE` 目标外还支持其他适用于类型的注解目标，则优先采用 `TYPE_USE` 目标。
> 例如，如果 `@Nullable` 同时具有 `TYPE_USE` 和 `METHOD` 目标，Java 方法签名
> `@Nullable String[] f()` 在 Kotlin 中会变成 `fun f(): Array<String?>!`
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

#### 编译器配置 {id=compiler-configuration}

可以通过添加 `-Xjsr305` 编译器标志和以下选项（及其组合）来配置 JSR-305 检查：

* `-Xjsr305={strict|warn|ignore}` 设置非 `@UnderMigration` 注解的行为。  
自定义可空性限定符，尤其是 `@TypeQualifierDefault`，已广泛应用于许多知名库中，因此用户在更新到支持
JSR-305 的 Kotlin 版本时可能需要平稳迁移。自 Kotlin 1.1.60 以来，此标志仅影响非 `@UnderMigration` 注解。

* `-Xjsr305=under-migration:{strict|warn|ignore}` 用于覆盖 `@UnderMigration` 注解的行为。  
  用户可能对库的迁移状态有不同看法：他们可能希望在官方迁移状态为 `WARN` 时强制显示错误，或反之，他们希望推迟错误报告，直到完成迁移。

* `-Xjsr305=@<fq.name>:{strict|warn|ignore}` 用于覆盖单个注解的行为，其中 `<fq.name>` 是注解的完全限定类名。
可以针对不同的注解多次出现。此选项在管理特定库的迁移状态时非常有用。

`strict`、`warn` 和 `ignore` 值的含义与 `MigrationStatus` 的含义相同，并且只有 `strict` 模式会影响 Kotlin 中查看到的注解声明中的类型。

> 注意：内置的 JSR-305 注解 [`@Nonnull`](https://www.javadoc.io/doc/com.google.code.findbugs/jsr305/latest/javax/annotation/Nonnull.html)、
> [`@Nullable`](https://www.javadoc.io/doc/com.google.code.findbugs/jsr305/3.0.1/javax/annotation/Nullable.html) 和
> [`@CheckForNull`](https://www.javadoc.io/doc/com.google.code.findbugs/jsr305/latest/javax/annotation/CheckForNull.html)
> 始终启用，并影响 Kotlin 中注解声明的类型，无论 `-Xjsr305` 标志的编译器配置如何。  
>
{style="note"}

例如，添加 `-Xjsr305=ignore -Xjsr305=under-migration:ignore -Xjsr305=@org.library.MyNullable:warn`
到编译器参数中，会使编译器为不当使用
`@org.library.MyNullable` 注解的类型生成警告，并忽略所有其他 JSR-305 注解。

默认行为与 `-Xjsr305=warn` 相同。`strict` 值应视为实验性功能（将来可能会添加更多检查）。

## 映射类型 {id=mapped-types}

Kotlin 特别处理某些 Java 类型。这些类型不会直接从 Java 加载，而是会 _映射_ 到相应的 Kotlin 类型。
此映射仅在编译时生效，运行时表示保持不变。
Java 的基本类型会映射到对应的 Kotlin 类型（需考虑[平台类型](#null-safety-and-platform-types)）：

| **Java 类型** | **Kotlin 类型**    |
|-------------|------------------|
| `byte`      | `kotlin.Byte`    |
| `short`     | `kotlin.Short`   |
| `int`       | `kotlin.Int`     |
| `long`      | `kotlin.Long`    |
| `char`      | `kotlin.Char`    |
| `float`     | `kotlin.Float`   |
| `double`    | `kotlin.Double`  |
| `boolean`   | `kotlin.Boolean` |

一些非原始内置类也被映射：

| **Java 类型**                       | **Kotlin 类型**          |
|-----------------------------------|------------------------|
| `java.lang.Object`                | `kotlin.Any!`          |
| `java.lang.Cloneable`             | `kotlin.Cloneable!`    |
| `java.lang.Comparable`            | `kotlin.Comparable!`   |
| `java.lang.Enum`                  | `kotlin.Enum!`         |
| `java.lang.annotation.Annotation` | `kotlin.Annotation!`   |
| `java.lang.CharSequence`          | `kotlin.CharSequence!` |
| `java.lang.String`                | `kotlin.String!`       |
| `java.lang.Number`                | `kotlin.Number!`       |
| `java.lang.Throwable`             | `kotlin.Throwable!`    |

Java 的装箱基本类型被映射为可空的 Kotlin 类型：

| **Java 类型**           | **Kotlin 类型**     |
|-----------------------|-------------------|
| `java.lang.Byte`      | `kotlin.Byte?`    |
| `java.lang.Short`     | `kotlin.Short?`   |
| `java.lang.Integer`   | `kotlin.Int?`     |
| `java.lang.Long`      | `kotlin.Long?`    |
| `java.lang.Character` | `kotlin.Char?`    |
| `java.lang.Float`     | `kotlin.Float?`   |
| `java.lang.Double`    | `kotlin.Double?`  |
| `java.lang.Boolean`   | `kotlin.Boolean?` |

请注意，作为类型形参使用的装箱基本类型会映射为平台类型：
例如，`List<java.lang.Integer>` 在 Kotlin 中会变成 `List<Int!>`。

在 Kotlin 中，集合类型可以是只读的或可变的，因此 Java 的集合映射如下表所示
（本表中所有 Kotlin 类型都位于 `kotlin.collections` 包中）：

| **Java 类型**       | **Kotlin 只读类型**   | **Kotlin 可变类型**                | **加载的平台类型**                          |
|-------------------|-------------------|--------------------------------|--------------------------------------|
| `Iterator<T>`     | `Iterator<T>`     | `MutableIterator<T>`           | `(Mutable)Iterator<T>!`              |
| `Iterable<T>`     | `Iterable<T>`     | `MutableIterable<T>`           | `(Mutable)Iterable<T>!`              |
| `Collection<T>`   | `Collection<T>`   | `MutableCollection<T>`         | `(Mutable)Collection<T>!`            |
| `Set<T>`          | `Set<T>`          | `MutableSet<T>`                | `(Mutable)Set<T>!`                   |
| `List<T>`         | `List<T>`         | `MutableList<T>`               | `(Mutable)List<T>!`                  |
| `ListIterator<T>` | `ListIterator<T>` | `MutableListIterator<T>`       | `(Mutable)ListIterator<T>!`          |
| `Map<K, V>`       | `Map<K, V>`       | `MutableMap<K, V>`             | `(Mutable)Map<K, V>!`                |
| `Map.Entry<K, V>` | `Map.Entry<K, V>` | `MutableMap.MutableEntry<K,V>` | `(Mutable)Map.(Mutable)Entry<K, V>!` |

Java 的数组映射如[下方](java-interop.md#java-arrays)所述：

| **Java 类型** | **Kotlin 类型**                  |
|-------------|--------------------------------|
| `int[]`     | `kotlin.IntArray!`             |
| `String[]`  | `kotlin.Array<(out) String!>!` |

> 这些 Java 类型的静态成员无法直接通过 Kotlin 类型的[伴生对象](object-declarations.md#companion-objects)访问。
> 要调用它们，请使用 Java 类型的完全限定名称，例如 `java.lang.Integer.toHexString(foo)`。
>
{style="note"}

## Kotlin 中的 Java 泛型 {id=java-generics-in-kotlin}

Kotlin 的泛型与 Java 略有不同（参见[泛型](generics.md)）。
导入 Java 类型到 Kotlin 时，会进行以下转换：

* Java 的通配符转换为类型投影：
  * `Foo<? extends Bar>` 转换为 `Foo<out Bar!>!`
  * `Foo<? super Bar>` 转换为 `Foo<in Bar!>!`

* Java 的原始类型转换为星号投影：
  * `List` 转换为 `List<*>!`，即 `List<out Any?>!`

与 Java 一样，Kotlin 的泛型在运行时不会保留：对象不会携带传递给其构造函数的实际类型实参信息。
例如，`ArrayList<Integer>()` 与 `ArrayList<Character>()` 无法区分。
这使得无法执行考虑泛型的 `is` 检查。
Kotlin 仅允许对星号投影的泛型类型进行 `is` 检查：

```kotlin
if (a is List<Int>) // 错误：无法检查是否确实为 Int 类型的 List
// 但
if (a is List<*>) // 可以：无法保证列表内容
```

## Java 数组 {id=java-arrays}

与 Java 不同，Kotlin 中的数组是不可变的。
这意味着 Kotlin 不允许将 `Array<String>` 赋值给 `Array<Any>`，从而防止可能的运行时失败。
将子类数组作为超类数组传递给 Kotlin 方法也是禁止的，但对于 Java 方法，通过 `Array<(out) String>!` 形式的平台类型可以实现此操作。

在 Java 平台上，数组与基本数据类型一起使用，以避免装箱/拆箱操作的开销。
由于 Kotlin 隐藏了这些实现细节，因此需要一个解决方法来与 Java 代码交互。
对于每种基本数据类型，都有专门的数组类（如 `IntArray`、`DoubleArray`、`CharArray` 等）来处理这种情况。
这些类与 `Array` 类无关，并且会编译为 Java 的基本类型数组以获得最大性能。

假设有一个 Java 方法接受一个整数数组作为索引：

``` java
public class JavaArrayExample {
    public void removeIndices(int[] indices) {
        // 代码在这里...
    }
}
```

要在 Kotlin 中传递一个原始值数组，可以这样做：

```kotlin
val javaObj = JavaArrayExample()
val array = intArrayOf(0, 1, 2, 3)
javaObj.removeIndices(array)  // 将 int[] 传递给方法
```

在编译为 JVM 字节码时，编译器优化了对数组的访问，以确保没有引入额外的开销：

```kotlin
val array = arrayOf(1, 2, 3, 4)
array[1] = array[1] * 2 // 不会生成实际的 get() 和 set() 调用
for (x in array) { // 不会创建迭代器
    print(x)
}
```

即使使用索引进行导航，也不会引入额外的开销：

```kotlin
for (i in array.indices) { // 不会创建迭代器
    array[i] += 2
}
```

最后，`in` 检查也没有开销：

```kotlin
if (i in array.indices) { // 同于 (i >= 0 && i < array.size)
    print(array[i])
}
```

## Java 可变参数 {id=java-varargs}

Java 类有时会使用可变参数（varargs）的方法声明来接受多个索引：

``` java
public class JavaArrayExample {

    public void removeIndicesVarArg(int... indices) {
        // 代码在这里...
    }
}
```

在这种情况下，您需要使用扩展运算符 `*` 来传递 `IntArray`：

```kotlin
val javaObj = JavaArrayExample()
val array = intArrayOf(0, 1, 2, 3)
javaObj.removeIndicesVarArg(*array)
```

## 运算符 {id=operators}

由于 Java 没有标记哪些方法适合使用运算符语法，因此 Kotlin 允许将任何具有正确名称和签名的
Java 方法用作运算符重载以及其他约定（如 `invoke()` 等）。
但不允许使用中缀调用语法调用 Java 方法。

## 受检异常 {id=checked-exceptions}

在 Kotlin 中，所有 [异常都是未受检的](exceptions.md)，这意味着编译器不会强制您捕获任何异常。
因此，当您调用一个声明了受检异常的 Java 方法时，Kotlin 不会强制您执行任何操作：

```kotlin
fun render(list: List<*>, to: Appendable) {
    for (item in list) {
        to.append(item.toString()) // Java 会要求我们在这里捕获 IOException
    }
}
```

## 对象方法 {id=object-methods}

当 Java 类型被导入到 Kotlin 时，所有对 `java.lang.Object` 类型的引用都会被转换为 `Any`。
由于 `Any` 不是特定于平台的，它仅声明了 `toString()`、`hashCode()` 和 `equals()` 作为其成员。
因此，为了使 `java.lang.Object` 的其他成员可用，Kotlin 使用了 [扩展函数](extensions.md)。

### wait()/notify() {id=wait-notify}

方法 `wait()` 和 `notify()` 在 `Any` 类型的引用上不可用。
一般不建议使用它们，建议使用 `java.util.concurrent`。
如果您确实需要调用这些方法，可以将其强制转换为 `java.lang.Object`：

```kotlin
(foo as java.lang.Object).wait()
```

### getClass() {id=getclass}

要获取对象的 Java 类，可以使用 [类引用](reflection.md#class-references) 上的 `java` 扩展属性：

```kotlin
val fooClass = foo::class.java
```

上面的代码使用了 [绑定类引用](reflection.md#bound-class-references)。您还可以使用 `javaClass` 扩展属性：

```kotlin
val fooClass = foo.javaClass
```

### clone() {id=clone}

要重写 `clone()`，您的类需要扩展 `kotlin.Cloneable`：

```kotlin
class Example : Cloneable {
    override fun clone(): Any { ... }
}
```

别忘了参考 [Effective Java, 3rd Edition](https://www.oracle.com/technetwork/java/effectivejava-136174.html)，
第 13 条：*谨慎重写 clone*。

### finalize() {id=finalize}

要重写 `finalize()`，您只需声明它，而无需使用 `override` 关键字：

```kotlin
class C {
    protected fun finalize() {
        // 清理逻辑
    }
}
```

根据 Java 的规则，`finalize()` 不能是 `private`。

## 从 Java 类的继承 {id=inheritance-from-java-classes}

在 Kotlin 中，一个类最多只能有一个 Java 类（以及任意数量的 Java 接口）作为其超类型。

## 访问静态成员 {id=accessing-static-members}

Java 类的静态成员构成了这些类的 “伴生对象”。
你不能将这样的“伴生对象”作为值传递，但可以显式访问其成员，例如：

```kotlin
if (Character.isLetter(a)) { ... }
```

要访问映射到 Kotlin 类型的 Java 类型的静态成员，请使用 Java 类型的完全限定名：`java.lang.Integer.bitCount(foo)`。

## Java 反射 {id=java-reflection}

Java 反射可以用于 Kotlin 类，反之亦然。
如上所述，你可以使用 `instance::class.java`、`ClassName::class.java` 或 `instance.javaClass` 通过 `java.lang.Class`
进行 Java 反射。不要为此目的使用 `ClassName.javaClass`，因为它指的是 `ClassName` 的伴生对象类，这与
`ClassName.Companion::class.java` 相同，而不是 `ClassName::class.java`。

对于每种基本类型，Java 有两个不同的类，而 Kotlin 提供了获取这两者的方法。
例如，`Int::class.java` 将返回表示原始类型本身的类实例，对应于 Java 中的 `Integer.TYPE`。
要获取对应包装类型的类，请使用 `Int::class.javaObjectType`，它等同于 Java 的 `Integer.class`。

其他支持的情况包括获取 Kotlin 属性的 Java getter/setter 方法或幕后字段、Java 字段的
`KProperty`、`KFunction` 的 Java 方法或构造函数，反之亦然。


## SAM 转换 {id=sam-conversions}

Kotlin 支持对 Java 和 [Kotlin 接口](fun-interfaces.md) 的 SAM 转换。
这种对 Java 的支持意味着，Kotlin 函数字面量可以自动转换为实现
Java 接口的单一非默认方法，只要接口方法的参数类型与 Kotlin 函数的参数类型匹配。

你可以用它来创建 SAM 接口的实例：

```kotlin
val runnable = Runnable { println("This runs in a runnable") }
```

...并在方法调用中使用：

```kotlin
val executor = ThreadPoolExecutor()
// Java 签名：void execute(Runnable command)
executor.execute { println("This runs in a thread pool") }
```

如果 Java 类有多个接受函数接口的方法，你可以通过使用适配器函数将
lambda 转换为特定的 SAM 类型来选择你需要调用的方法。这些适配器函数在需要时也会由编译器生成：

```kotlin
executor.execute(Runnable { println("This runs in a thread pool") })
```

> SAM 转换仅适用于接口，不适用于抽象类，即使它们也只有一个抽象方法。
>
{style="note"}

## 在 Kotlin 中使用 JNI {id=using-jni-with-kotlin}

要声明一个在原生（C 或 C++）代码中实现的函数，你需要用 `external` 修饰符标记它：

```kotlin
external fun foo(x: Int): Double
```

其余的过程与 Java 中的方式完全相同。

你还可以将属性的 getter 和 setter 标记为 `external`：

```kotlin
var myProperty: String
    external get
    external set
```

在幕后，这将创建两个函数 `getMyProperty` 和 `setMyProperty`，并且都被标记为 `external`。

## 在 Kotlin 中使用 Lombok 生成的声明 {id=using-lombok-generated-declarations-in-kotlin}

你可以在 Kotlin 代码中使用 Java 的 Lombok 生成的声明。
如果你需要在同一个混合的 Java/Kotlin 模块中生成并使用这些声明，你可以在 [Lombok 编译器插件页面](lombok.md) 学习如何做到这一点。
如果你从另一个模块调用这些声明，则不需要使用此插件来编译该模块。
