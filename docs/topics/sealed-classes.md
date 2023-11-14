[//]: # (title: 密封类和接口)

**密封类**和接口表示受限制的类层次结构，提供对继承更多的控制。
密封类的所有直接子类在编译时已知。
在密封类定义的模块和包之外，不允许出现其他子类。

例如，第三方客户端不能在其代码中扩展您的密封类。
因此，密封类的每个实例在编译时都有一个来自已知集合的类型。

密封接口和其实现也遵循相同的原则：一旦使用密封接口的模块被编译，就不允许出现新的实现。

可以把密封类想象成一种特殊的类，有点像 [`enum`](enum-classes.md) 类：
枚举类型的值是受限制的，但枚举常量只能有**一个**实例，而密封类的子类可以有**多个**实例，每个实例都有自己的状态。

举个例子，假设有一个库的API，其中可能包含一些错误类，目的是让库的用户能够处理可能发生的错误。

如果这些错误类的层次结构是在公共API中可见的接口或抽象类中，那么用户可以在他们的代码中自由地实现或扩展这些类。
然而，库本身并不知道在外部声明的错误类，因此无法将外部错误与库内部的错误类一一对应。

通过使用密封的错误类层次结构，库的作者可以确保他们知道所有可能的错误类型，并且在以后不会有其他类型的错误出现。

要声明一个密封的类或接口，请在其名称之前加上 `sealed` 修饰符：

```kotlin
sealed interface Error

sealed class IOError(): Error

class FileReadError(val file: File): IOError()
class DatabaseError(val source: DataSource): IOError()

object RuntimeError : Error
```

密封类本身是[抽象的](classes.md#抽象类)，不能直接实例化，并且可以有`abstract`成员。

密封类的构造函数可以具有两种[可见性](visibility-modifiers.md)之一：`protected`（默认）或`private`：

```kotlin
sealed class IOError {
    constructor() { /*...*/ } // 默认为 protected
    private constructor(description: String): this() { /*...*/ } // private 也是可以的
    // public constructor(code: Int): this() {} // 错误：不允许 public 和 internal
}
```

## 直接子类的位置 {id=直接子类的位置}

密封类和接口的直接子类必须在同一个包中声明。这些子类可以是顶层的，也可以嵌套在其他命名类、命名接口或命名对象中，数量没有限制。
子类可以有任何[可见性](visibility-modifiers.md)，只要它们遵循 Kotlin 中正常的继承规则。

封闭类的子类必须具有正确的限定名称。它们不能是局部对象，也不能是匿名对象。

> 枚举类不能扩展密封类（以及任何其他类），但它们可以实现密封接口。
>
{style="note"}

这些限制不适用于间接子类。如果密封类的直接子类没有被声明为密封类，那么它可以按照其修饰符允许的任何方式进行扩展：

```kotlin
sealed interface Error // 只在同一包和模块中有实现

sealed class IOError(): Error // 只在同一包和模块中扩展
open class CustomError(): Error // 可以在可见的任何地方扩展
```

### 多平台项目中的继承

在[多平台项目](multiplatform-get-started.md)中还有一项继承限制：密封类的直接子类必须位于同一源代码集中。
这适用于没有 [expected 和 actual 修饰符](multiplatform-expect-actual.md) 的密封类。

如果一个封闭类在公共源代码集中声明为 `expect`，并在平台源代码集中有 `actual` 实现，  
那么 `expect` 和 `actual` 版本都可以在其源代码集中拥有子类。  
此外，如果您使用层次结构，您可以在 `expect` 和 `actual` 声明之间的任何源代码集中创建子类。

[了解有关多平台项目分层结构的更多信息](multiplatform-hierarchy.md)。

## 密封类和`when`表达式

在使用密封类时的一个关键优势体现在您在 [`when`](control-flow.md#when表达式) 表达式中使用它们时。
如果能够验证该语句覆盖了所有情况，您就不需要在语句中添加 `else` 子句：

```kotlin
fun log(e: Error) = when(e) {
    is FileReadError -> { println("Error while reading file ${e.file}") }
    is DatabaseError -> { println("Error while reading from database ${e.source}") }
    is RuntimeError ->  { println("Runtime error") }
    // 不需要 `else` 子句，因为所有情况都已覆盖
}
```

> 在多平台项目的通用代码中，对于 [`expect`](multiplatform-expect-actual.md) 密封类的 `when` 表达式仍然需要一个 `else` 分支。
> 这是因为 `actual` 平台实现的子类在通用代码中是未知的。
>
{style="note"}
