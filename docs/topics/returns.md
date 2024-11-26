[//]: # (title: 返回和跳转)

Kotlin 有三个结构性跳转表达式：

* `return` 默认从最近的封闭函数或[匿名函数](lambdas.md#anonymous-functions)返回。
* `break` 终止最近的封闭循环。
* `continue` 继续最近的封闭循环的下一步。

所有这些表达式都可以作为更大表达式的一部分使用：

```kotlin
val s = person.name ?: return
```

这些表达式的类型是 [Nothing 类型](exceptions.md#nothing-type)。

## 中断和继续标签 {id=中断和继续标签}

Kotlin 中的任何表达式都可以用 **标签** 进行标记。
标签的形式是标识符后跟 `@` 符号，例如 `abc@` 或 `fooBar@`。
要给表达式加标签，只需在其前面添加一个标签。

```kotlin
loop@ for (i in 1..100) {
    // ...
}
```

现在，我们可以使用标签来限定 `break` 或 `continue`：

```kotlin
loop@ for (i in 1..100) {
    for (j in 1..100) {
        if (...) break@loop
    }
}
```

带有标签的 `break` 允许你跳出带有特定标签的循环，并从标签所在的循环后面的执行点开始执行。
而带有标签的 `continue` 允许你跳到带有特定标签的循环的下一次迭代。

通俗点说，带标签的 `break` 可以中断指定循环，而带标签的 `continue` 可以跳到指定循环的下一次迭代。

## 返回到标签 {id=返回到标签}

在 Kotlin 中，函数可以使用函数字面量、局部函数和对象表达式进行嵌套。
使用带有标签的 `return` 可以从外部函数中返回。
其中，最重要的用例之一是在 lambda 表达式中使用。
简而言之，当我们编写如下代码时，`return` 表达式将从最近的封闭函数 `foo` 返回：

```kotlin
//sampleStart
fun foo() {
    listOf(1, 2, 3, 4, 5).forEach {
        if (it == 3) return // 非局部返回直接返回给调用 `foo()` 的调用者。
        print(it)
    }
    println("这个点是无法到达的")
}
//sampleEnd

fun main() {
    foo()
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

需要注意的是，这种非局部返回只支持传递给[内联函数](inline-functions.md)的 lambda 表达式。
如果想要从 lambda 表达式中返回，需要对其进行标记，然后使用带有标签的 `return`：

```kotlin
//sampleStart
fun foo() {
    listOf(1, 2, 3, 4, 5).forEach lit@{
        if (it == 3) return@lit // 局部返回给 lambda 的调用者 - `forEach` 循环。
        print(it)
    }
    print(" 使用显式标签完成")
}
//sampleEnd

fun main() {
    foo()
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

现在，它只从 lambda 表达式返回。通常使用 **隐式标签** 更方便，因为这样的标签与传递 lambda 的函数具有相同的名称。

```kotlin
//sampleStart
fun foo() {
    listOf(1, 2, 3, 4, 5).forEach {
        if (it == 3) return@forEach // 局部返回给 lambda 的调用者 - `forEach` 循环。
        print(it)
    }
    print(" 使用隐式标签完成")
}
//sampleEnd

fun main() {
    foo()
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

或者，您可以用[匿名函数](lambdas.md#anonymous-functions)替换 lambda 表达式。
匿名函数中的 `return` 语句将从匿名函数本身返回。

```kotlin
//sampleStart
fun foo() {
    listOf(1, 2, 3, 4, 5).forEach(fun(value: Int) {
        if (value == 3) return  // 局部返回给匿名函数的调用者 - `forEach` 循环。
        print(value)
    })
    print(" 使用匿名函数完成")
}
//sampleEnd

fun main() {
    foo()
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

请注意，前面三个例子中的局部返回的使用类似于在常规循环中使用 `continue`。

对于 `break` 没有直接的等价物，但可以通过添加另一个嵌套的 lambda 并从其中进行非局部返回来模拟：

```kotlin
//sampleStart
fun foo() {
    run loop@{
        listOf(1, 2, 3, 4, 5).forEach {
            if (it == 3) return@loop // 从传递给 run 的 lambda 进行非局部返回
            print(it)
        }
    }
    print(" 使用嵌套循环完成")
}
//sampleEnd

fun main() {
    foo()
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

当返回一个值时，解析器会优先选用标签限定的返回：

```kotlin
return@a 1
```

这表示"在标签 `@a` 处返回 `1`"，而不是"返回一个带有标签的表达式 `(@a 1)`"。