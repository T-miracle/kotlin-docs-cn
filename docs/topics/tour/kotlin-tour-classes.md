[//]: # (title: 类)

<tldr>
    <p><img src="icon-1-done.svg" width="20" alt="First step" /> <a href="kotlin-tour-hello-world.md">Hello world</a><br />
        <img src="icon-2-done.svg" width="20" alt="Second step" /> <a href="kotlin-tour-basic-types.md">基本类型</a><br />
        <img src="icon-3-done.svg" width="20" alt="Third step" /> <a href="kotlin-tour-collections.md">集合</a><br />
        <img src="icon-4-done.svg" width="20" alt="Fourth step" /> <a href="kotlin-tour-control-flow.md">控制流</a><br />
        <img src="icon-5-done.svg" width="20" alt="Fifth step" /> <a href="kotlin-tour-functions.md">函数</a><br />
        <img src="icon-6.svg" width="20" alt="Sixth step" /> <strong>类</strong><br />
        <img src="icon-7-todo.svg" width="20" alt="Final step" /> <a href="kotlin-tour-null-safety.md">空值安全</a></p>
</tldr>

Kotlin 支持面向对象编程，使用类和对象。
对象对于在程序中存储数据非常有用。
类允许你声明对象的一组特征。
当你从一个类创建对象时，你可以节省时间和精力，因为你不必每次都声明这些特征。

要声明一个类，使用 `class` 关键字： 

```kotlin
class Customer
```

## 属性

类的对象的特征可以在属性中声明。你可以为一个类声明属性：
* 在类名后的括号 `()` 中。
```kotlin
class Contact(val id: Int, var email: String)
```
* 在由花括号 `{}` 定义的类体中。
```kotlin
class Contact(val id: Int, var email: String) {
    val category: String = ""
}
```

我们建议，除非在创建类的实例后需要更改它们，否则将属性声明为只读 (`val`)。

你可以在括号内声明没有 `val` 或 `var` 的属性，但是这些属性在实例创建后是不可访问的。

> * 括号 `()` 中包含的内容称为**类头**。
> * 在声明类属性时，你可以使用 [尾随逗号](coding-conventions.md#尾随逗号)。
>
{style="note"}

就像函数参数一样，类属性可以有默认值：
```kotlin
class Contact(val id: Int, var email: String = "example@gmail.com") {
    val category: String = "work"
}
```

## 创建实例

要从类创建对象，你使用一个**构造函数**声明一个类的**实例**。

默认情况下，Kotlin 会自动创建一个构造函数，该构造函数使用在类头中声明的参数。

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
* 使用默认构造函数，使用 `id` 和 `email` 创建了 `contact`。

Kotlin 类可以有许多构造函数，包括你自己定义的构造函数。要了解如何声明多个构造函数的更多信息，请参见[构造函数](classes.md#constructors)。

## 访问属性

要访问实例的属性，请在实例名称后写上属性名称，用句点 `.` 连接：

```kotlin
class Contact(val id: Int, var email: String)

fun main() {
    val contact = Contact(1, "mary@gmail.com")
    
    // 打印属性的值: email
    println(contact.email)           
    // mary@gmail.com

    // 更新属性的值: email
    contact.email = "jane@gmail.com"
    
    // 打印属性的新值: email
    println(contact.email)           
    // jane@gmail.com
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-access-property"}

> 要将属性的值作为字符串的一部分连接起来，你可以使用字符串模板（`$`）。
> 例如：
> ```kotlin
> println("Their email address is: ${contact.email}")
> ```
>
{style="tip"}

## 成员函数

除了将属性声明为对象特征的一部分之外，你还可以使用成员函数定义对象的行为。

在 Kotlin 中，成员函数必须在类体内声明。要在实例上调用成员函数，请在实例名称后写上函数名称，用句点 `.` 连接。例如：

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

## 数据类

Kotlin 有**数据类**，对于存储数据非常有用。
数据类具有与普通类相同的功能，但它们自动带有额外的成员函数。
这些成员函数允许你轻松地将实例打印为可读输出，比较类的实例，复制实例等。
由于这些函数是自动可用的，你不必为每个类编写相同的样板代码。

要声明一个数据类，使用关键字 `data`：
```kotlin
data class User(val name: String, val id: Int)
```

数据类最有用的预定义成员函数是：

| **函数**              | **描述**                          |
|---------------------|---------------------------------|
| `.toString()`       | 打印类实例及其属性的可读字符串。                |
| `.equals()` or `==` | 比较类的实例。                         |
| `.copy()`           | 通过复制另一个类实例来创建一个类实例，可能具有一些不同的属性。 |

有关如何使用每个函数的示例，请参阅以下部分：
* [打印为字符串](#打印为字符串)
* [比较实例](#比较实例)
* [复制实例](#复制实例)

### 打印为字符串 {id=打印为字符串}

要打印一个类实例的可读字符串，你可以显式调用 `.toString()` 函数，
或使用自动为你调用 `.toString()` 的打印函数（`println()` 和 `print()`）：

```kotlin
data class User(val name: String, val id: Int)

fun main() {
    //sampleStart
    val user = User("Alex", 1)
    
    // 自动使用 toString() 函数，以便输出易于阅读
    println(user)            
    // User(name=Alex, id=1)
    //sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-data-classes-print-string"}

这在调试或创建日志时特别有用。

### 比较实例 {id=比较实例}

要比较数据类实例，使用相等运算符 `==`：

```kotlin
data class User(val name: String, val id: Int)

fun main() {
    //sampleStart
    val user = User("Alex", 1)
    val secondUser = User("Alex", 1)
    val thirdUser = User("Max", 2)

    // 将用户与第二个用户进行比较
    println("user == secondUser: ${user == secondUser}") 
    // user == secondUser: true
    
    // 将用户与第三个用户进行比较
    println("user == thirdUser: ${user == thirdUser}")   
    // user == thirdUser: false
    //sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-data-classes-compare-instances"}

### 复制实例 {id=复制实例}

要创建数据类实例的精确副本，请在实例上调用 `.copy()` 函数。

要创建数据类实例的副本**并**更改某些属性，请在实例上调用 `.copy()` 函数**并**将替换值作为函数参数添加。

例如：

```kotlin
data class User(val name: String, val id: Int)

fun main() {
    //sampleStart
    val user = User("Alex", 1)
    val secondUser = User("Alex", 1)
    val thirdUser = User("Max", 2)

    // 创建用户的精确副本
    println(user.copy())       
    // User(name=Alex, id=1)

    // 创建名为“Max”的用户副本
    println(user.copy("Max"))  
    // User(name=Max, id=1)

    // 创建 id 为 3 的用户的副本
    println(user.copy(id = 3)) 
    // User(name=Alex, id=3)
    //sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-data-classes-copy-instance"}

创建实例的副本比修改原始实例更安全，因为依赖于原始实例的任何代码都不会受到副本及其操作的影响。

有关数据类的更多信息，请参见[数据类](data-classes.md)。

本教程的最后一章是关于 Kotlin 的[空值安全](kotlin-tour-null-safety.md)。

## 练习

### 练习 1 {collapsible="true"}

定义一个数据类 `Employee`，包含两个属性：一个用于姓名，另一个用于薪水。
确保薪水属性是可变的，否则你在年底将无法获得薪水提升！
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
{collapsible="true" collapsed-title="Example solution"}

### 练习 2 {collapsible="true"}

为了测试你的代码，你需要一个能够创建随机员工的生成器。
定义一个类，其中包含一个固定的潜在姓名列表（在类体内），并由最小和最大薪水配置（在类头部）。
再次强调，主函数演示了如何使用这个类。

<deflist collapsible="true" id="kotlin-tour-classes-exercise-2-hint-1">
    <def title="提示">
        列表具有一个名为 `<a href="https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/random.html"><code>.random()</code></a>` 的扩展函数，
        该函数返回列表中的一个随机项。
    </def>
</deflist>

<deflist collapsible="true" id="kotlin-tour-classes-exercise-2-hint-2">
    <def title="提示">
        <code>Random.nextInt(from = ..., until = ...)</code> 给你一个在指定区间内的随机 <code>Int</code> 数字。
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
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-classes-exercise-2"}

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
{collapsible="true" collapsed-title="Example solution"}

## 下一步

[空值安全](kotlin-tour-null-safety.md)
