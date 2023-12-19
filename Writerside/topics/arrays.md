[//]: # (title: 数组（Arrays）)

数组是一种数据结构，它可以容纳固定数量的相同类型或其子类型的值。
在 Kotlin 中，最常见的数组类型是对象类型数组，由 [`Array`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-array/) 类表示。

> 如果在对象类型数组中使用基本数据类型，这会对性能产生影响，因为基本数据类型会被
> [装箱](https://docs.oracle.com/javase/tutorial/java/data/autoboxing.html) 成对象。
> 为了避免装箱开销，请改用 [原始类型数组](#原始类型数组)。
>
{style="note"}

## 什么时候使用数组

在 Kotlin 中，当您有特殊的低级需求需要满足时，请使用数组。
例如，如果您有超出常规应用所需的性能要求，或者需要构建自定义数据结构。
如果您没有这些限制，建议使用 [集合](collections-overview.md)。

与数组相比，集合具有以下优点：
* 集合可以是只读的，这为您提供了更多的控制权，并允许您编写具有明确意图的健壮代码。
* 向集合添加或删除元素很容易。相比之下，数组的大小是固定的。向数组添加或删除元素的唯一方法是每次创建一个新数组，这非常低效：

  ```kotlin
  fun main() {
      var riversArray = arrayOf("Nile", "Amazon", "Yangtze")

      // 使用 `+=` 赋值操作会创建一个新的 `riversArray`，
      // 复制原始元素并添加 "Mississippi"。
      riversArray += "Mississippi"
      println(riversArray.joinToString())
      // Nile, Amazon, Yangtze, Mississippi
  }
  ```
  {kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="arrays-rivers-array-kotlin"}
  [**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGAbjgE4aN50yMDOAgo4zgE8MAXgxN%2BAgPJJC5EADk8AGxhyANBjndcALwhUQGuQE0caAOYAXHapDFK1WgHonGAKqcC5jJYAWMDABqURxOT3M0LBg0SwwIAAcOHEs8fQwwRhhkmE4xDDQYAHcWNg4ePkE1RxoXdIS8HLj2Zj8AiFZzAhwlDBgVKJjcsygxKChcuQBZPDCZz3j4vDlqkubyiSDRKbmdhaWQFfjWGKUiVjXeCQA6ACsIAgAVCABlS2PzEntMZ1dFFQ1tDg9GgNKYLNYYBpprNZnsHGgAL6GECWJjmGCWAAKSmSSHaWAQIBuOAYyMgWEWKkYADUyqk0ISAIxXACcVwATAAGEAIoA?_gl=1*10gpnv9*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMTAwMTk2OS40My4xLjE3MDEwMDYzNjEuNTUuMC4w&_ga=2.6601010.1212359242.1701001969-2060274793.1694940376)

* 您可以使用相等运算符 (`==`) 检查集合是否在结构上相等。
* 但是对于数组，您不能使用该运算符。
* 相反，您必须使用一个特殊的函数，您可以在 [比较数组](#比较数组) 中详细了解。

有关集合的更多信息，请参阅 [集合概述](collections-overview.md)。

## 创建数组

在 Kotlin 中创建数组，您可以使用：
* 函数，例如 [`arrayOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/array-of.html)、[`arrayOfNulls()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/array-of-nulls.html#kotlin$arrayOfNulls(kotlin.Int)) 或 [`emptyArray()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/empty-array.html)。
* `Array` 构造函数。

以下示例使用 [`arrayOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/array-of.html) 函数，并向其传递项目值：

```kotlin
fun main() {
    // 创建一个值为 [1, 2, 3] 的数组
    val simpleArray = arrayOf(1, 2, 3)
    println(simpleArray.joinToString())
    // 1, 2, 3
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="arrays-simple-array-kotlin"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGA9HRgMIBOMOALjAM4Y6Y4WLHAE8MAdzwcAFhgBuOADYoeGANoBGADQYATDoDMAXWq0FijNzxYADopgBBIaIwBePs5EB5JIW17DYlMaGxYCDkUiK1t7J2ERADoAKwgCABUIAGUOMLQAcxIgzFoGDH99DANKSgBfEC0QDkE8mA4ABUVOJAgWLAQQJJwFevAIWzx7FgA1GBYrCDR%2BjQSATgTdAAYQGqA%3D%3D?_gl=1*t912li*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMTAwMTk2OS40My4xLjE3MDEwMDYzNjEuNTUuMC4w&_ga=2.3570096.1212359242.1701001969-2060274793.1694940376)

此示例使用 [`arrayOfNulls()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/array-of-nulls.html#kotlin$arrayOfNulls(kotlin.Int))
函数创建一个指定大小、填充有 `null` 元素的数组：

```kotlin
fun main() {
    // 创建一个包含值 [null, null, null] 的数组
    val nullArray: Array<Int?> = arrayOfNulls(3)
    println(nullArray.joinToString())
    // null, null, null
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="arrays-null-array-kotlin"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGA9HRgMIBOMOALjAM4Y6Y4WLHAE8MAdzwcAFhgBuOADYoeGANpoUixQBoMm7XoOKAutVoLF%2BrYoCCQ0fAz3hIgDwBJNBwD8APgwAXj4HEQB5JAA5G25CAGZicxoABxYCDkUiYxdRADoAKwgCABUIAGUONLQAcxJEzFoGa0Nm3VbKSgBfEB0QDkFqmA4ABUVOJAgWLAQQfJwFHvAILGS8RRgWADUN7jwINBmARlyATlyAJgAGEE6gA%3D%3D%3D?_gl=1*13lnwo1*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMTAwMTk2OS40My4xLjE3MDEwMDYzNjEuNTUuMC4w&_ga=2.77377557.1212359242.1701001969-2060274793.1694940376)

此示例使用 [`emptyArray()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/empty-array.html) 函数创建一个空数组：

```kotlin
    var exampleArray = emptyArray<String>()
```

> 您可以通过 Kotlin 的类型推断，在赋值的左侧或右侧指定空数组的类型。
>
> 例如：
> ```Kotlin
> var exampleArray = emptyArray<String>()
> 
> var exampleArray: Array<String> = emptyArray()
>```
>
{style="note"}

`Array` 构造函数接受数组大小和一个函数，该函数根据索引返回数组元素的值：

```kotlin
fun main() {
    // 创建一个用零 [0, 0, 0] 初始化的 Array<Int>
    val initArray = Array<Int>(3) { 0 }
    println(initArray.joinToString())
    // 0, 0, 0

    // 创建一个值为 ["0", "1", "4", "9", "16"] 的 Array<String>
    val asc = Array(5) { i -> (i * i).toString() }
    asc.forEach { print(it) }
    // 014916
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="arrays-array-constructor-kotlin"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGA9HRgMIBOMOALjAM4Y6YBBFixwBPADwBJNBwB8GDgAtOGAng54cAGzwAvHhgDu6xRn0sIvANoAGADQZ7jgLrVaAN22q06oSNEYALwYfmJSMrKEAMykwI4YAL5uNAAOLAQcWkRqHKGiAHQAVhAEACoQAMoc6WgA5iTEyfSMTq2UTQzMbJwG%2FCHCYVU1tfLGShieWigGVuQgNnMOcwCMixhzACxrcwCc2yDLAGxzrpgeXjjcYEH9%2FoQArLGqGAC08oR4GABUqsT5HJVqgR6qQkmcaJcwPkkBAWABRHBgUxxNIZD4cUEdFrLDY7I7tNAJEB2EAcHAsWowDgABS0nBhLCwCBAhRwnmJ4AgWBSeC0MBYADV%2Bdw8BA0MzlvkdvkAEw2EAJIA%3D?_gl=1*13lnwo1*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMTAwMTk2OS40My4xLjE3MDEwMDYzNjEuNTUuMC4w&_ga=2.77377557.1212359242.1701001969-2060274793.1694940376)

> 与大多数编程语言一样，Kotlin 中的索引从 0 开始。
>
{style="note"}

### 嵌套数组

数组可以相互嵌套以创建多维数组：

```kotlin
fun main() {
    // 创建一个二维数组
    val twoDArray = Array(2) { Array<Int>(2) { 0 } }
    println(twoDArray.contentDeepToString())
    // [[0, 0], [0, 0]]

    // 创建一个三维数组
    val threeDArray = Array(3) { Array(3) { Array<Int>(3) { 0 } } }
    println(threeDArray.contentDeepToString())
    // [[[0, 0, 0], [0, 0, 0], [0, 0, 0]], [[0, 0, 0], [0, 0, 0], [0, 0, 0]], [[0, 0, 0], [0, 0, 0], [0, 0, 0]]]
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="arrays-multidimensional-array-kotlin"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGA9HRgMIBOMOALjAM4Y4YcA7hAC0UPFhhpueCGhwAbPixY4AntVoA3RQOEARAIIr1GALwZjqtYQBMpYJZNqAPAEk0HAHx2HGAAwYAL7BmjQADiwEHApEQhBGzgB0kJ5SHPowMOEAKhAAyhxRaADmJMRh9IwA2tX%2BADQBALqNdY3%2BTU2UlQzMbJw8fAIAFmwwYhJSMnK6OM6VOkoco1mJ1uZO1oQAzH5W6jt7zu6ePrtkAcFXQZWR0bGEy2Nr6ilyXJ6Z2XmFxWXEFUwtF6tTaAXaLQwYIazVaMPhnVa0IhcPaKKh8IhkNqmNhGLReORzU63TQQRA9RAHDmJRgHAACgpOEgICwsAgQAArHA6CngCBYcJ4BQwFgANVF0zQHIAjEkAJxJWz%2BEBBIA%3D%3D?_gl=1*13lnwo1*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMTAwMTk2OS40My4xLjE3MDEwMDYzNjEuNTUuMC4w&_ga=2.77377557.1212359242.1701001969-2060274793.1694940376)

> 嵌套数组不必具有相同的类型或相同的大小。
>
{style="note"}

## 访问和修改元素

数组始终是可变的。要访问和修改数组中的元素，请使用[索引访问运算符](operator-overloading.md#indexed-access-operator)`[]`：

```kotlin
fun main() {
    val simpleArray = arrayOf(1, 2, 3)
    val twoDArray = Array(2) { Array<Int>(2) { 0 } }

    // 访问元素并修改它
    simpleArray[0] = 10
    twoDArray[0][0] = 2

    // 打印修改后的元素
    println(simpleArray[0].toString()) // 10
    println(twoDArray[0][0].toString()) // 2
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="arrays-access-array-kotlin"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGAbjgDYYDOeWADozAIIBOfHAE8MAXgw4BwgPJJCARgA0GAEzKAzMWq0GzAC4B3CABF%2BgkeLPDCK0sAxWhAHgCSaPQD4bdjAAYMAL6BlNo0APRhDmBgMCwssRh6ABYwGDDcWDDuEmhQ2NB4SHgJeHqhrOxcvFJCANq%2BALpiGPK%2B5YYmjvUN3c0qIZi0ERgACnwEeiyJKflQhcV56TCZ7uUc4%2B6MRGyc3F2NAHR6EADKehsA5iSkw61rG3pbhB2mNd3dR6fnBFfEN5H9KhoAIgRQgPSSC4wPQjRg4PRICB8LAIEAAKxwDFB4AgnDw3D4ADUYHw2BA0Kj5AcAJwHFS%2BEABIA%3D%3D%3D?_gl=1*13lnwo1*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMTAwMTk2OS40My4xLjE3MDEwMDYzNjEuNTUuMC4w&_ga=2.77377557.1212359242.1701001969-2060274793.1694940376)

Kotlin 中的数组是 **不变** 的。这意味着 Kotlin 不允许将 `Array<String>` 赋值给 `Array<Any>`，以防止可能的运行时失败。
相反，您可以使用 `Array<out Any>`。
有关更多信息，请参阅 [类型投影](generics.md#类型投影)。

## 使用数组进行操作

在 Kotlin 中，您可以通过将数组用作传递可变数量的参数给函数或对数组本身执行操作的方式来处理数组。
例如，比较数组，转换其内容或将其转换为集合。

### 向函数传递可变数量的参数

在 Kotlin 中，您可以通过 [`vararg`](functions.md#variable-number-of-arguments-varargs) 参数向函数传递可变数量的参数。
当您事先不知道参数的数量时，比如在格式化消息或创建 SQL 查询时，这非常有用。

要将包含可变数量参数的数组传递给函数，请使用 **扩展** 操作符 (`*`)。扩展操作符将数组的每个元素作为个别参数传递给您选择的函数：

```kotlin
fun main() {
    val lettersArray = arrayOf("c", "d")
    printAllStrings("a", "b", *lettersArray)
    // abcd
}

fun printAllStrings(vararg strings: String) {
    for (string in strings) {
        print(string)
    }
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="arrays-vararg-array-kotlin"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6mG1AbjgDYb0wAuLMATgM4CCHHOAJ4YAvBhz8hAeSSFy4eQBoM8qPOKVq1AA4cCLHvXoBlFnrQBzLnJA4lKkACN7AKmZtOvSYI1VqAen9xRzA1NABfSkpUTF19QxMzAitCOgEOCwwuJMsueAxTcwtSCj8MJAgODEJsoowCLJyrEs0tNri0Fhqm3zbIiJBFEBYJC1YABXocFgqOLAQQACscOkHwCCxtPGYOADVPPAg0BYBGADoATjOAJgAGEHCgA%3D%3D?_gl=1*1t2d88r*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMTAwMTk2OS40My4xLjE3MDEwMDYzNjEuNTUuMC4w&_ga=2.6083251.1212359242.1701001969-2060274793.1694940376)

有关更多信息，请参阅 [可变数量的参数（varargs）](functions.md#variable-number-of-arguments-varargs)。

### 比较数组 {id=比较数组}

要比较两个数组是否具有相同顺序的相同元素，请使用
[`.contentEquals()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/content-equals.html) 和
[`.contentDeepEquals()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/content-deep-equals.html) 函数：

```kotlin
fun main() {
    val simpleArray = arrayOf(1, 2, 3)
    val anotherArray = arrayOf(1, 2, 3)

    // 比较数组的内容
    println(simpleArray.contentEquals(anotherArray))
    // true

    // 使用中缀表示法，在元素更改后比较数组的内容
    simpleArray[0] = 10
    println(simpleArray contentEquals anotherArray)
    // false
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="arrays-compare-array-kotlin"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGAbjgDYYDOeWADozAIIBOfHAE8MAXgw4BwgPJJCARgA0GAEzKAzMWq0GzHGggAXABYw%2B%2FQSPGTLshcrUZNlbTQD0bjAGEInSTBYMSDRDGBDAiCQJKSEWVwwOPgJDRiI2Tm4LYQA6YNCQgFEARxQmFkJ9I1NzGOItTFoPDEM%2BFBgXBvdPAFU2NABzDAIkPAAPDANDHEM8CDRlSD8%2BAKC5%2FMMIqJthQJwkUL4JTBhuLDDDDHimvECwY31%2BmCh49K5eGIBtAAYAXTEMeRfeKJZKpQivTIxVYhc7FUqMXaTapZIT1WgYJpIMrtKhoAC%2BIEUICmfEehgACoxpkgIHwsAgQAArHAMQngXwcPDcPgANTMbDmDPk2QAnNkVF8QHigA%3D%3D?_gl=1*1t2d88r*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMTAwMTk2OS40My4xLjE3MDEwMDYzNjEuNTUuMC4w&_ga=2.6083251.1212359242.1701001969-2060274793.1694940376)

> 不要使用等号 (`==`) 和不等号 (`!=`) [运算符](equality.md#structural-equality) 来比较数组的内容。
> 这些运算符会检查分配的变量是否指向相同的对象。
>
> 要了解 Kotlin 中数组行为的更多原因，请参阅我们的
> [博客文章](https://blog.jetbrains.com/kotlin/2015/09/feedback-request-limitations-on-data-classes/#Appendix.Comparingarrays)。
> 
{style="warning"}

### 转换数组

Kotlin 提供了许多有用的函数来转换数组。
本文档只是突出了其中一些，但这并不是一个详尽无遗的列表。
有关完整的函数列表，请参阅我们的 [API 参考](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-array/)。

#### 求和

要返回数组中所有元素的总和，请使用 [`.sum()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sum.html) 函数：

```Kotlin
fun main() {
    val sumArray = arrayOf(1, 2, 3)

    // 对数组元素求和
    println(sumArray.sum())
    // 6
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="arrays-sum-array-kotlin"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGAbjgDYYDOKWAggE5c4CeGALwYcPfgHkkhAIwAaDACZ5AZmKVqtAPSaMAZXYsRYgTEYwsMNABcWGmgAcuBK4yJtOxgHTuSazFp0ANnU0AF8QWRArUQBzGCsABUYcKyQILiwEEAArHAYI8AgsezwzLgA1GC4WPAg0LOlPAE5PBQAGEFCgA%3D%3D%3D?_gl=1*1t2d88r*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMTAwMTk2OS40My4xLjE3MDEwMDYzNjEuNTUuMC4w&_ga=2.6083251.1212359242.1701001969-2060274793.1694940376)

> `.sum()` 函数仅适用于 [数字数据类型](numbers.md) 的数组，例如 `Int`。
>
{style="note"}

#### 洗牌（随机排列）

要对数组中的元素进行随机洗牌，请使用 [`.shuffle()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/shuffle.html) 函数：

```Kotlin
fun main() {
    val simpleArray = arrayOf(1, 2, 3)

    // 随机排列元素 [3, 2, 1]
    simpleArray.shuffle()
    println(simpleArray.joinToString())

    // 再次打乱元素 [2, 3, 1]
    simpleArray.shuffle()
    println(simpleArray.joinToString())
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="arrays-shuffle-array-kotlin"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGAbjgDYYDOeWADozAIIBOfHAE8MAXgw4BwgPJJCARgA0GAEzKAzMUrVaAel0YAygAsUSJNxYYY3LDDQAXKwG11ytRnkBdHTTadufkEhADoWU3NuEl8MDj4CB0Yify5eKVCAKwgCABUIQwd4tABzEi0qTD0DEzMLGCsbGDtHKxxi%2FExnDzdPH0q%2FdlSg4TCIuuj%2B2KLE5MHA9JCs3PzCglLicsoAXxBFEAdJYpgHAAVGHAckCD4sBBAMnAZd8AhOPG4%2BADUYPjYINDu8hCAE4QioAAwgLZAA%3D?_gl=1*1t2d88r*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMTAwMTk2OS40My4xLjE3MDEwMDYzNjEuNTUuMC4w&_ga=2.6083251.1212359242.1701001969-2060274793.1694940376)

### 将数组转换为集合

如果您在使用不同的 API，其中一些使用数组，另一些使用集合，那么您可以将数组转换为 [集合](collections-overview.md) 反之亦然。

#### 转换为 List 或 Set

要将数组转换为 `List` 或 `Set`，请使用
[`.toList()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/to-list.html)
和
[`.toSet()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/to-set.html) 函数。

```kotlin
fun main() {
    val simpleArray = arrayOf("a", "b", "c", "c")

    // 转换为 Set
    println(simpleArray.toSet())
    // [a, b, c]

    // 转换为 List
    println(simpleArray.toList())
    // [a, b, c, c]
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="arrays-convert-list-set-kotlin"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGAbjgDYYDOeWADozAIIBOfHAE8MAXgw4BwgPJJC5EDgUAaDAoBGKteC0KwC4pWq0A9CYwBhCGjow%2BAFxYZ7ECRgDKMe8Zoc%2BBe0YiNk5ufkEhADoXT3sSQ0xTcwBtHFV1VTAAXSNEmjNLa1sHJxc3ABk8Fm88jD8AoMIQrl4pKJdK6vifDALU9MzMnKo0AF8QZRB7SQBzLwAFRhx7JAg%2BLAQQACscBgnwCE48bj4ANTs2a02ARkiATkiAJgAGEFGgA%3D%3D?_gl=1*jnabh1*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMTAwMTk2OS40My4xLjE3MDEwMDYzNjEuNTUuMC4w&_ga=2.172930211.1212359242.1701001969-2060274793.1694940376)

#### 转换为 Map

要将数组转换为 `map`，请使用 [`.toMap()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/to-map.html) 函数。

只有 [`Pair<K,V>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-pair/) 类型的数组才能转换为 `map`。
`Pair` 实例的第一个值成为键，第二个值成为值。此示例使用 [中缀表示法](functions.md#infix-notation)
调用 [`to`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/to.html) 函数来创建 `Pair` 的元组：

```kotlin
fun main() {
    val pairArray = arrayOf("apple" to 120, "banana" to 150, "cherry" to 90, "apple" to 140)

    // 转换为 Map
    // 键是水果，值是它们的卡路里数
    // 注意键必须是唯一的，因此"apple"的最新值会覆盖第一个
    println(pairArray.toMap())
    // {apple=140, banana=150, cherry=90}
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="arrays-convert-map-kotlin"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGAbjgDYYAO%2BATgILvs4CeGALwYcPfgHkkhciBwsWjGDIwAXCBgCMAJgAMAGgwyARjjSmcytZoCs%2Bw%2BAAWMHn0vqAnHZlyFSkKvUNABYdYkpqWgB6SIwAYQg0OmcVAGcAkQwAWTkImmiMABUnDABrGD400RgMJHYUPFSRNChVYoZGFBhK9mqVJzx2DDQULCNnDAgkDDAmCHY8LtyMfIA5CBVqhwgAd1LytKwUFJUMMYx0PABHToMU9T7qxhwN4%2FomTomp73lFGSX8iBJdjbeYvVrVJADY5LFjzNAqRhENgDbi8PgAOjU2RYJDCmCiMWAPkUgmCdhMZjMpNsBjAThcgk8AF9wlQ0EyQHoQCpRABzGAqAAKTxUSDmWAQIAAVjgGJzwBAsCw8Ip2AA1ZwpPAJSUadHudG6EBMoA%3D?_gl=1*jnabh1*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMTAwMTk2OS40My4xLjE3MDEwMDYzNjEuNTUuMC4w&_ga=2.172930211.1212359242.1701001969-2060274793.1694940376)

## 原始类型数组 {id=原始类型数组}

如果您使用 `Array` 类处理原始值，这些值会被装箱为对象。
作为替代方案，您可以使用原始类型数组，它允许您在数组中存储原始值而不会产生装箱开销：

| 原始类型数组                                                                                | Java 中的等效类型 |
|---------------------------------------------------------------------------------------|-------------|
| [`BooleanArray`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-boolean-array/) | `boolean[]` |
| [`ByteArray`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-byte-array/)       | `byte[]`    |
| [`CharArray`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-char-array/)       | `char[]`    |
| [`DoubleArray`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-double-array/)   | `double[]`  |
| [`FloatArray`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-float-array/)     | `float[]`   |
| [`IntArray`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-int-array/)         | `int[]`     |
| [`LongArray`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-long-array/)       | `long[]`    |
| [`ShortArray`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-short-array/)     | `short[]`   |

这些类与 `Array` 类没有继承关系，但它们具有相同的一组函数和属性。

此示例创建了 `IntArray` 类的一个实例：

```kotlin
fun main() {
    // 创建一个大小为 5 的 Int 数组，并初始化为 0
    val exampleArray = IntArray(5)
    println(exampleArray.joinToString())
    // 0, 0, 0, 0, 0
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="arrays-primitive-type-array-kotlin"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGA9HRgMIBOMOALjAM4Y6Y4WLHAE8MEJBgCSaDuMnc8ALxgYArBgDueDgAsMANxwAbFD2q0jxjDAAeOLAAdjMAIJDRGALzTZ74SKEasQWNI4sBBzGRHYOzm4eIgB0AFYQBAAqEADKHBFoAOYkIZi0DBgADAA0lTXVtZWUlAC%2BIFUgHIIFMBwACsacSBAsWAggKThGbeAQTnguLABqMCyKEGhjAIxJAJxJAEwVIM1AA%3D%3D%3D?_gl=1*jnabh1*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMTAwMTk2OS40My4xLjE3MDEwMDYzNjEuNTUuMC4w&_ga=2.172930211.1212359242.1701001969-2060274793.1694940376)

> 要将原始类型数组转换为对象类型数组，请使用 [`.toTypedArray()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/to-typed-array.html) 函数。
>
> 要将对象类型数组转换为原始类型数组，请使用
> [`.toBooleanArray()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/to-boolean-array.html)、
> [`.toByteArray()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/to-byte-array.html)、
> [`.toCharArray()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/to-char-array.html) 等。
> 
{style="note"}

## 下一步是什么？

* 要了解为什么我们建议在大多数情况下使用集合，请阅读我们的 [集合概述](collections-overview.md)。
* 了解其他 [基本类型](basic-types.md)。
* 如果您是 Java 开发人员，请阅读我们的 Java 到 Kotlin 迁移指南，了解 [集合](java-to-kotlin-collections-guide.md)。
