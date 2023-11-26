[//]: # (title: 集合)

<p><img src="icon-1-done.svg" width="20" alt="First step" /> <a href="kotlin-tour-hello-world.md">Hello world</a><br />
    <img src="icon-2-done.svg" width="20" alt="Second step" /> <a href="kotlin-tour-basic-types.md">基本类型</a><br />
    <img src="icon-3.svg" width="20" alt="Third step" /> <strong>集合</strong><br />
    <img src="icon-4-todo.svg" width="20" alt="Fourth step" /> <a href="kotlin-tour-control-flow.md">控制流</a><br />
    <img src="icon-5-todo.svg" width="20" alt="Fifth step" /> <a href="kotlin-tour-functions.md">函数</a><br />
    <img src="icon-6-todo.svg" width="20" alt="Sixth step" /> <a href="kotlin-tour-classes.md">类</a><br />
    <img src="icon-7-todo.svg" width="20" alt="Final step" /> <a href="kotlin-tour-null-safety.md">空值安全</a>
</p>

在编程时，能够将数据分组到结构中以供以后处理是很有用的。Kotlin 提供了用于这个目的的集合。

Kotlin 有以下用于分组项目的集合：

| **集合类型**  | **描述**                  |
|-----------|-------------------------|
| Lists（列表） | 有序的项目集合                 |
| Sets（集合）  | 无序的唯一项目集合               |
| Maps（映射）  | 键值对的集合，其中键是唯一的，并映射到唯一的值 |

每种集合类型都可以是可变的或只读的。

## List（列表）

列表按添加的顺序存储项目，并允许重复项目。

要创建只读列表（[`List`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list/)），使用
`[`listOf()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/list-of.html)` 函数。

要创建可变列表（[`MutableList`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list.html)），
使用 [`mutableListOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/mutable-list-of.html)` 函数。

在创建列表时，Kotlin 可以推断存储的项目类型。要显式声明类型，请在列表声明后的尖括号 `<>` 中添加类型：

```kotlin
fun main() {
    // 只读列表
    val readOnlyShapes = listOf("triangle", "square", "circle")
    println(readOnlyShapes)
    // [triangle, square, circle]
    
    // 具有显式类型声明的可变列表
    val shapes: MutableList<String> = mutableListOf("triangle", "square", "circle")
    println(shapes)
    // [triangle, square, circle]
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-lists-declaration"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAYA6aVGtGA9PRgEow5QYRoA2AnhtzwBnAC406ANxzcMAJzZQA8j14BlABY4ADjCEYAvAOEjFSQhRAjZeHGgDm3GBYA0lEEICOKHPJduweLJgjhbE4rRa1mgi3ETy7Mp8Gtq6YZh0jBgA2lY29o6unt7yrgFBjgC64ZTptJkAsigiOABGjkaiGADueCLqGDAAHlqCASIYIrw6GLDBPjgieFzVUjJCmjpC8BiNzW0wADLGADyqufYAfAbYTa2OR6Km5pbWtg5OIK4WRT4fX%2BCBYIfNJ0DCRAgxIjrFJCEEZJg5V75GCFLy%2FUqAypUKgAX0%2Blh8dhgIgACtwFkgILIsAgQAArHBSfGQLBaPCOWQANRgsiESzQtIAjAA6ACcwsFAAYQDigA?_gl=1*e5z6aw*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4MjYzMC4xNy4wLjE2OTk3ODI2MzAuNjAuMC4w&_ga=2.242527874.1684546783.1699700997-2060274793.1694940376)

> 为了防止不必要的修改，您可以通过将可变列表分配给 `List` 来获得只读视图：
> ```kotlin
>     val shapes: MutableList<String> = mutableListOf("triangle", "square", "circle")
>     val shapesLocked: List<String> = shapes
> ```
> 这也被称为 **转型**。

列表是有序的，因此要访问列表中的项，请使用[索引访问运算符](operator-overloading.md#indexed-access-operator) `[]`：

```kotlin
fun main() {
    val readOnlyShapes = listOf("triangle", "square", "circle")
    println("The first item in the list is: ${readOnlyShapes[0]}")
    // The first item in the list is: triangle
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-list-access"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAYA6aVGtGAbjgDYYBOMOUA8mkwJ4BlABY4ADjADOGALwYmeCQBcuSQhRCLWeHGgDmTGOoA0lEBICOKHO2OmweVmAPriNOqK1pFTIuoAqQjAYSA5KGHiKMFjhmIqBcgqK4RLwGAAkwOycPPzCYpIA2gAMALoAvi5utAD01RgBQSGsYRFRMRhxQfItKR1aOvqG1GhlIEYa1rowigAKTDiKSBCsWAggAFY4jGPgEFiieAasAGowzXgQaGsAjAB0AJy310UgZUA?_gl=1*e5z6aw*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4MjYzMC4xNy4wLjE2OTk3ODI2MzAuNjAuMC4w&_ga=2.242527874.1684546783.1699700997-2060274793.1694940376)

要获取列表中的第一个或最后一个项目，请分别使用 [`.first()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/first.html) 和 [`.last()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/last.html) 函数：

```kotlin
fun main() {
    val readOnlyShapes = listOf("triangle", "square", "circle")
    println("The first item in the list is: ${readOnlyShapes.first()}")
    // The first item in the list is: triangle
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-list-first"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAYA6aVGtGAbjgDYYBOMOUA8mkwJ4BlABY4ADjADOGALwYmeCQBcuSQhRCLWeHGgDmTGOoA0lEBICOKHO2OmweVmAPriNOqK1pFTIuoAqQjAYSA5KGHiKMFjhmIqBcgqK4RLwGAAkwOycPPzCYpIAdCGsSiQAvi5utAD01RgBQcVhEVExGHFB8s0p7Vo6%2BobUaGUgRhrWujCKAApMOIpIEKxYCCAAVjiMo%2BAQWKJ4BqwAajAleBBoqwCMBQCcBVcADCBlQA%3D?_gl=1*1n21fcf*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4MjYzMC4xNy4wLjE2OTk3ODI2MzAuNjAuMC4w&_ga=2.148882103.1684546783.1699700997-2060274793.1694940376)

> [`.first()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/first.html) 和 [`.last()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/last.html)
> 函数是**扩展**函数的示例。要在对象上调用扩展函数，请在对象后写函数名，并加上一个点 `.`
>
> 有关扩展函数的更多信息，请参阅 [扩展函数](extensions.md#extension-functions)。
> 对于本教程，您只需要知道如何调用它们。 
> 
{style="note"}

要获取列表中的项目数量，请使用 [`.count()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/count.html) 函数：

```kotlin
fun main() {
    val readOnlyShapes = listOf("triangle", "square", "circle")
    println("This list has ${readOnlyShapes.count()} items")
    // This list has 3 items
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-list-count"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAYA6aVGtGAbjgDYYBOMOUA8mkwJ4BlABY4ADjADOGALwYmeCQBcuSQhRCLWeHGgDmTGOoA0lEBICOKHO2OmweVmAPriNOqK1pFTIuoAqQgpyCooYIlIAJMDsnDz8wmKSAHSQ6IokAL4YeIowWBIubrQA9MUYAUHySmE4UgDM2bn5VFQZIEYa1rowigAKTDiKSBCsWAggAFY4jO3gEFiieAasAGowrBJ4EGjjAIxJAJxJuwAMIBlAA%3D%3D?_gl=1*1n21fcf*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4MjYzMC4xNy4wLjE2OTk3ODI2MzAuNjAuMC4w&_ga=2.148882103.1684546783.1699700997-2060274793.1694940376)

要检查一个项目是否在列表中，请使用 [`in` 运算符](operator-overloading.md#in-operator)：

```kotlin
fun main() {
    val readOnlyShapes = listOf("triangle", "square", "circle")
    println("circle" in readOnlyShapes)
    // true
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-list-in"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGAbjgDYYBOMOUA8mowJ4DKACxwAHGAGcMAXgyM84gC6ckhciAUs8ONAHNGMNQBoMa8QEcUONkZPg8LMPrXFqtEZrQLGRNWHuODEAwCVnYuHgFhMXEXTFoAeniMDRQDKjQAXxBDdSsdGAUABUYcBSQIFiwEEAArHAZs8AgsETx9FgA1GBZxPAg0aoBGADoATmHBgAYQDKA%3D%3D%3D?_gl=1*1wuwie7*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4MjYzMC4xNy4xLjE2OTk3ODI5MzkuNTMuMC4w&_ga=2.146271796.1684546783.1699700997-2060274793.1694940376)

要从可变列表中添加或删除项目，请分别使用 [`.add()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/add.html)
和 [`.remove()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/remove.html) 函数：

```kotlin
fun main() {
    val shapes: MutableList<String> = mutableListOf("triangle", "square", "circle")
    // 添加 "pentagon" 到列表中
    shapes.add("pentagon") 
    println(shapes)  
    // [triangle, square, circle, pentagon]

    // 从列表中删除第一个 "pentagon"
    shapes.remove("pentagon") 
    println(shapes)  
    // [triangle, square, circle]
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-list-add-remove"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAYA6aVGtGAbjgDYYDOAFjgA4yvwYBZFABccAIyYwAMnlbCAPAGVhAJwIBzAHwYAvNhHjJMuQHkkhCiFV4cadZMsAaSiFYBHFDhUwnLsHhUwBxBiGjoAenCMAEEoKBceNFF1CGoQDGEIDPYYDCZZYTDaDm5eADocOIsQROTUy1IijC41JKYiEp5WUkpMCKiAbWtbexhnd09vZ39AyWdanBS0AF0qJsiMACUYLAh6XOEcjCQAuQSYJMX69KQVCCxs3Py5Js7y71396oWlht66ZqtYTtQhvbq0daDYZ2OZsDxeMYYGZBGCrahoAC%2BIEcVi86hgwgACkwcMIkBAVFgECAAFY4RjY8D3Lh4SQqABqMBUrDwqWpAEYygBOMr8gAMIAxQA?_gl=1*nf02nl*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4MjYzMC4xNy4xLjE2OTk3ODI5MzkuNTMuMC4w&_ga=2.155479739.1684546783.1699700997-2060274793.1694940376)

## Set（集合）

与列表有序且允许重复项目不同，集合是**无序**的，并且只存储**唯一**项目。

要创建只读集合（[`Set`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-set/)），使用
[`setOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/set-of.html)` 函数。

要创建可变集合（[`MutableSet`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-set/)），
使用 [`mutableSetOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/mutable-set-of.html)` 函数。

在创建集合时，Kotlin 可以推断存储的项目类型。要显式声明类型，请在集合声明后的尖括号 `<>` 中添加类型：

```kotlin
fun main() {
    // 只读集合
    val readOnlyFruit = setOf("apple", "banana", "cherry", "cherry")
    // 具有显式类型声明的可变集合
    val fruit: MutableSet<String> = mutableSetOf("apple", "banana", "cherry", "cherry")
    
    println(readOnlyFruit)
    // [apple, banana, cherry]
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-sets-declaration"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGA9HRgEow5QC0EaANgJ4YBnGABdqtAG45uGAE6soAeR68AYjJR5hGALyCRCpIXIgcAB1PcYxgDQZjAIxxonOG3fAALGDJm83xsC8fPxBiMRoGDABZFGEce0s9LQB3TQ8MGAAPCzwwTQxhXlMYDFgwbhwZHGE8LnCMSWkkdU14aNj4ywBlEQAeLuEZAgBzAD4dbA6EmB7hAyMTc0t%2FEEdnZxXA719NoJ3Q%2BvrTIbRhbiI5NiU%2BNQ1hMMxaSIBtMwsYWzWXWy3ggF1KJQAL4gawgOIyYYiAAKFWESAgMiwCBAACscJJQeAIFhTHhLDIAGreAS1NAogCMADoAJxUikABhAQKAA%3D?_gl=1*nf02nl*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4MjYzMC4xNy4xLjE2OTk3ODI5MzkuNTMuMC4w&_ga=2.155479739.1684546783.1699700997-2060274793.1694940376)

您可以在先前的示例中看到，因为集合只包含唯一的元素，重复的 `"cherry"` 项目被删除。

> 为了防止不必要的修改，通过将可变集合转换为 `Set` 来获取只读视图：
> ```kotlin
>     val fruit: MutableSet<String> = mutableSetOf("apple", "banana", "cherry", "cherry")
>     val fruitLocked: Set<String> = fruit
> ```
>
{type="tip"}

> 由于集合是**无序的**，因此您无法访问特定索引处的项目。
> 
{style="note"}

要获取集合中的项目数，请使用 [`.count()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/count.html) 函数：

```kotlin
fun main() {
    val readOnlyFruit = setOf("apple", "banana", "cherry", "cherry")
    println("This set has ${readOnlyFruit.count()} items")
    // This set has 3 items
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-set-count"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAYA6aVGtGAbjgDYYBOMOUA8mkwJ4AxVijwAXDAF4MAZxiiuSQhRA4ADqqYxlAGkogARjjRGcOvWAAWMVqz5nll67eXEadVawKimRZQBULPGkZOQwLHGCAEmB2Th5%2BIRFRADpIdFESAF8MMRgsaRc3WgB6YowAoJDxcOCAZhzRPILqNEyQbRBRHFYAczkABSYcUSQIViwEEAArHEZ28AgsVTxNVgA1a2k8CDRJgEZkgE5kvYAGEEygA%3D?_gl=1*nf02nl*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4MjYzMC4xNy4xLjE2OTk3ODI5MzkuNTMuMC4w&_ga=2.155479739.1684546783.1699700997-2060274793.1694940376)

要检查某个项目是否在集合中，请使用 [`in` 运算符](operator-overloading.md#in-operator)：

```kotlin
fun main() {
    val readOnlyFruit = setOf("apple", "banana", "cherry", "cherry")
    println("banana" in readOnlyFruit)
    // true
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-set-in"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGAbjgDYYBOMOUA8mowJ4BiLFHgAuGALwYAzjBGckhciBwAHFYxhKANBiUAjHGkM5tu8AAsYLFr1NKwl67ZDFqtFSwIjGRfcaNKGASs7Fw8AkKirpi0APSxGCJCmlRoAL4gWiAiOCwA5rIACow4IkgQLFgIIABWOAyZ4BBYKngaLABqVlJ4EGjVAIwAdACcQwMADCBpQA?_gl=1*df6h0r*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4MjYzMC4xNy4xLjE2OTk3ODI5MzkuNTMuMC4w&_ga=2.148867639.1684546783.1699700997-2060274793.1694940376)

要从可变集合中添加或删除项目，请分别使用 [`.add()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/add.html)
和 [`.remove()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/remove.html) 函数：

```kotlin
fun main() {
    val fruit: MutableSet<String> = mutableSetOf("apple", "banana", "cherry", "cherry")
    fruit.add("dragonfruit")    // 添加 "dragonfruit" 到集合中
    println(fruit)              // [apple, banana, cherry, dragonfruit]
    
    fruit.remove("dragonfruit") // 从集合中删除 "dragonfruit"
    println(fruit)              // [apple, banana, cherry]
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-set-add-remove"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAYA6aVGtGAbjgDYZIBOKeALvBgLIouOAEZMYAZRhcAPOK5sCAcwB8GALzZBIsZK4B5JIQogcAB1NjjAGkohhONA5zXbYABYw2bAJ4vj7zx9jYho6dk4uADocKCgjECg2HEUINHDuYLoMAHpsjABBWNtE5NT0rmMMLggqjwwAZylQ2lMFNC4mInLSLN6s3IwAbTMLGBt7R0cbAK9vGxKUtI5uAF1mykww5ai2GCwIehh4hbLtzIGAJT2DmGKkxfLK9ggsWtvGis2Wto6u7Z6%2Bn0BsNzGJxk4phgZj41tQ0ABfEBWEBCNiKKQABSYOC4SAgbCwCBAACscIwkeAXqY8GI2AA1Tz1PCpIkARkiAE5IqyAAwgeFAA%3D%3D%3D?_gl=1*df6h0r*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4MjYzMC4xNy4xLjE2OTk3ODI5MzkuNTMuMC4w&_ga=2.148867639.1684546783.1699700997-2060274793.1694940376)

## Map（映射）

映射以键值对的形式存储项目。
通过引用键，您可以访问值。
您可以将映射想象成食物菜单。
您可以通过查找要吃的食物（键）来找到价格（值）。
如果您想要查找一个值而不使用编号索引（如在列表中），映射将非常有用。

> * 映射中的每个键必须是唯一的，以便 Kotlin 可以理解您要获取哪个值。
> * 映射中可以有重复的值。
>
{style="note"}

要创建只读映射（[`Map`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-map/)），请使用
[`mapOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map-of.html)` 函数。

要创建可变映射（[`MutableMap`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-map/)），
请使用 [`mutableMapOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/mutable-map-of.html)` 函数。

在创建映射时，Kotlin 可以推断存储的项目类型。要显式声明类型，请在映射声明后的尖括号 `<>` 中添加键和值的类型。例如：`MutableMap<String, Int>`。键的类型为 `String`，值的类型为 `Int`。

创建映射的最简单方法是在每个键和其相关值之间使用 [`to`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/to.html)：

```kotlin
fun main() {
    // 只读映射
    val readOnlyJuiceMenu = mapOf("apple" to 100, "kiwi" to 190, "orange" to 100)
    println(readOnlyJuiceMenu)
    // {apple=100, kiwi=190, orange=100}

    // 具有显式类型声明的可变映射
    val juiceMenu: MutableMap<String, Int> = mutableMapOf("apple" to 100, "kiwi" to 190, "orange" to 100)
    println(juiceMenu)
    // {apple=100, kiwi=190, orange=100}
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-maps-declaration"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGA9HRgEow5QC0EaANgJ7Y4ADtVoA3HNwwAnVlADyPXgCkUeMDACyMNCgwBeAYLlJC5EEMHcYZjABcIGAIwAGZwBoMZgNZ4A7nht7JwBOd08QCCkcNABzaxA7BxdnYhEaQSkCW24iGTYFPhU1TW0UVMxaBjILKz1kjx9%2FOtCPSOi4utcAX0o0%2BkYNFFscACMrQwx%2FWwALDBgAD0s1PFs7XkEYDFgwbhwo2zwuPvFJACtVdS0deAxB4bHNIQAeAGVbTNiPAEk0WwA%2BfTYIajKwaITGUzmQSWeKJJyuDzePwBBJBRwtcJtWKwtGucq0DAZLI5QjnYpXMp9KrAGowTphRp4ZphLEdZI9KhoLogNwgYZSOK2AAKu1sSEiWAQIFOOHEPPAECwgjwVikADUYFIAM6HNBSxwAOmCBpcIC6QA?_gl=1*hygxby*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4MjYzMC4xNy4xLjE2OTk3ODI5MzkuNTMuMC4w&_ga=2.190800171.1684546783.1699700997-2060274793.1694940376)

> 为了防止不必要的修改，通过将可变映射转换为 `Map` 来获取只读视图：
> ```kotlin
>     val juiceMenu: MutableMap<String, Int> = mutableMapOf("apple" to 100, "kiwi" to 190, "orange" to 100)
>     val juiceMenuLocked: Map<String, Int> = juiceMenu
> ```

要访问映射中的值，请使用 [索引访问运算符](operator-overloading.md#indexed-access-operator) `[]` 与其键：

```kotlin
fun main() {
    // 只读映射
    val readOnlyJuiceMenu = mapOf("apple" to 100, "kiwi" to 190, "orange" to 100)
    println("The value of apple juice is: ${readOnlyJuiceMenu["apple"]}")
    // The value of apple juice is: 100
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-map-access"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGA9HRgEow5QC0EaANgJ7Y4ADtVoA3HNwwAnVlADyPXgCkUeMDACyMNCgwBeAYLlJC5EEMHcYZjABcIGAIwAGZwBoMZgNZ4A7nht7JwBOd08QCCkcNABzaxA7BxdnYhEaQSkCW24iMwAVAAsYDHFuFGKIJAwLKwwAK1V1DDwAZ3gMABJgGTYFPhU1TW0UAG0zGviAXQBfM1TMWgYMQuLS8oxK6sFLYobB5ranV0pKaZA3EFscKTjbAAVuHFskSKwEEDqccXPwCCxBPBWKQANRgUhaeC470cADpgjCXCBpkA%3D%3D?_gl=1*hygxby*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4MjYzMC4xNy4xLjE2OTk3ODI5MzkuNTMuMC4w&_ga=2.190800171.1684546783.1699700997-2060274793.1694940376)

要获取映射中的项目数，请使用 [`.count()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/count.html) 函数：

```kotlin
fun main() {
    // 只读映射
    val readOnlyJuiceMenu = mapOf("apple" to 100, "kiwi" to 190, "orange" to 100)
    println("This map has ${readOnlyJuiceMenu.count()} key-value pairs")
    // This map has 3 key-value pairs
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-map-count"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGA9HRgEow5QC0EaANgJ7Y4ADtVoA3HNwwAnVlADyPXgCkUeMDACyMNCgwBeAYLlJC5EEMHcYZjABcIGAIwAGZwBoMZgNZ4A7nht7JwBOd08QCCkcNABzaxA7BxdnYhEaQSkCW24iMwAVAAs8AGdDDAKcUoASYBk2BT4VNU1tFAA6SHRbEgBfDC8YXnZxbhQYDEF8KWKzVMxaBgxCkrKK0oBmfsHhiTGJqZmqNB6QNxBbHCk42wAFbhxbJEisBBAAKxxxU%2FAILEE8KxSABqMGmeC4r0cbWCbRcIB6QA%3D%3D?_gl=1*a4o9yc*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4MjYzMC4xNy4xLjE2OTk3ODI5MzkuNTMuMC4w&_ga=2.219975321.1684546783.1699700997-2060274793.1694940376)

要从可变映射中添加或删除项目，请分别使用 [`.put()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-map/put.html)
和 [`.remove()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/remove.html) 函数：

```kotlin
fun main() {
    val juiceMenu: MutableMap<String, Int> = mutableMapOf("apple" to 100, "kiwi" to 190, "orange" to 100)
    juiceMenu.put("coconut", 150) // 将键 "coconut" 添加到映射中，其值为 150
    println(juiceMenu)
    // {apple=100, kiwi=190, orange=100, coconut=150}

    juiceMenu.remove("orange")    // 从映射中删除键 "orange"
    println(juiceMenu)
    // {apple=100, kiwi=190, coconut=150}
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-map-put-remove"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGAbjgDYYBWKeYMAsjGivBi4oALjgBGjbjgAOAHgDKwgE4EA5gBoMASTTCAfBgC82EeMlcZAeSSFyIGdMl2MwiBgCMABk%2Ba7AazwAdzxnVw8ATh8MOwglHDRVGFC3L09ialo2Dm5eFAA6aRFbcAhIPmE7TXcAVjSMAHp6jABBKCgMPxgAT2iSspFnYOEAC3omFBgPWpc3EcncaQyaaRVdRiIszh4%2BdMxaRrIHSUNUzQDgk8jNWPjEk%2B9NSH7hE9qAX0ol1nYt3LylGBYCB0GDFG4JJIgUj7JoAJUBwMmnR6MTiEOcSCUECwLmG8xkXxWBGE60ImxyOy%2BB2ARxg9yi5zwlyiTwg5Veng%2BVDQbxA6hAoiUiWEAAVGDhhEhYlgECAWDgGHySlhpHhJEoAGowJQAZzwbNl7jy4TyXhAbyAA%3D%3D?_gl=1*yqfuhk*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4MjYzMC4xNy4xLjE2OTk3ODI5MzkuNTMuMC4w&_ga=2.216909334.1684546783.1699700997-2060274793.1694940376)

要检查映射中是否已包含特定键，请使用 [`.containsKey()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/contains-key.html) 函数：

```kotlin
fun main() {
    val readOnlyJuiceMenu = mapOf("apple" to 100, "kiwi" to 190, "orange" to 100)
    println(readOnlyJuiceMenu.containsKey("kiwi"))
    // true
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-map-contains-keys"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGAbjgDYYBOMOUA8mowJ4BSKPGBgBZGGhQYAvNhwAHTkkLkQC%2BYxiqMAFwgYAjAAYjAGgyqA1ngDuebXsMBOMxZAQWONAHMtIXfrGRsTUtPIsBDqMRGwc3HyCwmISKAB0kGg6%2BGgAzgDSMLwqINZ2qsQhmLQA9NW6LChaVGgAviCmIFksvjoACow4OkgeWAggAFY4DO3gEFjyeJosAGowLDl4EGhjBqlOqcYgLUA%3D?_gl=1*yqfuhk*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4MjYzMC4xNy4xLjE2OTk3ODI5MzkuNTMuMC4w&_ga=2.216909334.1684546783.1699700997-2060274793.1694940376)

要获取映射的键或值的集合，请分别使用 [`keys`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-map/keys.html)
和 [`values`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-map/values.html) 属性：

```kotlin
fun main() {
    val readOnlyJuiceMenu = mapOf("apple" to 100, "kiwi" to 190, "orange" to 100)
    println(readOnlyJuiceMenu.keys)
    // [apple, kiwi, orange]
    println(readOnlyJuiceMenu.values)
    // [100, 190, 100]
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-map-keys-values"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGAbjgDYYBOMOUA8mowJ4BSKPGBgBZGGhQYAvNhwAHTkkLkQC%2BYxiqMAFwgYAjAAYjAGgyqA1ngDuebXsMBOMxZAQWONAHMtIXfrGRsTUtPIsBDqMRGwc3HyCwmISKAB0ljC8AM4hmLQA9PkYANrqmubWduYeXr4AuqE04ZHRhLFcPAJCIuKSqQyMKDA5jRiFJUHmBi5TJg1UaAC%2BIKYgOjgsvjoACow4OkgeWAggAFY4DCvgEFjyeJosAGowLFl4EGgnBqlOqcYgiyAA?_gl=1*yqfuhk*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4MjYzMC4xNy4xLjE2OTk3ODI5MzkuNTMuMC4w&_ga=2.216909334.1684546783.1699700997-2060274793.1694940376)

> [`keys`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-map/keys.html) 和 [`values`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-map/values.html)
> 是对象的**属性**的示例。要访问对象的属性，请在对象之后添加一个点 `.`，然后写入属性名。
>
> 对象的属性在 [Classes](kotlin-tour-classes.md) 章节中进行了更详细的讨论。
> 在本教程的这一部分，您只需要知道如何访问它们。
>
{style="note"}

要检查映射中是否存在键或值，请使用 [`in` 操作符](operator-overloading.md#in-operator)：

```kotlin
fun main() {
    val readOnlyJuiceMenu = mapOf("apple" to 100, "kiwi" to 190, "orange" to 100)
    println("orange" in readOnlyJuiceMenu.keys)
    // true
    println(200 in readOnlyJuiceMenu.values)
    // false
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-map-in"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGAbjgDYYBOMOUA8mowJ4BSKPGBgBZGGhQYAvNhwAHTkkLkQC%2BYxiqMAFwgYAjAAYjAGgyqA1ngDuebXsMBOMxZAQWONAHMtIXfrGRsTUtPIsBDqMRKoeXr7aBKzsXDwCQiLikgB0ljC8AM4hmLQA9KW6LChaJTThkdGEAEwmGElsHNx8gsJiEijZDIzVRaE05RhITAU1lAC%2BIKYgOjgsvjoACow4OkgeWAggAFY4DIvgEFjyeJosAGowLAV4EGiHBtlO2cYgc0A%3D?_gl=1*6yjf0i*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4MjYzMC4xNy4xLjE2OTk3ODI5MzkuNTMuMC4w&_ga=2.214281879.1684546783.1699700997-2060274793.1694940376)

要了解有关您可以使用集合执行的操作的更多信息，请参阅 [Collections](collections-overview.md)。

现在您已经了解了基本类型以及如何管理集合，是时候探索您可以在程序中使用的 [控制流](kotlin-tour-control-flow.md) 了。

## 实践

### 练习 1 {collapsible="true"}

你有一个包含“绿色”数字的列表和一个包含“红色”数字的列表。
请完成代码以打印出总共有多少个数字。

|---|---|
```kotlin
fun main() {
    val greenNumbers = listOf(1, 4, 23)
    val redNumbers = listOf(17, 2)
    // 在这里写下你的代码
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-collections-exercise-1"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6mG1AbjgDYYDmATjDGgHIpYBGMLAM4YAvBnp5BAFwDySQgEYANBgAsKgEwBmYpWq0GGNlG58BwsROlzFAdk26q1APTOMAdRZ4pMDAE8IFBYMSFgMAAsBGEoAXxAlECkcFiYYKQAFehwpJAgWLAQQACscOnjwCCwABzx6AQA1czwINEKFADoATnaFAAYQGKA%3D%3D?_gl=1*6yjf0i*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4MjYzMC4xNy4xLjE2OTk3ODI5MzkuNTMuMC4w&_ga=2.214281879.1684546783.1699700997-2060274793.1694940376)

|---|---|
```kotlin
fun main() {
    val greenNumbers = listOf(1, 4, 23)
    val redNumbers = listOf(17, 2)
    val totalCount = greenNumbers.count() + redNumbers.count()
    println(totalCount)
}
```
{collapsible="true" collapsed-title="Example solution" id="kotlin-tour-collections-solution-1"}

### 练习 2 {collapsible="true"}

你的服务器支持一组协议。用户请求使用特定协议。请完成程序以检查请求的协议是否受支持（`isSupported` 必须是一个布尔值）。

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
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6mG1AbjgDYYDKAqgApsDyASgCoCiAEQwBeDAGcYAF05JC5EAAlevNgoA0GBctVMNWkADFVC4pWq0GGAE4wAjihjipMKKIPisUgA4LzFukY8cSYUb28Iaxc3MQB6WIwAdWs8FwwATwgUawxIWAwACxhbLSpqbxS0KXoiBVDwyKkMJEiMABJbBydo%2BHbg%2Boio11NKAF8QdRApHGsAc2k2ehwpFussBBAAKxw6CfAILG88emKANWLxPAg0DYBGADoATnvbgAYQUaA%3D%3D%3D?_gl=1*6yjf0i*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4MjYzMC4xNy4xLjE2OTk3ODI5MzkuNTMuMC4w&_ga=2.214281879.1684546783.1699700997-2060274793.1694940376)

<deflist collapsible="true" id="kotlin-tour-collections-exercise-2-hint">
    <def title="示意">
        确保以大写形式检查请求的协议。你可以使用<a href="https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/uppercase.html"><code>.uppercase()</code></a>函数来帮助你实现这一点。
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
{collapsible="true" collapsed-title="Example solution" id="kotlin-tour-collections-solution-2"}

### 练习 3 {collapsible="true"}

定义一个将整数1到3与它们相应拼写关联起来的映射。
使用这个映射来拼写给定的数字。

|---|---|
```kotlin
fun main() {
    val number2word = // 在这里写下你的代码
    val n = 2
    println("$n is spelt as '${<Write your code here >}'")
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-collections-exercise-3"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6mG1AbjgDYZopYBGMATgEwDuEHUDAF4MAelEYA6hzwAXGBgCeEFBwyRYGABacYlarQZNhGLvoMAHGWln0i5EABJMeAM4ZXFmPVkYc7gHJHYAAeaTkFZVV1aAUdDgUAPgBfAIdiSmSQABoQWRwOAHMYWQAFehxZJH4sBBAAKxw6HPAILAs8ek4ANU5XPAg0OoBGADoATlHhgAYQZKA?_gl=1*6yjf0i*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTY5OTc4MjYzMC4xNy4xLjE2OTk3ODI5MzkuNTMuMC4w&_ga=2.214281879.1684546783.1699700997-2060274793.1694940376)

|---|---|
```kotlin
fun main() {
    val number2word = mapOf(1 to "one", 2 to "two", 3 to "three")
    val n = 2
    println("$n is spelt as '${number2word[n]}'")
}
```
{collapsible="true" collapsed-title="Example solution" id="kotlin-tour-collections-solution-3"}

## 下一步

[控制流](kotlin-tour-control-flow.md)