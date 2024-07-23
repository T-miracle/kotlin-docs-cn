[//]: # (title: 相等性)

在 Kotlin 中，有两种类型的相等性：

* _结构性_ 相等性 (`==`) - 使用 `equals()` 函数检查
* _引用性_ 相等性 (`===`) - 检查两个引用是否指向同一个对象

## 结构性相等性 {id=structural-equality}

结构性相等性验证两个对象是否具有相同的内容或结构。
结构性相等性通过 `==` 运算符及其相反的 `!=` 运算符来检查。
按照约定惯例，类似 `a == b` 的表达式会被转换为：

```kotlin
a?.equals(b) ?: (b === null)
```

如果 `a` 不是 `null`，则调用 `equals(Any?)` 函数。否则（`a` 为 `null`），则检查 `b` 是否引用性等于 `null`：

```kotlin
fun main() {
    var a = "hello"
    var b = "hello"
    var c = null
    var d = null
    var e = d

    println(a == b)
    // true
    println(a == c)
    // false
    println(c == e)
    // true
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

注意，当显式比较 `null` 时，优化代码没有意义：
`a == null` 会自动转换为 `a === null`。

在 Kotlin 中，`equals()` 函数是所有类从 `Any` 类继承来的。
默认情况下，`equals()` 函数实现了[引用性相等](#referential-equality)。
然而，Kotlin 中的类可以重写 `equals()` 函数，以提供自定义的相等逻辑，从而实现结构性相等。

值类（Value Class）和数据类（Data Class）是两种特殊的 Kotlin 类型，它们会自动重写 `equals()` 函数。
这就是它们默认实现结构性相等的原因。

然而，在数据类的情况下，如果父类中的 `equals()` 函数被标记为 `final`，其行为将保持不变。

与此不同的是，非数据类（未使用 `data` 修饰符声明的类）默认不会重写 `equals()` 函数。
相反，非数据类实现了从 `Any` 类继承的引用性相等行为。
要实现结构性相等，非数据类需要重写 `equals()` 函数，提供自定义的相等逻辑。

为了提供自定义的 `equals` 检查实现，重写
[`equals(other: Any?): Boolean`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-any/equals.html) 函数：

```kotlin
class Point(val x: Int, val y: Int) {
    override fun equals(other: Any?): Boolean {
        if (this === other) return true
        if (other !is Point) return false

        // 比较属性的结构性相等
        return this.x == other.x && this.y == other.y
    }
}
```
> 当重写 `equals()` 函数时，还应该重写 [`hashCode()` 函数](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-any/hash-code.html)
> 以保持相等性和哈希的一致性，确保这些函数的正确行为。
>
{style="note"}

具有相同名称和其他签名（例如 `equals(other: Foo)`）的函数不会影响使用
`==` 和 `!=` 操作符的相等性检查。

结构性相等与 `Comparable<...>` 接口定义的比较无关，因此只有自定义的
`equals(Any?)` 实现可能会影响操作符的行为。

## 引用相等 {id=referential-equality}

引用相等验证两个对象的内存地址，以确定它们是否是相同的实例。

引用相等通过 `===` 操作符及其否定操作符 `!==` 来检查。只有当 `a` 和 `b` 指向同一个对象时，`a === b` 的结果才为 true：

```kotlin
fun main() {
    var a = "Hello"
    var b = a
    var c = "world"
    var d = "world"

    println(a === b)
    // true
    println(a === c)
    // false
    println(c === d)
    // true

}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

对于在运行时由原始类型表示的值（例如 `Int`），`===` 相等性检查等同于 `==` 检查。

> 引用相等在 Kotlin/JS 中的实现方式不同。有关相等性的更多信息，请参见 [Kotlin/JS](js-interop.md#equality) 文档。
>
{style="tip"}

## 浮点数相等 {id=floating-point-numbers-equality}

当相等性检查的操作数在静态类型上被认为是 `Float` 或 `Double`（可为空或不可为空）时，检查遵循
[IEEE 754 浮点数运算标准](https://en.wikipedia.org/wiki/IEEE_754)。

对于静态类型不是浮点数的操作数，行为有所不同。在这些情况下，结构性相等性被实现。
因此，对于静态类型不是浮点数的操作数的检查与 IEEE 标准不同。
在这种情况下：

* `NaN` 等于它自身
* `NaN` 大于任何其他元素（包括 `POSITIVE_INFINITY`）
* `-0.0` 不等于 `0.0`

有关更多信息，请参见 [浮点数比较](numbers.md#floating-point-numbers-comparison)。

## 数组相等 {id=array-equality}

要比较两个数组是否具有相同的元素并且顺序相同，请使用 [`contentEquals()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/content-equals.html)。

有关更多信息，请参见 [比较数组](arrays.md#compare-arrays)。
