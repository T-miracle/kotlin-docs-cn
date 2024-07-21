[//]: # (title: 数据类)

在 Kotlin 中，数据类的主要目的是用于保存数据。
数据类会自动生成额外的成员函数，使得你可以将实例输出为可读的格式、比较实例、复制实例等等。
要声明数据类，你可以使用 `data` 关键字标记：

```kotlin
data class User(val name: String, val age: Int)
```

编译器会自动根据主构造函数中声明的属性生成以下成员：

* `.equals()` 和 `.hashCode()` 方法，用于比较对象是否相等。
* `.toString()` 方法，生成类似于 `"User(name=John, age=42)"` 的字符串表示。
* [`.componentN()` 函数](destructuring-declarations.md)，与属性声明的顺序一一对应。
* `.copy()` 方法，用于创建对象的副本（详见下文）。

为了确保生成的代码一致并且具有有意义的行为，数据类必须符合以下规定：

* 主构造函数必须至少有一个参数。
* 所有主构造函数的参数必须标记为 `val` 或 `var`。
* 数据类不能是抽象的、开放的、封闭的 或 内部的。

此外，关于数据类成员的生成，遵循以下继承规则：

* 如果在数据类体内有明确实现 `.equals()`、`.hashCode()` 或 `.toString()`，或者在超类中有 `final` 实现，系统将不会自动生成这些函数，而是沿用已有的实现。
* 如果超类型具有 `open` 且返回兼容类型的 `.componentN()` 函数，那么将为数据类生成相应的函数，并覆盖超类型的函数。
  如果由于不兼容的签名或它们是最终实现而无法覆盖超类型的函数，则会报告错误。
* 请勿为 `.componentN()` 和 `.copy()` 函数提供显式实现，这是不允许的。

数据类可以扩展其他类（参见[密封类](sealed-classes.md)以获取示例）。

> 在JVM上，如果生成的类需要有一个无参构造函数，就必须为属性指定默认值（参见[构造函数](classes.md#构造函数)）。
>
{style="note"}

```kotlin
data class User(val name: String = "", val age: Int = 0)
```

## 在类体中声明的属性

编译器只利用主构造函数内定义的属性来自动生成函数。若要排除某个属性不纳入生成的实现中，请将其声明在类体内：

```kotlin
data class Person(val name: String) {
    var age: Int = 0
}
```

在这个例子中，只有 `name` 属性会在 `.toString()`、`.equals()`、`.hashCode()` 和 `.copy()` 的实现中被使用，同时只有一个组件函数 `.component1()`。

相反，`age` 属性不能在 `.toString()`、`.equals()`、`.hashCode()` 和 `.copy()` 的实现中使用，因为它在类体内被声明。

如果两个 `Person` 对象具有不同的年龄但相同的 `name`，它们将被视为相等。这是因为 `.equals()` 函数只检查 `name` 属性的相等性。例如：

```kotlin
data class Person(val name: String) {
    var age: Int = 0
}
fun main() {
//sampleStart
    val person1 = Person("John")
    val person2 = Person("John")
    person1.age = 10
    person2.age = 20

    println("person1 == person2: ${person1 == person2}")
    // person1 == person2: true
  
    println("person1 with age ${person1.age}: ${person1}")
    // person1 with age 10: Person(name=John)
  
    println("person2 with age ${person2.age}: ${person2}")
    // person2 with age 20: Person(name=John)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

## 复制

使用 `.copy()` 函数可以复制一个对象，并允许你改变**一些**属性，同时保持其余属性不变。对于上述的 `User` 类，该函数的实现如下：

```kotlin
fun copy(name: String = this.name, age: Int = this.age) = User(name, age)
```

之后，你可以这样写：

```kotlin
val jack = User(name = "Jack", age = 1)
val olderJack = jack.copy(age = 2)
```

## 数据类和解构声明

为数据类生成的 **组件函数** 使它们可以在 [解构声明](destructuring-declarations.md) 中使用：

```kotlin
val jane = User("Jane", 35)
val (name, age) = jane
println("$name, $age years of age") 
// Jane, 35 years of age
```

## 标准数据类

标准库提供了 `Pair` 和 `Triple` 类。然而，在大多数情况下，命名的数据类是更好的设计选择，因为它们通过为属性提供有意义的名称使代码更易读。

