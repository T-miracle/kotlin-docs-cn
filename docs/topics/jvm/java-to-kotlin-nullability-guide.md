[//]: # (title: Java 与 Kotlin 中的可空性)
[//]: # (description: 学习如何将 Java 的可空结构迁移到 Kotlin。本指南涵盖 Kotlin 对可空类型的支持、Kotlin 如何处理来自 Java 的可空注解等内容。)

_可空性_ 是指变量能够持有 `null` 值的能力。
当变量包含 `null` 时，尝试解引用该变量会导致 `NullPointerException`。
有许多编码方式可以最小化出现空指针异常的概率。

本指南涵盖 Java 和 Kotlin 在处理可能为空的变量时的差异。
它将帮助您从 Java 迁移到 Kotlin，并以地道的 Kotlin 风格编写代码。

指南的第一部分介绍最重要的区别 —— Kotlin 对可空类型的支持以及
Kotlin 如何处理 [来自 Java 代码的类型](#platform-types)。第二部分从
[检查函数调用结果](#checking-the-result-of-a-function-call) 开始，通过几个具体案例解释某些差异。

[了解更多关于 Kotlin 的空安全机制](null-safety.md)。

## 对可空类型的支持 {id=support-for-nullable-types}

Kotlin 与 Java 类型系统最重要的区别在于 Kotlin 显式支持 [可空类型](null-safety.md)。
这是一种明确标识哪些变量可能持有 `null` 值的方式。
如果变量可能为 `null`，则直接调用该变量的方法是不安全的，因为这可能导致 `NullPointerException`。
Kotlin 在编译时禁止此类调用，从而避免大量潜在异常。
在运行时，可空类型对象与非可空类型对象的处理方式相同：
可空类型并不是非可空类型的包装器。所有检查均在编译时完成。
这意味着在 Kotlin 中使用可空类型几乎不会产生运行时开销。

> 之所以说“几乎”，是因为尽管会生成 [内置](https://en.wikipedia.org/wiki/Intrinsic_function) 检查，但其开销微乎其微。
>
{style="note"}

在 Java 中，如果不编写空值检查，方法可能会抛出 `NullPointerException`：

```java
// Java
int stringLength(String a) {
    return a.length();
}

void main() {
    stringLength(null); // 抛出 `NullPointerException`
}
```
{id="get-length-of-null-java"}

该调用会产生以下输出：

```java
java.lang.NullPointerException: Cannot invoke "String.length()" because "a" is null
    at test.java.Nullability.stringLength(Nullability.java:8)
    at test.java.Nullability.main(Nullability.java:12)
    at java.base/java.util.ArrayList.forEach(ArrayList.java:1511)
    at java.base/java.util.ArrayList.forEach(ArrayList.java:1511)
```

在 Kotlin 中，所有常规类型默认都是非空的，除非显式标记为可空。
如果不希望 `a` 为 `null`，可以如下声明 `stringLength()` 函数：

```kotlin
// Kotlin
fun stringLength(a: String) = a.length
```
{id="get-length-kotlin"}

参数 `a` 的类型为 `String`，在 Kotlin 中这意味着它必须始终包含一个 `String` 实例且不能为 `null`。
Kotlin 中的可空类型会使用问号 `?` 标记，例如 `String?`。
当 `a` 是 `String` 类型时，运行时不可能出现 `NullPointerException`，因为编译器强制要求所有传入
`stringLength()` 的参数都不能为 `null`。

尝试将 `null` 值传给 `stringLength(a: String)` 函数会导致编译时错误：
"Null can not be a value of a non-null type String"（null 不能作为非空类型 String 的值）：

![向非空函数传递 null 的错误](passing-null-to-function.png){width=700}

如果希望该函数能接受任何参数（包括 `null`），需要在参数类型后添加问号 `String?`，并在函数体内检查参数值是否非空：

```kotlin
// Kotlin
fun stringLength(a: String?): Int = if (a != null) a.length else 0
```
{id="get-length-of-null-kotlin"}

当检查通过后，编译器在该检查作用域内会将变量视为非空类型 `String` 处理。

如果不执行此检查，代码将无法编译并显示以下错误信息：
"Only [safe (?.)](null-safety.md#safe-call-operator) or
[non-nullable asserted (!!.) calls](null-safety.md#not-null-assertion-operator) are allowed
on a [nullable receiver](extensions.md#nullable-receiver) of type String?
（译：在类型为 String? 的 [可空接收者](extensions.md#nullable-receiver) 上，只允许使用
[安全调用 (?.)](null-safety.md#safe-call-operator) 或
[非空断言 (!!.) 调用](null-safety.md#not-null-assertion-operator)）"

你可以用更简洁的方式编写 —— 使用 [安全调用操作符 ?. (If-not-null 简写)](idioms.md#if-not-null简写)，
它允许你将空值检查和方法调用合并为单个操作：

```kotlin
// Kotlin
fun stringLength(a: String?): Int = a?.length ?: 0  // 若 a 为 null 则返回 0
```  
{id="get-length-of-null-shorter-kotlin"}

## 平台类型 {id=platform-types}

在 Java 中，你可以使用注解来标识变量是否可以为 `null`。
这类注解并非标准库的一部分，但你可以单独添加它们。
例如，你可以使用 JetBrains 的 `@Nullable` 和 `@NotNull` 注解（来自 `org.jetbrains.annotations` 包）， 
或者 Eclipse 的注解（`org.eclipse.jdt.annotation`）。
Kotlin 在 [从 Kotlin 代码调用 Java 代码](java-interop.md#nullability-annotations) 时可以识别这些注解，
并根据注解处理相应的类型。

如果你的 Java 代码没有这些注解，Kotlin 会将 Java 类型视为 **平台类型**。
但由于 Kotlin 无法获取这些类型的可空性信息，编译器会允许对它们执行所有操作。
你需要自行决定是否进行空值检查，因为：

* 和 Java 一样，如果你尝试对 `null` 执行操作，将会抛出 `NullPointerException`。
* 编译器不会像处理非空类型那样，高亮显示任何冗余的空值检查。

了解更多关于 [从 Kotlin 调用 Java 时的空安全与平台类型](java-interop.md#null-safety-and-platform-types)。

## 明确非空类型的支持 {id=support-for-definitely-non-nullable-types}

在 Kotlin 中，如果你需要覆写一个参数带有 `@NotNull` 注解的 Java 方法，就必须使用 Kotlin 的 **明确非空类型**。

例如，考虑以下 Java 中的 `load()` 方法：

```java
import org.jetbrains.annotations.*;

public interface Game<T> {
  public T save(T x) {}
  @NotNull
  public T load(@NotNull T x) {}
}
```

要在 Kotlin 中正确覆写 `load()` 方法，必须将 `T1` 声明为明确非空类型（`T1 & Any`）：

```kotlin
interface ArcadeGame<T1> : Game<T1> {
  override fun save(x: T1): T1
  // T1 必须是明确非空的
  override fun load(x: T1 & Any): T1 & Any
}
```

了解更多关于 [明确非空类型](generics.md#definitely-non-nullable-types) 的泛型用法。

## 检查函数调用的结果 {id="checking-the-result-of-a-function-call"}

最常见的需要检查 `null` 的情况之一是从函数调用获取结果时。

在以下示例中，有两个类：`Order` 和 `Customer`。`Order` 包含对 `Customer` 实例的引用。
`findOrder()` 函数返回 `Order` 类的实例，如果找不到订单则返回 `null`。
目标是对检索到的订单的客户实例进行处理。

以下是 Java 中的类定义：

```java
//Java
record Order (Customer customer) {}

record Customer (String name) {}
```

在 Java 中，调用该函数并对结果执行非空检查，以便继续解引用所需属性：

```java
// Java
Order order = findOrder();

if (order != null) {
    processCustomer(order.getCustomer());
}
```
{id="process-customer-if-not-null-java"}

将上面的 Java 代码直接转换为 Kotlin 代码会得到以下结果：

```kotlin
// Kotlin
data class Order(val customer: Customer)

data class Customer(val name: String)

val order = findOrder()

// 直接转换
if (order != null){
    processCustomer(order.customer)
}
```
{id="process-customer-if-not-null-kotlin"}

使用[安全调用操作符 `?.` (If-not-null 简写)](idioms.md#if-not-null简写) 
结合标准库中的[作用域函数](scope-functions.md)。
通常使用 `let` 函数来实现：

```kotlin
// Kotlin
val order = findOrder()

order?.let {
    processCustomer(it.customer)
}
```
{id="process-customer-with-let-kotlin"}

以下是更简短的版本：

```kotlin
// Kotlin
findOrder()?.customer?.let(::processCustomer)
```
{id="process-customer-with-let-short-kotlin"}

## 默认值替代 null {id=default-values-instead-of-null}

检查 `null` 通常与[设置默认值](functions.md#default-arguments)结合使用，
以防 null 检查成功。

带有 null 检查的 Java 代码：

```java
// Java
Order order = findOrder();
if (order == null) {
    order = new Order(new Customer("Antonio"))
}
```
{id="default-value-instead-of-null-java"}

在 Kotlin 中表达相同逻辑，使用 [Elvis 操作符 (If-not-null-else 简写)](null-safety.md#elvis-operator)：

```kotlin
// Kotlin
val order = findOrder() ?: Order(Customer("Antonio"))
```
{id="default-value-instead-of-null-kotlin"}

## 返回可能为 null 值的方法 {id=functions-returning-a-value-or-null}

在 Java 中处理列表元素时需要格外小心，在使用元素前必须检查索引位置是否存在元素：

```java
// Java
var numbers = new ArrayList<Integer>();
numbers.add(1);
numbers.add(2);

System.out.println(numbers.get(0));
//numbers.get(5) // 异常！
```
{id="functions-returning-null-java"}

Kotlin 标准库提供了许多通过函数名就能判断是否可能返回 `null` 的方法。
这在集合 API 中尤为常见：

```kotlin
fun main() {
//sampleStart
    // Kotlin
    // 与 Java 相同的代码：
    val numbers = listOf(1, 2)
    
    println(numbers[0])  // 如果集合为空可能抛出 IndexOutOfBoundsException
    //numbers.get(5)     // 异常！

    // 更安全的操作方式：
    println(numbers.firstOrNull())
    println(numbers.getOrNull(5)) // 返回 null
//sampleEnd
}
```
{kotlin-runnable="true" id="functions-returning-null-kotlin"}

## 聚合操作 {id=aggregate-operations}

当你需要获取最大元素或为空时返回 `null`，在 Java 中你会使用
[Stream API](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/stream/package-summary.html):

```java
// Java
var numbers = new ArrayList<Integer>();
var max = numbers.stream().max(Comparator.naturalOrder()).orElse(null);
System.out.println("Max: " + max);
```
{id="aggregate-functions-java"}

在 Kotlin 中，使用[聚合操作](collection-aggregate.md):

```kotlin
// Kotlin
val numbers = listOf<Int>()
println("Max: ${numbers.maxOrNull()}")
```
{id="aggregate-functions-kotlin"}

了解更多关于[Java 和 Kotlin 中的集合](java-to-kotlin-collections-guide.md)。

## 安全类型转换 {id=casting-types-safely}

当你需要安全地进行类型转换时，在 Java 中你会使用 `instanceof` 运算符并检查其执行结果：

```java
// Java
int getStringLength(Object y) {
    return y instanceof String x ? x.length() : -1;
}

void main() {
    System.out.println(getStringLength(1)); // 输出 `-1`
}
```
{id="casting-types-java"}

在 Kotlin 中，为避免异常，可使用[安全转换运算符](typecasts.md#安全可空强制转换运算符) `as?`，转换失败时返回 `null`：

```kotlin
// Kotlin
fun main() {
    println(getStringLength(1)) // 输出 `-1`
}

fun getStringLength(y: Any): Int {
    val x: String? = y as? String // null
    return x?.length ?: -1 // 返回 -1 因为 `x` 为 null
}
```
{kotlin-runnable="true" id="casting-types-kotlin"}

> 在上面 Java 示例中，`getStringLength()` 函数返回基本类型 `int` 的结果。
若需要返回 `null`，可以使用[_装箱_类型](https://docs.oracle.com/javase/tutorial/java/data/autoboxing.html) `Integer`。
但更高效的做法是让此类函数返回负值后进行校验 ——
你终究需要校验返回值，而这种方式避免了额外的装箱操作。
>
{style="note"}

## 下一步 {id=whats-next}

* 浏览其他 [Kotlin 惯用法](idioms.md)
* 学习如何使用 [Java 转 Kotlin (J2K) 转换器](mixing-java-kotlin-intellij.md#converting-an-existing-java-file-to-kotlin-with-j2k) 将现有 Java 代码转换为 Kotlin
* 查看其他迁移指南：
  * [Java 和 Kotlin 中的字符串](java-to-kotlin-idioms-strings.md) {id=strings-in-java-and-kotlin}
  * [Java 和 Kotlin 中的集合](java-to-kotlin-collections-guide.md) {id=collections-in-java-and-kotlin}

如果你有喜欢的惯用法，欢迎通过提交 Pull Request 与我们分享！
