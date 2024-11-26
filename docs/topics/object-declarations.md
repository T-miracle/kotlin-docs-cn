[//]: # (title: 对象声明和表达式)

[//]: # (TODO 待校对)

在 Kotlin 中，`object` 允许你在一步中定义一个类并创建它的实例。
这在你需要一个可重用的单例实例或一次性的对象时非常有用。
为了处理这些场景，Kotlin 提供了两种关键方法：_对象声明_ 用于创建单例和 _对象表达式_ 用于创建匿名的一次性对象。

> 单例确保一个类只有一个实例，并提供一个全局访问点。

{style="tip"}

对象声明和对象表达式最适用于以下场景：

* **使用单例来管理共享资源：** 你需要确保类在整个应用中只有一个实例。例如，管理数据库连接池。
* **创建工厂方法：** 你需要一种方便的方式来高效创建实例。  
  [伴生对象](#companion-objects) 允许你定义与类相关的类级函数和属性，简化这些实例的创建和管理。
* **临时修改现有类的行为：** 你希望修改现有类的行为，而无需创建新的子类。例如，为特定操作向对象添加临时功能。
* **需要类型安全设计：** 你需要通过对象表达式实现接口或 [抽象类](classes.md#abstract-classes) 的一次性实现。
* 这在像按钮点击处理器这样的场景中非常有用。

## 对象声明
{id="object-declarations-overview"}

你可以通过对象声明在 Kotlin 中创建单一实例，声明总是跟随 `object` 关键字并有一个名称。
这使得你能够在一步中定义一个类并创建它的实例，这在实现单例时非常有用：

```kotlin
//sampleStart
// 声明一个单例对象来管理数据提供者
object DataProviderManager {
    private val providers = mutableListOf<DataProvider>()

    // 注册新的数据提供者
    fun registerDataProvider(provider: DataProvider) {
        providers.add(provider)
    }

    // 获取所有注册的数据提供者
    val allDataProviders: Collection<DataProvider> 
        get() = providers
}
//sampleEnd

// 示例数据提供者接口
interface DataProvider {
    fun provideData(): String
}

// 示例数据提供者实现
class ExampleDataProvider : DataProvider {
    override fun provideData(): String {
        return "Example data"
    }
}

fun main() {
    // 创建 ExampleDataProvider 的实例
    val exampleProvider = ExampleDataProvider()

    // 直接使用对象名称来引用该对象
    DataProviderManager.registerDataProvider(exampleProvider)

    // 获取并打印所有数据提供者
    println(DataProviderManager.allDataProviders.map { it.provideData() })
    // [Example data]
}
```

{kotlin-runnable="true" id="object-declaration-register-provider"}

> 对象声明的初始化是线程安全的，并且在首次访问时进行初始化。  
{style="tip"}

要引用 `object`，可以直接使用它的名称：

```kotlin
DataProviderManager.registerDataProvider(exampleProvider)
```

对象声明也可以有父类，类似于 [匿名对象可以继承现有类或实现接口](#inherit-anonymous-objects-from-supertypes)：

```kotlin
object DefaultListener : MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) { ... }

    override fun mouseEntered(e: MouseEvent) { ... }
}
```

与变量声明类似，对象声明不是表达式，因此不能用于赋值语句的右侧：

```kotlin
// 语法错误：对象表达式不能绑定名称。
val myObject = object MySingleton {
    val name = "Singleton"
}
```

对象声明不能是局部的，这意味着它们不能直接嵌套在函数内部。  
然而，它们可以嵌套在其他对象声明或非内部类中。

### 数据对象 {id="data-objects"}

在 Kotlin 中，打印一个普通对象声明时，其字符串表示包含对象的名称和 `object` 的哈希值：

```kotlin
object MyObject

fun main() {
    println(MyObject) 
    // MyObject@hashcode
}
```
{kotlin-runnable="true" id="object-declaration-plain"}

然而，通过使用 `data` 修饰符标记对象声明，你可以指示编译器在调用 `toString()`
时返回对象的实际名称，这与 [数据类](data-classes.md) 的行为相同：

```kotlin
data object MyDataObject {
    val number: Int = 3
}

fun main() {
    println(MyDataObject) 
    // MyDataObject
}
```
{kotlin-runnable="true" id="object-declaration-dataobject"}

此外，编译器会为你的 `data object` 生成几个函数：

* `toString()` 返回数据对象的名称
* `equals()`/`hashCode()` 支持平等性检查和基于哈希的集合

  > 你不能为 `data object` 提供自定义的 `equals` 或 `hashCode` 实现。
  >
  {style="note"}

`data object` 的 `equals()` 函数确保所有类型为 `data object` 的对象都被视为相等。  
在大多数情况下，你的 `data object` 在运行时只有一个实例，因为 `data object` 声明了一个单例。
然而，在某些极端情况下，如果在运行时生成了另一个相同类型的对象（例如，通过使用平台反射 `java.lang.reflect` 或一个在底层使用该
API 的 JVM 序列化库），`equals()` 函数确保这些对象被视为相等。

> 确保你只对 `data objects` 进行结构性比较（使用 `==` 运算符），而不要通过引用进行比较（使用 `===` 运算符）。  
> 这可以帮助你避免在运行时存在多个 `data object` 实例时的陷阱。
>
{style="warning"}

```kotlin
import java.lang.reflect.Constructor

data object MySingleton

fun main() {
    val evilTwin = createInstanceViaReflection()

    println(MySingleton) 
    // MySingleton

    println(evilTwin) 
    // MySingleton

    // 即使库强制创建了 MySingleton 的第二个实例， 
    // 它的 equals() 函数仍然返回 true：
    println(MySingleton == evilTwin) 
    // true

    // 不要使用 === 比较 data objects
    println(MySingleton === evilTwin) 
    // false
}

fun createInstanceViaReflection(): MySingleton {
    // Kotlin 反射不允许实例化 data objects。
    // 这通过 Java 平台反射“强制”创建了一个新的 MySingleton 实例。
    // 切勿自己这样做！
    return (MySingleton.javaClass.declaredConstructors[0].apply { isAccessible = true } as Constructor<MySingleton>).newInstance()
}
```

生成的 `hashCode()` 函数与 `equals()` 函数的行为一致，因此所有运行时的 `data object` 实例具有相同的哈希值。

#### 数据对象与数据类的区别 {id="differences-between-data-objects-and-data-classes"}

虽然 `data object` 和 `data class` 声明常常一起使用，并且有一些相似之处，但 `data object` 并不会生成一些特定的函数：

* 没有 `copy()` 函数。由于 `data object` 声明旨在用作单例，因此不会生成 `copy()` 函数。
  单例限制了类的实例化为唯一一个实例，允许创建实例的副本会违反这一限制。
* 没有 `componentN()` 函数。与 `data class` 不同，`data object` 没有任何数据属性。
  由于试图在没有数据属性的情况下对这样的对象进行解构是没有意义的，因此不会生成 `componentN()` 函数。

#### 在密封层级中使用数据对象 {id="use-data-objects-with-sealed-hierarchies"}

数据对象声明对于密封层级（如 [密封类或密封接口](sealed-classes.md)）特别有用。  
它们允许你与可能与对象一起定义的任何数据类保持对称性。

在这个例子中，将 `EndOfFile` 声明为 `data object` 而不是普通的 `object`，意味着它将自动获得 `toString()` 函数，而不需要手动重写：

```kotlin
sealed interface ReadResult
data class Number(val number: Int) : ReadResult
data class Text(val text: String) : ReadResult
data object EndOfFile : ReadResult

fun main() {
    println(Number(7)) 
    // Number(number=7)
    println(EndOfFile) 
    // EndOfFile
}
```
{kotlin-runnable="true" id="data-objects-sealed-hierarchies"}

### 伴生对象 {id="companion-objects"}

_伴生对象_ 允许你定义类级别的函数和属性。  
这使得创建工厂方法、保存常量以及访问共享工具变得容易。

类内部的对象声明可以使用 `companion` 关键字标记：

```kotlin
class MyClass {
    companion object Factory {
        fun create(): MyClass = MyClass()
    }
}
```

`companion object` 的成员可以通过类名作为限定符直接调用：

```kotlin
class User(val name: String) {
    // 定义一个伴生对象，充当创建 User 实例的工厂
    companion object Factory {
        fun create(name: String): User = User(name)
    }
}

fun main(){
    // 使用类名作为限定符调用伴生对象的工厂方法，创建一个新的 User 实例
    val userInstance = User.create("John Doe")
    println(userInstance.name)
    // John Doe
}
```
{kotlin-runnable="true" id="object-expression-companion-object"}

`companion object` 的名称可以省略，在这种情况下，默认使用名称 `Companion`：

```kotlin
class User(val name: String) {
    // 定义一个没有名称的伴生对象
    companion object { }
}

// 访问伴生对象
val companionUser = User.Companion
```

类成员可以访问其对应 `companion object` 中的 `private` 成员：

```kotlin
class User(val name: String) {
    companion object {
        private val defaultGreeting = "Hello"
    }

    fun sayHi() {
        println(defaultGreeting)
    }
}
User("Nick").sayHi()
// Hello
```

当类名单独使用时，它会作为该类伴生对象的引用，不管伴生对象是否有名称：

```kotlin
//sampleStart
class User1 {
    // 定义一个命名的伴生对象
    companion object Named {
        fun show(): String = "User1's Named Companion Object"
    }
}

// 使用类名引用 User1 的伴生对象
val reference1 = User1

class User2 {
    // 定义一个没有名称的伴生对象
    companion object {
        fun show(): String = "User2's Companion Object"
    }
}

// 使用类名引用 User2 的伴生对象
val reference2 = User2
//sampleEnd

fun main() {
    // 调用 User1 的伴生对象中的 show() 函数
    println(reference1.show()) 
    // User1's Named Companion Object

    // 调用 User2 的伴生对象中的 show() 函数
    println(reference2.show()) 
    // User2's Companion Object
}
```
{kotlin-runnable="true" id="object-expression-companion-object-names"}

尽管 Kotlin 中伴生对象的成员看起来像其他语言中的静态成员，但它们实际上是伴生对象的实例成员，意味着它们属于该对象本身。
这允许伴生对象实现接口：

```kotlin
interface Factory<T> {
    fun create(name: String): T
}

class User(val name: String) {
    // 定义一个实现 Factory 接口的伴生对象
    companion object : Factory<User> {
        override fun create(name: String): User = User(name)
    }
}

fun main() {
    // 将伴生对象作为工厂使用
    val userFactory: Factory<User> = User
    val newUser = userFactory.create("Example User")
    println(newUser.name)
    // Example User
}
```
{kotlin-runnable="true" id="object-expression-factory"}

然而，在 JVM 上，如果你使用 `@JvmStatic` 注解，伴生对象的成员可以作为真正的静态方法和字段生成。
详情请参见 [Java 互操作性](java-to-kotlin-interop.md#static-fields) 部分。


## 对象表达式 {id="object-expressions"}

对象表达式声明一个类并创建该类的实例，但不为它们命名。
这些类适用于一次性使用。它们可以从头开始创建，继承现有类，或者实现接口。
这些类的实例也被称为 _匿名对象_，因为它们是通过表达式定义的，而不是通过名称定义的。

### 从头创建匿名对象 {id="create-anonymous-objects-from-scratch"}

对象表达式以 `object` 关键字开始。

如果对象没有继承任何类或实现接口，可以直接在 `object` 关键字后面的花括号内定义对象的成员：

```kotlin
fun main() {
//sampleStart
    val helloWorld = object {
        val hello = "Hello"
        val world = "World"
        // 对象表达式扩展了 Any 类，Any 类已经有一个 toString() 函数，
        // 所以必须重写
        override fun toString() = "$hello $world"
    }

    print(helloWorld)
    // Hello World
//sampleEnd
}
```
{kotlin-runnable="true" id="object-expression-object"}

### 从超类继承匿名对象 {id="inherit-anonymous-objects-from-supertypes"}

要创建一个继承自某个类型（或多个类型）的匿名对象，在 `object` 和冒号 `:` 后面指定该类型。
然后像从该类 [继承](inheritance.md) 一样实现或重写该类的成员：

```kotlin
window.addMouseListener(object : MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) { /*...*/ }

    override fun mouseEntered(e: MouseEvent) { /*...*/ }
})
```

如果超类有构造函数，则需要传递适当的构造函数参数。多个超类可以在冒号后面通过逗号分隔指定：

```kotlin
//sampleStart
// 创建一个带有余额属性的开放类 BankAccount
open class BankAccount(initialBalance: Int) {
    open val balance: Int = initialBalance
}

// 定义一个接口 Transaction，包含一个 execute() 函数
interface Transaction {
    fun execute()
}

// 执行特殊交易的函数
fun specialTransaction(account: BankAccount) {
    // 创建一个匿名对象，继承自 BankAccount 类并实现 Transaction 接口
    // 提供的 account 的余额会传递给 BankAccount 超类的构造函数
    val temporaryAccount = object : BankAccount(account.balance), Transaction {

        override val balance = account.balance + 500  // 临时奖金

        // 实现 Transaction 接口中的 execute() 函数
        override fun execute() {
            println("Executing special transaction. New balance is $balance.")
        }
    }
    // 执行交易
    temporaryAccount.execute()
}
//sampleEnd
fun main() {
    // 创建一个初始余额为 1000 的 BankAccount
    val myAccount = BankAccount(1000)
    // 对创建的账户执行特殊交易
    specialTransaction(myAccount)
    // Executing special transaction. New balance is 1500.
}
```
{kotlin-runnable="true" id="object-expression-anonymous-object"}

### 将匿名对象用作返回值和类型 {id="use-anonymous-objects-as-return-and-value-types"}

当你从局部函数或 [`private`](visibility-modifiers.md#packages) 函数或属性返回匿名对象时（但不是
[inline 函数](inline-functions.md)），该匿名对象的所有成员都可以通过该函数或属性访问：

```kotlin
//sampleStart
class UserPreferences {
    private fun getPreferences() = object {
        val theme: String = "Dark"
        val fontSize: Int = 14
    }

    fun printPreferences() {
        val preferences = getPreferences()
        println("Theme: ${preferences.theme}, Font Size: ${preferences.fontSize}")
    }
}
//sampleEnd

fun main() {
    val userPreferences = UserPreferences()
    userPreferences.printPreferences()
    // Theme: Dark, Font Size: 14
}
```
{kotlin-runnable="true" id="object-expression-object-return"}

这使得你可以返回一个具有特定属性的匿名对象，为你提供了一种简单的方式来封装数据或行为，而无需创建单独的类。

如果返回匿名对象的函数或属性是 `public` 或 `private`，则其实际类型为：

* 如果匿名对象没有声明的超类，则类型为 `Any`。
* 如果匿名对象有且仅有一个声明的超类，则类型为该超类。
* 如果匿名对象有多个声明的超类，则类型为显式声明的类型。

在所有这些情况下，匿名对象中添加的成员是不可访问的。如果重写的成员在函数或属性的实际类型中声明，则是可以访问的。例如：

```kotlin
//sampleStart
interface Notification {
    // 在 Notification 接口中声明 notifyUser()
    fun notifyUser()
}

interface DetailedNotification

class NotificationManager {
    // 返回类型是 Any。message 属性不可访问。
    // 当返回类型是 Any 时，只能访问 Any 类的成员。
    fun getNotification() = object {
        val message: String = "General notification"
    }

    // 返回类型是 Notification，因为匿名对象只实现了一个接口
    // notifyUser() 函数可访问，因为它是 Notification 接口的一部分
    // message 属性不可访问，因为它没有在 Notification 接口中声明
    fun getEmailNotification() = object : Notification {
        override fun notifyUser() {
            println("Sending email notification")
        }
        val message: String = "You've got mail!"
    }

    // 返回类型是 DetailedNotification。notifyUser() 函数和 message 属性都不可访问
    // 只能访问 DetailedNotification 接口中声明的成员
    fun getDetailedNotification(): DetailedNotification = object : Notification, DetailedNotification {
        override fun notifyUser() {
            println("Sending detailed notification")
        }
        val message: String = "Detailed message content"
    }
}
//sampleEnd
fun main() {
    // 这里没有输出
    val notificationManager = NotificationManager()

    // message 属性不可访问，因为返回类型是 Any
    // 这里没有输出
    val notification = notificationManager.getNotification()

    // notifyUser() 函数可访问
    // message 属性不可访问，因为返回类型是 Notification
    val emailNotification = notificationManager.getEmailNotification()
    emailNotification.notifyUser()
    // Sending email notification

    // notifyUser() 函数和 message 属性不可访问，因为返回类型是 DetailedNotification
    // 这里没有输出
    val detailedNotification = notificationManager.getDetailedNotification()
}
```
{kotlin-runnable="true" id="object-expression-object-override"}

### 访问匿名对象中的变量 {id="access-variables-from-anonymous-objects"}

对象表达式中的代码可以访问封闭作用域中的变量：

```kotlin
import java.awt.event.MouseAdapter
import java.awt.event.MouseEvent

fun countClicks(window: JComponent) {
    var clickCount = 0
    var enterCount = 0

    // MouseAdapter 提供了鼠标事件函数的默认实现
    // 模拟 MouseAdapter 处理鼠标事件
    window.addMouseListener(object : MouseAdapter() {
        override fun mouseClicked(e: MouseEvent) {
            clickCount++  // 访问封闭作用域中的变量
        }

        override fun mouseEntered(e: MouseEvent) {
            enterCount++  // 访问封闭作用域中的变量
        }
    })
    // clickCount 和 enterCount 变量在对象表达式中是可访问的
}
```

这段代码展示了如何在匿名对象中访问和修改外部作用域中的变量（如 `clickCount` 和 `enterCount`）。

## 对象声明与对象表达式的行为差异 {id="behavior-difference-between-object-declarations-and-expressions"}

对象声明和对象表达式在初始化行为上存在差异：

* **对象表达式**会在使用它们的地方立即执行（并初始化）。
* **对象声明**会在首次访问时懒加载（即懒初始化）。
* **伴生对象**会在对应的类被加载（解析）时初始化，这与 Java 中的静态初始化器的语义相匹配。
