[//]: # (title: 集合)

<tldr>
    <p><img src="icon-1-done.svg" width="20" alt="第一步" /> <a href="kotlin-tour-hello-world.md">Hello world</a><br />
        <img src="icon-2-done.svg" width="20" alt="第二步" /> <a href="kotlin-tour-basic-types.md">基本类型</a><br />
        <img src="icon-3.svg" width="20" alt="第三步" /> <strong>集合</strong><br />
        <img src="icon-4-todo.svg" width="20" alt="第四步" /> <a href="kotlin-tour-control-flow.md">控制流</a><br />
        <img src="icon-5-todo.svg" width="20" alt="第五步" /> <a href="kotlin-tour-functions.md">函数</a><br />
        <img src="icon-6-todo.svg" width="20" alt="第六步" /> <a href="kotlin-tour-classes.md">类</a><br />
        <img src="icon-7-todo.svg" width="20" alt="最后一步" /> <a href="kotlin-tour-null-safety.md">空值安全</a></p>
</tldr>

在编程中，将数据分组到结构中以便后续处理是非常有用的。
Kotlin 为此提供了专门的集合类型。

Kotlin 提供了以下几种集合来分组元素：

| **集合类型** | **描述**                     |
|----------|----------------------------|
| 列表       | 有序的元素集合                    |
| 集合       | 唯一的无序元素集合                  |
| 映射       | 键值对集合，其中键是唯一的，并且每个键只映射到一个值 |

每种集合类型可以是可变的或只读的。

## 列表 {id=list}

列表按添加的顺序存储元素，并允许重复的元素。

要创建只读列表 ([`List`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list/))，使用
[`listOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/list-of.html) 函数。

要创建可变列表 ([`MutableList`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list.html))，使用
[`mutableListOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/mutable-list-of.html) 函数。

在创建列表时，Kotlin 可以推断存储元素的类型。
要显式声明类型，请在列表声明后添加尖括号 `<>` 中的类型：

```kotlin
fun main() { 
//sampleStart
    // 只读列表
    val readOnlyShapes = listOf("triangle", "square", "circle")
    println(readOnlyShapes)
    // [triangle, square, circle]
    
    // 带有显式类型声明的可变列表
    val shapes: MutableList<String> = mutableListOf("triangle", "square", "circle")
    println(shapes)
    // [triangle, square, circle]
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-lists-declaration"}

> 为了防止不必要的修改，您可以通过将可变列表赋值给 `List` 来创建一个只读视图：
> 
> ```kotlin
>     val shapes: MutableList<String> = mutableListOf("triangle", "square", "circle")
>     val shapesLocked: List<String> = shapes
> ```
> 这也称为 **类型转换**。
> 
{style="tip"}

列表是有序的，因此要访问列表中的项，请使用 [索引访问操作符](operator-overloading.md#indexed-access-operator) `[]`：

```kotlin
fun main() { 
//sampleStart
    val readOnlyShapes = listOf("triangle", "square", "circle")
    println("列表中的第一个元素是: ${readOnlyShapes[0]}")
    // 列表中的第一个元素是: triangle
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-list-access"}

要获取列表中的第一个或最后一个元素，分别使用 [`.first()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/first.html) 和
[`.last()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/last.html) 函数：

```kotlin
fun main() { 
//sampleStart
    val readOnlyShapes = listOf("triangle", "square", "circle")
    println("列表中的第一个元素是: ${readOnlyShapes.first()}")
    // 列表中的第一个元素是: triangle
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-list-first"}

> [`.first()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/first.html) 和 [`.last()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/last.html)
> 函数是 **扩展** 函数的示例。
> 要在对象上调用扩展函数，在对象后面写上函数名称并附加一个点 `.`。
> 
> 有关扩展函数的更多信息，请参见 [扩展函数](extensions.md#extension-functions)。
> 在本教程中，您只需了解如何调用它们。
> 
{style="note"}

要获取列表中元素的数量，使用 [`.count()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/count.html) 函数：

```kotlin
fun main() { 
//sampleStart
    val readOnlyShapes = listOf("triangle", "square", "circle")
    println("该列表有 ${readOnlyShapes.count()} 项")
    // 该列表有 3 项
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-list-count"}

要检查某项是否在列表中，使用 [`in` 操作符](operator-overloading.md#in-operator)：

```kotlin
fun main() {
//sampleStart
    val readOnlyShapes = listOf("triangle", "square", "circle")
    println("circle" in readOnlyShapes)
    // true
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-list-in"}

要向可变列表中添加或移除元素，分别使用
[`.add()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/add.html) 和
[`.remove()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/remove.html) 函数：

```kotlin
fun main() { 
//sampleStart
    val shapes: MutableList<String> = mutableListOf("triangle", "square", "circle")
    // 将 "pentagon" 添加到列表中
    shapes.add("pentagon") 
    println(shapes)  
    // [triangle, square, circle, pentagon]

    // 从列表中移除第一个 "pentagon"
    shapes.remove("pentagon") 
    println(shapes)  
    // [triangle, square, circle]
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-list-add-remove"}

## 集合 {id=set}

与列表有序且允许重复元素不同，集合是 **无序的** 并且只存储 **唯一** 元素。

要创建只读集合 ([`Set`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-set/))，使用
[`setOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/set-of.html) 函数。

要创建可变集合 ([`MutableSet`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-set/))，使用
[`mutableSetOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/mutable-set-of.html) 函数。

在创建集合时，Kotlin 可以推断存储元素的类型。
要显式声明类型，请在集合声明后添加尖括号 `<>` 中的类型：

```kotlin
fun main() {
//sampleStart
    // 只读集合
    val readOnlyFruit = setOf("apple", "banana", "cherry", "cherry")
    // 带有显式类型声明的可变集合
    val fruit: MutableSet<String> = mutableSetOf("apple", "banana", "cherry", "cherry")
    
    println(readOnlyFruit)
    // [apple, banana, cherry]
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-sets-declaration"}

你可以在前面的示例中看到，由于集合只包含唯一的元素，重复的 `"cherry"` 项被丢弃了。

> 为了防止不必要的修改，你可以通过将可变集合赋值给 `Set` 来创建一个只读视图：
>
> ```kotlin
>     val fruit: MutableSet<String> = mutableSetOf("apple", "banana", "cherry", "cherry")
>     val fruitLocked: Set<String> = fruit
> ```
>
{style="tip"}

> 由于集合是 **无序的**，你无法按特定索引访问某个元素。
> 
{style="note"}

要获取集合中的元素数量，使用 [`.count()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/count.html) 函数：

```kotlin
fun main() { 
//sampleStart
    val readOnlyFruit = setOf("apple", "banana", "cherry", "cherry")
    println("该集合有 ${readOnlyFruit.count()} 项")
    // 该集合有 3 项
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-set-count"}

要检查某项是否在集合中，使用 [`in` 操作符](operator-overloading.md#in-operator)：

```kotlin
fun main() {
//sampleStart
    val readOnlyFruit = setOf("apple", "banana", "cherry", "cherry")
    println("banana" in readOnlyFruit)
    // true
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-set-in"}

要向可变集合中添加或移除元素，分别使用
[`.add()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-set/add.html) 和
[`.remove()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/remove.html) 函数：

```kotlin
fun main() { 
//sampleStart
    val fruit: MutableSet<String> = mutableSetOf("apple", "banana", "cherry", "cherry")
    fruit.add("dragonfruit")    // 将 "dragonfruit" 添加到集合中
    println(fruit)              // [apple, banana, cherry, dragonfruit]
    
    fruit.remove("dragonfruit") // 从集合中移除 "dragonfruit"
    println(fruit)              // [apple, banana, cherry]
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-set-add-remove"}

## 映射 {id=map}

映射将元素存储为键值对。通过引用键来访问值。
你可以把映射想象成一个菜单。你可以通过找到你想吃的食物（键）来查找价格（值）。
如果你想在不使用数字索引的情况下查找值，映射是很有用的，例如在列表中。

> * 映射中的每个键必须是唯一的，以便 Kotlin 能够理解你想要获取哪个值。
> * 映射中可以有重复的值。
>
{style="note"}

要创建只读映射 ([`Map`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-map/))，使用
[`mapOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map-of.html) 函数。

要创建可变映射 ([`MutableMap`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-map/))，使用
[`mutableMapOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/mutable-map-of.html) 函数。

在创建映射时，Kotlin 可以推断存储元素的类型。
要显式声明类型，请在映射声明后添加键和值的类型，在尖括号 `<>` 中，例如：`MutableMap<String, Int>`。
其中键的类型为 `String`，值的类型为 `Int`。

创建映射的最简单方法是使用 [`to`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/to.html) 来连接每个键及其相关值：

```kotlin
fun main() {
//sampleStart
    // 只读映射
    val readOnlyJuiceMenu = mapOf("apple" to 100, "kiwi" to 190, "orange" to 100)
    println(readOnlyJuiceMenu)
    // {apple=100, kiwi=190, orange=100}

    // 带有显式类型声明的可变映射
    val juiceMenu: MutableMap<String, Int> = mutableMapOf("apple" to 100, "kiwi" to 190, "orange" to 100)
    println(juiceMenu)
    // {apple=100, kiwi=190, orange=100}
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-maps-declaration"}

> 为了防止不必要的修改，你可以通过将可变映射赋值给 `Map` 来创建一个只读视图：
>
> ```kotlin
>     val juiceMenu: MutableMap<String, Int> = mutableMapOf("apple" to 100, "kiwi" to 190, "orange" to 100)
>     val juiceMenuLocked: Map<String, Int> = juiceMenu
> ```
>
{style="tip"}

要访问映射中的值，使用带有键的 [索引访问操作符](operator-overloading.md#indexed-access-operator) `[]`：

```kotlin
fun main() {
//sampleStart
    // 只读映射
    val readOnlyJuiceMenu = mapOf("apple" to 100, "kiwi" to 190, "orange" to 100)
    println("苹果汁的价格是: ${readOnlyJuiceMenu["apple"]}")
    // 苹果汁的价格是: 100
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-map-access"}

> 如果你尝试使用一个不存在的键访问键值对，你将看到一个 `null` 值：
>
> ```kotlin
> fun main() {
> //sampleStart
>     // 只读映射
>     val readOnlyJuiceMenu = mapOf("apple" to 100, "kiwi" to 190, "orange" to 100)
>     println("菠萝汁的价格是: ${readOnlyJuiceMenu["pineapple"]}")
>     // 菠萝汁的价格是: null
> //sampleEnd
> }
> ```
> {kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-map-no-key" validate="false"}
> 
> 本教程将在 [空值安全](kotlin-tour-null-safety.md) 章节中解释空值。
> 
{style="note"}

你也可以使用 [索引访问操作符](operator-overloading.md#indexed-access-operator) `[]` 来向可变映射中添加元素：

```kotlin
fun main() {
//sampleStart
    val juiceMenu: MutableMap<String, Int> = mutableMapOf("apple" to 100, "kiwi" to 190, "orange" to 100)
    juiceMenu["coconut"] = 150 // 将键 "coconut" 和值 150 添加到映射中
    println(juiceMenu)
    // {apple=100, kiwi=190, orange=100, coconut=150}
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-map-add-item"}

要从可变映射中移除元素，使用 [`.remove()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/remove.html) 函数：

```kotlin
fun main() {
//sampleStart
    val juiceMenu: MutableMap<String, Int> = mutableMapOf("apple" to 100, "kiwi" to 190, "orange" to 100)
    juiceMenu.remove("orange")    // 从映射中移除键 "orange"
    println(juiceMenu)
    // {apple=100, kiwi=190}
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-map-put-remove"}

要获取映射中的元素数量，使用 [`.count()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/count.html) 函数：

```kotlin
fun main() {
//sampleStart
    // 只读映射
    val readOnlyJuiceMenu = mapOf("apple" to 100, "kiwi" to 190, "orange" to 100)
    println("这个映射有 ${readOnlyJuiceMenu.count()} 个键值对")
    // 这个映射有 3 个键值对
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-map-count"}

要检查映射中是否包含特定的键，使用 [`.containsKey()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/contains-key.html) 函数：

```kotlin
fun main() {
//sampleStart
    val readOnlyJuiceMenu = mapOf("apple" to 100, "kiwi" to 190, "orange" to 100)
    println(readOnlyJuiceMenu.containsKey("kiwi"))
    // true
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-map-contains-keys"}

要获取映射的键集合或值集合，分别使用 [`keys`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-map/keys.html)
和 [`values`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-map/values.html) 属性：

```kotlin
fun main() {
//sampleStart
    val readOnlyJuiceMenu = mapOf("apple" to 100, "kiwi" to 190, "orange" to 100)
    println(readOnlyJuiceMenu.keys)
    // [apple, kiwi, orange]
    println(readOnlyJuiceMenu.values)
    // [100, 190, 100]
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-map-keys-values"}

> [`keys`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-map/keys.html) 和
> [`values`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-map/values.html) 是对象的 **属性** 示例。
> 要访问对象的属性，在对象后面写属性名称，属性名称前加点 `.`。
>
> 属性在 [类](kotlin-tour-classes.md) 章节中有更详细的讨论。
> 在本教程的这一阶段，你只需知道如何访问它们即可。
>
{style="note"}

要检查映射中是否存在某个键或值，使用 [`in` 操作符](operator-overloading.md#in-operator)：

```kotlin
fun main() {
//sampleStart
    val readOnlyJuiceMenu = mapOf("apple" to 100, "kiwi" to 190, "orange" to 100)
    println("orange" in readOnlyJuiceMenu.keys)
    // true
    
    // 另外，你不需要使用 keys 属性
    println("orange" in readOnlyJuiceMenu)
    // true
    
    println(200 in readOnlyJuiceMenu.values)
    // false
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-map-in"}

有关集合的更多信息，请参阅 [集合](collections-overview.md)。

现在你了解了基本类型和如何管理集合，接下来可以探索在程序中使用的 [控制流](kotlin-tour-control-flow.md)。

## 练习 {id=practice}

### 练习 1 {id=exercise-1 collapsible="true"}

你有一个 “green” 数字列表和一个 “red” 数字列表。 完成代码以打印总共有多少个数字。

|---|---|
```kotlin
fun main() {
    val greenNumbers = listOf(1, 4, 23)
    val redNumbers = listOf(17, 2)
    // 在这里写下你的代码
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-collections-exercise-1"}

|---|---|
```kotlin
fun main() {
    val greenNumbers = listOf(1, 4, 23)
    val redNumbers = listOf(17, 2)
    val totalCount = greenNumbers.count() + redNumbers.count()
    println(totalCount)
}
```
{collapsible="true" collapsed-title="示例解答" id="kotlin-tour-collections-solution-1"}

### 练习 2 {id=exercise-2 collapsible="true"}

你有一组由服务器支持的协议。用户请求使用某个特定协议。
完成程序以检查所请求的协议是否受支持（`isSupported` 必须是一个布尔值）。

|---|---|
```kotlin
fun main() {
    val SUPPORTED = setOf("HTTP", "HTTPS", "FTP")
    val requested = "smtp"
    val isSupported = // 在这里写下你的代码
    println("Support for $requested: $isSupported")
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-collections-exercise-2"}

<deflist collapsible="true" id="kotlin-tour-collections-exercise-2-hint">
    <def title="提示">
        确保你以大写形式检查请求的协议。你可以使用 <a href="https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/uppercase.html"><code>.uppercase()</code></a> 函数来帮助你完成这个任务。
    </def>
</deflist>

|---|---|
```kotlin
fun main() {
    val SUPPORTED = setOf("HTTP", "HTTPS", "FTP")
    val requested = "smtp"
    val isSupported = requested.uppercase() in SUPPORTED
    println("Support for $requested: $isSupported")
}
```
{collapsible="true" collapsed-title="示例解答" id="kotlin-tour-collections-solution-2"}

### 练习 3 {id=exercise-3 collapsible="true"}

定义一个将 1 到 3 的整数与它们对应的拼写关联的映射。
使用这个映射来拼写给定的数字。

|---|---|
```kotlin
fun main() {
    val number2word = // 在这里写下你的代码
    val n = 2
    println("$n is spelt as '${<在这里写下你的代码>}'")
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-collections-exercise-3"}

|---|---|
```kotlin
fun main() {
    val number2word = mapOf(1 to "one", 2 to "two", 3 to "three")
    val n = 2
    println("$n is spelt as '${number2word[n]}'")
}
```
{collapsible="true" collapsed-title="示例解答" id="kotlin-tour-collections-solution-3"}

## 下一步

[控制流](kotlin-tour-control-flow.md)
