[//]: # (title: 类型检查和强制转换)

In Kotlin, you can do two things with types at runtime: check whether an object is a specific type, or convert it to another type.
Type **checks** help you confirm the kind of object you're dealing with, while type **casts** attempt to convert the object to another type.

> 要了解关于 **泛型** 类型检查和转换（例如 `List<T>`、`Map<K,V>`）的具体信息，请参阅
> [泛型类型检查和转换](generics.md#泛型类型检查和转换)。
> 
{style="tip"}

## Checks with `is` and `!is` operators {id="is-and-is-operators"}

Use the `is` operator (or `!is` to negate it) to check if an object matches a type at runtime:

```kotlin
fun main() {
    val input: Any = "Hello, Kotlin"

    if (input is String) {
        println("Message length: ${input.length}")
        // Message length: 13
    }

    if (input !is String) { // 和 !(input is String) 一样
        println("Input is not a valid message")
    } else {
        println("Processing message: ${input.length} characters")
        // Processing message: 13 characters
    }
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-typecasts-is-operator"}

You can also use `is` and `!is` operators to check if an object matches a subtype:

```kotlin
interface Animal {
    val name: String
    fun speak()
}

class Dog(override val name: String) : Animal {
    override fun speak() = println("$name says: Woof!")
}

class Cat(override val name: String) : Animal {
    override fun speak() = println("$name says: Meow!")
}
//sampleStart
fun handleAnimal(animal: Animal) {
    println("Handling animal: ${animal.name}")
    animal.speak()
    
    // Use is operator to check for subtypes
    if (animal is Dog) {
        println("Special care instructions: This is a dog.")
    } else if (animal is Cat) {
        println("Special care instructions: This is a cat.")
    }
}
//sampleEnd
fun main() {
    val pets: List<Animal> = listOf(
        Dog("Buddy"),
        Cat("Whiskers"),
        Dog("Rex")
    )

    for (pet in pets) {
        handleAnimal(pet)
        println("---")
    }
    // Handling animal: Buddy
    // Buddy says: Woof!
    // Special care instructions: This is a dog.
    // ---
    // Handling animal: Whiskers
    // Whiskers says: Meow!
    // Special care instructions: This is a cat.
    // ---
    // Handling animal: Rex
    // Rex says: Woof!
    // Special care instructions: This is a dog.
    // ---
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-typecasts-is-operator-subtype"}

This example uses the `is` operator to check if the `Animal` class instance has subtype `Dog` or `Cat` to print the relevant
care instructions.

You can check if an object is a supertype of its declared type, but it's not worthwhile because the answer is always true.
Every class instance is already an instance of its supertypes.

> To identify the type of an object at runtime, see [Reflection](reflection.md).
> 
{type="tip"}

## Type casts

To convert the type of an object in Kotlin to another type is called **casting**.

In some cases, the compiler automatically casts objects for you. This is called smart-casting.

If you need to explicitly cast a type, use `as?` or `as` [cast operators](#unsafe-cast-operator). 

## 智能转换 {id=smart-casts}

The compiler tracks the type checks and [explicit casts](#unsafe-cast-operator) for immutable
values and inserts implicit (safe) casts automatically:

```kotlin
fun logMessage(data: Any) {
    // data is automatically cast to String
    if (data is String) {
        println("Received text: ${data.length} characters")
    }
}

fun main() {
    logMessage("Server started")
    // Received text: 14 characters
    logMessage(404)
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-typecasts-smartcast"}

编译器甚至可以智能地识别，当负面检查导致 return 时，类型转换是安全的：

```kotlin
fun logMessage(data: Any) {
    // data is automatically cast to String
    if (data !is String) return

    println("Received text: ${data.length} characters")
}

fun main() {
    logMessage("User signed in")
    // Received text: 14 characters
    logMessage(true)
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-typecasts-smartcast-negative"}

### 控制流 {id=control-flow}

Smart casts work not only for `if` conditional expressions, but also for [`when` expressions](control-flow.md#when-expressions-and-statements):

```kotlin
fun processInput(data: Any) {
    when (data) {
        // data is automatically cast to Int
        is Int -> println("Log: Assigned new ID ${data + 1}")
        // data is automatically cast to String
        is String -> println("Log: Received message \"$data\"")
        // data is automatically cast to IntArray
        is IntArray -> println("Log: Processed scores, total = ${data.sum()}")
    }
}

fun main() {
    processInput(1001)
    // Log: Assigned new ID 1002
    processInput("System rebooted")
    // Log: Received message "System rebooted"
    processInput(intArrayOf(10, 20, 30))
    // Log: Processed scores, total = 60
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-typecasts-smartcast-when"}

And for [`while` loops](control-flow.md#while-loops):

```kotlin
sealed interface Status
data class Ok(val currentRoom: String) : Status
data object Error : Status

class RobotVacuum(val rooms: List<String>) {
    var index = 0

    fun status(): Status =
        if (index < rooms.size) Ok(rooms[index])
        else Error

    fun clean(): Status {
        println("Finished cleaning ${rooms[index]}")
        index++
        return status()
    }
}

fun main() {
    //sampleStart
    val robo = RobotVacuum(listOf("Living Room", "Kitchen", "Hallway"))

    var status: Status = robo.status()
    while (status is Ok) {
        // The compiler smart casts status to OK type, so the currentRoom
        // property is accessible.
        println("Cleaning ${status.currentRoom}...")
        status = robo.clean()
    }
    // Cleaning Living Room...
    // Finished cleaning Living Room
    // Cleaning Kitchen...
    // Finished cleaning Kitchen
    // Cleaning Hallway...
    // Finished cleaning Hallway
    //sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-typecasts-smartcast-while"}

In this example, the sealed interface `Status` has two implementations: the data class `Ok` and the data object `Error`.
Only the `Ok` data class has the `currentRoom` property. When the `while` loop condition evaluates to true, the
compiler smart casts the `status` variable to `Ok` type, making the `currentRoom` property accessible within the loop body.

If you declare a variable of `Boolean` type before using it in your `if`, `when`, or `while` condition, any
information collected by the compiler about the variable is accessible in the corresponding block for
smart-casting.

This can be useful when you want to do things like extract boolean conditions into variables. Then, you can give the
variable a meaningful name, which improves your code readability and makes it possible to reuse the variable later
in your code. For example:

```kotlin
class Cat {
    fun purr() {
        println("Purr purr")
    }
}
//sampleStart
fun petAnimal(animal: Any) {
    val isCat = animal is Cat
    if (isCat) {
        // 编译器可以访问有关
        // isCat 的信息，因此知道 animal 已经被智能转换
        // 为 Cat 类型。
        // 因此，可以调用 purr() 函数。
        animal.purr()
    }
}

fun main(){
    val kitty = Cat()
    petAnimal(kitty)
    // Purr purr
}
//sampleEnd
```
{kotlin-runnable="true" kotlin-min-compiler-version="2.0" id="kotlin-smart-casts-local-variables" validate="false"}

### 逻辑运算符 {id=logical-operators}

如果在 `&&` 或 `||` 运算符的左侧有类型检查（不论是确认某个类型还是排除某个类型），编译器可以对右侧进行智能类型转换：

```kotlin
// 在 `||` 的右侧，x 会自动转换为 String
if (x !is String || x.length == 0) return

// 在 `&&` 的右侧，x 会自动转换为 String
if (x is String && x.length > 0) {
    print(x.length) // x 会自动转换为 String
}
```

如果你将对象的类型检查与 `||` 运算符结合使用，编译器会将对象智能地转换为它们的最接近的共同超类型：

```kotlin
interface Status {
    fun signal() {}
}

interface Ok : Status
interface Postponed : Status
interface Declined : Status

fun signalCheck(signalStatus: Any) {
    if (signalStatus is Postponed || signalStatus is Declined) {
        // signalStatus 被智能转换为共同超类型 Status
        signalStatus.signal()
    }
}
```

> 共同超类型是对 **联合类型** 的一种 **近似**。
> Kotlin [目前不支持](https://youtrack.jetbrains.com/issue/KT-13108/Denotable-union-and-intersection-types) 联合类型。
>
{style="note"}

### 内联函数 {id=inline-functions}

编译器可以对传递给 [内联函数](inline-functions.md) 的 lambda 函数中捕获的变量进行智能类型转换。

内联函数被视为具有隐式
[`callsInPlace`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.contracts/-contract-builder/calls-in-place.html) 合约。
这意味着传递给内联函数的任何 lambda 函数都是在原地调用的。
由于 lambda 函数是在原地调用的，编译器知道 lambda 函数不能泄漏对其函数体内任何变量的引用。

The compiler uses this knowledge, along with other analyses, to decide whether it's safe to smart-cast any of the
captured variables. For example:

```kotlin
interface Processor {
    fun process()
}

inline fun inlineAction(f: () -> Unit) = f()

fun nextProcessor(): Processor? = null

fun runProcessor(): Processor? {
    var processor: Processor? = null
    inlineAction {
        // 编译器知道 processor 是一个局部变量，并且 inlineAction()
        // 是一个内联函数，因此对 processor 的引用不能泄漏。
        // 因此，智能类型转换 processor 是安全的。
      
        // 如果 processor 不为 null，processor 被智能转换
        if (processor != null) {
            // 编译器知道 processor 不为 null，所以不需要安全调用
            processor.process()
        }

        processor = nextProcessor()
    }

    return processor
}
```

### 异常处理 {id=exception-handling}

智能类型转换信息会传递到 `catch` 和 `finally` 块。这使代码更安全，因为编译器会跟踪对象是否具有可空类型。例如：

```kotlin
//sampleStart
fun testString() {
    var stringInput: String? = null
    // stringInput 被智能转换为 String 类型
    stringInput = ""
    try {
        // 编译器知道 stringInput 不是 null
        println(stringInput.length)
        // 0

        // 编译器拒绝之前关于 stringInput 的智能转换信息。
        // 现在 stringInput 的类型是 String?。
        stringInput = null

        // 触发异常
        if (2 > 1) throw Exception()
        stringInput = ""
    } catch (exception: Exception) {
        // 编译器知道 stringInput 可能为 null，
        // 所以 stringInput 保持可空类型。
        println(stringInput?.length)
        // null
    }
}
//sampleEnd
fun main() {
    testString()
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="2.0" id="kotlin-smart-casts-exception-handling"}

### 智能类型转换的前提条件 {id=smart-cast-prerequisites}

Smart casts work only when the compiler can guarantee that the variable won't change between the check and its usage.
They can be used in the following conditions:

<table style="none">
    <tr>
        <td>
            <code>val</code> 局部变量
        </td>
        <td>
            始终可以，除了 <a href='delegated-properties.md'>局部委托属性</a>。
        </td>
    </tr>
    <tr>
        <td>
            <code>val</code> 属性
        </td>
        <td>
            如果属性是<code>private</code>、<code>internal</code>，或者检查是在声明属性的相同
            <a href="visibility-modifiers.md#modules">模块</a>
            内执行的，那么智能转换不能用于<code>open</code>属性或具有自定义 getter 的属性。
        </td>
    </tr>
    <tr>
        <td>
            <code>var</code> 局部变量
        </td>
        <td>
            如果变量在检查和使用之间没有被修改，在修改它的 lambda 中没有被捕获，并且不是局部委托属性。
        </td>
    </tr>
    <tr>
        <td>
            <code>var</code> 属性
        </td>
        <td>
            永远不会，因为变量可能随时被其他代码修改。
        </td>
    </tr>
</table>

## `as` and `as?` cast operators {id="unsafe-cast-operator"}

Kotlin has two cast operators: `as` and `as?`. You can use both to cast, but they have different behaviors.

If a cast fails with the `as` operator, a `ClassCastException` is thrown at runtime. That's why it's also called the **unsafe** operator.
You can use `as` when casting to a non-null type:

```kotlin
fun main() {
    val rawInput: Any = "user-1234"

    // Casts to String successfully
    val userId = rawInput as String
    println("Logging in user with ID: $userId")
    // Logging in user with ID: user-1234

    // Triggers ClassCastException
    val wrongCast = rawInput as Int
    println("wrongCast contains: $wrongCast")
    // Exception in thread "main" java.lang.ClassCastException
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="2.0" id="kotlin-unsafe-cast-operator" validate="false"}

If you use the `as?` operator instead, and the cast fails, the operator returns `null`. That's why it's also
called the **safe** operator:

```kotlin
fun main() {
    val rawInput: Any = "user-1234"

    // Casts to String successfully
    val userId = rawInput as? String
    println("Logging in user with ID: $userId")
    // Logging in user with ID: user-1234

    // Assigns a null value to wrongCast
    val wrongCast = rawInput as? Int
    println("wrongCast contains: $wrongCast")
    // wrongCast contains: null
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="2.0" id="kotlin-safe-cast-operator"}

To cast a nullable type safely, use the `as?` operator to prevent triggering a `ClassCastException` if the cast fails.

You _can_ use `as` with a nullable type. This allows the result to be `null`, but it still throws a `ClassCastException` 
if the cast is unsuccessful. For this reason, `as?` is the safer option:

```kotlin
fun main() {
    val config: Map<String, Any?> = mapOf(
        "username" to "kodee",
        "alias" to null,
        "loginAttempts" to 3
    )

    // Unsafely casts to a nullable String
    val username: String? = config["username"] as String?
    println("Username: $username")
    // Username: kodee

    // Unsafely casts a null value to a nullable String
    val alias: String? = config["alias"] as String?
    println("Alias: $alias")
    // Alias: null

    // Fails to cast to nullable String and throws ClassCastException
    // val unsafeAttempts: String? = config["loginAttempts"] as String?
    // println("Login attempts (unsafe): $unsafeAttempts")
    // Exception in thread "main" java.lang.ClassCastException

    // Fails to cast to nullable String and returns null
    val safeAttempts: String? = config["loginAttempts"] as? String
    println("Login attempts (safe): $safeAttempts")
    // Login attempts (safe): null
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="2.0" id="kotlin-cast-nullable-types"}

### Up and downcasting

In Kotlin, you can cast objects to supertypes and subtypes. 

Casting an object to an instance of its superclass is called **upcasting**. Upcasting doesn't need any special syntax or
cast operators. For example:

```kotlin
interface Animal {
    fun makeSound()
}

class Dog : Animal {
    // Implements behavior for makeSound()
    override fun makeSound() {
        println("Dog says woof!")
    }
}

fun printAnimalInfo(animal: Animal) {
    animal.makeSound()
}

fun main() {
    val dog = Dog()
    // Upcasts Dog instance to Animal
    printAnimalInfo(dog)  
    // Dog says woof!
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="2.0" id="kotlin-upcast"}

In this example, when the `printAnimalInfo()` function is called with a `Dog` instance, the compiler upcasts it
to `Animal` because that's the expected parameter type. Since the actual object is still a `Dog` instance, the compiler dynamically
resolves the `makeSound()` function from the `Dog` class, printing `"Dog says woof!"`.

You'll often see explicit upcasting in Kotlin APIs where behavior depends on an abstract type. It's also common in Jetpack Compose
and UI toolkits, which typically treat all UI elements as supertypes and later operate on specific subclasses:

```kotlin
    val textView = TextView(this)
    textView.text = "Hello, View!"

    // Upcasts from TextView to View
    val view: View = textView  

    // Use View functions
    view.setPadding(20, 20, 20, 20)
    // Activity expects a View type
    setContentView(view)
```

Casting an object to an instance of a subclass is called **downcasting**. Because downcasting can be unsafe, you need to use
explicit cast operators. To avoid throwing exceptions on failed casts, we recommend using the safe cast operator `as?`,
to return `null` if the cast fails:

```kotlin
interface Animal {
    fun makeSound()
}

class Dog : Animal {
    override fun makeSound() {
        println("Dog says woof!")
    }

    fun bark() {
        println("BARK!")
    }
}

fun main() {
    // Creates animal as a Dog instance with Animal
    // type
    val animal: Animal = Dog()
    
    // Safely downcasts animal to Dog type
    val dog: Dog? = animal as? Dog

    // Uses a safe call to call bark() if dog isn't null
    dog?.bark()
    // "BARK!"
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="2.0" id="kotlin-downcast"}

In this example, `animal` is declared as type `Animal`, but it holds a `Dog` instance. The code safely casts `animal` to 
`Dog` type and uses a [safe call](null-safety.md#safe-call-operator) (`?.`) to access the `bark()` function.

You'll use downcasting in serialization when deserializing a base class to a specific subtype. It's also common when 
working with Java libraries that return supertype objects, which you may need to downcast in Kotlin.