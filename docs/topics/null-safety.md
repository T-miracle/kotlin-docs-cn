[//]: # (title: 空值安全)

## 可空类型与非空类型 {id=nullable-types-and-non-nullable-types}

Kotlin 的类型系统旨在消除空引用的危险，这也被称为[价值十亿美元的错误](https://en.wikipedia.org/wiki/Null_pointer#History)。

在包括 Java 在内的许多编程语言中，最常见的陷阱之一是访问一个空引用的成员会导致空引用异常。
在 Java 中，这相当于 `NullPointerException`，或者简称为 **NPE**。

Kotlin 中唯一可能导致 NPE 的原因是：

* 显式调用 `throw NullPointerException()`。
* 使用下面描述的 `!!` 操作符。
* 初始化数据不一致，例如：
  * 一个在构造函数中未初始化的 `this` 被传递并在某处使用（“泄漏的 `this`”）。
  * [父类构造函数调用一个 open 成员](inheritance.md#派生类初始化顺序)，其在派生类中的实现使用了未初始化的状态。
* Java 互操作性：
  * 尝试访问一个 [平台类型](java-interop.md#null-safety-and-platform-types) 的 `null` 引用的成员；
  * 使用泛型进行 Java 互操作时的空值问题。
    例如，一段 Java 代码可能向 Kotlin 的 `MutableList<String>` 中添加 `null`，因此需要一个 `MutableList<String?>` 来处理它。
  * 由外部 Java 代码引起的其他问题。

在 Kotlin 中，类型系统区分了可以持有 `null` 的引用（可空引用）和不能持有 `null` 的引用（非空引用）。
例如，类型为 `String` 的普通变量不能持有 `null`：

```kotlin
fun main() {
//sampleStart
    var a: String = "abc" // 常规初始化默认意味着非空
    a = null // 编译错误
//sampleEnd
}
```
{kotlin-runnable="true" validate="false"}

为了允许 `null`，你可以通过写 `String?` 来声明一个可空字符串变量：

```kotlin
fun main() {
//sampleStart
    var b: String? = "abc" // 可以设置为 null
    b = null // 可以
    print(b)
//sampleEnd
}
```
{kotlin-runnable="true"}

现在，当你调用 `a` 的方法或访问它的属性，可以保证不会导致 NPE，所以你可以安全地编写：

```kotlin
val l = a.length
```

但是如果你想访问 `b` 的同一个属性，这样做是不安全的，编译器会报错：

```kotlin
val l = b.length // 错误：变量 'b' 可能为 null
```

但是你仍然需要访问那个属性，对吧？有几种方法可以做到这一点。

## 检查条件中的 `null` {id=checking-for-null-in-conditions}

首先，你可以显式检查 `b` 是否为 `null`，并分别处理这两种情况：

```kotlin
val l = if (b != null) b.length else -1
```

编译器会跟踪你执行的检查信息，并允许在 `if` 内调用 `length`。
更复杂的条件也是支持的：

```kotlin
fun main() {
//sampleStart
    val b: String? = "Kotlin"
    if (b != null && b.length > 0) {
        print("字符串的长度是 ${b.length}")
    } else {
        print("空字符串")
    }
//sampleEnd
}
```
{kotlin-runnable="true"}

注意，这仅在 `b` 是不可变的情况下有效（即，它是一个在检查和使用之间没有被修改的局部变量，或者它是一个有幕后字段且不可重写的成员 `val`）。
否则，在检查之后 `b` 可能会变为 `null`。

## 安全调用 {id=safe-calls}

访问可空变量的属性的第二种选择，是使用安全调用操作符 `?.`：

```kotlin
fun main() {
//sampleStart
    val a = "Kotlin"
    val b: String? = null
    println(b?.length)
    println(a?.length) // 不必要的安全调用
//sampleEnd
}
```
{kotlin-runnable="true"}

如果 `b` 不为 null，则返回 `b.length`，否则返回 `null`。这个表达式的类型是 `Int?`。

安全调用在链式调用中非常有用。
例如，Bob 是一个员工，可能会被分配到一个部门（也可能不会）。
那个部门又可能有另一个员工担任部门主管。为了获取 Bob 部门主管的名字（如果有的话），你可以这样写：

```kotlin
bob?.department?.head?.name
```

这样的链式调用在其中任何一个属性为 `null` 时都会返回 `null`。

为了仅对非空值执行特定操作，你可以将安全调用操作符与
[`let`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/let.html) 一起使用：

```kotlin
fun main() {
//sampleStart
    val listWithNulls: List<String?> = listOf("Kotlin", null)
    for (item in listWithNulls) {
         item?.let { println(it) } // 打印 Kotlin，忽略 null
    }
//sampleEnd
}
```
{kotlin-runnable="true"}

安全调用也可以放在赋值的左侧。如果安全调用链中的一个接收者为 `null`，赋值会被跳过，并且右侧的表达式根本不会被求值：

```kotlin
// 如果 `person` 或 `person.department` 为空，则不调用该函数：
person?.department?.head = managersPool.getManager()
```

## 可空接收者 {id=nullable-receiver}

扩展函数可以定义在[可空接收者](extensions.md#可空接收者)上。
这样，你可以为 `null` 值指定行为，而无需在每个调用点使用空值检查逻辑。

例如，[`toString()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/to-string.html) 函数定义在一个可空接收者上。
它返回字符串 "null"（而不是 `null` 值）。这在某些情况下非常有用，例如日志记录：

```kotlin
val person: Person? = null
logger.debug(person.toString()) // 记录 "null"，不会抛出异常
```

如果你希望 `toString()` 调用返回一个可空字符串，请使用[安全调用操作符 `?.`](#safe-calls)：

```kotlin
var timestamp: Instant? = null
val isoTimestamp = timestamp?.toString() // 返回一个 String? 对象，其值为 `null`
if (isoTimestamp == null) {
   // 处理 timestamp 为 `null` 的情况
}
```

## Elvis 运算符 {id=elvis-operator}

当你有一个可空引用 `b` 时，你可以这样说：“如果 `b` 不为 `null`，使用它，否则使用某个非空值”：

```kotlin
val l: Int = if (b != null) b.length else -1
```

不用写完整的 `if` 表达式，你也可以使用 Elvis 运算符 `?:` 来表达这个意思：

```kotlin
val l = b?.length ?: -1
```

如果 `?:` 左侧的表达式不为 `null`，Elvis 运算符返回它本身的值，否则，返回右侧的表达式。
注意，只有当左侧为 `null` 时，右侧的表达式才会被求值。

由于 `throw` 和 `return` 在 Kotlin 中也是表达式，因此它们也可以用在 Elvis 运算符的右侧。这在检查函数参数时非常方便，例如：

```kotlin
fun foo(node: Node): String? {
    val parent = node.getParent() ?: return null
    val name = node.getName() ?: throw IllegalArgumentException("name expected")
    // ...
}
```

## `!!` 操作符 {id=the-operator}

第三种选择是为 NPE 爱好者准备的：非空断言操作符（`!!`）将任何值转换为非空类型，如果值为 `null`，则抛出异常。
你可以写 `b!!`，这将返回 `b` 的非空值（例如，我们的示例中的 `String`），或者如果 `b` 为 `null` 则抛出 NPE：

```kotlin
val l = b!!.length
```

因此，如果你想要一个 NPE，你可以获得它，但你必须明确要求它，它不会突然出现。

## 安全类型转换 {id=safe-casts}

普通的类型转换如果对象不是目标类型可能会导致 `ClassCastException`。
另一种选择是使用安全类型转换，它在尝试失败时返回 `null`：

```kotlin
val aInt: Int? = a as? Int
```

## 可空类型的集合 {id=collections-of-a-nullable-type}

如果你有一个可空类型元素的集合，并且想要过滤掉非空元素，可以使用 `filterNotNull`：

```kotlin
val nullableList: List<Int?> = listOf(1, 2, null, 4)
val intList: List<Int> = nullableList.filterNotNull()
```

## 接下来做什么？ {id=whats-next}

* 学习如何[在 Java 和 Kotlin 中处理空值](java-to-kotlin-nullability-guide.md)。
* 了解[绝对非空类型的泛型](generics.md#definitely-non-nullable-types)。
