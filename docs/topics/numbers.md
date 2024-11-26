[//]: # (title: 数字（Numbers）)

## 整数类型 {id=整数类型}

Kotlin 提供了一组内置类型来表示数字。对于整数，有四种类型，它们具有不同的大小，因此值的范围也不同：

| 类型      | 大小（位） | 最小值                                          | 最大值                                            |
|---------|-------|----------------------------------------------|------------------------------------------------|
| `Byte`  | 8     | -128                                         | 127                                            |
| `Short` | 16    | -32768                                       | 32767                                          |
| `Int`   | 32    | -2,147,483,648 (-2<sup>31</sup>)             | 2,147,483,647 (2<sup>31</sup> - 1)             |
| `Long`  | 64    | -9,223,372,036,854,775,808 (-2<sup>63</sup>) | 9,223,372,036,854,775,807 (2<sup>63</sup> - 1) |

当你初始化一个没有指定显式类型的变量时，编译器会自动推断类型，该类型从 `Int` 开始推断，直到找到符合值范围的类型。
如果不超过 `Int` 的范围，类型为 `Int`。
如果超过范围，则类型为 `Long`。要明确指定 `Long` 值，请将后缀 `L` 追加到值。
显式类型规定会触发编译器检查值是否超过指定类型的范围。

```kotlin
val one = 1 // Int
val threeBillion = 3000000000 // Long
val oneLong = 1L // Long
val oneByte: Byte = 1
```

> 除了整数类型之外，Kotlin 还提供了无符号整数类型。有关更多信息，请参阅[无符号整数类型](unsigned-integer-types.md)。
>
{style="tip"}

## 浮点数类型

对于实数，Kotlin 提供了遵循 [IEEE 754 标准](https://en.wikipedia.org/wiki/IEEE_754) 的浮点数类型 `Float` 和 `Double`。
`Float` 表示 IEEE 754 的 **单精度**，而 `Double` 表示 **双精度**。

这些类型在大小上有所不同，并提供了不同精度的浮点数存储：

| 类型       | 大小（位） | 有效位数 | 指数位数 | 十进制数字位数 |
|----------|-------|------|------|---------|
| `Float`  | 32    | 24   | 8    | 6-7     |
| `Double` | 64    | 53   | 11   | 15-16   |    

你可以用具有小数部分的数字初始化 `Double` 和 `Float` 变量。
小数部分与整数部分由句点（`.`）分隔。
对于用小数初始化的变量，编译器会推断为 `Double` 类型：

```kotlin
val pi = 3.14 // Double
// val one: Double = 1 // 错误：类型不匹配
val oneDouble = 1.0 // Double
```

要显式指定值为 `Float` 类型，请添加后缀 `f` 或 `F`。
如果这样的值包含超过 6-7 个小数位，它将被舍入：

```kotlin
val e = 2.7182818284 // Double
val eFloat = 2.7182818284f // Float，实际值为2.7182817
```

与其他一些语言不同，在 Kotlin 中，数字没有隐式的扩展转换。
例如，具有 `Double` 参数的函数只能在 `Double` 值上调用，而不能在 `Float`、`Int` 或其他数值上调用：

```kotlin
fun main() {
    fun printDouble(d: Double) { print(d) }

    val i = 1    
    val d = 1.0
    val f = 1.0f 

    printDouble(d)
//    printDouble(i) // 错误：类型不匹配
//    printDouble(f) // 错误：类型不匹配
}
```

要将数字值转换为不同类型，请使用[显式转换](#显式数字转换)。

## 数字的字面常量

对于整数值，有以下几种字面常量：

* 十进制：`123`
* 长整型使用大写 `L` 标记：`123L`
* 十六进制：`0x0F`
* 二进制：`0b00001011`

> 在 Kotlin 中不支持八进制字面常量。
>
{style="note"}

Kotlin 还支持浮点数的传统表示法：

* 默认为双精度：`123.5`，`123.5e10`
* 浮点数使用 `f` 或 `F` 标记：`123.5f`

你可以使用下划线使数字常量更易读：

```kotlin
val oneMillion = 1_000_000
val creditCardNumber = 1234_5678_9012_3456L
val socialSecurityNumber = 999_99_9999L
val hexBytes = 0xFF_EC_DE_5E
val bytes = 0b11010010_01101001_10010100_10010010
```

> 对于无符号整数字面量，还有特殊的标签。  
> 阅读更多关于 [无符号整数类型的字面量](unsigned-integer-types.md) 的内容。
>
{style="tip"}


## JVM 平台上的数字表示 {id=numbers-representation-on-the-jvm}

在 JVM 平台上，数字被存储为原始类型：`int`、`double` 等。
特例是当你创建可空的数字引用，如 `Int?` 或使用泛型时。
在这些情况下，数字会被装箱为 Java 类 `Integer`、`Double` 等。

对同一数字的可空引用可能引用不同的对象：

```kotlin
fun main() {
//sampleStart
    val a: Int = 100
    val boxedA: Int? = a
    val anotherBoxedA: Int? = a
    
    val b: Int = 10000
    val boxedB: Int? = b
    val anotherBoxedB: Int? = b
    
    println(boxedA === anotherBoxedA) // true
    println(boxedB === anotherBoxedB) // false
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

所有对于 `a` 的可空引用实际上都是相同的对象，这是因为 JVM 在 `-128` 到 `127` 之间的 `Integer` 上应用的内存优化。
这并不适用于 `b` 引用，因此它们是不同的对象。

另一方面，它们仍然是相等的：

```kotlin
fun main() {
//sampleStart
    val b: Int = 10000
    println(b == b) // 打印 'true'
    val boxedB: Int? = b
    val anotherBoxedB: Int? = b
    println(boxedB == anotherBoxedB) // 打印 'true'
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

## 显式数字转换 {id=显式数字转换}

由于不同的表示方式，较小的类型 **并不是** 较大类型的子类型。
如果是的话，我们将会遇到以下类似的问题：

```kotlin
// 假设的代码，实际上无法编译：
val a: Int? = 1 // 一个装箱 Int (java.lang.Integer)
val b: Long? = a // 隐式转换产生一个装箱的 Long (java.lang.Long)
print(b == a) // 惊喜！这会打印出 "false"，因为 `Long` 的 `equals()` 方法检查另一个是否也是 `Long` 类型。
```

因此，相等性会默默丧失，更不用说标识了。

因此，较小的类型 **不会被隐式转换** 为较大的类型。
这意味着将 `Byte` 类型的值分配给 `Int` 变量需要进行显式转换：

```kotlin
val b: Byte = 1 // 正确，文字是静态检查的
// val i: Int = b // 错误
val i1: Int = b.toInt()
```

所有数字类型都支持转换为其他类型：

* `toByte(): Byte`
* `toShort(): Short`
* `toInt(): Int`
* `toLong(): Long`
* `toFloat(): Float`
* `toDouble(): Double`

在许多情况下，不需要显式转换，因为类型会从上下文中推断出来，并且算术操作已经为适当的转换进行了重载，例如：

```kotlin
val l = 1L + 3 // Long + Int => Long
```

## 数字的运算 {id=数字的运算}

Kotlin 支持一组标准的数字算术操作：`+`、`-`、`*`、`/`、`%`。
它们被声明为适当类的成员：

```kotlin
fun main() {
//sampleStart
    println(1 + 2)
    println(2_500_000_000L - 1L)
    println(3.14 * 2.71)
    println(10.0 / 3)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

你还可以为自定义类重写这些操作符。有关详细信息，请参阅[运算符重载](operator-overloading.md)。

### 整数的除法

整数之间的除法始终返回一个整数。任何小数部分都将被丢弃。

```kotlin
fun main() {
//sampleStart
    val x = 5 / 2
    //println(x == 2.5) // 错误：无法将操作符 '==' 应用于 'Int' 和 'Double'
    println(x == 2)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

这对于任何两个整数类型之间的除法都是成立的：

```kotlin
fun main() {
//sampleStart
    val x = 5L / 2
    println(x == 2L)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

如果想返回浮点类型，请显式将其中一个参数转换为浮点类型：

```kotlin
fun main() {
//sampleStart
    val x = 5 / 2.toDouble()
    println(x == 2.5)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

### 位运算

Kotlin 提供了一组在整数数字上进行 **位运算** 的操作。
它们直接在二进制级别上操作数字表示的位。
位运算由可以以中缀形式调用的函数表示。它们仅适用于 `Int` 和 `Long`：

```kotlin
val x = (1 shl 2) and 0x000FF000
```

以下是完整的位运算列表：

* `shl(bits)` – 有符号左移
* `shr(bits)` – 有符号右移
* `ushr(bits)` – 无符号右移
* `and(bits)` – 按位 **与**
* `or(bits)` – 按位 **或**
* `xor(bits)` – 按位 **异或**
* `inv()` – 按位求反

### 浮点数的比较 {id=floating-point-numbers-comparison}

本节讨论的浮点数上的操作有：

* 相等性检查：`a == b` 和 `a != b`
* 比较运算符：`a < b`、`a > b`、`a <= b`、`a >= b`
* 区间的创建和检查：`a..b`、`x in a..b`、`x !in a..b`

当操作数 `a` 和 `b` 在静态上已知为 `Float` 或 `Double` 或它们的可空对应类型（类型被声明、推断或是 [智能转换](typecasts.md#smart-casts) 的结果）时，
这些数字和它们形成的区间的操作遵循 [IEEE 754 浮点运算标准](https://en.wikipedia.org/wiki/IEEE_754)。

然而，为了支持泛型场景并提供总体排序，对于 **非** 静态类型为浮点数的情况，行为是不同的。
例如，`Any`、`Comparable<...>` 或 `Collection<T>` 类型。
在这种情况下，这些操作使用 `Float` 和 `Double` 的 `equals` 和 `compareTo` 实现。因此：

* `NaN` 被视为等于自身
* `NaN` 被视为大于任何其他元素，包括 `POSITIVE_INFINITY（正无穷大）`
* `-0.0` 被视为小于 `0.0`

下面是一个示例，展示了在作为静态类型为浮点数的操作数（`Double.NaN`）和作为 **非** 静态类型为浮点数的操作数（`listOf(T)`）之间的行为差异。

```kotlin
fun main() {
    //sampleStart
    // 操作数在静态上已知为浮点数
    println(Double.NaN == Double.NaN)                 // false
    // 操作数在静态上不已知为浮点数
    // 因此 NaN 等于它自己
    println(listOf(Double.NaN) == listOf(Double.NaN)) // true

    // 操作数在静态上已知为浮点数
    println(0.0 == -0.0)                              // true
    // 操作数在静态上不已知为浮点数
    // 因此 -0.0 小于 0.0
    println(listOf(0.0) == listOf(-0.0))              // false

    println(listOf(Double.NaN, Double.POSITIVE_INFINITY, 0.0, -0.0).sorted())
    // [-0.0, 0.0, Infinity, NaN]
    //sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-numbers-floating-comp"}