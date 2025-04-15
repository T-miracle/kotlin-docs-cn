[//]: # (title: 预期声明和实际声明)

预期声明和实际声明允许你从 Kotlin 跨平台模块访问平台特定的 API。你可以在通用代码中提供与平台无关的 API。

> 本文描述了预期声明和实际声明的语言机制。关于使用平台特定 API 的一般建议，请参阅
> [使用平台特定 API](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-connect-to-apis.html)。
>
{style="tip"}

## 预期声明和实际声明的规则 {id="rules-for-expected-and-actual-declarations"}

要定义预期声明和实际声明，请遵循以下规则：

1. 在通用源代码集中，声明一个标准的 Kotlin 构造。
   这可以是一个函数、属性、类、接口、枚举或注解。
2. 使用 `expect` 关键字标记这个构造。这是你的 _预期声明_。
   这些声明可以在通用代码中使用，但不应包含任何实现。相应的实现由平台特定代码提供。
3. 在每个平台特定的源代码集中，在相同的包中声明相同的构造，并使用 `actual` 关键字标记它。
   这是你的 _实际声明_，通常包含使用平台特定库的实现。

在为特定目标进行编译时，编译器会尝试将找到的每个 _实际_ 声明与通用代码中的对应 _预期_ 声明匹配。编译器会确保：

* 通用源代码集中的每个预期声明在每个平台特定的源代码集中都有一个匹配的实际声明。
* 预期声明不包含任何实现。
* 每个实际声明与对应的预期声明共享相同的包名，例如 `org.mygroup.myapp.MyType`。

在为不同平台生成最终代码时，Kotlin 编译器会合并相互对应的预期声明和实际声明。
它为每个平台生成一个具有实际实现的声明。
在通用代码中对预期声明的每次使用都会在生成的目标平台代码中调用正确的实际声明。

当你使用在不同目标平台之间共享的中间源代码集时，可以声明实际声明。
例如，考虑 `iosMain` 作为一个在 `iosX64Main`、
`iosArm64Main` 和 `iosSimulatorArm64Main` 平台源代码集之间共享的中间源代码集。通常只有 `iosMain` 包含实际声明，而不是平台源代码集。
然后，Kotlin 编译器将使用这些实际声明来生成对应平台的最终代码。

IDE 会帮助你解决常见问题，包括：

* 缺少声明
* 包含实现的预期声明
* 声明签名不匹配
* 不同包中的声明

你还可以使用 IDE 从预期声明导航到实际声明。选择行号区域的图标查看实际声明，或使用
[快捷键](https://www.jetbrains.com/help/idea/navigating-through-the-source-code.html#go_to_implementation)。

![IDE 从预期声明导航到实际声明](expect-actual-gutter.png){width=500}

## 使用预期和实际声明的不同方法 {id="different-approaches-for-using-expected-and-actual-declarations"}

让我们探讨使用 `expect/actual` 机制解决访问平台 API 问题的不同选择，同时仍然提供在通用代码中处理这些 API 的方法。

假设你有一个 Kotlin 跨平台项目，需要实现 `Identity` 类型，该类型应包含用户的登录名和当前进程 ID。
该项目有 `commonMain`、`jvmMain` 和 `nativeMain` 源代码集，以便让应用程序在 JVM 和像 iOS 这样的原生环境中运行。

### 预期函数和实际函数 {id="expected-and-actual-functions"}

你可以定义一个 `Identity` 类型和一个工厂函数 `buildIdentity()`，它在通用源代码集中声明，并在平台源代码集中有不同的实现：

1. 在 `commonMain` 中，声明一个简单的类型并预期一个工厂函数：

   ```kotlin
   package identity

   class Identity(val userName: String, val processID: Long)
  
   expect fun buildIdentity(): Identity
   ```

2. 在 `jvmMain` 源代码集中，使用标准 Java 库实现解决方案：

   ```kotlin
   package identity
  
   import java.lang.System
   import java.lang.ProcessHandle

   actual fun buildIdentity() = Identity(
       System.getProperty("user.name") ?: "None",
       ProcessHandle.current().pid()
   )
   ```

3. 在 `nativeMain` 源代码集中，使用 [POSIX](https://en.wikipedia.org/wiki/POSIX) 和原生依赖项实现解决方案：

   ```kotlin
   package identity
  
   import kotlinx.cinterop.toKString
   import platform.posix.getlogin
   import platform.posix.getpid

   actual fun buildIdentity() = Identity(
       getlogin()?.toKString() ?: "None",
       getpid().toLong()
   )
   ```

   此处，平台函数返回平台特定的 `Identity` 实例。

> 从 Kotlin 1.9.0 开始，使用 `getlogin()` 和 `getpid()` 函数需要使用 `@OptIn` 注解。
>
{style="note"}

### 带有预期函数和实际函数的接口 {id="interfaces-with-expected-and-actual-functions"}

如果工厂函数变得过于庞大，可以考虑使用一个通用的 `Identity` 接口，并在不同平台上以不同方式实现它。

一个 `buildIdentity()` 工厂函数应该返回 `Identity`，但这次它是一个实现了通用接口的对象：

1. 在 `commonMain` 中，定义 `Identity` 接口和 `buildIdentity()` 工厂函数：

   ```kotlin
   // 在 commonMain 源代码集中：
   expect fun buildIdentity(): Identity
   
   interface Identity {
       val userName: String
       val processID: Long
   }
   ```

2. 在平台特定的源代码集中创建接口的实现，无需额外使用预期声明和实际声明：

   ```kotlin
   // 在 jvmMain 源代码集中：
   actual fun buildIdentity(): Identity = JVMIdentity()

   class JVMIdentity(
       override val userName: String = System.getProperty("user.name") ?: "none",
       override val processID: Long = ProcessHandle.current().pid()
   ) : Identity
   ```

   ```kotlin
   // 在 nativeMain 源代码集中：
   actual fun buildIdentity(): Identity = NativeIdentity()
  
   class NativeIdentity(
       override val userName: String = getlogin()?.toKString() ?: "None",
       override val processID: Long = getpid().toLong()
   ) : Identity
   ```

这些平台函数返回平台特定的 `Identity` 实例，分别实现为 `JVMIdentity` 和 `NativeIdentity` 平台类型。

#### 预期属性和实际属性 {id="expected-and-actual-properties"}

你可以修改之前的示例，预期一个 `val` 属性来存储 `Identity`。

将该属性标记为 `expect val`，然后在平台源代码集中进行实际化：

```kotlin
// 在 commonMain 源代码集中：
expect val identity: Identity

interface Identity {
    val userName: String
    val processID: Long
}
```

```kotlin
// 在 jvmMain 源代码集中：
actual val identity: Identity = JVMIdentity()

class JVMIdentity(
    override val userName: String = System.getProperty("user.name") ?: "none",
    override val processID: Long = ProcessHandle.current().pid()
) : Identity
```

```kotlin
// 在 nativeMain 源代码集中：
actual val identity: Identity = NativeIdentity()

class NativeIdentity(
    override val userName: String = getlogin()?.toKString() ?: "None",
    override val processID: Long = getpid().toLong()
) : Identity
```

#### 预期对象和实际对象 {id="expected-and-actual-objects"}

当 `IdentityBuilder` 预期在每个平台上都是一个单例时，你可以将其定义为预期对象，并让各个平台进行实际化：

```kotlin
// 在 commonMain 源代码集中：
expect object IdentityBuilder {
    fun build(): Identity
}

class Identity(
    val userName: String,
    val processID: Long
)
```

```kotlin
// 在 jvmMain 源代码集中：
actual object IdentityBuilder {
    actual fun build() = Identity(
        System.getProperty("user.name") ?: "none",
        ProcessHandle.current().pid()
    )
}
```

```kotlin
// 在 nativeMain 源代码集中：
actual object IdentityBuilder {
    actual fun build() = Identity(
        getlogin()?.toKString() ?: "None",
        getpid().toLong()
    )
}
```

#### 关于依赖注入的建议 {id="recommendations-on-dependency-injection"}

为了创建松耦合的架构，许多 Kotlin 项目采用了依赖注入（DI）框架。
DI 框架允许根据当前环境将依赖项注入到组件中。

例如，你可能在测试环境和生产环境中注入不同的依赖，或者在云端部署时与本地托管时注入不同的依赖。
只要依赖项通过接口表达，就可以在编译时或运行时注入任意数量的不同实现。

当依赖项是平台特定的时，相同的原则适用。在通用代码中，组件可以使用常规的 [Kotlin 接口](interfaces.md) 表达其依赖项。
然后，DI 框架可以配置为注入平台特定的实现，例如来自 JVM 或 iOS 模块的实现。

这意味着预期声明和实际声明仅在 DI 框架的配置中需要。有关示例，请参阅
[使用平台特定 API](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-connect-to-apis.html#dependency-injection-framework)。

使用这种方法，你可以通过使用接口和工厂函数来简单地使用 Kotlin 跨平台。
如果你已经在项目中使用 DI 框架来管理依赖，建议使用相同的方法来管理平台依赖。

### 预期类和实际类 {id="expected-and-actual-classes"}

> 预期类和实际类目前处于 [Beta](components-stability.md) 阶段。
> 它们几乎稳定，但未来可能需要迁移步骤。
> 我们会尽力将进一步的更改降到最低。
>
{style="warning"}

你可以使用预期和实际类来实现相同的解决方案：

```kotlin
// 在 commonMain 源代码集中：
expect class Identity() {
    val userName: String
    val processID: Int
}
```

```kotlin
// 在 jvmMain 源代码集中：
actual class Identity {
    actual val userName: String = System.getProperty("user.name") ?: "None"
    actual val processID: Long = ProcessHandle.current().pid()
}
```

```kotlin
// 在 nativeMain 源代码集中：
actual class Identity {
    actual val userName: String = getlogin()?.toKString() ?: "None"
    actual val processID: Long = getpid().toLong()
}
```

你可能已经在演示材料中看到过这种方法。
然而，在简单的情况下，**不推荐** 使用类代替接口。

使用接口时，你不会将设计限制为每个目标平台一个实现。
此外，在测试中用假实现代替真实实现或者在单个平台上提供多个实现也要容易得多。

作为一般规则，尽可能依赖标准语言构造，而不是使用预期声明和实际声明。

如果你决定使用预期和实际类，Kotlin 编译器会警告你该功能处于 Beta 状态。
要抑制此警告，请在你的 Gradle 构建文件中添加以下编译器选项：

```kotlin
kotlin {
    compilerOptions {
        // 应用于所有 Kotlin 源代码集的通用编译器选项
        freeCompilerArgs.add("-Xexpect-actual-classes")
    }
}
```

#### 从平台类继承 {id=inheritance-from-platform-classes}

使用 `expect` 关键字与类一起可能是最佳方法。
在 JVM 上，假设 `Identity` 类型已经存在：

```kotlin
open class Identity {
    val login: String = System.getProperty("user.name") ?: "none"
    val pid: Long = ProcessHandle.current().pid()
}
```

为了适配现有的代码库和框架，你的 `Identity` 类型实现可以继承自这个类型并重用它的功能：

1. 为了解决这个问题，在 `commonMain` 中声明一个使用 `expect` 关键字的类：

   ```kotlin
   expect class CommonIdentity() {
       val userName: String
       val processID: Long
   }
   ```

2. 在 `nativeMain` 中，提供一个实际声明以实现功能：

   ```kotlin
   actual class CommonIdentity {
       actual val userName = getlogin()?.toKString() ?: "None"
       actual val processID = getpid().toLong()
   }
   ```

3. 在 `jvmMain` 中，提供一个实际声明，继承自平台特定的基类：

   ```kotlin
   actual class CommonIdentity : Identity() {
       actual val userName = login
       actual val processID = pid
   }
   ```

在这里，`CommonIdentity` 类型与自己的设计兼容，同时利用了 JVM 上现有的类型。

#### 框架中的应用 {id=application-in-frameworks}

作为框架作者，你也可以发现 `expect` 和 `actual` 声明对你的框架很有用。

如果上面的示例是框架的一部分，用户需要从 `CommonIdentity` 派生一个类型以提供显示名称。

在这种情况下，预期声明是抽象的，并声明一个抽象方法：

```kotlin
// 在框架代码库的 commonMain 中：
expect abstract class CommonIdentity() {
    val userName: String
    val processID: Long
    abstract val displayName: String
}
```

类似地，实际实现是抽象的，并声明 `displayName` 方法：

```kotlin
// 在框架代码库的 nativeMain 中：
actual abstract class CommonIdentity {
    actual val userName = getlogin()?.toKString() ?: "None"
    actual val processID = getpid().toLong()
    actual abstract val displayName: String
}
```

```kotlin
// 在框架代码库的 jvmMain 中：
actual abstract class CommonIdentity : Identity() {
    actual val userName = login
    actual val processID = pid
    actual abstract val displayName: String
}
```

框架用户需要编写从预期声明继承的通用代码，并自行实现缺失的方法：

```kotlin
// 在用户代码库的 commonMain 中：
class MyCommonIdentity : CommonIdentity() {
    override val displayName = "Admin"
}
```

<!-- 类似的方案适用于任何为 Android 或 iOS 开发提供通用 `ViewModel` 的库。
这样的库通常提供一个预期的 `CommonViewModel` 类，其实际的 Android 对应类扩展了 Android 框架中的 `ViewModel` 类。
有关此示例的详细描述，请参见
[使用平台特定 API](multiplatform-connect-to-apis.md#adapting-to-an-existing-hierarchy-using-expected-actual-classes)。 -->

## 高级用例 {id=advanced-use-cases}

关于预期声明和实际声明，有一些特殊的情况。

### 使用类型别名满足实际声明 {id=using-type-aliases-to-satisfy-actual-declarations}

实际声明的实现不必从头编写。它可以是现有的类型，例如第三方库提供的类。

只要满足所有与预期声明相关的要求，你可以使用这些类型。例如，考虑这两个预期声明：

```kotlin
expect enum class Month {
    JANUARY, FEBRUARY, MARCH, APRIL, MAY, JUNE, JULY,
    AUGUST, SEPTEMBER, OCTOBER, NOVEMBER, DECEMBER
}

expect class MyDate {
    fun getYear(): Int
    fun getMonth(): Month
    fun getDayOfMonth(): Int
}
```

在 JVM 模块中，可以使用 `java.time.Month` 枚举来实现第一个预期声明，使用 `java.time.LocalDate` 类来实现第二个。
然而，不能直接将 `actual` 关键字添加到这些类型。

相反，你可以使用 [类型别名](type-aliases.md) 将预期声明与平台特定类型连接起来：

```kotlin
actual typealias Month = java.time.Month
actual typealias MyDate = java.time.LocalDate
```

在这种情况下，将 `typealias` 声明定义在与预期声明相同的包中，并在其他地方创建所引用的类。

> 由于 `LocalDate` 类型使用了 `Month` 枚举，你需要在公共代码中将它们都声明为预期类。
>
{style="note"}

<!-- 请参见
[使用平台特定 API](multiplatform-connect-to-apis.md#actualizing-an-interface-or-a-class-with-an-existing-platform-class-using-typealiases)。 -->

### 扩展实际声明的可见性 {id=expanded-visibility-in-actual-declarations}

你可以使实际实现比对应的预期声明具有更高的可见性。
如果你不想将你的 API 公开给公共客户端，这会很有用。

目前，Kotlin 编译器会在可见性更改的情况下发出错误。
你可以通过将 `@Suppress("ACTUAL_WITHOUT_EXPECT")` 应用于实际类型别名声明来抑制这个错误。
从 Kotlin 2.0 开始，这个限制将不再适用。

例如，如果你在公共源代码集中声明以下预期声明：

```kotlin
internal expect class Messenger {
    fun sendMessage(message: String)
}
```

你也可以在平台特定的源代码集中使用以下实际实现：

```kotlin
@Suppress("ACTUAL_WITHOUT_EXPECT")
public actual typealias Messenger = MyMessenger
```

在这里，一个内部的预期类通过类型别名有一个现有的公共 `MyMessenger` 实现。

### 实现时的附加枚举条目 {id=additional-enumeration-entries-on-actualization}

当在公共源代码集中使用 `expect` 声明一个枚举时，每个平台模块应该有一个对应的 `actual` 声明。
这些声明必须包含相同的枚举常量，但也可以包含额外的常量。

这在用现有的平台枚举实现预期枚举时很有用。
例如，考虑以下在公共源代码集中声明的枚举：

```kotlin
// 在 commonMain 源代码集中：
expect enum class Department { IT, HR, Sales }
```

当你在平台源代码集中提供 `Department` 的实际声明时，你可以添加额外的常量：

```kotlin
// 在 jvmMain 源代码集中：
actual enum class Department { IT, HR, Sales, Legal }
```

```kotlin
// 在 nativeMain 源代码集中：
actual enum class Department { IT, HR, Sales, Marketing }
```

然而，在这种情况下，平台源代码集中额外的常量将与公共代码中的常量不匹配。
因此，编译器要求你处理所有额外的情况。

实现 `Department` 上的 `when` 结构的函数需要一个 `else` 子句：

```kotlin
// 需要 else 子句：
fun matchOnDepartment(dept: Department) {
    when (dept) {
        Department.IT -> println("The IT Department")
        Department.HR -> println("The HR Department")
        Department.Sales -> println("The Sales Department")
        else -> println("Some other department")
    }
}
```

<!-- 如果你希望禁止在实际枚举中添加新常量，请为这个问题投票 [TODO]。 -->

### 预期的注解类 {id=expected-annotation-classes}

预期声明和实际声明可以与注解一起使用。例如，你可以声明一个 `@XmlSerializable`
注解，它必须在每个平台源代码集中有一个对应的实际声明：

```kotlin
@Target(AnnotationTarget.CLASS)
@Retention(AnnotationRetention.RUNTIME)
expect annotation class XmlSerializable()

@XmlSerializable
class Person(val name: String, val age: Int)
```

在特定平台上重用现有类型可能会很有帮助。
例如，在 JVM 上，你可以使用 [JAXB 规范](https://javaee.github.io/jaxb-v2/) 中的现有类型来定义你的注解：

```kotlin
import javax.xml.bind.annotation.XmlRootElement

actual typealias XmlSerializable = XmlRootElement
```

在使用 `expect` 与注解类时，还需要考虑一个额外的因素。
注解用于将元数据附加到代码上，并不作为类型出现在签名中。
对于那些从未需要的注解，预期注解在某个平台上不必有一个实际类。

你只需要在注解被使用的平台上提供 `actual` 声明。
此行为默认不启用，需要将类型标记为 [`OptionalExpectation`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-optional-expectation/)。

对上述 `@XmlSerializable` 注解进行修改，添加 `OptionalExpectation`：

```kotlin
@OptIn(ExperimentalMultiplatform::class)
@Target(AnnotationTarget.CLASS)
@Retention(AnnotationRetention.RUNTIME)
@OptionalExpectation
expect annotation class XmlSerializable()
```

如果在某个平台上缺少实际声明且该平台不需要它，编译器不会生成错误。

## 接下来做什么？ {id=whats-next}

有关不同方式使用平台特定 API 的一般建议，请参见 [使用平台特定 API](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-connect-to-apis.html)。
