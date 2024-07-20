[//]: # (title: 无符号整数类型（Unsigned integer types）)

除了[整数类型](numbers.md#整数类型)之外，Kotlin 还提供了以下用于无符号整数的类型：

* `UByte`：无符号 8 位整数，范围从 0 到 255
* `UShort`：无符号 16 位整数，范围从 0 到 65535
* `UInt`：无符号 32 位整数，范围从 0 到 2^32 - 1
* `ULong`：无符号 64 位整数，范围从 0 到 2^64 - 1

无符号类型支持它们有符号对应类型的大多数操作。

> 无符号数被实现为[内联类](inline-classes.md)，具有相同宽度的相应有符号对应类型的单一存储属性。
> 然而，从无符号类型更改为有符号对应类型（反之亦然）是一种 **二进制不兼容的** 更改。
>
{style="note"}

## 无符号数组和区间

> 无符号数组及其上的操作处于[Beta](components-stability.md)阶段。它们可以随时进行不兼容的更改。
> 需要选择性启用（请参阅下面的详细信息）。
>
{style="warning"}

与原始类型相同，每个无符号类型都有相应的数组类型：

* `UByteArray`：无符号字节数组
* `UShortArray`：无符号短整数数组
* `UIntArray`：无符号整数数组
* `ULongArray`：无符号长整数数组

与有符号整数数组一样，它们提供了与 `Array` 类似的 API，没有装箱开销。

当你使用无符号数组时，你会收到一个警告，指示这个特性还不够稳定。
为了消除警告，使用 `@ExperimentalUnsignedTypes` 注解选择性启用。
决定是否要求客户明确选择使用你的 API 是由你决定的，但请注意，无符号数组不是一个稳定的特性，因此使用它们的 API 可能会受到语言变化的影响。
[了解更多关于选择性启用的要求](opt-in-requirements.md)。

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