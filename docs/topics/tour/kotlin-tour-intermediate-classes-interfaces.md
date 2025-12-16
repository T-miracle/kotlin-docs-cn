[//]: # (title: 中级：类和接口)

<no-index/>

<tldr>  
    <p><img src="icon-1-done.svg" width="20" alt="第一步" /> <a href="kotlin-tour-intermediate-extension-functions.md">扩展函数</a><br />  
        <img src="icon-2-done.svg" width="20" alt="第二步" /> <a href="kotlin-tour-intermediate-scope-functions.md">作用域函数</a><br />  
        <img src="icon-3-done.svg" width="20" alt="第三步" /> <a href="kotlin-tour-intermediate-lambdas-receiver.md">带接收者的 Lambda 表达式</a><br />   
        <img src="icon-4.svg" width="20" alt="第四步" /> <strong>类和接口</strong><br />  
        <img src="icon-5-todo.svg" width="20" alt="第五步" /> <a href="kotlin-tour-intermediate-objects.md">对象</a><br />  
        <img src="icon-6-todo.svg" width="20" alt="第六步" /> <a href="kotlin-tour-intermediate-open-special-classes.md">开放类和特殊类</a><br />  
        <img src="icon-7-todo.svg" width="20" alt="第七步" /> <a href="kotlin-tour-intermediate-properties.md">属性</a><br />  
        <img src="icon-8-todo.svg" width="20" alt="第八步" /> <a href="kotlin-tour-intermediate-null-safety.md">空安全</a><br />  
        <img src="icon-9-todo.svg" width="20" alt="第九步" /> <a href="kotlin-tour-intermediate-libraries-and-apis.md">库和 API</a></p>  
</tldr>  

在初学者教程中，你学习了如何使用类和数据类来存储数据并维护一组可以在代码中共享的特性。
最终，你会希望创建一个层次结构，以便在项目中高效共享代码。
本章解释了 Kotlin 提供的共享代码的选项，以及它们如何使你的代码更安全、更易于维护。

## 类继承 {id=class-inheritance}

在前一章中，我们介绍了如何使用扩展函数在不修改原始源代码的情况下，对类进行功能扩展。
但当你正在处理更复杂的场景，需要在不同类之间共享代码时，仅靠扩展函数就显得不够用了。
在这种情况下，可以使用 类继承 来解决问题。

默认情况下，Kotlin 中的类是不能被继承的。
Kotlin 之所以这样设计，是为了防止意外继承，从而让你的类更容易维护、行为更可控。

Kotlin 的类只支持**单继承**，也就是说，同一时间只能继承**一个类**。
被继承的那个类称为**父类**。

父类还可以再继承另一个类（祖父类），从而形成一个层级结构。
在 Kotlin 的类层级结构顶部，是一个公共父类：`Any`。所有类最终都会继承自 `Any` 类：

![一个以 Any 类型为顶层的类层级结构示例](any-type-class.png){width="200"}

`Any` 类 会自动 提供 `toString()` 函数 作为 成员函数。
因此，你可以在任意自定义类中使用这个继承而来的函数。例如：

```kotlin
class Car(val make: String, val model: String, val numberOfDoors: Int)

fun main() {
    //sampleStart
    val car1 = Car("Toyota", "Corolla", 4)

    // 通过 字符串模板 使用 .toString() 函数 来打印 类属性
    println("Car1: make=${car1.make}, model=${car1.model}, numberOfDoors=${car1.numberOfDoors}")
    // Car1: make=Toyota, model=Corolla, numberOfDoors=4
    //sampleEnd
}
```
{kotlin-runnable="true" id="kotlin-tour-any-class"}

如果你希望通过继承在类之间共享一部分代码，首先应该考虑使用抽象类。

### 抽象类 {id=abstract-classes}

抽象类默认是可以被继承的。
抽象类的目的在于提供一些成员，让其他类去继承或实现。
因此，抽象类可以有构造函数，但你不能直接创建它的实例。
在子类中，你需要使用`override`关键字来定义父类中属性和函数的具体行为。
通过这种方式，可以说子类“重写”了父类的成员。

> 当你为继承而来的函数或属性定义具体行为时，这被称为一次**实现**。
> 
{style="tip"}

抽象类既可以包含**有实现**的函数和属性，也可以包含**没有实现**的函数和属性，后者被称为抽象函数和抽象属性。

要创建一个抽象类，需要使用`abstract`关键字：

```kotlin
abstract class Animal
```

要声明**没有实现**的函数或属性，同样需要使用`abstract`关键字：

```kotlin
abstract fun makeSound()
abstract val sound: String
```

例如，假设你希望创建一个名为`Product`的抽象类，用它派生出子类来定义不同的产品分类：

```kotlin
abstract class Product(val name: String, var price: Double) {
    // 产品分类的抽象属性
    abstract val category: String

    // 所有产品都可以共享的函数
    fun productInfo(): String {
        return "Product: $name, Category: $category, Price: $price"
    }
}
```

在这个抽象类中：

* 构造函数包含两个参数，分别表示产品的`name`和`price`。
* 定义了一个抽象属性，用字符串表示产品分类。
* 定义了一个函数，用于输出产品的相关信息。

现在为电子产品创建一个子类。在子类中为`category`属性定义实现之前，必须使用`override`关键字：

```kotlin
class Electronic(name: String, price: Double, val warranty: Int) : Product(name, price) {
    override val category = "Electronic"
}
```

`Electronic`类的特点如下：

* 继承自`Product`抽象类。
* 构造函数中新增了一个参数`warranty`，用于表示电子产品特有的保修期。
* 重写了`category`属性，并将其值设置为字符串`"Electronic"`。

现在，你可以像下面这样使用这些类：

```kotlin
abstract class Product(val name: String, var price: Double) {
    // 产品分类的抽象属性
    abstract val category: String

    // 所有产品都可以共享的函数
    fun productInfo(): String {
        return "Product: $name, Category: $category, Price: $price"
    }
}

class Electronic(name: String, price: Double, val warranty: Int) : Product(name, price) {
    override val category = "Electronic"
}

//sampleStart
fun main() {
    // 创建Electronic类的一个实例
    val laptop = Electronic(name = "Laptop", price = 1000.0, warranty = 2)

    println(laptop.productInfo())
    // Product: Laptop, Category: Electronic, Price: 1000.0
}
//sampleEnd
```
{kotlin-runnable="true" id="kotlin-tour-abstract-class"}

尽管抽象类非常适合用这种方式来共享代码，但它们也有局限性，因为Kotlin中的类只支持单继承。
如果你需要从多个来源继承行为，应当考虑使用接口。

## 接口 {id=interfaces}

接口与类类似，但也存在一些区别：

* 你不能创建接口的实例。接口没有构造函数或类头。
* 接口中的函数和属性默认都是可继承的。在Kotlin中，我们称它们是“open”的。
* 如果你没有为函数提供实现，就不需要将它们标记为`abstract`。

与抽象类类似，接口用于定义一组函数和属性，供类在之后去继承和实现。
这种方式可以让你关注接口所描述的抽象能力，而不是具体的实现细节。使用接口可以让你的代码：

* 更加模块化，通过隔离不同的部分，使它们可以独立演进。
* 更容易理解，将相关的函数组织成一个内聚的集合。
* 更容易测试，因为你可以在测试时快速用 mock 替换具体实现。

要声明一个接口，需要使用`interface`关键字：

```kotlin
interface PaymentMethod
```

### 接口实现 {id=interface-implementation}

接口支持多继承，因此一个类可以同时实现多个接口。
首先来看一个类只实现**一个**接口的场景。

要创建一个实现接口的类，需要在类头后面加上冒号，然后写上要实现的接口名称。
接口名称后面不需要使用括号`()`，因为接口没有构造函数：

```kotlin
class CreditCardPayment : PaymentMethod
```

例如：

```kotlin
interface PaymentMethod {
    // 函数默认是可继承的
    fun initiatePayment(amount: Double): String
}

class CreditCardPayment(val cardNumber: String, val cardHolderName: String, val expiryDate: String) : PaymentMethod {
    override fun initiatePayment(amount: Double): String {
        // 模拟使用信用卡处理支付
        return "Payment of $$amount initiated using Credit Card ending in ${cardNumber.takeLast(4)}."
    }
}

fun main() {
    val paymentMethod = CreditCardPayment("1234 5678 9012 3456", "John Doe", "12/25")
    println(paymentMethod.initiatePayment(100.0))
    // Payment of $100.0 initiated using Credit Card ending in 3456.
}
```
{kotlin-runnable="true" id="kotlin-tour-interface-inheritance"}

In the example:

* `PaymentMethod` is an interface that has an `initiatePayment()` function without an implementation.
* `CreditCardPayment` is a class that implements the `PaymentMethod` interface.
* The `CreditCardPayment` class overrides the inherited `initiatePayment()` function.
* `paymentMethod` is an instance of the `CreditCardPayment` class.
* The overridden `initiatePayment()` function is called on the `paymentMethod` instance with a parameter of `100.0`.

To create a class that implements **multiple** interfaces, add a colon after your class header followed by the name of the interfaces
that you want to implement separated by a comma:

```kotlin
class CreditCardPayment : PaymentMethod, PaymentType
```

For example:

```kotlin
interface PaymentMethod {
    fun initiatePayment(amount: Double): String
}

interface PaymentType {
    val paymentType: String
}

class CreditCardPayment(val cardNumber: String, val cardHolderName: String, val expiryDate: String) : PaymentMethod,
    PaymentType {
    override fun initiatePayment(amount: Double): String {
        // Simulate processing payment with credit card
        return "Payment of $$amount initiated using Credit Card ending in ${cardNumber.takeLast(4)}."
    }

    override val paymentType: String = "Credit Card"
}

fun main() {
    val paymentMethod = CreditCardPayment("1234 5678 9012 3456", "John Doe", "12/25")
    println(paymentMethod.initiatePayment(100.0))
    // Payment of $100.0 initiated using Credit Card ending in 3456.

    println("Payment is by ${paymentMethod.paymentType}")
    // Payment is by Credit Card
}
```
{kotlin-runnable="true" id="kotlin-tour-interface-multiple-inheritance"}

In the example:

* `PaymentMethod` is an interface that has the `initiatePayment()` function without an implementation.
* `PaymentType` is an interface that has the `paymentType` property that isn't initialized.
* `CreditCardPayment` is a class that implements the `PaymentMethod` and `PaymentType` interfaces.
* The `CreditCardPayment` class overrides the inherited `initiatePayment()` function and the `paymentType` property.
* `paymentMethod` is an instance of the `CreditCardPayment` class.
* The overridden `initiatePayment()` function is called on the `paymentMethod` instance with a parameter of `100.0`.
* The overridden `paymentType` property is accessed on the `paymentMethod` instance.

For more information about interfaces and interface inheritance, see [Interfaces](interfaces.md).

## Delegation

Interfaces are useful, but if your interface contains many functions, its child classes can end up with a lot of 
boilerplate code. If you only want to override a small part of a class's behavior, you need to repeat yourself a lot.

> Boilerplate code is a chunk of code that is reused with little or no alteration in multiple parts of a software project.
> 
{style="tip"}

For example, let's say that you have an interface called `DrawingTool` that contains a number of functions and one property
called `color`:

```kotlin
interface DrawingTool {
    val color: String
    fun draw(shape: String)
    fun erase(area: String)
    fun getToolInfo(): String
}
```

You create a class called `PenTool` which implements the `DrawingTool` interface and provides implementations for all of
its members:

```kotlin
class PenTool : DrawingTool {
    override val color: String = "black"

    override fun draw(shape: String) {
        println("Drawing $shape using a pen in $color")
    }

    override fun erase(area: String) {
        println("Erasing $area with pen tool")
    }

    override fun getToolInfo(): String {
        return "PenTool(color=$color)"
    }
}
```

You want to create a class like `PenTool` with the same behavior but a different `color` value. 
One approach is to create a new class that expects an object implementing the `DrawingTool` interface as a parameter,
like a `PenTool` class instance. Then, inside the class, you can override the `color` property.

But in this scenario, you need to add implementations for each member of the `DrawingTool` interface:

```kotlin
interface DrawingTool {
    val color: String
    fun draw(shape: String)
    fun erase(area: String)
    fun getToolInfo(): String
}

class PenTool : DrawingTool {
    override val color: String = "black"

    override fun draw(shape: String) {
        println("Drawing $shape using a pen in $color")
    }

    override fun erase(area: String) {
        println("Erasing $area with pen tool")
    }

    override fun getToolInfo(): String {
        return "PenTool(color=$color)"
    }
}
//sampleStart
class CanvasSession(val tool: DrawingTool) : DrawingTool {
    override val color: String = "blue"

    override fun draw(shape: String) {
        tool.draw(shape)
    }

    override fun erase(area: String) {
        tool.erase(area)
    }

    override fun getToolInfo(): String {
        return tool.getToolInfo()
    }
}
//sampleEnd
fun main() {
    val pen = PenTool()
    val session = CanvasSession(pen)

    println("Pen color: ${pen.color}")
    // Pen color: black

    println("Session color: ${session.color}")
    // Session color: blue

    session.draw("circle")
    // Drawing circle with pen in black

    session.erase("top-left corner")
    // Erasing top-left corner with pen tool

    println(session.getToolInfo())
    // PenTool(color=black)
}
```
{kotlin-runnable="true" id="kotlin-tour-interface-non-delegation"}

You can see that if you have a large number of member functions in the `DrawingTool` interface, the amount of boilerplate
code in the `CanvasSession` class can be large. However, there is an alternative.

In Kotlin, you can delegate the interface implementation to a class instance using the `by` keyword. For example:

```kotlin
class CanvasSession(val tool: DrawingTool) : DrawingTool by tool
```

Here, `tool` is the name of the `PenTool` class instance where the implementations of member functions are delegated to.

Now you don't have to add implementations for the member functions in the `CanvasSession` class. The compiler does
this for you automatically from the `PenTool` class. This saves you from having to write a lot of boilerplate code. Instead,
you add code only for the behavior you want to change for your child class. 

For example, if you want to change the value of the `color` property:

```kotlin
interface DrawingTool {
    val color: String
    fun draw(shape: String)
    fun erase(area: String)
    fun getToolInfo(): String
}

class PenTool : DrawingTool {
    override val color: String = "black"

    override fun draw(shape: String) {
        println("Drawing $shape using a pen in $color")
    }

    override fun erase(area: String) {
        println("Erasing $area with pen tool")
    }

    override fun getToolInfo(): String {
        return "PenTool(color=$color)"
    }
}

//sampleStart
class CanvasSession(val tool: DrawingTool) : DrawingTool by tool {
    // No boilerplate code!
    override val color: String = "blue"
}
//sampleEnd
fun main() {
    val pen = PenTool()
    val session = CanvasSession(pen)

    println("Pen color: ${pen.color}")
    // Pen color: black

    println("Session color: ${session.color}")
    // Session color: blue

    session.draw("circle")
    // Drawing circle with pen in black

    session.erase("top-left corner")
    // Erasing top-left corner with pen tool

    println(session.getToolInfo())
    // PenTool(color=black)
}
```
{kotlin-runnable="true" id="kotlin-tour-interface-delegation"}

If you want to, you can also override the behavior of an inherited member function in the `CanvasSession` class, but now
you don't have to add new lines of code for every inherited member function.

For more information, see [Delegation](delegation.md).

## Practice

### Exercise 1 {initial-collapse-state="collapsed" collapsible="true" id="classes-interfaces-exercise-1"}

Imagine you're working on a smart home system. A smart home typically has different types of devices that all have some
basic features but also unique behaviors. In the code sample below, complete the `abstract` class called `SmartDevice` 
so that the child class `SmartLight` can compile successfully.

Then, create another child class called `SmartThermostat` that inherits from the `SmartDevice` class and implements 
`turnOn()` and `turnOff()` functions that return print statements describing which thermostat is heating or turned off.
Finally, add another function called `adjustTemperature()` that accepts a temperature measurement as an input and prints:
`$name thermostat set to $temperature°C.`

<deflist collapsible="true">
    <def title="Hint">
        In the <code>SmartDevice</code> class, add the <code>turnOn()</code> and <code>turnOff()</code> functions so that 
        you can override their behavior later in the <code>SmartThermostat</code> class.
    </def>
</deflist>

|--|--|

```kotlin
abstract class // Write your code here

class SmartLight(name: String) : SmartDevice(name) {
    override fun turnOn() {
        println("$name is now ON.")
    }

    override fun turnOff() {
        println("$name is now OFF.")
    }

   fun adjustBrightness(level: Int) {
        println("Adjusting $name brightness to $level%.")
    }
}

class SmartThermostat // Write your code here

fun main() {
    val livingRoomLight = SmartLight("Living Room Light")
    val bedroomThermostat = SmartThermostat("Bedroom Thermostat")
    
    livingRoomLight.turnOn()
    // Living Room Light is now ON.
    livingRoomLight.adjustBrightness(10)
    // Adjusting Living Room Light brightness to 10%.
    livingRoomLight.turnOff()
    // Living Room Light is now OFF.

    bedroomThermostat.turnOn()
    // Bedroom Thermostat thermostat is now heating.
    bedroomThermostat.adjustTemperature(5)
    // Bedroom Thermostat thermostat set to 5°C.
    bedroomThermostat.turnOff()
    // Bedroom Thermostat thermostat is now off.
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-classes-interfaces-exercise-1"}

|---|---|
```kotlin
abstract class SmartDevice(val name: String) {
    abstract fun turnOn()
    abstract fun turnOff()
}

class SmartLight(name: String) : SmartDevice(name) {
    override fun turnOn() {
        println("$name is now ON.")
    }

    override fun turnOff() {
        println("$name is now OFF.")
    }

   fun adjustBrightness(level: Int) {
        println("Adjusting $name brightness to $level%.")
    }
}

class SmartThermostat(name: String) : SmartDevice(name) {
    override fun turnOn() {
        println("$name thermostat is now heating.")
    }

    override fun turnOff() {
        println("$name thermostat is now off.")
    }

   fun adjustTemperature(temperature: Int) {
        println("$name thermostat set to $temperature°C.")
    }
}


fun main() {
    val livingRoomLight = SmartLight("Living Room Light")
    val bedroomThermostat = SmartThermostat("Bedroom Thermostat")
    
    livingRoomLight.turnOn()
    // Living Room Light is now ON.
    livingRoomLight.adjustBrightness(10)
    // Adjusting Living Room Light brightness to 10%.
    livingRoomLight.turnOff()
    // Living Room Light is now OFF.

    bedroomThermostat.turnOn()
    // Bedroom Thermostat thermostat is now heating.
    bedroomThermostat.adjustTemperature(5)
    // Bedroom Thermostat thermostat set to 5°C.
    bedroomThermostat.turnOff()
    // Bedroom Thermostat thermostat is now off.
}
```
{initial-collapse-state="collapsed" collapsible="true" collapsed-title="Example solution" id="kotlin-tour-classes-interfaces-solution-1"}

### Exercise 2 {initial-collapse-state="collapsed" collapsible="true" id="classes-interfaces-exercise-2"}

Create an interface called `Media` that you can use to implement specific media classes like `Audio`, `Video`, or 
`Podcast`. Your interface must include:

* A property called `title` to represent the title of the media.
* A function called `play()` to play the media.

Then, create a class called `Audio` that implements the `Media` interface. The `Audio` class must use the `title` property
in its constructor as well as have an additional property called `composer` that has `String` type. In the class, implement
the `play()` function to print the following: `"Playing audio: $title, composed by $composer"`.

<deflist collapsible="true">
    <def title="Hint">
        You can use the <code>override</code> keyword in class headers to implement a property from an interface in the constructor.
    </def>
</deflist>

|---|---|
```kotlin
interface // Write your code here

class // Write your code here

fun main() {
    val audio = Audio("Symphony No. 5", "Beethoven")
    audio.play()
   // Playing audio: Symphony No. 5, composed by Beethoven
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-classes-interfaces-exercise-2"}

|---|---|
```kotlin
interface Media {
    val title: String
    fun play()
}

class Audio(override val title: String, val composer: String) : Media {
    override fun play() {
        println("Playing audio: $title, composed by $composer")
    }
}

fun main() {
    val audio = Audio("Symphony No. 5", "Beethoven")
    audio.play()
   // Playing audio: Symphony No. 5, composed by Beethoven
}
```
{initial-collapse-state="collapsed" collapsible="true" collapsed-title="Example solution" id="kotlin-tour-classes-interfaces-solution-2"}

### Exercise 3 {initial-collapse-state="collapsed" collapsible="true" id="classes-interfaces-exercise-3"}

You're building a payment processing system for an e-commerce application. Each payment method needs to be able to 
authorize a payment and process a transaction. Some payments also need to be able to process refunds.

1. In the `Refundable` interface, add a function called `refund()` to process refunds.

2. In the `PaymentMethod` abstract class:
   * Add a function called `authorize()` that takes an amount and prints a message containing the amount.
   * Add an abstract function called `processPayment()` that also takes an amount.

3. Create a class called `CreditCard` that implements the `Refundable` interface and `PaymentMethod` abstract class.
In this class, add implementations for the `refund()` and `processPayment()` functions so that they print the following 
statements:
   * `"Refunding $amount to the credit card."`
   * `"Processing credit card payment of $amount."`

|---|---|
```kotlin
interface Refundable {
    // Write your code here
}

abstract class PaymentMethod(val name: String) {
    // Write your code here
}

class CreditCard // Write your code here

fun main() {
    val visa = CreditCard("Visa")
    
    visa.authorize(100.0)
    // Authorizing payment of $100.0.
    visa.processPayment(100.0)
    // Processing credit card payment of $100.0.
    visa.refund(50.0)
    // Refunding $50.0 to the credit card.
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-classes-interfaces-exercise-3"}

|---|---|
```kotlin
interface Refundable {
    fun refund(amount: Double)
}

abstract class PaymentMethod(val name: String) {
    fun authorize(amount: Double) {
        println("Authorizing payment of $$amount.")
    }

    abstract fun processPayment(amount: Double)
}

class CreditCard(name: String) : PaymentMethod(name), Refundable {
    override fun processPayment(amount: Double) {
        println("Processing credit card payment of $$amount.")
    }

    override fun refund(amount: Double) {
        println("Refunding $$amount to the credit card.")
    }
}

fun main() {
    val visa = CreditCard("Visa")
    
    visa.authorize(100.0)
    // Authorizing payment of $100.0.
    visa.processPayment(100.0)
    // Processing credit card payment of $100.0.
    visa.refund(50.0)
    // Refunding $50.0 to the credit card.
}
```
{initial-collapse-state="collapsed" collapsible="true" collapsed-title="Example solution" id="kotlin-tour-classes-interfaces-solution-3"}

### Exercise 4 {initial-collapse-state="collapsed" collapsible="true" id="classes-interfaces-exercise-4"}

You have a simple messaging app that has some basic functionality, but you want to add some functionality for 
_smart_ messages without significantly duplicating your code.

In the code below, define a class called `SmartMessenger` that inherits from the `Messenger` interface but delegates 
the implementation to an instance of the `BasicMessenger` class. 

In the `SmartMessenger` class, override the `sendMessage()` function to send smart messages. The function must accept
a `message` as an input and return a printed statement: `"Sending a smart message: $message"`. In addition, call the 
`sendMessage()` function from the `BasicMessenger` class and prefix the message with `[smart]`.

> You don't need to rewrite the `receiveMessage()` function in the `SmartMessenger` class.
> 
{style="note"}

|--|--|

```kotlin
interface Messenger {
    fun sendMessage(message: String)
    fun receiveMessage(): String
}

class BasicMessenger : Messenger {
    override fun sendMessage(message: String) {
        println("Sending message: $message")
    }

    override fun receiveMessage(): String {
        return "You've got a new message!"
    }
}

class SmartMessenger // Write your code here

fun main() {
    val basicMessenger = BasicMessenger()
    val smartMessenger = SmartMessenger(basicMessenger)
    
    basicMessenger.sendMessage("Hello!")
    // Sending message: Hello!
    println(smartMessenger.receiveMessage())
    // You've got a new message!
    smartMessenger.sendMessage("Hello from SmartMessenger!")
    // Sending a smart message: Hello from SmartMessenger!
    // Sending message: [smart] Hello from SmartMessenger!
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-classes-interfaces-exercise-4"}

|---|---|
```kotlin
interface Messenger {
    fun sendMessage(message: String)
    fun receiveMessage(): String
}

class BasicMessenger : Messenger {
    override fun sendMessage(message: String) {
        println("Sending message: $message")
    }

    override fun receiveMessage(): String {
        return "You've got a new message!"
    }
}

class SmartMessenger(val basicMessenger: BasicMessenger) : Messenger by basicMessenger {
    override fun sendMessage(message: String) {
        println("Sending a smart message: $message")
        basicMessenger.sendMessage("[smart] $message")
    }
}

fun main() {
    val basicMessenger = BasicMessenger()
    val smartMessenger = SmartMessenger(basicMessenger)
    
    basicMessenger.sendMessage("Hello!")
    // Sending message: Hello!
    println(smartMessenger.receiveMessage())
    // You've got a new message!
    smartMessenger.sendMessage("Hello from SmartMessenger!")
    // Sending a smart message: Hello from SmartMessenger!
    // Sending message: [smart] Hello from SmartMessenger!
}
```
{initial-collapse-state="collapsed" collapsible="true" collapsed-title="Example solution" id="kotlin-tour-classes-interfaces-solution-4"}

## Next step

[Intermediate: Objects](kotlin-tour-intermediate-objects.md)
