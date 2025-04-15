[//]: # (title: 类)

<no-index/>

<tldr>
    <p><img src="icon-1-done.svg" width="20" alt="第一步" /> <a href="kotlin-tour-hello-world.md">Hello world</a><br />
        <img src="icon-2-done.svg" width="20" alt="第二步" /> <a href="kotlin-tour-basic-types.md">基本类型</a><br />
        <img src="icon-3-done.svg" width="20" alt="第三步" /> <a href="kotlin-tour-collections.md">集合</a><br />
        <img src="icon-4-done.svg" width="20" alt="第四步" /> <a href="kotlin-tour-control-flow.md">控制流</a><br />
        <img src="icon-5-done.svg" width="20" alt="第五步" /> <a href="kotlin-tour-functions.md">函数</a><br />
        <img src="icon-6.svg" width="20" alt="第六步" /> <strong>类</strong><br />
        <img src="icon-7-todo.svg" width="20" alt="最后一步" /> <a href="kotlin-tour-null-safety.md">空值安全</a></p>
</tldr>

Kotlin 支持面向对象编程，通过类和对象实现。对象对于在程序中存储数据很有用。  
类允许你为对象声明一组特征。
当你从一个类创建对象时，你可以节省时间和精力，因为你不需要每次都声明这些特征。

要声明一个类，请使用 `class` 关键字：

```kotlin
class Customer
```

## 属性 {id=properties}

类的对象特征可以通过属性来声明。你可以为类声明属性：

* 在类名后的括号 `()` 内声明。
```kotlin
class Contact(val id: Int, var email: String)
```

* 在由花括号 `{}` 定义的类体内声明。
```kotlin
class Contact(val id: Int, var email: String) {
    val category: String = ""
}
```

我们建议你将属性声明为只读 (`val`)，除非在类的实例创建后需要修改它们。

你可以在括号内声明不带 `val` 或 `var` 的属性，但这些属性在实例创建后无法访问。

> * 括号 `()` 内的内容被称为 **类头**。
> * 声明类属性时，你可以使用 [尾随逗号](coding-conventions.md#trailing-commas)。
>
{style="note"}

与函数参数类似，类属性也可以有默认值：
```kotlin
class Contact(val id: Int, var email: String = "example@gmail.com") {
    val category: String = "work"
}
```

## 创建实例 {id=create-instance}

要从一个类创建对象，你需要使用 **构造函数** 声明类的 **实例**。

默认情况下，Kotlin 会自动创建一个包含类头中声明的参数的构造函数。

例如：
```kotlin
class Contact(val id: Int, var email: String)

fun main() {
    val contact = Contact(1, "mary@gmail.com")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-class-create-instance"}

在这个例子中：

* `Contact` 是一个类。
* `contact` 是 `Contact` 类的一个实例。
* `id` 和 `email` 是属性。
* `id` 和 `email` 被默认构造函数用来创建 `contact`。

Kotlin 类可以有多个构造函数，包括你自己定义的构造函数。
要了解更多关于如何声明多个构造函数的信息，请参见 [构造函数](classes.md#constructors)。

## 访问属性 {id=access-properties}

要访问实例的属性，可以在实例名称后加上句点 `.`，然后写上属性名称：

```kotlin
class Contact(val id: Int, var email: String)

fun main() {
    val contact = Contact(1, "mary@gmail.com")
    
    // 打印属性 email 的值
    println(contact.email)           
    // mary@gmail.com

    // 更新属性 email 的值
    contact.email = "jane@gmail.com"
    
    // 打印属性 email 的新值
    println(contact.email)           
    // jane@gmail.com
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-access-property"}

> 要将属性的值作为字符串的一部分进行拼接，可以使用字符串模板 (`$`)。
> 例如：
> ```kotlin
> println("他们的电子邮件地址是：${contact.email}")
> ```
>
{style="tip"}

## 成员函数 {id=member-functions}

除了将属性声明为对象的特征的一部分外，你还可以通过成员函数定义对象的行为。

在 Kotlin 中，成员函数必须在类体内声明。
要在实例上调用成员函数，可以在实例名称后加上句点 `.`，然后写上函数名称。例如：

```kotlin
class Contact(val id: Int, var email: String) {
    fun printId() {
        println(id)
    }
}

fun main() {
    val contact = Contact(1, "mary@gmail.com")
    // 调用成员函数 printId()
    contact.printId()           
    // 1
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-member-function"}

## 数据类 {id=data-classes}

Kotlin 提供了 **数据类**，它们在存储数据时特别有用。
数据类具有与普通类相同的功能，但它们自动包含额外的成员函数。
这些成员函数允许你轻松地将实例打印为可读的输出，比较类的实例，复制实例等。
由于这些函数是自动可用的，你不需要为每个类编写相同的模板代码。

要声明数据类，请使用关键字 `data`：

```kotlin
data class User(val name: String, val id: Int)
```

数据类最有用的预定义成员函数有：

| **函数**            | **描述**                        |
|-------------------|-------------------------------|
| `toString()`      | 打印类实例及其属性的可读字符串。              |
| `equals()` 或 `==` | 比较类的实例。                       |
| `copy()`          | 通过复制另一个实例创建类的实例，可以选择性地修改某些属性。 |

参见以下章节以了解如何使用每个函数：

* [打印为字符串](#print-as-string)
* [比较实例](#compare-instances)
* [复制实例](#copy-instance)

### 打印为字符串 {id=print-as-string}

要打印类实例的可读字符串，你可以显式调用 `toString()` 函数，或者使用打印函数
（`println()` 和 `print()`），它们会自动为你调用 `toString()`：

```kotlin
data class User(val name: String, val id: Int)

fun main() {
    //sampleStart
    val user = User("Alex", 1)
    
    // 自动调用 toString() 函数，使输出易于阅读
    println(user)            
    // User(name=Alex, id=1)
    //sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-data-classes-print-string"}

这在调试或创建日志时特别有用。

### 比较实例 {id=compare-instances}

要比较数据类的实例，可以使用等号运算符 `==`：

```kotlin
data class User(val name: String, val id: Int)

fun main() {
    //sampleStart
    val user = User("Alex", 1)
    val secondUser = User("Alex", 1)
    val thirdUser = User("Max", 2)

    // 比较 user 和 secondUser
    println("user == secondUser: ${user == secondUser}") 
    // user == secondUser: true
    
    // 比较 user 和 thirdUser
    println("user == thirdUser: ${user == thirdUser}")   
    // user == thirdUser: false
    //sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-data-classes-compare-instances"}

### 复制实例 {id=copy-instance}

要创建数据类实例的精确副本，可以调用实例的 `copy()` 函数。

要创建数据类实例的副本 **并** 更改某些属性，可以调用实例的 `copy()` 函数
**并** 将替换的属性值作为函数参数传入。

例如：

```kotlin
data class User(val name: String, val id: Int)

fun main() {
    //sampleStart
    val user = User("Alex", 1)

    // 创建 user 的精确副本
    println(user.copy())       
    // User(name=Alex, id=1)

    // 创建一个 name 为 "Max" 的 user 副本
    println(user.copy("Max"))  
    // User(name=Max, id=1)

    // 创建一个 id 为 3 的 user 副本
    println(user.copy(id = 3)) 
    // User(name=Alex, id=3)
    //sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-data-classes-copy-instance"}

创建实例的副本比修改原始实例更安全，因为依赖于原始实例的代码不会受到副本及其后续操作的影响。

有关数据类的更多信息，请参见 [数据类](data-classes.md)。

本次教程的最后一章是关于 Kotlin 的 [空值安全](kotlin-tour-null-safety.md)。

## 实践

### 练习 1 {collapsible="true" collapsible="true"}

定义一个数据类 `Employee`，具有两个属性：一个是姓名，另一个是工资。
确保工资属性是可变的，否则你在年底时将无法获得工资提升！
主函数演示了如何使用这个数据类。

|---|---|
```kotlin
// 在这里写下你的代码

fun main() {
    val emp = Employee("Mary", 20)
    println(emp)
    emp.salary += 10
    println(emp)
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-classes-exercise-1"}

|---|---|
```kotlin
data class Employee(val name: String, var salary: Int)

fun main() {
    val emp = Employee("Mary", 20)
    println(emp)
    emp.salary += 10
    println(emp)
}
```
{collapsible="true" collapsible="true" collapsed-title="示例解答" id="kotlin-tour-classes-solution-1"}

### 练习 2 {collapsible="true" collapsible="true"}

声明该代码编译所需的额外数据类。

|---|---|
```kotlin
data class Person(val name: Name, val address: Address, val ownsAPet: Boolean = true)
// 在这里写下你的代码
// data class Name(...)

fun main() {
    val person = Person(
        Name("John", "Smith"),
        Address("123 Fake Street", City("Springfield", "US")),
        ownsAPet = false
    )
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-classes-exercise-2"}

|---|---|
```kotlin
data class Person(val name: Name, val address: Address, val ownsAPet: Boolean = true)
data class Name(val first: String, val last: String)
data class Address(val street: String, val city: City)
data class City(val name: String, val countryCode: String)

fun main() {
    val person = Person(
        Name("John", "Smith"),
        Address("123 Fake Street", City("Springfield", "US")),
        ownsAPet = false
    )
}
```
{collapsible="true" collapsible="true" collapsed-title="示例解答" id="kotlin-tour-classes-solution-2"}

### 练习 3 {collapsible="true" collapsible="true"}

为了测试你的代码，你需要一个生成器来创建随机员工。
定义一个 `RandomEmployeeGenerator` 类，该类内部有一个固定的潜在姓名列表。
在类头部配置最小和最大工资。在类体内，定义 `generateEmployee()` 函数。
主函数再次演示了如何使用这个类。

> 在这个练习中，您导入一个包，以便可以使用 [`Random.nextInt()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.random/-random/next-int.html) 函数。
> 有关导入包的更多信息，请参见 [包和导入](packages.md)。
>
{style = "tip"}

<deflist collapsible="true" id="kotlin-tour-classes-exercise-3-hint-1">
    <def title="提示 1">
        列表有一个扩展函数叫做 <a href="https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/random.html"><code>.random()</code></a> ，
        它会返回列表中的一个随机项。
    </def>
</deflist>

<deflist collapsible="true" id="kotlin-tour-classes-exercise-3-hint-2">
    <def title="提示 2">
        `<code>Random.nextInt(from = ..., until = ...)</code>` 给你一个在指定范围内的随机 `<code>Int</code>` 数字。
    </def>
</deflist>

|---|---|
```kotlin
import kotlin.random.Random

data class Employee(val name: String, var salary: Int)

// 在这里写下你的代码

fun main() {
    val empGen = RandomEmployeeGenerator(10, 30)
    println(empGen.generateEmployee())
    println(empGen.generateEmployee())
    println(empGen.generateEmployee())
    empGen.minSalary = 50
    empGen.maxSalary = 100
    println(empGen.generateEmployee())
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-classes-exercise-3"}

|---|---|
```kotlin
import kotlin.random.Random

data class Employee(val name: String, var salary: Int)

class RandomEmployeeGenerator(var minSalary: Int, var maxSalary: Int) {
    val names = listOf("John", "Mary", "Ann", "Paul", "Jack", "Elizabeth")
    fun generateEmployee() =
        Employee(names.random(),
            Random.nextInt(from = minSalary, until = maxSalary))
}

fun main() {
    val empGen = RandomEmployeeGenerator(10, 30)
    println(empGen.generateEmployee())
    println(empGen.generateEmployee())
    println(empGen.generateEmployee())
    empGen.minSalary = 50
    empGen.maxSalary = 100
    println(empGen.generateEmployee())
}
```
{collapsible="true" collapsible="true" collapsed-title="示例解答" id="kotlin-tour-classes-solution-3"}

## 下一步

[空值安全](kotlin-tour-null-safety.md)
