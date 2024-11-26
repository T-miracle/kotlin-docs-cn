[//]: # (title: 空安全)

空安全是 Kotlin 的一项特性，旨在显著减少空引用的风险，也被称为
[十亿美元的错误](https://en.wikipedia.org/wiki/Null_pointer#History)。

许多编程语言（包括 Java）中的一个常见陷阱是，访问空引用的成员会导致空引用异常。
在 Java 中，这等同于 `NullPointerException`，简称 _NPE_。

Kotlin 明确支持可空性作为其类型系统的一部分，这意味着你可以显式声明哪些变量或属性允许为 `null`。
此外，当你声明非空变量时，编译器会强制确保这些变量不能持有 `null` 值，从而防止出现 NPE。

Kotlin 的空安全通过在编译时捕获潜在的与 `null` 相关的问题，而不是在运行时捕获，从而确保代码的安全性。
此特性通过显式表达 `null` 值，改善了代码的稳健性、可读性和可维护性，使代码更易于理解和管理。

Kotlin 中可能导致 NPE 的原因只可能是以下几种：

* 显式调用 [`throw NullPointerException()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-null-pointer-exception/)。
* 使用 [非空断言操作符 `!!`](#not-null-assertion-operator)。
* 初始化期间的数据不一致，例如：
  * 构造函数中未初始化的 `this` 被在其他地方使用（[“泄漏 `this`”](https://youtrack.jetbrains.com/issue/KTIJ-9751)）。
  * 调用父类构造函数时，调用一个开放（open）成员，该成员在派生类中的实现使用了未初始化的状态（[继承中的初始化顺序](inheritance.md#derived-class-initialization-order)）。
* Java 互操作：
  * 尝试访问 `null` 引用的成员，引用的是 [平台类型](java-interop.md#null-safety-and-platform-types)。
  * 与泛型类型相关的可空性问题。例如，Java 代码片段将 `null` 添加到 Kotlin 的 `MutableList<String>` 中，而正确的做法是使用 `MutableList<String?>` 来处理。
  * 由外部 Java 代码引起的其他问题。

> 除了 NPE，另一个与空安全相关的异常是 [`UninitializedPropertyAccessException`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-uninitialized-property-access-exception/)。
> 当你尝试访问一个尚未初始化的属性时，Kotlin 会抛出这个异常，确保在属性准备好之前，非空属性不能被使用。
> 这通常发生在 [`lateinit` 属性](properties.md#late-initialized-properties-and-variables) 中。
>
{style="tip"}

## 可空类型和非空类型 {id=nullable-types-and-non-nullable-types}

在 Kotlin 中，类型系统区分了可以保存 `null` 的类型（可空类型）和不能保存 `null` 的类型（非空类型）。
例如，`String` 类型的普通变量不能保存 `null`：

```kotlin
fun main() {
//sampleStart
    // 给变量赋值一个非空字符串
    var a: String = "abc"
    // 尝试将 null 重新赋值给非空变量
    a = null
    print(a)
    // null 不能作为非空类型 String 的值
//sampleEnd
}
```
{kotlin-runnable="true" validate="false"}

你可以安全地调用方法或访问 `a` 的属性。由于 `a` 是一个非空变量，调用不会导致空指针异常（NPE）。
编译器确保 `a` 始终持有有效的 `String` 值，因此没有访问其属性或方法时发生空指针异常的风险：

```kotlin
fun main() {
//sampleStart
    // 给变量赋值一个非空字符串
    val a: String = "abc"
    // 返回非空变量的长度
    val l = a.length
    print(l)
    // 3
//sampleEnd
}
```
{kotlin-runnable="true" validate="false"}

为了允许 `null` 值，在变量类型后加上 `?` 符号进行声明。例如，你可以通过写 `String?` 来声明一个可空字符串。
这个表达式使得 `String` 成为一个可以接受 `null` 的类型：

```kotlin
fun main() {
//sampleStart
    // 给变量赋值一个可空字符串
    var b: String? = "abc"
    // 成功将 null 重新赋值给可空变量
    b = null
    print(b)
    // null
//sampleEnd
}
```
{kotlin-runnable="true"}

如果你尝试直接访问 `b` 的 `length` 属性，编译器会报错。这是因为 `b` 被声明为可空变量，可以保存 `null` 值。
直接在可空类型上访问属性会导致空指针异常（NPE）：

```kotlin
fun main() {
//sampleStart
    // 给变量赋值一个可空字符串  
    var b: String? = "abc"
    // 将 null 重新赋值给可空变量
    b = null
    // 尝试直接返回可空变量的长度
    val l = b.length
    print(l)
    // 只能对可空接收者类型 String? 使用安全调用 (?.) 或非空断言 (!!.) 
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="2.0" validate="false"}

在上述示例中，编译器要求你使用安全调用来检查是否为 `null`，然后再访问属性或执行操作。处理可空类型有几种方式：

* [使用 `if` 条件检查 `null`](#check-for-null-with-the-if-conditional)
* [安全调用操作符 `?.`](#safe-call-operator)
* [Elvis 操作符 `?:`](#elvis-operator)
* [非空断言操作符 `!!`](#not-null-assertion-operator)
* [可空接收者](#nullable-receiver)
* [`let` 函数](#let-function)
* [安全类型转换 `as?`](#safe-casts)
* [可空类型的集合](#collections-of-a-nullable-type)

请继续阅读接下来的章节，了解更多关于 `null` 处理的工具和技巧。

## 使用 `if` 条件检查 `null` {id=check-for-null-with-the-if-conditional}

在处理可空类型时，需要安全地处理空值以避免空指针异常（NPE）。一种方法是使用 `if` 条件表达式显式地检查空值。

例如，检查 `b` 是否为 `null`，然后再访问 `b.length`：

```kotlin
fun main() {
//sampleStart
    // 给可空变量赋值 null
    val b: String? = null
    // 先检查是否为 null，然后再访问长度
    val l = if (b != null) b.length else -1
    print(l)
    // -1
//sampleEnd
}
```
{kotlin-runnable="true"}

在上述示例中，编译器执行了 [智能类型转换](typecasts.md#smart-casts)，将类型从可空的 `String?` 转换为非空的 `String`。
它还跟踪你执行的检查，并允许在 `if` 条件内调用 `length`。

更复杂的条件也受到支持：

```kotlin
fun main() {
//sampleStart
    // 给可空字符串赋值
    val b: String? = "Kotlin"

    // 先检查是否为 null，然后再访问长度
    if (b != null && b.length > 0) {
        print("String of length ${b.length}")
    // 如果条件不满足，提供替代方案  
    } else {
        print("Empty string")
        // 字符串长度为 6
    }
//sampleEnd
}
```
{kotlin-runnable="true"}

请注意，上述示例仅在编译器可以保证 `b` 在检查和使用之间不会改变时才有效，这与
[智能类型转换的前提条件](typecasts.md#smart-cast-prerequisites) 相同。

## 安全调用操作符 {id=safe-call-operator}

安全调用操作符 `?.` 允许你以更简洁的方式安全地处理空值。
如果对象为 `null`，则 `?.` 操作符会直接返回 `null`，而不是抛出空指针异常（NPE）：

```kotlin
fun main() {
//sampleStart
    // 给可空字符串赋值
    val a: String? = "Kotlin"
    // 给可空变量赋值 null
    val b: String? = null
    
    // 检查 null 性并返回长度或 null
    println(a?.length)
    // 6
    println(b?.length)
    // null
//sampleEnd
}
```
{kotlin-runnable="true"}

`b?.length` 表达式会检查是否为 `null`，如果 `b` 不为 `null`，则返回 `b.length`，否则返回 `null`。这个表达式的类型是 `Int?`。

你可以在 Kotlin 中使用 `?.` 操作符与 [`var` 和 `val` 变量](basic-syntax.md#variables)：

* 一个可空的 `var` 可以保存 `null`（例如，`var nullableValue: String? = null`）或非空值（例如，`var nullableValue: String? = "Kotlin"`）。如果它是非空值，你可以随时将其改为 `null`。
* 一个可空的 `val` 可以保存 `null`（例如，`val nullableValue: String? = null`）或非空值（例如，`val nullableValue: String? = "Kotlin"`）。如果它是非空值，之后不能再将其改为 `null`。

安全调用在链式调用中非常有用。例如，Bob 是一个可能被分配到某个部门（也可能没有）的员工。
这个部门可能有另一个作为部门负责人的员工。为了获得 Bob 的部门负责人的名字（如果有的话），你可以这样写：

```kotlin
bob?.department?.head?.name
```

如果链中的任何一个属性为 `null`，这个链式调用会返回 `null`。以下是用 `if` 条件表达式实现相同的安全调用：

```kotlin
if (person != null && person.department != null) {
    person.department.head = managersPool.getManager()
}
```

你还可以将安全调用放在赋值语句的左侧：

```kotlin
person?.department?.head = managersPool.getManager()
```

在上述示例中，如果安全调用链中的任何接收者为 `null`，赋值操作将被跳过，右侧的表达式根本不会被计算。
例如，如果 `person` 或 `person.department` 为 `null`，函数将不会被调用。

## Elvis 操作符 {id=elvis-operator}

在处理可空类型时，你可以检查是否为 `null` 并提供一个替代值。
例如，如果 `b` 不为 `null`，则访问 `b.length`。否则，返回一个替代值：

```kotlin
fun main() {
//sampleStart
    // 给可空变量赋值 null  
    val b: String? = null
    // 检查是否为 null。如果不为 null，返回长度；如果为 null，返回 0
    val l: Int = if (b != null) b.length else 0
    println(l)
    // 0
//sampleEnd
}
```
{kotlin-runnable="true"}

你可以使用 Elvis 操作符 `?:` 更简洁地处理这个问题，而不需要写完整的 `if` 表达式：

```kotlin
fun main() {
//sampleStart
    // 给可空变量赋值 null  
    val b: String? = null
    // 检查是否为 null。如果不为 null，返回长度；如果为 null，返回一个非 null 值
    val l = b?.length ?: 0
    println(l)
    // 0
//sampleEnd
}
```
{kotlin-runnable="true"}

如果 Elvis 操作符左侧的表达式不为 `null`，则返回它。
否则，Elvis 操作符返回右侧的表达式。右侧的表达式仅在左侧为 `null` 时才会被计算。

由于 `throw` 和 `return` 在 Kotlin 中也是表达式，你也可以将它们用于 Elvis 操作符的右侧。
例如，在检查函数参数时，这非常方便：

```kotlin
fun foo(node: Node): String? {
    // 检查 getParent()。如果不为 null，赋值给 parent；如果为 null，返回 null
    val parent = node.getParent() ?: return null
    // 检查 getName()。如果不为 null，赋值给 name；如果为 null，抛出异常
    val name = node.getName() ?: throw IllegalArgumentException("name expected")
    // ...
}
```

## 非空断言操作符 {id=not-null-assertion-operator}

非空断言操作符 `!!` 将任何值转换为非空类型。

当你对一个值不为 `null` 的变量使用 `!!` 操作符时，它会被安全地处理为非空类型，代码会正常执行。
然而，如果该值为 `null`，`!!` 操作符会强制它作为非空类型来处理，从而导致空指针异常 (NPE)。

当 `b` 不为 `null` 且 `!!` 操作符使其返回非空值（在这个例子中是 `String` 类型）时，它可以正确地访问 `length` 属性：

```kotlin
fun main() {
//sampleStart
    // 将一个可空字符串赋值给变量  
    val b: String? = "Kotlin"
    // 将 b 视为非空并访问其长度
    val l = b!!.length
    println(l)
    // 6
//sampleEnd
}
```
{kotlin-runnable="true"}

当 `b` 为 `null` 且 `!!` 操作符强制它返回非空值时，会发生空指针异常 (NPE)：

```kotlin
fun main() {
//sampleStart
    // 将 null 赋值给一个可空变量  
    val b: String? = null
    // 将 b 视为非空并尝试访问其长度
    val l = b!!.length
    println(l) 
    // 异常：线程 "main" 中的 java.lang.NullPointerException
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="2.0" validate="false"}

`!!` 操作符特别有用，当你确信一个值不为 `null` 且没有发生空指针异常的风险时，但编译器无法保证这一点（由于某些规则）。
在这种情况下，你可以使用 `!!` 操作符显式地告诉编译器该值不为 `null`。

## 可空接收者 {id=nullable-receiver}

你可以对 [可空接收者类型](extensions.md#nullable-receiver) 使用扩展函数，从而允许这些函数在可能为 `null` 的变量上调用。

通过在可空接收者类型上定义扩展函数，你可以在函数内部处理 `null` 值，而无需在每次调用函数时都进行 `null` 检查。

例如，[`.toString()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/to-string.html) 扩展函数可以在可空接收者上调用。
当它在 `null` 值上被调用时，会安全地返回字符串 `"null"`，而不会抛出异常：

```kotlin
//sampleStart
fun main() {
    // 将 null 赋值给存储在 person 变量中的可空 Person 对象
    val person: Person? = null

    // 对可空的 person 变量应用 .toString 并打印字符串
    println(person.toString())
    // null
}

// 定义一个简单的 Person 类
data class Person(val name: String)
//sampleEnd
```
{kotlin-runnable="true"}

在上面的例子中，即使 `person` 为 `null`，`.toString()` 函数仍然安全地返回了字符串 `"null"`。这对于调试和日志记录非常有帮助。

如果你期望 `.toString()` 函数返回一个可空字符串（即可能是字符串表示或 `null`），可以使用 [安全调用操作符 `?.`](#safe-call-operator)。
`?.` 操作符只有在对象不为 `null` 时才会调用 `.toString()`，否则会返回 `null`：

```kotlin
//sampleStart
fun main() {
    // 将一个可空的 Person 对象赋值给变量
    val person1: Person? = null
    val person2: Person? = Person("Alice")

    // 如果 person 为 null，则打印 "null"；否则打印 person.toString() 的结果
    println(person1?.toString())
    // null
    println(person2?.toString())
    // Person(name=Alice)
}

// 定义一个 Person 类
data class Person(val name: String)
//sampleEnd
```
{kotlin-runnable="true"}

`?.` 操作符使你能够安全地处理可能为 `null` 的值，同时仍然访问可能为 `null` 对象的属性或方法。

## Let 函数 {id=let-function}

为了处理 `null` 值并仅在非空类型上执行操作，你可以将安全调用操作符 `?.` 与 [`let` 函数](scope-functions.md#let) 一起使用。

这种组合对于评估一个表达式，检查结果是否为 `null`，并且仅在非空时执行代码非常有用，从而避免了手动进行 `null` 检查：

```kotlin
fun main() {
//sampleStart
    // 声明一个包含可空字符串的列表
    val listWithNulls: List<String?> = listOf("Kotlin", null)

    // 遍历列表中的每个项
    for (item in listWithNulls) {
        // 检查项是否为 null，只有在非 null 时才打印值
        item?.let { println(it) }
        // Kotlin 
    }
//sampleEnd
}
```
{kotlin-runnable="true"}

## 安全类型转换 {id=safe-casts}

Kotlin 中用于 [类型转换](typecasts.md#unsafe-cast-operator) 的常规操作符是 `as` 操作符。
然而，常规类型转换如果目标类型不匹配，可能会抛出异常。

你可以使用 `as?` 操作符进行安全类型转换。
它尝试将一个值转换为指定的类型，如果该值不是目标类型，则返回 `null`：

```kotlin
fun main() {
//sampleStart
    // 声明一个 Any 类型的变量，可以持有任何类型的值
    val a: Any = "Hello, Kotlin!"

    // 使用 'as?' 操作符安全转换为 Int
    val aInt: Int? = a as? Int
    // 使用 'as?' 操作符安全转换为 String
    val aString: String? = a as? String

    println(aInt)
    // null
    println(aString)
    // "Hello, Kotlin!"
//sampleEnd
}
```
{kotlin-runnable="true"}

上述代码打印 `null`，因为 `a` 不是 `Int` 类型，转换失败并安全地返回 `null`。
它也打印了 `"Hello, Kotlin!"`，因为它匹配了 `String?` 类型，安全转换成功。

## 可空类型的集合 {id=collections-of-a-nullable-type}

如果你有一个包含可空元素的集合，并且希望只保留非空元素，可以使用
`filterNotNull()` 函数：

```kotlin
fun main() {
//sampleStart
    // 声明一个包含一些 null 和非 null 整数值的列表
    val nullableList: List<Int?> = listOf(1, 2, null, 4)

    // 过滤掉 null 值，得到一个只包含非 null 整数的列表
    val intList: List<Int> = nullableList.filterNotNull()
  
    println(intList)
    // [1, 2, 4]
//sampleEnd
}
```
{kotlin-runnable="true"}

## 接下来做什么？ {id=whats-next}

* 学习如何在 [Java 和 Kotlin 中处理 nullability](java-to-kotlin-nullability-guide.md)。
* 了解哪些泛型类型是 [绝对非空的](generics.md#definitely-non-nullable-types)。
