[//]: # (title: 在 Kotlin 中使用 Java 记录类（records）)

_记录类_（_Records_）是 Java 中用来存储不可变数据的[类](https://openjdk.java.net/jeps/395)。
记录类包含一组固定的值 —— _记录组件_（_record components_）。
它们在 Java 中有简洁的语法，省去了大量样板代码：

```java
// Java
public record Person (String name, int age) {}
```

编译器会自动生成一个继承自
[`java.lang.Record`](https://docs.oracle.com/en/java/javase/16/docs/api/java.base/java/lang/Record.html)
的 **final** 类，包含以下成员：
* 每个记录组件对应的 **private final** 字段
* 一个包含所有字段参数的 **public** 构造函数
* 用于实现结构化相等的方法：`equals()`、`hashCode()`、`toString()`
* 每个记录组件对应的 **public** 读取方法

记录类和 Kotlin 的 [数据类](data-classes.md) 非常相似。

## 在 Kotlin 代码中使用 Java 记录类 {id=using-java-records-from-kotlin-code}

你可以像在 Kotlin 中使用属性类一样，使用 Java 中声明的记录类及其组件。
要访问记录组件，直接使用它的名字，就像访问 [Kotlin 属性](properties.md) 一样：

```kotlin
val newPerson = Person("Kotlin", 10)
val firstName = newPerson.name
```

## 在 Kotlin 中声明记录类 {id=declare-records-in-kotlin}

Kotlin 仅支持数据类声明为记录类，并且数据类必须满足 [要求](#requirements)。

要在 Kotlin 中声明一个记录类，可以使用 `@JvmRecord` 注解：

> 对现有类应用 `@JvmRecord` 并不是一个二进制兼容的修改，它会改变类属性访问器的命名约定。  
>
{style="note"}

```kotlin
@JvmRecord
data class Person(val name: String, val age: Int)
```

这个 JVM 特定的注解可以生成：

* 对应于类属性的记录组件，在类文件中生成
* 按照 Java 记录命名约定命名的属性访问方法

数据类还提供了 `equals()`、`hashCode()` 和 `toString()` 方法的实现。

### 使用要求 {id=requirements}

要使数据类能够使用 `@JvmRecord` 注解声明，必须满足以下条件：

* 该类必须位于目标为 JVM 16 字节码的模块中（如果启用了 `-Xjvm-enable-preview` 编译器选项，则可以使用 15）
* 该类不能显式继承任何其他类（包括 `Any`），因为所有 JVM 记录都隐式继承 `java.lang.Record`，但可以实现接口
* 该类不能声明任何具有幕后字段的属性（通过主构造函数参数初始化的属性除外）
* 该类不能声明任何具有幕后字段的可变属性
* 该类不能是局部类
* 该类的主构造函数可见性必须与类本身相同

### 启用 JVM 记录 {id=enabling-jvm-records}

JVM 记录要求生成的 JVM 字节码目标版本为 `16` 或更高。

要显式指定目标版本，可以在 [Gradle](gradle-compiler-options.md#attributes-specific-to-jvm) 或
[Maven](maven.md#attributes-specific-to-jvm) 中使用 `jvmTarget` 编译器选项。

## 深入讨论 {id=further-discussion}

有关更多技术细节和讨论，请参阅 [JVM 记录的语言提案](https://github.com/Kotlin/KEEP/blob/master/proposals/jvm-records.md)。
