[//]: # (title: 运算符重载)

Kotlin 允许您为预定义的一组类型操作符提供自定义实现。这些操作符具有预定义的符号表示（例如 `+` 或 `*`）和优先级。
要实现一个操作符，可以为相应的类型提供一个 [成员函数](functions.md#成员函数) 或者 [扩展函数](extensions.md)，并使用特定的名称。

为了重载一个操作符，需要在相应的函数上标记 `operator` 修饰符：

```kotlin
interface IndexedContainer {
    operator fun get(index: Int)
}
```

当您重写操作符重载时，可以省略 `operator`：

```kotlin
class OrdersList: IndexedContainer {
    override fun get(index: Int) { /*...*/ }   
}
```

## 一元操作符 {id=unary-operations}

### 一元前缀操作符 {id=unary-prefix-operators}

| 表达式  | 转换为              |
|------|------------------|
| `+a` | `a.unaryPlus()`  |
| `-a` | `a.unaryMinus()` |
| `!a` | `a.not()`        |

这个表格说明当编译器处理例如表达式 `+a` 时，它执行以下步骤：

- 确定 `a` 的类型，假设为 `T`。
- 查找具有 `operator` 修饰符且没有参数的 `unaryPlus()` 函数，用于接收者 `T`，即成员函数或扩展函数。
- 如果函数不存在或不确定，则会编译错误。
- 如果函数存在且其返回类型为 `R`，则表达式 `+a` 的类型为 `R`。

> 这些操作以及其他所有操作都针对 [基本类型](basic-types.md) 进行了优化，不会为它们引入函数调用开销。
>
{style="note"}

以下是重载一元减号操作符的示例：

```kotlin
data class Point(val x: Int, val y: Int)

operator fun Point.unaryMinus() = Point(-x, -y)

val point = Point(10, 20)

fun main() {
   println(-point)  // 输出 "Point(x=-10, y=-20)"
}
```

{kotlin-runnable="true"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIoEMAuyAEYA2yDOeGAChAJYB2qAFAG7LYYAe8GAkpQDQZ0MCeL7VAEoAOuTEQADjABOaCDIwAzAK7liZSgDo1yGbwCyFFXipCMAXg0VqAWkZdbvUePI8MkzakvXKVAIwADFwATIEuYqrqALbIFGYYwGIYKZIyNtjkVLaeNuYYAPQFHumUhCIgJDZUjBa2QVy8dWGiIGIAviAcIOgyAOYwqES4qEoK0QggAFbIdF3gENGSpNiyAGqyeKQQ5JMhWoEHIO1AA?_gl=1*mb3tl*_gcl_au*MjA4MDk2OTM1NS4xNzIwNjg2OTgy*_ga*MTE1NjIyMzg3NC4xNzIwNDg0ODI0*_ga_9J976DJZ68*MTcyMDY4Njk4NS4yLjEuMTcyMDY4NzAwMi40My4wLjA.)

### 增量和减量操作符 {id=increments-and-decrements}

| 表达式   | 转换为              |
|-------|------------------|
| `a++` | `a.inc()` + 详见下文 |
| `a--` | `a.dec()` + 详见下文 |

`inc()` 和 `dec()` 函数必须返回一个值，该值将赋给使用 `++` 或 `--` 操作的变量。它们不应该改变调用 `inc` 或 `dec` 的对象。

编译器为解析 **后缀** 形式的操作符（例如 `a++`）执行以下步骤：

- 确定 `a` 的类型，假设为 `T`。
- 查找具有 `operator` 修饰符且没有参数的 `inc()` 函数，适用于类型为 `T` 的接收者。
- 检查函数的返回类型是否是 `T` 的子类型。

计算表达式的效果如下：

- 将 `a` 的初始值存储到临时存储 `a0` 中。
- 将 `a0.inc()` 的结果赋给 `a`。
- 将 `a0` 作为表达式的结果返回。

对于 `a--`，步骤完全类似。

对于 **前缀** 形式 `++a` 和 `--a`，解析方式相同，效果是：

- 将 `a.inc()` 的结果赋给 `a`。
- 将 `a` 的新值作为表达式的结果返回。

## 二元操作符 {id=binary-operations}

### 算术操作符 {id=arithmetic-operators}

| 表达式     | 转换为               |
|---------|-------------------|
| `a + b` | `a.plus(b)`       |
| `a - b` | `a.minus(b)`      |
| `a * b` | `a.times(b)`      |
| `a / b` | `a.div(b)`        |
| `a % b` | `a.rem(b)`        |
| `a..b`  | `a.rangeTo(b)`    |
| `a..<b` | `a.rangeUntil(b)` |

对于这个表中的操作，编译器会直接解析为 **转换为** 列中的表达式。

下面是一个示例 `Counter` 类，它从给定的值开始，并可以使用重载的 `+` 操作符进行增量：

```kotlin
data class Counter(val dayIndex: Int) {
    operator fun plus(increment: Int): Counter {
        return Counter(dayIndex + increment)
    }
}
```

### `in` 操作符 {id=in-operator}

| 表达式       | 转换为              |
|-----------|------------------|
| `a in b`  | `b.contains(a)`  |
| `a !in b` | `!b.contains(a)` |

对于 `in` 和 `!in`，解析过程相同，但参数的顺序是相反的。

### 索引访问操作符 {id=indexed-access-operator}

| 表达式                     | 转换为                       |
|-------------------------|---------------------------|
| `a[i]`                  | `a.get(i)`                |
| `a[i, j]`               | `a.get(i, j)`             |
| `a[i_1, ...,  i_n]`     | `a.get(i_1, ...,  i_n)`   |
| `a[i] = b`              | `a.set(i, b)`             |
| `a[i, j] = b`           | `a.set(i, j, b)`          |
| `a[i_1, ...,  i_n] = b` | `a.set(i_1, ..., i_n, b)` |

方括号被转换为对应数量参数的 `get` 和 `set` 方法的调用。

### 调用操作符 {id=invoke-operator}

| 表达式                 | 转换为                        |
|---------------------|----------------------------|
| `a()`               | `a.invoke()`               |
| `a(i)`              | `a.invoke(i)`              |
| `a(i, j)`           | `a.invoke(i, j)`           |
| `a(i_1, ...,  i_n)` | `a.invoke(i_1, ...,  i_n)` |

圆括号被转换为对应数量参数的 `invoke` 方法的调用。

### 复合赋值操作符 {id=augmented-assignments}

| 表达式      | 转换为                |
|----------|--------------------|
| `a += b` | `a.plusAssign(b)`  |
| `a -= b` | `a.minusAssign(b)` |
| `a *= b` | `a.timesAssign(b)` |
| `a /= b` | `a.divAssign(b)`   |
| `a %= b` | `a.remAssign(b)`   |

对于赋值操作，例如 `a += b`，编译器执行以下步骤：

- 如果右列的函数可用：
    - 如果相应的二元函数（即 `plus()` 对应于 `plusAssign()`）也可用，并且 `a` 是可变变量，并且 `plus` 的返回类型是 `a`
      类型的子类型，则报告错误（模糊性）。
    - 确保其返回类型为 `Unit`，否则报告错误。
    - 生成 `a.plusAssign(b)` 的代码。
- 否则，尝试生成 `a = a + b` 的代码（包括类型检查：`a + b` 的类型必须是 `a` 的子类型）。

> Kotlin 中的赋值操作 **不是** 表达式。
>
{style="note"}

### 相等性和不等性操作符 {id=equality-and-inequality-operators}

| 表达式      | 转换为                               |
|----------|-----------------------------------|
| `a == b` | `a?.equals(b) ?: (b === null)`    |
| `a != b` | `!(a?.equals(b) ?: (b === null))` |

这些操作符只能使用 [`equals(other: Any?): Boolean`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-any/equals.html)
函数进行工作，
它可以被重写以提供自定义的相等性检查实现。任何其他具有相同名称的函数（如 `equals(other: Foo)`）都不会被调用。

> `===` 和 `!==`（身份检查）不可重载，因此没有它们的约定存在。
>
{style="note"}

`==` 操作是特殊的：它被转换为一个复杂的表达式，用于检查 `null`。
`null == null` 总是 true，对于非空的 `x`，`x == null` 总是 false，并且不会调用 `x.equals()`。

### 比较操作符 {id=comparison-operators}

| 表达式      | 转换为                   |
|----------|-----------------------|
| `a > b`  | `a.compareTo(b) > 0`  |
| `a < b`  | `a.compareTo(b) < 0`  |
| `a >= b` | `a.compareTo(b) >= 0` |
| `a <= b` | `a.compareTo(b) <= 0` |

所有比较操作都被转换为调用 `compareTo`，该函数需要返回 `Int` 类型。

### 属性委托操作符 {id=property-delegation-operators}

`provideDelegate`、`getValue` 和 `setValue` 操作符函数在 [Delegated properties](delegated-properties.md) 中有详细描述。

## 命名函数的中缀调用 {id=infix-calls-for-named-functions}

您可以通过使用 [中缀函数调用](functions.md#infix-notation) 来模拟自定义的中缀操作。
