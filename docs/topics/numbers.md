[//]: # (title: 数字（Numbers）)

## 整数类型 {id=整数类型}

Kotlin 提供了一组内置类型来表示数字。  
对于整数类型，有四种不同大小和值范围的类型：

| 类型      | 大小(比特) | 最小值                                          | 最大值                                            |
|---------|--------|----------------------------------------------|------------------------------------------------|
| `Byte`  | 8      | -128                                         | 127                                            |
| `Short` | 16     | -32768                                       | 32767                                          |
| `Int`   | 32     | -2,147,483,648 (-2<sup>31</sup>)             | 2,147,483,647 (2<sup>31</sup> - 1)             |
| `Long`  | 64     | -9,223,372,036,854,775,808 (-2<sup>63</sup>) | 9,223,372,036,854,775,807 (2<sup>63</sup> - 1) |

> 除了有符号整数类型外，Kotlin 还提供了无符号整数类型。
> 由于无符号整数针对不同的使用场景，它们将单独介绍。
> 参见 [](unsigned-integer-types.md)。
>
{style="tip"}

当您初始化变量且未显式指定类型时，编译器会自动推断出从 `Int` 开始、足以表示该值的最小范围类型。
如果值不超过 `Int` 的范围，则类型为 `Int`；如果超过该范围，则类型为 `Long`。
要显式指定 `Long` 值，请在值后追加后缀 `L`。
要使用 `Byte` 或 `Short` 类型，请在声明中显式指定。
显式类型声明会触发编译器检查该值是否不超过指定类型的范围。

```kotlin
val one = 1 // Int
val threeBillion = 3000000000 // Long
val oneLong = 1L // Long
val oneByte: Byte = 1
```

## 浮点类型 {id=floating-point-types}

对于实数，Kotlin 提供了遵循 [IEEE 754 标准](https://en.wikipedia.org/wiki/IEEE_754) 的浮点数类型 `Float` 和 `Double`。
`Float` 表示 IEEE 754 的 **单精度**，而 `Double` 表示 **双精度**。

这些类型在大小上有所不同，并提供了不同精度的浮点数存储：

| 类型       | 位数（比特） | 有效位数 | 指数位数 | 十进制精度 |
|----------|--------|------|------|-------|
| `Float`  | 32     | 24   | 8    | 6-7   |
| `Double` | 64     | 53   | 11   | 15-16 |    

初始化 `Double` 和 `Float` 变量时，只能使用带有小数部分的数字。  
用点号（`.`）分隔整数部分和小数部分。

对于使用小数初始化的变量，编译器会推断为 `Double` 类型：

```kotlin
val pi = 3.14          // Double

val one: Double = 1    // 推断为 Int
// 初始化器类型不匹配

val oneDouble = 1.0    // Double
```
{validate="false"}

若要显式指定值的 `Float` 类型，请添加后缀 `f` 或 `F`。  
如果以这种方式提供的值超过 7 位小数，则会进行四舍五入：

```kotlin
val e = 2.7182818284          // Double
val eFloat = 2.7182818284f    // Float，实际值为 2.7182817
```

Unlike in some other languages, there are no implicit widening conversions for numbers in Kotlin.
For example, a function with a `Double` parameter can be called only on `Double` values, but not `Float`,
`Int`, or other numeric values:

```kotlin
fun main() {
//sampleStart
    fun printDouble(x: Double) { print(x) }

    val x = 1.0
    val xInt = 1    
    val xFloat = 1.0f 

    printDouble(x)
    
    printDouble(xInt)   
    // 参数类型不匹配
    
    printDouble(xFloat)
    // 参数类型不匹配
//sampleEnd
}
```
{kotlin-runnable="true" validate="false"}

要将数字值转换为不同类型，请使用[显式转换](#显式数字转换)。

## 数字的字面常量

整型数值的字面常量有以下几种形式：

* 十进制：`123`
* Long 类型，以大写 `L` 结尾：`123L`
* 十六进制：`0x0F`
* 二进制：`0b00001011`

> 在 Kotlin 中不支持八进制字面常量。
>
{style="note"}

Kotlin 同样支持标准的浮点数字面量表示法：

* Double 类型（默认，小数部分不以字母结尾）：`123.5`、`123.5e10`
* Float 类型，以字母 `f` 或 `F` 结尾：`123.5f`

你可以使用下划线使数字常量更易读：

```kotlin
val oneMillion = 1_000_000
val creditCardNumber = 1234_5678_9012_3456L
val socialSecurityNumber = 999_99_9999L
val hexBytes = 0xFF_EC_DE_5E
val bytes = 0b11010010_01101001_10010100_10010010
val bigFractional = 1_234_567.7182818284
```

> 无符号整型字面量还包含特殊后缀。  
> 了解更多关于[无符号整型字面量](unsigned-integer-types.md)的内容。
>
{style="tip"}

## Java 虚拟机上的数字装箱与缓存 {id=boxing-and-caching-numbers-on-the-java-virtual-machine}

由于 JVM 默认对较小（字节大小）数值使用缓存机制，其存储数字的方式可能导致代码行为与直觉不符。

JVM 将数字存储为基本类型：`int`、`double` 等。  
当使用[泛型类型](generics.md)或创建可空数字引用（如 `Int?`）时，数字会被装箱为 `Integer` 或 `Double` 等 Java 类。

JVM 对表示 `−128` 到 `127` 之间数值的 `Integer` 等对象应用了[内存优化技术](https://docs.oracle.com/javase/specs/jls/se22/html/jls-5.html#jls-5.1.7)。  
所有对此类对象的可空引用均指向同一个缓存对象。  
例如，以下代码中的可空对象具有[引用相等性](equality.md#referential-equality):

```kotlin
fun main() {
//sampleStart
    val a: Int = 100
    val boxedA: Int? = a
    val anotherBoxedA: Int? = a
    
    println(boxedA === anotherBoxedA) // true
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

对于超出此范围的数值，可空对象虽然不同但具有[结构相等性](equality.md#structural-equality)：

```kotlin
fun main() {
//sampleStart
    val b: Int = 10000
    val boxedB: Int? = b
    val anotherBoxedB: Int? = b
    
    println(boxedB === anotherBoxedB) // false
    println(boxedB == anotherBoxedB) // true
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

因此，Kotlin 会对可装箱数字和字面量使用引用相等性操作发出警告，提示：  
`"禁止对...和...类型的参数使用同一性相等检查"`
在比较 `Int`、`Short`、`Long` 和 `Byte` 类型（以及 `Char` 和 `Boolean`）时，  
应使用结构相等性检查以获取一致结果。

## 显式数字转换 {id=explicit-number-conversions}

由于采用不同的存储方式，数字类型之间_不存在子类型关系_。  
因此，较小类型_不会_隐式转换为较大类型，反之亦然。  
例如，将 `Byte` 类型值赋给 `Int` 变量需要显式转换：

```kotlin
fun main() {
//sampleStart
    val byte: Byte = 1
    // 允许，字面量会进行静态检查
    
    val intAssignedByte: Int = byte 
    // 初始化器类型不匹配
    
    val intConvertedByte: Int = byte.toInt()
    
    println(intConvertedByte)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" validate="false"}

所有数字类型都支持转换为其他类型：

* `toByte(): Byte` (deprecated for [Float](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin/-float/to-byte.html) and [Double](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin/-double/to-byte.html))
* `toShort(): Short`
* `toInt(): Int`
* `toLong(): Long`
* `toFloat(): Float`
* `toDouble(): Double`

在很多情况下，不需要显式转换，因为类型会从上下文中推断出来，而且算术运算符已经重载以自动处理转换。例如：

```kotlin
fun main() {
//sampleStart
    val l = 1L + 3       // Long + Int => Long
    println(l is Long)   // true
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.5"}

### Reasoning against implicit conversions

Kotlin doesn't support implicit conversions because they can lead to unexpected behavior.

If numbers of different types were converted implicitly, we could sometimes lose equality and identity silently.
For example, imagine if `Int` was a subtype of `Long`:

```kotlin
// Hypothetical code, does not actually compile:
val a: Int? = 1    // A boxed Int (java.lang.Integer)
val b: Long? = a   // Implicit conversion yields a boxed Long (java.lang.Long)
print(b == a)      // Prints "false" as Long.equals() checks not only the value but whether the other number is Long as well
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

您可以在自定义数字类中重写这些运算符。  
详情请参阅[运算符重载](operator-overloading.md) {id=operator-overloading}

### 整数的除法

整数之间的除法运算总是返回整数，小数部分会被舍弃。

```kotlin
fun main() {
//sampleStart
    val x = 5 / 2
    println(x == 2.5) 
    // 运算符 '==' 不能应用于 'Int' 和 'Double' 类型
    
    println(x == 2)   
    // 输出 true  
//sampleEnd
}
``` {id=division-between-integer-numbers}
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" validate="false"}

这对于任何两个整数类型之间的除法都是成立的：

```kotlin
fun main() {
//sampleStart
    val x = 5L / 2
    println (x == 2)
    // Error, as Long (x) cannot be compared to Int (2)
    
    println(x == 2L)
    // true
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" validate="false"}

要返回带小数部分的除法结果，请显式地将其中一个参数转换为浮点类型：

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
fun main() {
//sampleStart
    val x = 1
    val xShiftedLeft = (x shl 2)
    println(xShiftedLeft)  
    // 4
    
    val xAnd = x and 0x000FF000
    println(xAnd)          
    // 0
//sampleEnd
}
```

按位运算的完整列表：

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
    
    // 操作数未静态类型化为浮点数  
    // 因此 NaN 等于其自身
    println(listOf(Double.NaN) == listOf(Double.NaN)) // true

    // 操作数在静态上已知为浮点数
    println(0.0 == -0.0)                              // true
    
    // 操作数未静态类型化为浮点数  
    // 因此 -0.0 小于 0.0
    println(listOf(0.0) == listOf(-0.0))              // false

    println(listOf(Double.NaN, Double.POSITIVE_INFINITY, 0.0, -0.0).sorted())
    // [-0.0, 0.0, Infinity, NaN]
    //sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-numbers-floating-comp"}