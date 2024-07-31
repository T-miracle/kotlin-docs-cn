[//]: # (title: 解构声明)

有时，将对象 *解构* 成多个变量能更方便我们的使用，例如：

```kotlin
val (name, age) = person 
```

这种语法称为 *解构声明*。解构声明一次能创建多个变量。
现在你已经声明了两个新变量：`name` 和 `age`，并且可以独立使用它们：

```kotlin
println(name)
println(age)
```

解构声明会被编译成以下代码：

```kotlin
val name = person.component1()
val age = person.component2()
```

`component1()` 和 `component2()` 函数是 *约定原则* 的另一个例子，在 Kotlin 中广泛使用
（参见像 `+` 和 `*` 的操作符，`for` 循环也是一个例子）。
只要能在右侧调用所需数量的组件函数，任何东西都可以放在解构声明的右侧。
当然，还可以有 `component3()` 和 `component4()` 等等。

> `componentN()` 函数需要用 `operator` 关键字标记，以便在解构声明中使用。
>
{style="note"}

解构声明同样适用于 `for` 循环：

```kotlin
for ((a, b) in collection) { ... }
```

变量 `a` 和 `b` 获取集合元素调用 `component1()` 和 `component2()` 返回的值。

## 示例：从函数返回两个值 {id=example-returning-two-values-from-a-function}

假设你需要从函数返回两样东西 —— 例如，一个结果对象和某种状态。
在 Kotlin 中，有一种简洁的方法，我们可以声明一个 [数据类](data-classes.md) 并返回它的实例：

```kotlin
data class Result(val result: Int, val status: Status)
fun function(...): Result {
    // 计算
    
    return Result(result, status)
}

// 现在，使用这个函数：
val (result, status) = function(...)
```

由于数据类自动声明了 `componentN()` 函数，所以解构声明在这里有效的。

> 你也可以使用标准类 `Pair` 并让 `function()` 返回 `Pair<Int, Status>`，
> 但通常更好的设计是将你的数据正确命名。
>
{style="note"}

## 示例：解构声明和 Map 映射 {id=example-destructuring-declarations-and-maps}

遍历映射可能最好的方式是这样：

```kotlin
for ((key, value) in map) {
   // 对键和值进行处理
}
```

要实现这一点，你应该

* 通过提供 `iterator()` 函数将映射表示为一系列值。
* 通过提供 `component1()` 和 `component2()` 函数将每个元素表示为一个对。

事实上，标准库提供了这样的扩展：

```kotlin
operator fun <K, V> Map<K, V>.iterator(): Iterator<Map.Entry<K, V>> = entrySet().iterator()
operator fun <K, V> Map.Entry<K, V>.component1() = getKey()
operator fun <K, V> Map.Entry<K, V>.component2() = getValue()
```

因此，你可以在 `for` 循环中自由地使用解构声明处理映射（以及数据类实例的集合或类似的情况）。

## 用下划线表示未使用的变量 {id=underscore-for-unused-variables}

如果在解构声明中不需要某个变量，可以用下划线代替其名称：

```kotlin
val (_, status) = getResult()
```

对于以这种方式跳过的组件，不会调用 `componentN()` 操作符函数。

## 在 lambda 中进行解构 {id=destructuring-in-lambdas}

你可以对 lambda 参数使用解构声明语法。
如果 lambda 有一个 `Pair` 类型的参数（或 `Map.Entry`，或任何其他具有适当 `componentN` 函数的类型），你可以通过将它们放在括号中引入几个新参数，而不是一个：

```kotlin
map.mapValues { entry -> "${entry.value}!" }
map.mapValues { (key, value) -> "$value!" }
```

注意声明两个参数和声明一个解构组作为参数的区别：

```kotlin
{ a -> ... } // 一个参数
{ a, b -> ... } // 两个参数
{ (a, b) -> ... } // 一个解构对
{ (a, b), c -> ... } // 一个解构组和另一个参数
```

如果解构参数的某个组件未使用，你可以用下划线代替它的名称以避免命名：

```kotlin
map.mapValues { (_, value) -> "$value!" }
```

你可以为整个解构参数或特定组件分别指定类型：

```kotlin
map.mapValues { (_, value): Map.Entry<Int, String> -> "$value!" }

map.mapValues { (_, value: String) -> "$value!" }
```

