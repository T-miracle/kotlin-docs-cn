[//]: # (title: 异常)

## 异常类

Kotlin 中的所有异常类都继承自 `Throwable` 类。
每个异常都有一个消息、一个堆栈跟踪和一个可选的原因。

要抛出一个异常对象，请使用 `throw` 表达式：

```kotlin
fun main() {
    throw Exception("Hi There!")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

要捕获异常，请使用 `try`...`catch` 表达式：

```kotlin
try {
    // 一些代码
} catch (e: SomeException) {
    // 捕获并处理
} finally {
    // 带有可选 `finally` 块：
}
```

可能有零个或多个 `catch` 块，并且 `finally` 块可以被省略。
但是，`catch` 或 `finally` 块至少需要有一个。

### Try 是一个表达式

`try` 是一个表达式，这意味着它可以有一个返回值：

```kotlin
val a: Int? = try { input.toInt() } catch (e: NumberFormatException) { null }
```

`try` 表达式的返回值要么是 `try` 块中的最后一个表达式，要么是 `catch` 块（或块）中的最后一个表达式。
`finally` 块的内容不影响表达式的结果。

## 受检异常

Kotlin 没有受检异常。这背后有很多原因，我们将提供一个简单的示例，说明为什么会这样。

以下是 JDK 中由 `StringBuilder` 类实现的示例接口：

```java
Appendable append(CharSequence csq) throws IOException;
```

这个签名表示，每次我将一个字符串追加到某个地方（例如 `StringBuilder`、某种日志、控制台等）时，我都必须捕获 `IOException` 异常。
为什么？因为实现可能正在执行 I/O 操作（`Writer` 也实现了 `Appendable`）。结果是到处都是这样的代码：

```kotlin
try {
    log.append(message)
} catch (IOException e) {
    // 一定要安全
}
```

这不是一个好的设计。
只需查看 [《Effective Java 第3版》](https://www.oracle.com/technetwork/java/effectivejava-136174.html)，第 77 条：**不要忽略异常**。

Bruce Eckel 在关于受检异常的问题上说：

> 对小型程序的研究得出的结论是，要求异常规范既可以提高开发人员的生产率，又可以提高代码质量，
> 但大型软件项目的经验表明了不同的结果 —— 生产率下降，代码质量几乎没有提高。

以下是关于这个问题的一些建议：

* [Java 的受检异常是一个错误](https://radio-weblogs.com/0122027/stories/2003/04/01/JavasCheckedExceptionsWereAMistake.html)（Rod Waldhoff）
* [受检异常的麻烦](https://www.artima.com/intv/handcuffs.html)（Anders Hejlsberg）

如果你希望在从 Java、Swift 或 Objective-C 调用 Kotlin 代码时通知调用者可能发生的异常，你可以使用 `@Throws` 注解。
阅读更多关于如何在 [Java 中使用此注解](java-to-kotlin-interop.md#checked-exceptions)
以及在 [Swift 和 Objective-C 中使用此注解](native-objc-interop.md#errors-and-exceptions) 的信息。

## Nothing 类型 {id=Nothing类型}

`throw` 是 Kotlin 中的一个表达式，因此你可以将其用作 Elvis（空值合并）表达式的一部分，例如：

```kotlin
val s = person.name ?: throw IllegalArgumentException("姓名必填")
```

`throw` 表达式的类型是 `Nothing`。
这种类型没有值，用于标记永远不会到达的代码位置。
在你自己的代码中，你可以使用 `Nothing` 来标记一个永远不会返回的函数：

```kotlin
fun fail(message: String): Nothing {
    throw IllegalArgumentException(message)
}
```

当您调用此函数时，编译器将知道不会在该调用之后继续执行：

```kotlin
val s = person.name ?: fail("Name required")
println(s)     // 's' 在此时已知已初始化
```

在处理类型推断时，你可能也会遇到这种类型。这种类型的可空变体，`Nothing?`，有一个可能的值，即 `null`。
如果你使用 `null` 初始化推断类型的值，并且没有其他信息可用于确定更具体的类型，编译器将推断为 `Nothing?` 类型：

```kotlin
val x = null           // 'x' 的类型是 `Nothing?`
val l = listOf(null)   // 'l' 的类型是 `List<Nothing?>`
```

## Java 互操作性

有关 Java 互操作性的信息，请参阅 [Java 互操作性页面](java-interop.md) 中关于异常的部分。
