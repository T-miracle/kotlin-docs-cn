[//]: # (title: 异常)

异常有助于你的代码在发生运行时错误时仍能按预期运行，即使这些错误可能会中断程序的执行。
Kotlin 默认将所有异常视为 _unchecked_（未检查）异常。
未检查异常简化了异常处理过程：你可以捕获异常，但你不需要显式处理或 [声明](java-to-kotlin-interop.md#checked-exceptions) 它们。

> 了解更多关于 Kotlin 在与 Java、Swift 和 Objective-C 交互时如何处理异常的信息，请参阅
> [Java、Swift 和 Objective-C 的异常互操作性](#exception-interoperability-with-java-swift-and-objective-c) 章节。
> 
{style="tip"}

处理异常主要包括两个步骤：

* **抛出异常：** 指出何时发生问题。
* **捕获异常：** 手动处理意外异常，解决问题或通知开发者或应用程序用户。

异常由 [`Exception`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-exception/)
类的子类表示，`Exception` 类是 [`Throwable`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-throwable/) 类的子类。
关于层次结构的更多信息，请参阅 [异常层次结构](#exception-hierarchy) 章节。
由于 `Exception` 是一个 [`open class`](inheritance.md)，你可以创建
[自定义异常](#create-custom-exceptions) 以满足你应用程序的特定需求。

## 抛出异常 {id=throw-exceptions}

使用 `throw` 关键字可以手动抛出异常。
抛出异常表示代码中发生了意外的运行时错误。
异常是 [对象](classes.md#creating-instances-of-classes)，抛出异常将创建异常类的一个实例。

你可以不带任何参数地抛出异常：

```kotlin
throw IllegalArgumentException()
```

为了更好地理解问题的来源，可以包含额外的信息，如自定义消息和原始原因：

```kotlin
val cause = IllegalStateException("原始原因：非法状态")

// 如果 userInput 是负数，则抛出 IllegalArgumentException
// 同时显示原始原因，由 cause IllegalStateException 表示
if (userInput < 0) {
    throw IllegalArgumentException("输入必须是非负数", cause)
}
```

在这个例子中，当用户输入负数时抛出 `IllegalArgumentException`。
你可以创建自定义错误消息，并保留异常的原始原因 (`cause`)，它将包含在 [堆栈跟踪](#stack-trace) 中。

### 使用前置条件函数抛出异常 {id=throw-exceptions-with-precondition-functions}

Kotlin 提供了额外的方式来自动抛出异常，使用前置条件函数。
前置条件函数包括：

| 前置条件函数                           | 使用场景          | 抛出的异常                                                                                                          |
|----------------------------------|---------------|----------------------------------------------------------------------------------------------------------------|
| [`require()`](#require-function) | 检查用户输入的有效性    | [`IllegalArgumentException`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-illegal-argument-exception/) |
| [`check()`](#check-function)     | 检查对象或变量状态的有效性 | [`IllegalStateException`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-illegal-state-exception/)       |
| [`error()`](#error-function)     | 表示非法状态或条件     | [`IllegalStateException`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-illegal-state-exception/)       |

这些函数适用于当特定条件不满足时程序流程无法继续的情况。这简化了代码并使这些检查的处理更加高效。

#### `require()` 函数 {id=require-function}

使用 [`require()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/require.html)
函数在函数的操作中验证输入参数的重要性，如果这些参数无效则函数无法继续执行。

如果 `require()` 中的条件未满足，它会抛出 [`IllegalArgumentException`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-illegal-argument-exception/)：

```kotlin
fun getIndices(count: Int): List<Int> {
    require(count >= 0) { "Count must be non-negative. You set count to $count." }
    return List(count) { it + 1 }
}

fun main() {
    // 这将抛出 IllegalArgumentException
    println(getIndices(-1))
    
    // 取消下面一行的注释以查看一个有效的例子
    // println(getIndices(3))
    // [1, 2, 3]
}
```
{kotlin-runnable="true"}

> `require()` 函数允许编译器执行 [智能类型转换](typecasts.md#smart-casts)。
> 在成功检查之后，变量会自动转换为非空类型。
> 这些函数常用于空值性检查，以确保变量在继续执行之前不为 null。例如：
>
> ```kotlin
> fun printNonNullString(str: String?) {
>     // 空值性检查
>     require(str != null) 
>     // 在成功检查后，'str' 保证不为 null，并自动智能转换为非空的 String
>     println(str.length)
> }
> ```
>
{style="note"}

#### `check()` 函数 {id=check-function}

使用 [`check()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/check.html) 函数来验证对象或变量的状态。
如果检查失败，表示存在需要解决的逻辑错误。

如果 `check()` 函数中指定的条件为 `false`，它会抛出 [`IllegalStateException`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-illegal-state-exception/)：

```kotlin
fun main() {
    var someState: String? = null

    fun getStateValue(): String {

        val state = checkNotNull(someState) { "State must be set beforehand!" }
        check(state.isNotEmpty()) { "State must be non-empty!" }
        return state
    }
    // 如果取消下面一行的注释，程序将抛出 IllegalStateException
    // getStateValue()

    someState = ""

    // 如果取消下面一行的注释，程序将抛出 IllegalStateException
    // getStateValue() 
    someState = "non-empty-state"

    // 这将打印 "non-empty-state"
    println(getStateValue())
}
```
{kotlin-runnable="true"}

> `check()` 函数允许编译器执行 [智能类型转换](typecasts.md#smart-casts)。
> 在成功检查之后，变量会自动转换为非空类型。
> 这些函数常用于空值性检查，以确保变量在继续执行之前不为 null。例如：
>
> ```kotlin
> fun printNonNullString(str: String?) {
>     // 空值性检查
>     check(str != null) 
>     // 在成功检查后，'str' 保证不为 null，并自动智能转换为非空的 String
>     println(str.length)
> }
> ```
>
{style="note"}

#### `error()` 函数 {id=error-function}

[`error()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/error.html)
函数用于信号非法状态或代码中逻辑上不应发生的条件。
适用于在代码中有意抛出异常的场景，例如当代码遇到意外状态时。
这个函数在 `when` 表达式中特别有用，提供了一种清晰的方式来处理逻辑上不应发生的情况。

在以下示例中，`error()` 函数用于处理未定义的用户角色。
如果角色不是预定义的角色之一，将抛出 [`IllegalStateException`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-illegal-state-exception/)：

```kotlin
class User(val name: String, val role: String)

fun processUserRole(user: User) {
    when (user.role) {
        "admin" -> println("${user.name} 是管理员。")
        "editor" -> println("${user.name} 是编辑。")
        "viewer" -> println("${user.name} 是查看者。")
        else -> error("未定义的角色: ${user.role}")
    }
}

fun main() {
    // 这按预期工作
    val user1 = User("Alice", "admin")
    processUserRole(user1)
    // Alice 是管理员。

    // 这将抛出 IllegalStateException
    val user2 = User("Bob", "guest")
    processUserRole(user2)
}
```
{kotlin-runnable="true"}

## 使用 try-catch 块处理异常 {id=handle-exceptions-using-try-catch-blocks}

当抛出异常时，它会中断程序的正常执行。
你可以使用 `try` 和 `catch` 关键字优雅地处理异常，以保持程序的稳定。
`try` 块包含可能抛出异常的代码，而 `catch` 块捕获并处理异常（如果发生的话）。
异常会被第一个匹配其特定类型或异常的 [超类](inheritance.md) 的 `catch` 块捕获。

下面是如何将 `try` 和 `catch` 关键字一起使用的示例：

```kotlin
try {
    // 可能抛出异常的代码
} catch (e: SomeException) {
    // 处理异常的代码
}
```

常见的方法是将 `try-catch` 作为表达式使用，这样它可以从
`try` 块或 `catch` 块中返回一个值：

```kotlin
fun main() {
    val num: Int = try {

        // 如果 count() 成功完成，其返回值将赋值给 num
        count()
        
    } catch (e: ArithmeticException) {
        
        // 如果 count() 抛出异常，catch 块返回 -1，
        // 并将其赋值给 num
        -1
    }
    println("结果: $num")
}

// 模拟一个可能抛出 ArithmeticException 的函数
fun count(): Int {
    
    // 修改此值以返回不同的 num 值
    val a = 0
    
    return 10 / a
}
```
{kotlin-runnable="true"}

你可以为同一个 `try` 块使用多个 `catch` 处理器。
可以添加尽可能多的 `catch` 块来处理不同的异常。
当有多个 `catch` 块时，重要的是按从最具体到最一般的异常顺序排列它们，遵循从上到下的顺序。
这种排序与程序的执行流程一致。

考虑以下示例中的 [自定义异常](#create-custom-exceptions)：

```kotlin
open class WithdrawalException(message: String) : Exception(message)
class InsufficientFundsException(message: String) : WithdrawalException(message)

fun processWithdrawal(amount: Double, availableFunds: Double) {
    if (amount > availableFunds) {
        throw InsufficientFundsException("余额不足，无法进行取款。")
    }
    if (amount < 1 || amount % 1 != 0.0) {
        throw WithdrawalException("无效的取款金额。")
    }
    println("取款处理完成")
}

fun main() {
    val availableFunds = 500.0

    // 修改此值以测试不同的场景
    val withdrawalAmount = 500.5

    try {
        processWithdrawal(withdrawalAmount.toDouble(), availableFunds)

    // catch 块的顺序很重要！
    } catch (e: InsufficientFundsException) {
        println("捕获到 InsufficientFundsException: ${e.message}")
    } catch (e: WithdrawalException) {
        println("捕获到 WithdrawalException: ${e.message}")
    }
}
```
{kotlin-runnable="true"}

处理 `WithdrawalException` 的通用 `catch` 块会捕获所有该类型的异常，包括像
`InsufficientFundsException` 这样的特定异常，除非它们已经被更具体的 `catch` 块捕获。

### `finally` 块 {id=finally-block}

`finally` 块包含的代码会始终执行，无论 `try` 块是否成功完成或抛出异常。
使用 `finally` 块可以在执行完 `try` 和 `catch` 块后进行清理。
当处理文件或网络连接等资源时，这尤其重要，因为 `finally` 确保这些资源被正确关闭或释放。

以下是如何通常将 `try-catch-finally` 块一起使用的示例：

```kotlin
try {
    // 可能抛出异常的代码
}
catch (e: YourException) {
    // 异常处理器
}
finally {
    // 始终执行的代码
}
```

`try` 表达式的返回值由 `try` 或 `catch` 块中最后执行的表达式决定。
如果没有发生异常，结果来自 `try` 块；如果处理了异常，则结果来自 `catch` 块。
`finally` 块始终执行，但不会改变 `try-catch` 块的结果。

让我们看一个示例来演示：

```kotlin
fun divideOrNull(a: Int): Int {
    
    // try 块始终会被执行
    // 如果发生异常（如除零错误），会立即跳转到 catch 块
    try {
        val b = 44 / a
        println("try 块: 执行除法: $b")
        return b
    }
    
    // catch 块由于 ArithmeticException（如果 a == 0 则为除零错误）被执行
    catch (e: ArithmeticException) {
        println("catch 块: 遇到 ArithmeticException $e")
        return -1
    }
    finally {
        println("finally 块: finally 块始终会被执行")
    }
}

fun main() {
    
    // 修改此值以获取不同的结果。ArithmeticException 将返回: -1
    divideOrNull(0)
}
```
{kotlin-runnable="true"}

> 在 Kotlin 中，处理实现 [`AutoClosable`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-auto-closeable/) 接口的资源
> （如 `FileInputStream` 或 `FileOutputStream`）的惯用方法是使用 [`.use()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/use.html) 函数。
> 该函数在代码块完成后自动关闭资源，无论是否抛出异常，从而消除了对 `finally` 块的需求。
> 因此，Kotlin 不需要像 [Java 的 try-with-resources](https://docs.oracle.com/javase/tutorial/essential/exceptions/tryResourceClose.html) 那样的特殊语法来进行资源管理。
> 
> ```kotlin
> FileWriter("test.txt").use { writer ->
> writer.write("some text") 
> // 在此代码块之后，.use 函数会自动调用 writer.close()，类似于 finally 块
> }
> ```
> 
{style="note"}

如果你的代码需要在不处理异常的情况下进行资源清理，你也可以使用带有 `finally` 块的 `try`，而不使用 `catch` 块：

```kotlin
class MockResource { 
    fun use() { 
        println("资源正在使用中") 
        // 模拟资源的使用 
        // 如果发生除零错误则会抛出 ArithmeticException
        val result = 100 / 0
        
        // 如果抛出异常，则此行不会被执行
        println("结果: $result") 
    }
    
    fun close() { 
        println("资源已关闭") 
    }
}

fun main() { 
    val resource = MockResource()
//sampleStart 
    try {
        
        // 尝试使用资源 
        resource.use()
        
    } finally {
        
        // 确保资源始终被关闭，即使发生异常 
        resource.close()
    }

    // 如果抛出异常，则此行不会被打印
    println("程序结束")
//sampleEnd
}
```
{kotlin-runnable="true"}

如你所见，`finally` 块保证了资源被关闭，无论是否发生异常。

在 Kotlin 中，你可以根据具体需要选择只使用 `catch` 块、只使用 `finally` 块或同时使用两者，但
`try` 块必须至少伴随一个 `catch` 块或一个 `finally` 块。

## 创建自定义异常 {id=create-custom-exceptions}

在 Kotlin 中，你可以通过创建继承自内置 `Exception` 类的类来定义自定义异常。
这允许你创建更具体的错误类型，以满足应用程序的需求。

要创建自定义异常，你可以定义一个继承自 `Exception` 的类：

```kotlin
class MyException: Exception("我的消息")
```

在这个示例中，有一个默认的错误消息 "我的消息"，但如果需要，你也可以留空。

> 在 Kotlin 中，异常是有状态的对象，携带特定于其创建上下文的信息，这称为 [堆栈跟踪](#stack-trace)。
> 避免使用 [对象声明](object-declarations.md#object-declarations-overview) 来创建异常。
> 相反，每次需要异常时都创建一个新的实例。
> 这样，你可以确保异常的状态准确反映特定的上下文。
>
{style="tip"}

自定义异常也可以是任何已存在的异常子类的子类，例如 `ArithmeticException` 子类：

```kotlin
class NumberTooLargeException: ArithmeticException("我的消息")
```

> 如果你想创建自定义异常的子类，你必须将父类声明为 `open`，
> 因为 [类默认是 final 的](inheritance.md)，否则不能被子类化。
> 
> 例如：
>
> ```kotlin
> // 将自定义异常声明为 open 类，使其可以被子类化
> open class MyCustomException(message: String): Exception(message)
>
> // 创建自定义异常的子类
> class SpecificCustomException: MyCustomException("特定错误消息")
> ```
>
{style="note"}

自定义异常的行为与内置异常一样。你可以使用 `throw` 关键字抛出它们，并通过 `try-catch-finally` 块进行处理。
下面是一个示例：

```kotlin
class NegativeNumberException: Exception("参数小于零。")
class NonNegativeNumberException: Exception("参数为非负数。")

fun myFunction(number: Int) {
    if (number < 0) throw NegativeNumberException()
    else if (number >= 0) throw NonNegativeNumberException()
}

fun main() {
    
    // 修改此函数中的值以获取不同的异常
    myFunction(1)
}
```
{kotlin-runnable="true"}

在具有多种错误场景的应用程序中，创建异常层次结构可以使代码更清晰和更具针对性。
你可以通过使用 [抽象类](classes.md#abstract-classes) 或 [密封类](sealed-classes.md#constructors)
作为通用异常功能的基类，并为详细的异常类型创建具体的子类来实现。
此外，具有可选参数的自定义异常提供了灵活性，允许用不同的消息进行初始化，从而实现更细粒度的错误处理。

让我们看一个示例，使用密封类 `AccountException` 作为异常层次结构的基类，`APIKeyExpiredException`
类作为子类，展示了如何使用可选参数来提供更详细的异常信息：

```kotlin
//sampleStart
// 创建一个密封类作为与账户相关的错误的异常层次结构的基类
sealed class AccountException(message: String, cause: Throwable? = null):
    Exception(message, cause)

// 创建 AccountException 的子类
class InvalidAccountCredentialsException : AccountException("检测到无效的账户凭证")

// 创建 AccountException 的子类，允许添加自定义消息和原因
class APIKeyExpiredException(message: String = "API 密钥过期", cause: Throwable? = null) : AccountException(message, cause)

// 修改占位符函数的值以获得不同的结果
fun areCredentialsValid(): Boolean = true
fun isAPIKeyExpired(): Boolean = true
//sampleEnd

// 验证账户凭证和 API 密钥
fun validateAccount() {
    if (!areCredentialsValid()) throw InvalidAccountCredentialsException()
    if (isAPIKeyExpired()) {
        // 示例：抛出带有特定原因的 APIKeyExpiredException
        val cause = RuntimeException("由于网络错误，API 密钥验证失败")
        throw APIKeyExpiredException(cause = cause)
    }
}

fun main() {
    try {
        validateAccount()
        println("操作成功：账户凭证和 API 密钥有效。")
    } catch (e: AccountException) {
        println("错误：${e.message}")
        e.cause?.let { println("原因：${it.message}") }
    }
}
```
{kotlin-runnable="true"}

## Nothing 类型

在 Kotlin 中，每个表达式都有一个类型。
表达式 `throw IllegalArgumentException()` 的类型是 [`Nothing`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-nothing.html)，
这是一个内置类型，是所有其他类型的子类型，也被称为 [底层类型](https://en.wikipedia.org/wiki/Bottom_type)。
这意味着 `Nothing` 可以用作返回类型或泛型类型，适用于任何其他类型而不会引起类型错误。

`Nothing` 是 Kotlin 中的一个特殊类型，用于表示永远不会成功完成的函数或表达式，
无论是因为它们总是抛出异常还是进入无限执行路径，如无限循环。
你可以使用 `Nothing` 来标记尚未实现的函数或设计为总是抛出异常的函数，
清晰地向编译器和代码读者表明你的意图。
如果编译器在函数签名中推断出 `Nothing` 类型，它会发出警告。
显式地定义 `Nothing` 作为返回类型可以消除这种警告。

这段 Kotlin 代码展示了 `Nothing` 类型的使用，其中编译器标记了函数调用后的代码为不可达代码：

```kotlin
class Person(val name: String?)

fun fail(message: String): Nothing {
    throw IllegalArgumentException(message)
    // 这个函数永远不会成功返回。
    // 它总是会抛出异常。
}

fun main() {
    // 创建一个 Person 实例，其中 'name' 为 null
    val person = Person(name = null)
    
    val s: String = person.name ?: fail("Name required")

    // 此时 's' 保证已被初始化
    println(s)
}
```
{kotlin-runnable="true"}

Kotlin 的 [`TODO()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-t-o-d-o.html) 函数，也使用了
`Nothing` 类型，作为占位符，突出显示代码中需要将来实现的区域：

```kotlin
fun notImplementedFunction(): Int {
    TODO("This function is not yet implemented")
}

fun main() {
    val result = notImplementedFunction()
    // 这会抛出 NotImplementedError
    println(result)
}
```
{kotlin-runnable="true"}

如你所见，`TODO()` 函数总是会抛出一个 [`NotImplementedError`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-not-implemented-error/) 异常。

## 异常类 {id=exception-classes}

让我们来探索一些 Kotlin 中常见的异常类型，它们都是 [`RuntimeException`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-runtime-exception/) 类的子类：

* [`ArithmeticException`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-arithmetic-exception/): 当无法执行某个算术操作时，比如除以零时，就会发生这个异常。

    ```kotlin
    val example = 2 / 0 // 抛出 ArithmeticException
    ```

* [`IndexOutOfBoundsException`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-index-out-of-bounds-exception/): 这个异常在某种索引超出范围时抛出，比如数组或字符串的索引超出了有效范围。

    ```kotlin
    val myList = mutableListOf(1, 2, 3)
    myList.removeAt(3)  // 抛出 IndexOutOfBoundsException
    ```

    > 为了避免这个异常，可以使用更安全的替代方案，比如 [`getOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/get-or-null.html) 函数：
    > 
    > ```kotlin
    > val myList = listOf(1, 2, 3)
    > // 返回 null，而不是 IndexOutOfBoundsException
    > val element = myList.getOrNull(3)
    > println("Index 3 处的元素: $element")
    > ```
    >
    {style="note"}

* [`NoSuchElementException`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-no-such-element-exception/): 当访问某个集合中不存在的元素时，就会抛出这个异常。
它发生在使用期望特定元素的方法时，例如 [`first()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/first.html)、
[`last()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/last.html) 或
[`elementAt()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/element-at.html)。

    ```kotlin
    val emptyList = listOf<Int>()
    val firstElement = emptyList.first()  // 抛出 NoSuchElementException
    ```

    > 避免此异常的方法是使用更安全的替代方案，例如 [`firstOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/first-or-null.html) 函数：
    >
    > ```kotlin
    > val emptyList = listOf<Int>()
    > // 返回 null，而不是 NoSuchElementException
    > val firstElement = emptyList.firstOrNull()
    > println("空列表中的第一个元素: $firstElement")
    > ```
    >
    {style="note"}

* [`NumberFormatException`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-number-format-exception/): 当尝试将字符串转换为数字类型，但字符串格式不正确时，就会发生这个异常。

    ```kotlin
    val string = "This is not a number"
    val number = string.toInt() // 抛出 NumberFormatException
    ```
    
    > 为了避免这个异常，可以使用更安全的替代方案，例如 [`toIntOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/to-int-or-null.html) 函数：
    >
    > ```kotlin
    > val nonNumericString = "not a number"
    > // 返回 null，而不是 NumberFormatException
    > val number = nonNumericString.toIntOrNull()
    > println("转换后的数字: $number")
    > ```
    >
    {style="note"}

* [`NullPointerException`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-null-pointer-exception/): 当应用程序尝试使用值为 `null` 的对象引用时，就会抛出这个异常。
尽管 Kotlin 的空安全特性显著减少了 `NullPointerException` 的风险，但它们仍可能发生，例如通过故意使用 `!!` 操作符或与不具备
Kotlin 空安全特性的 Java 进行交互时。

    ```kotlin
    val text: String? = null
    println(text!!.length)  // 抛出 NullPointerException
    ```

虽然 Kotlin 中所有异常都是未检查异常，你不必显式捕获它们，但你仍然可以根据需要捕获它们。

### 异常层次结构 {id=exception-hierarchy}

Kotlin 异常层次结构的根是 [`Throwable`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-throwable/) 类。
它有两个直接子类，[`Error`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-error/) 和 [`Exception`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-exception/):

* `Error` 子类表示应用程序可能无法自行恢复的严重基础问题。
这些是你通常不会尝试处理的问题，例如 [`OutOfMemoryError`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-out-of-memory-error/) 或 `StackOverflowError`。

* `Exception` 子类用于你可能希望处理的情况。
`Exception` 类型的子类型，如 [`RuntimeException`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-runtime-exception/) 和 `IOException`（输入/输出异常），处理应用程序中的异常事件。

![异常层次结构 - Throwable 类](throwable.svg){width=700}

`RuntimeException` 通常是由于程序代码中检查不充分造成的，可以通过编程手段来防止。
Kotlin 帮助防止常见的 `RuntimeExceptions`，如 `NullPointerException`，并提供编译时警告，以避免潜在的运行时错误，如除零错误。
下图展示了 `RuntimeException` 的子类型层次结构：

![RuntimeExceptions 的层次结构](runtime-exception.svg){width=700}

## 堆栈跟踪 {id=stack-trace}

_堆栈跟踪_ 是由运行时环境生成的报告，用于调试。
它显示了导致程序中特定点的函数调用序列，尤其是发生错误或异常的地方。

让我们来看一个例子，在 JVM 环境中，由于异常，堆栈跟踪会自动打印出来：

```kotlin
fun main() {
//sampleStart    
    throw ArithmeticException("This is an arithmetic exception!")
//sampleEnd    
}
```
{kotlin-runnable="true"}

在 JVM 环境中运行此代码会生成以下输出：

```text
Exception in thread "main" java.lang.ArithmeticException: This is an arithmetic exception!
    at MainKt.main(Main.kt:3)
    at MainKt.main(Main.kt)
```

第一行是异常描述，包括：

* 异常类型：`java.lang.ArithmeticException`
* 线程：`main`
* 异常消息：`"This is an arithmetic exception!"`

在异常描述之后，以 `at` 开头的每一行都是堆栈跟踪。一行堆栈跟踪被称为 _堆栈跟踪元素_ 或 _堆栈帧_：

* `at MainKt.main (Main.kt:3)`：显示方法名称（`MainKt.main`）以及调用该方法的源文件和行号（`Main.kt:3`）。
* `at MainKt.main (Main.kt)`：表示异常发生在 `Main.kt` 文件的 `main()` 函数中。

## Kotlin 与 Java、Swift 和 Objective-C 的异常互操作性 {id=exception-interoperability-with-java-swift-and-objective-c}

由于 Kotlin 将所有异常视为未检查异常，这可能会导致从区分检查异常和未检查异常的语言中调用这些异常时出现复杂情况。
为了解决 Kotlin 与 Java、Swift 和 Objective-C 等语言之间的异常处理差异，你可以使用
[`@Throws`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-throws/) 注解。
这个注解会提醒调用者可能会抛出异常。有关更多信息，请参见 [从 Java 调用 Kotlin](java-to-kotlin-interop.md#checked-exceptions) 和
[与 Swift/Objective-C 的互操作性](native-objc-interop.md#errors-and-exceptions)。
