[//]: # (title: 无符号整数类型（Unsigned integer types）)

除了[整数类型](numbers.md#整数类型)之外，Kotlin 还提供了以下用于无符号整数的类型：

| 类型       | 大小（位） | 最小值 | 最大值                                             |
|----------|-------|-----|-------------------------------------------------|
| `UByte`  | 8     | 0   | 255                                             |
| `UShort` | 16    | 0   | 65,535                                          |
| `UInt`   | 32    | 0   | 4,294,967,295 (2<sup>32</sup> - 1)              |
| `ULong`  | 64    | 0   | 18,446,744,073,709,551,615 (2<sup>64</sup> - 1) |

无符号类型支持大多数有符号类型的操作。

> 无符号数字实现为具有单一存储属性的 [inline classes](inline-classes.md)，该属性包含对应的有符号类型，宽度相同。
> 如果你想在无符号和有符号整数类型之间转换，确保更新你的代码，以便任何函数调用和操作都支持新的类型。
>
{style="note"}

## 无符号数组和区间

> 无符号数组及其上的操作处于[Beta](components-stability.md)阶段。它们可以随时进行不兼容的更改。
> 需要选择性启用（请参阅下面的详细信息）。
>
{style="warning"}

与基本类型相同，每种无符号类型都有一个对应的类型，表示该类型的数组：

* `UByteArray`：无符号字节数组。
* `UShortArray`：无符号短整型数组。
* `UIntArray`：无符号整型数组。
* `ULongArray`：无符号长整型数组。

与有符号整数数组类似，它们提供了类似于 `Array` 类的 API，而且没有装箱开销。

当你使用无符号数组时，会收到一个警告，指示该功能尚不稳定。
要移除警告，可以使用 `@ExperimentalUnsignedTypes` 注解进行 opt-in。
你可以决定是否要求客户端显式 opt-in 使用你的 API，但请记住，无符号数组不是稳定的特性，因此使用它们的 API 可能会被语言的变化破坏。
[了解更多关于 opt-in 要求](opt-in-requirements.md)。

对于 `UInt` 和 `ULong`，[区间和数列](ranges.md)由类 `UIntRange`、`UIntProgression`、`ULongRange` 和 `ULongProgression` 支持。
与无符号整数类型一起，这些类是稳定的。

## 无符号整数字面常量

为了使无符号整数更容易使用，Kotlin 提供了一种方法，可以使用后缀标记整数字面常量，指示特定的无符号类型（类似于 `Float` 或 `Long`）：

* `u` 和 `U` 标签用于无符号字面常量。确切的类型取决于期望的类型。
  如果没有提供期望的类型，编译器将根据字面常量的大小使用 `UInt` 或 `ULong`。

  ```kotlin
  val b: UByte = 1u  // UByte, 已提供的预期类型
  val s: UShort = 1u // UShort, 已提供的预期类型
  val l: ULong = 1u  // ULong, 已提供的预期类型
  
  val a1 = 42u // UInt: 未提供预期类型，常量适合 UInt
  val a2 = 0xFFFF_FFFF_FFFFu // ULong: 未提供预期类型，常量不适合 UInt
  ```

* `uL` 和 `UL` 明确将字面常量标记为无符号长整数：

  ```kotlin
  val a = 1UL // ULong，即使没有提供期望的类型，常量仍然适合 UInt
  ```

## 使用案例

无符号数字的主要用例是利用整数的完整位区间来表示正值。  
例如，表示在 32 位 `AARRGGBB` 格式中不适合有符号类型的十六进制常量，比如颜色：

```kotlin
data class Color(val representation: UInt)

val yellow = Color(0xFFCC00CCu)
```

您可以使用无符号数字初始化字节数组，而无需显式的 `toByte()` 字面转换：

```kotlin
val byteOrderMarkUtf8 = ubyteArrayOf(0xEFu, 0xBBu, 0xBFu)
```

另一个用例是与本地 API 的互操作性。Kotlin 允许在签名中包含包含无符号类型的本地声明。映射不会用有符号整数替换无符号整数，保持语义不变。

### 非目标

尽管无符号整数只能表示正数和零，但不打算在应用程序领域需要非负整数的情况下使用它们。例如，作为集合大小或集合索引值的类型。

有几个原因：

* 使用有符号整数可以帮助检测意外溢出并发出错误条件，例如对于空列表 [`List.lastIndex`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/last-index.html)，其值为 -1。
* 无符号整数不能被视为有符号整数的范围限定版本，因为它们的值范围不是有符号整数范围的子集。有符号整数和无符号整数都不是彼此的子类型。