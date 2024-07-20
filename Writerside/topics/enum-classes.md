[//]: # (title: 枚举类)

枚举类最基本的用例是实现类型安全的枚举：

```kotlin
enum class Direction {
    NORTH, SOUTH, WEST, EAST
}
```

每个枚举常量都是一个对象。枚举常量之间用逗号分隔。

由于每个枚举都是枚举类的实例，因此可以像这样进行初始化：

```kotlin
enum class Color(val rgb: Int) {
    RED(0xFF0000),
    GREEN(0x00FF00),
    BLUE(0x0000FF)
}
```

## 匿名类

枚举常量可以声明它们自己的匿名类，包括相应的方法，以及覆盖基本方法。

```kotlin
enum class ProtocolState {
    WAITING {
        override fun signal() = TALKING
    },

    TALKING {
        override fun signal() = WAITING
    };

    abstract fun signal(): ProtocolState
}
```

如果枚举类定义了任何成员，请使用分号将常量定义与成员定义分隔开。

## 在枚举类中实现接口

枚举类具有实现接口的能力（但无法从其他类继承）。它可以为所有枚举条目提供接口成员的共同实现，或者在每个枚举条目的匿名类中单独实现。
要实现接口，只需在枚举类声明中添加希望实现的接口，如下所示：

```kotlin
import java.util.function.BinaryOperator
import java.util.function.IntBinaryOperator

//sampleStart
enum class IntArithmetics : BinaryOperator<Int>, IntBinaryOperator {
    PLUS {
        override fun apply(t: Int, u: Int): Int = t + u
    },
    TIMES {
        override fun apply(t: Int, u: Int): Int = t * u
    };
    
    override fun applyAsInt(t: Int, u: Int) = apply(t, u)
}
//sampleEnd

fun main() {
    val a = 13
    val b = 31
    for (f in IntArithmetics.values()) {
        println("$f($a, $b) = ${f.apply(a, b)}")
    }
}
```
{kotlin-runnable="true"}


所有枚举类默认都实现了 [Comparable](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-comparable/index.html) 接口。
枚举类中的常量按照自然顺序定义。有关详细信息，请参阅 [排序](collection-ordering.md)。

## 使用枚举常量

Kotlin 中的枚举类具有用于列出定义的枚举常量和通过名称获取枚举常量的合成方法。这些方法的签名如下（假设枚举类的名称为 `EnumClass`）：

```kotlin
EnumClass.valueOf(value: String): EnumClass
EnumClass.values(): Array<EnumClass>
```

以下是这些方法的示例用法：

```kotlin
enum class RGB { RED, GREEN, BLUE }

fun main() {
    for (color in RGB.values()) println(color.toString()) // 打印 RED, GREEN, BLUE
    println("第一种颜色是: ${RGB.valueOf("RED")}") // 打印 "第一种颜色是: RED"
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="rgb-enums-kotlin"}

`valueOf()` 方法如果指定的名称与类中定义的任何枚举常量不匹配，则会抛出 `IllegalArgumentException`。

在 Kotlin 1.9.0 中，引入了 `entries` 属性作为 `values()` 函数的替代。
`entries` 属性返回预分配的枚举常量的不可变列表。在处理 [集合](collections-overview.md) 时，这特别有用，并且有助于避免 [性能问题](https://github.com/Kotlin/KEEP/blob/master/proposals/enum-entries.md#examples-of-performance-issues)。

例如：

```kotlin
enum class RGB { RED, GREEN, BLUE }

fun main() {
    for (color in RGB.entries) println(color.toString()) // 打印 RED, GREEN, BLUE
}
```

每个枚举常量还具有 [`name`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-enum/name.html) 和 [`ordinal`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-enum/ordinal.html) 属性，用于获取其名称和在枚举类声明中的位置（从 0 开始，相当于索引）：

```kotlin
enum class RGB { RED, GREEN, BLUE }

fun main() {
    //sampleStart
    println(RGB.RED.name) // 打印 RED
    println(RGB.RED.ordinal) // 打印 0
    //sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="rgb-enums-properties-kotlin"}

可以使用 [`enumValues<T>()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/enum-values.html) 和 [`enumValueOf<T>()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/enum-value-of.html) 函数以一种通用的方式访问枚举类中的常量：

```kotlin
enum class RGB { RED, GREEN, BLUE }

inline fun <reified T : Enum<T>> printAllValues() {
    println(enumValues<T>().joinToString { it.name })
}

printAllValues<RGB>() // 打印 RED, GREEN, BLUE
```

> 有关内联函数和具体化类型参数的更多信息，请参阅 [内联函数](inline-functions.md)。
>
> {type="tip"}

在 Kotlin 1.9.20 中，引入了 `enumEntries<T>()` 函数，作为 [`enumValues<T>()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/enum-values.html) 函数的未来替代。

`enumValues<T>()` 函数仍然受支持，但我们建议改用 `enumEntries<T>()` 函数，因为它的性能影响较小。
每次调用 `enumValues<T>()` 都会创建一个新数组，而每次调用 `enumEntries<T>()` 时都会返回相同的列表，这更为高效。

例如：

```kotlin
enum class RGB { RED, GREEN, BLUE }

@OptIn(ExperimentalStdlibApi::class)
inline fun <reified T : Enum<T>> printAllValues() {
    println(enumEntries<T>().joinToString { it.name })
}

printAllValues<RGB>() 
// RED, GREEN, BLUE
```

> `enumEntries<T>()` 函数是实验性的。
> 要使用它，请通过 `@OptIn(ExperimentalStdlibApi)` 进行选择，并将 [语言版本设置至少为 1.9](gradle-compiler-options.md#attributes-common-to-jvm-and-js)。
>
{style="warning"}