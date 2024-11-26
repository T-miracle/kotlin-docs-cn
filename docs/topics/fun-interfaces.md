[//]: # (title: 函数式编程（SAM）接口)

一个只有一个抽象成员函数的接口称为 _函数式接口_，或 _单抽象方法 (SAM) 接口_。
函数式接口可以有多个非抽象成员函数，但只能有一个抽象成员函数。

一个仅包含一个抽象方法的接口被称为**函数式接口**，或者**单一抽象方法（SAM）接口**。
函数式接口可以拥有多个非抽象成员，但只能有一个抽象成员。

在Kotlin中声明函数式接口时，使用`fun`修饰符。

```kotlin
fun interface KRunnable {
   fun invoke()
}
```

## SAM 转换 {id=sam-转换}

对于函数式接口，可以通过使用[lambda 表达式](lambdas.md#lambda-expressions-and-anonymous-functions)进行 SAM 转换来使代码更简洁、易读。

不需手动创造函数式接口的实现类，可用 lambda 表达式替代。
Kotlin 通过 SAM 转换，能将任何与接口单一方法签名匹配的 lambda 表达式转为代码，实现接口的动态实例化。

例如，有以下 Kotlin 函数式接口：

```kotlin
fun interface IntPredicate {
   fun accept(i: Int): Boolean
}
```

如果不使用 SAM 转换，代码需要这样编写：

```kotlin
// 创建类的实例
val isEven = object : IntPredicate {
   override fun accept(i: Int): Boolean {
       return i % 2 == 0
   }
}
```

通过充分利用 Kotlin 的 SAM 转换，可以用以下等效代码替代：

```kotlin
// 使用 lambda 创建实例
val isEven = IntPredicate { it % 2 == 0 }
```

一个简短的 lambda 表达式替代了所有不必要的代码。

```kotlin
fun interface IntPredicate {
   fun accept(i: Int): Boolean
}

val isEven = IntPredicate { it % 2 == 0 }

fun main() {
   println("Is 7 even? - ${isEven.accept(7)}")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.4"}

也可以使用[Java 接口的 SAM 转换](java-interop.md#sam-conversions)。

## 从带构造函数的接口迁移到函数式接口 {id=从带构造函数的接口迁移到函数式接口}

从1.6.20版本开始，Kotlin支持调用[可调用引用](reflection.md#可调用引用)的函数接口构造函数，使得从带构造函数的接口迁移到函数式接口更加源兼容。

以下是相关代码示例：

```kotlin
interface Printer { 
    fun print() 
}

fun Printer(block: () -> Unit): Printer = object : Printer { override fun print() = block() }
```

启用对函数式接口构造函数的可调用引用后，你可以仅通过使用函数式接口声明来替换这段代码：

```kotlin
fun interface Printer { 
    fun print()
}
```

它的构造函数将被隐式创建，并且任何使用 `::Printer` 函数引用的代码都将编译通过。例如：

```kotlin
documentsStorage.addPrinter(::Printer)
```

通过使用 [`@Deprecated`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-deprecated/) 注解和 `DeprecationLevel.HIDDEN`，保留二进制兼容性，标记旧的 `Printer` 函数：

```kotlin
@Deprecated(message = "关于弃用的消息", level = DeprecationLevel.HIDDEN)
fun Printer(...) {...}
```

## 函数接口 vs. 类型别名

你也可以简单地使用[类型别名](type-aliases.md)来重写上述内容，作为函数类型的别名：

```kotlin
typealias IntPredicate = (i: Int) -> Boolean

val isEven: IntPredicate = { it % 2 == 0 }

fun main() {
   println("Is 7 even? - ${isEven(7)}")
}
```

然而，函数式接口和[类型别名](type-aliases.md)有不同的用途。
类型别名只是现有类型的名称，它们不创建新的类型；而函数式接口则会创建新类型。
<tooltip term="直译">你可以提供专门适用于特定函数式接口的扩展，以确保其不适用于普通函数或其类型别名的情况。</tooltip>

类型别名只能有一个成员，而函数式接口可以有多个非抽象成员函数和一个抽象成员函数。  
函数式接口还可以实现和扩展其他接口。

函数式接口相对于类型别名来说更加灵活，能够提供更多的功能。
但需要注意的是，在语法和运行时方面，它们可能会更耗资源，因为可能需要进行特定接口的转换。
在选择在代码中使用哪一个时，请考虑您的需求：
- 如果您的 API 需要接受具有特定参数和返回类型的函数（任何函数）—— 使用简单的函数类型或定义类型别名，以提供相应函数类型的更短的名称。
- 如果您的 API 需要接受的实体比函数更为复杂 ——
  例如，它具有不寻常的约定和/或涉及在函数类型签名中无法表示的操作，那么为其单独声明一个函数式接口可能更为适合。
  这些“非平凡的合同”指的是不常见或特殊的规则和条件。
