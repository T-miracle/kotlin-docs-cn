[//]: # (title: 密封类和接口)

[//]: # (TODO: 等待校验)

**密封** 类和接口提供了对类层次结构的控制继承。  
所有直接子类在编译时都是已知的。
其他子类不能出现在密封类定义的模块和包之外。
同样的逻辑也适用于密封接口及其实现：一旦包含密封接口的模块被编译，就不能创建新的实现。

> 直接子类是那些直接继承自其超类的类。  
> 间接子类是那些从其超类下多级继承的类。
>
{style="note"}

当你将密封类和接口与 `when` 表达式结合使用时，你可以覆盖所有可能的子类的行为，并确保不会创建新的子类来对你的代码产生不利影响。

密封类最适用于以下场景：

* **需要限制类的继承：** 你有一个预定义的、有限的子类集合，这些子类在编译时都是已知的。
* **需要类型安全设计：** 安全性和模式匹配在你的项目中至关重要，尤其是在状态管理或处理复杂的条件逻辑时。有关示例，请查看 [使用密封类与 when 表达式](#use-sealed-classes-with-when-expression)。
* **处理封闭的 API：** 你希望为库提供稳健且易于维护的公共 API，确保第三方客户端按照预期使用这些 API。

有关更详细的实际应用，请参阅 [用例场景](#use-case-scenarios)。

> Java 15 引入了 [类似的概念](https://docs.oracle.com/en/java/javase/15/language/sealed-classes-and-interfaces.html#GUID-0C709461-CC33-419A-82BF-61461336E65F)，  
> 在那里，密封类使用 `sealed` 关键字以及 `permits` 子句来定义受限的层次结构。
>
{style="tip"}

## 声明一个密封类或接口 {id=declare-a-sealed-class-or-interface}

要声明一个密封类或接口，使用 `sealed` 修饰符：

```kotlin
// 创建一个密封接口
sealed interface Error

// 创建一个实现密封接口 Error 的密封类
sealed class IOError(): Error

// 定义扩展密封类 'IOError' 的子类
class FileReadError(val file: File): IOError()
class DatabaseError(val source: DataSource): IOError()

// 创建一个实现 'Error' 密封接口的单例对象
object RuntimeError : Error
```

这个示例可以表示一个库的 API，其中包含错误类，允许库用户处理库可能抛出的错误。  
如果这种错误类的层次结构包括在公共 API 中可见的接口或抽象类，那么没有什么能阻止其他开发者在客户端代码中实现或扩展它们。  
由于库无法知道库外部声明的错误，因此无法将它们与自己的类一致地处理。  
然而，通过使用 **密封** 的错误类层次结构，库作者可以确保他们知道所有可能的错误类型，并且其他错误类型不能后来出现。

该示例的层次结构如下所示：  

![密封类和接口的层次结构插图](sealed-classes-interfaces.svg){width=700 style=block}

### 构造函数 {id=constructors}

一个密封类本身总是一个 [抽象类](classes.md#abstract-classes)，因此不能直接实例化。  
然而，它可以包含或继承构造函数。
这些构造函数不是用来创建密封类本身的实例，而是用于其子类。
考虑以下示例，其中有一个名为 `Error` 的密封类及其几个子类，我们对其进行实例化：

```kotlin
sealed class Error(val message: String) {
    class NetworkError : Error("网络故障")
    class DatabaseError : Error("数据库无法访问")
    class UnknownError : Error("发生未知错误")
}

fun main() {
    val errors = listOf(Error.NetworkError(), Error.DatabaseError(), Error.UnknownError())
    errors.forEach { println(it.message) }
}
// 网络故障
// 数据库无法访问
// 发生未知错误
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.5"}

你可以在密封类中使用 [`enum`](enum-classes.md) 类，通过枚举常量来表示状态并提供额外的细节。
每个枚举常量只有 **一个** 实例，而密封类的子类可以有 **多个** 实例。  
在这个示例中，`sealed class Error` 及其几个子类使用 `enum` 来表示错误的严重性。
每个子类构造函数初始化了 `severity`，并可以修改其状态：

```kotlin
enum class ErrorSeverity { MINOR, MAJOR, CRITICAL }

sealed class Error(val severity: ErrorSeverity) {
    class FileReadError(val file: File): Error(ErrorSeverity.MAJOR)
    class DatabaseError(val source: DataSource): Error(ErrorSeverity.CRITICAL)
    object RuntimeError : Error(ErrorSeverity.CRITICAL)
    // 可以在这里添加更多的错误类型
}
```

密封类的构造函数可以具有以下两种 [可见性](visibility-modifiers.md)：`protected`（默认）或 `private`：

```kotlin
sealed class IOError {
    // 密封类的构造函数默认具有受保护的可见性。它在这个类及其子类中可见
    constructor() { /*...*/ }

    // 私有构造函数，仅在这个类内部可见。  
    // 在密封类中使用私有构造函数可以对实例化进行更严格的控制，允许在类内部进行特定的初始化过程。
    private constructor(description: String): this() { /*...*/ }

    // 这将引发错误，因为密封类不允许公共和内部构造函数
    // public constructor(code: Int): this() {} 
}
```

## 继承 {id=inheritance}

密封类和接口的直接子类必须在同一个包中声明。它们可以是顶层的，也可以嵌套在任何数量的其他命名类、命名接口或命名对象中。
子类可以具有任何 [可见性](visibility-modifiers.md)，只要它们与 Kotlin 中的正常继承规则兼容。

密封类的子类必须具有适当的限定名称。它们不能是局部或匿名对象。

> `enum` 类不能扩展密封类或任何其他类。然而，它们可以实现密封接口：
>
> ```kotlin
> sealed interface Error
> 
> // enum 类实现密封接口 Error
> enum class ErrorType : Error {
>     FILE_ERROR, DATABASE_ERROR
> }
> 
> ```
> 
{style="note"}

这些限制不适用于间接子类。
如果一个密封类的直接子类没有被标记为密封，它可以以其修饰符允许的任何方式进行扩展：

```kotlin
// 密封接口 'Error' 只有在同一个包和模块中有实现
sealed interface Error

// 密封类 'IOError' 扩展了 'Error'，并且仅在同一个包内可扩展
sealed class IOError(): Error

// 开放类 'CustomError' 扩展了 'Error'，可以在任何可见的地方进行扩展
open class CustomError(): Error
```

### 跨平台项目中的继承 {id=inheritance-in-multiplatform-projects}

在 [跨平台项目](multiplatform-intro.md) 中，还有一个继承限制：密封类的直接子类必须位于同一个
[源代码集](multiplatform-discover-project.md#source-sets) 中。
这适用于没有 [expected 和 actual 修饰符](multiplatform-expect-actual.md) 的密封类。

如果一个密封类在公共源代码集中声明为 `expect` 并在平台源代码集中有 `actual`
实现，`expect` 和 `actual` 版本都可以在它们的源代码集中有子类。
此外，如果使用层次结构，你可以在 `expect` 和 `actual` 声明之间的任何源代码集中创建子类。

[了解更多关于跨平台项目的层次结构](multiplatform-hierarchy.md)。

## 使用密封类与 when 表达式 {id=use-sealed-classes-with-when-expression}

使用密封类的主要好处体现在将它们与 [`when`](control-flow.md#when-expressions-and-statements) 表达式结合使用时。  
当 `when` 表达式与密封类一起使用时，Kotlin 编译器能够进行全面检查，确保所有可能的情况都已覆盖。  
在这种情况下，你无需添加 `else` 子句：

```kotlin
// 密封类及其子类
sealed class Error {
    class FileReadError(val file: String): Error()
    class DatabaseError(val source: String): Error()
    object RuntimeError : Error()
}

//sampleStart
// 记录错误的函数
fun log(e: Error) = when(e) {
    is Error.FileReadError -> println("读取文件 ${e.file} 时出错")
    is Error.DatabaseError -> println("从数据库 ${e.source} 读取时出错")
    Error.RuntimeError -> println("运行时错误")
    // 不需要 `else` 子句，因为所有情况都已覆盖
}
//sampleEnd

// 列出所有错误
fun main() {
    val errors = listOf(
        Error.FileReadError("example.txt"),
        Error.DatabaseError("usersDatabase"),
        Error.RuntimeError
    )

    errors.forEach { log(it) }
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.5"}

> 在跨平台项目中，如果你在公共代码中有一个带有 `when` 表达式的密封类作为
> [expected 声明](multiplatform-expect-actual.md)，你仍然需要一个 `else` 分支。  
> 这是因为 `actual` 平台实现的子类可能扩展了在公共代码中未知的密封类。
>
{style="note"}

## 用例场景 {id=use-case-scenarios}

让我们探讨一些实际场景，其中密封类和接口特别有用。

### UI 应用中的状态管理 {id=state-management-in-ui-applications}

你可以使用密封类来表示应用中的不同 UI 状态。  
这种方法允许对 UI 更改进行结构化和安全的处理。  
以下示例演示了如何管理各种 UI 状态：

```kotlin
sealed class UIState { 
    data object Loading : UIState()
    data class Success(val data: String) : UIState()
    data class Error(val exception: Exception) : UIState()
}

fun updateUI(state: UIState) { 
    when (state) {
        is UIState.Loading -> showLoadingIndicator()
        is UIState.Success -> showData(state.data)
        is UIState.Error -> showError(state.exception) 
    }
}
```

### 支付方式处理 {id=payment-method-handling}

在实际的业务应用中，高效地处理各种支付方式是一个常见的需求。  
你可以使用密封类和 `when` 表达式来实现这样的业务逻辑。  
通过将不同的支付方式表示为密封类的子类，可以建立一个清晰且易于管理的交易处理结构：

```kotlin
sealed class Payment {
    data class CreditCard(val number: String, val expiryDate: String) : Payment()
    data class PayPal(val email: String) : Payment()
    data object Cash : Payment()
}

fun processPayment(payment: Payment) { 
    when (payment) {
        is Payment.CreditCard -> processCreditCardPayment(payment.number, payment.expiryDate)
        is Payment.PayPal -> processPayPalPayment(payment.email)
        is Payment.Cash -> processCashPayment() 
    }
}
```

`Payment` 是一个密封类，用于表示电子商务系统中的不同支付方式：  
`CreditCard`、`PayPal` 和 `Cash`。
每个子类可以具有特定的属性，如 `CreditCard` 的 `number` 和 `expiryDate`，以及 `PayPal` 的 `email`。

`processPayment()` 函数演示了如何处理不同的支付方式。  
这种方法确保考虑到所有可能的支付类型，并且系统在将来添加新的支付方式时保持灵活性。

### API 请求-响应处理 {id=api-request-response-handling}

你可以使用密封类和密封接口来实现一个处理 API 请求和响应的用户认证系统。  
该用户认证系统具有登录和登出功能。  
`ApiRequest` 密封接口定义了特定的请求类型：`LoginRequest` 用于登录，`LogoutRequest` 用于登出操作。  
密封类 `ApiResponse` 封装了不同的响应场景：`UserSuccess` 包含用户数据，`UserNotFound` 表示用户不存在，`Error` 处理任何失败。
`handleRequest` 函数使用 `when` 表达式以类型安全的方式处理这些请求，同时 `getUserById` 模拟用户检索：

```kotlin
sealed interface ApiRequest
data class LoginRequest(val username: String, val password: String) : ApiRequest
data class LogoutRequest(val userId: String) : ApiRequest

sealed class ApiResponse
data class UserSuccess(val userData: String) : ApiResponse()
object UserNotFound : ApiResponse()
data class Error(val message: String) : ApiResponse()

fun handleRequest(request: ApiRequest): ApiResponse {
    return when (request) {
        is LoginRequest -> {
            val user = getUserById(request.username)
            if (user != null) UserSuccess(user) else UserNotFound
        }
        is LogoutRequest -> {
            // 处理登出逻辑
            UserSuccess("成功登出")
        }
    }
}

fun getUserById(username: String): String? {
    // 模拟用户检索
    return if (username == "validUser") "UserDataFor_$username" else null
}
```

```kotlin
// 导入必要的模块
import io.ktor.server.application.*
import io.ktor.server.resources.*

import kotlinx.serialization.*

// 使用 Ktor 资源定义 API 请求的密封接口
@Resource("api")
sealed interface ApiRequest

@Serializable
@Resource("login")
data class LoginRequest(val username: String, val password: String) : ApiRequest

@Serializable
@Resource("logout")
object LogoutRequest : ApiRequest

// 定义带有详细响应类型的 ApiResponse 密封类
sealed class ApiResponse {
    data class UserSuccess(val user: UserData) : ApiResponse()
    data object UserNotFound : ApiResponse()
    data class Error(val message: String) : ApiResponse()
}

// 用于成功响应的用户数据类
data class UserData(val userId: String, val name: String, val email: String)

// 验证用户凭据的函数（用于演示）
fun isValidUser(username: String, password: String): Boolean {
    // 一些验证逻辑（仅为占位符）
    return username == "validUser" && password == "validPass"
}

// 处理 API 请求并提供详细响应的函数
fun handleRequest(request: ApiRequest): ApiResponse {
    return when (request) {
        is LoginRequest -> {
            if (isValidUser(request.username, request.password)) {
                ApiResponse.UserSuccess(UserData("userId", "userName", "userEmail"))
            } else {
                ApiResponse.Error("无效的用户名或密码")
            }
        }
        is LogoutRequest -> {
            // 假设登出操作在此示例中总是成功
            ApiResponse.UserSuccess(UserData("userId", "userName", "userEmail")) // 用于演示
        }
    }
}

// 模拟 getUserById 调用的函数
fun getUserById(userId: String): ApiResponse {
    return if (userId == "validUserId") {
        ApiResponse.UserSuccess(UserData("validUserId", "John Doe", "john@example.com"))
    } else {
        ApiResponse.UserNotFound
    }
    // 错误处理也会导致 Error 响应。
}

// 主函数以演示用法
fun main() {
    val loginResponse = handleRequest(LoginRequest("user", "pass"))
    println(loginResponse)

    val logoutResponse = handleRequest(LogoutRequest)
    println(logoutResponse)

    val userResponse = getUserById("validUserId")
    println(userResponse)

    val userNotFoundResponse = getUserById("invalidId")
    println(userNotFoundResponse)
}
```
