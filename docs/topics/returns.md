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

现在，你可以使用标签来限定 `break` 或 `continue`：

```kotlin
loop@ for (i in 1..100) {
    for (j in 1..100) {
        if (...) break@loop
    }
}
```

带有标签的 `break` 允许你跳出带有特定标签的循环，并从标签所在的循环后面的执行点开始执行。
而带有标签的 `continue` 允许你跳到带有特定标签的循环的下一次迭代。

> 在某些情况下，你可以在不显式定义标签的情况下 *非局部* 使用 `break` 和 `continue`。  
> 这种非局部使用在用于封闭的 [内联函数](inline-functions.md#break-and-continue) 中的 lambda 表达式中是有效的。
>
{style="note"}

## 返回到标签 {id=return-to-labels}

在 Kotlin 中，函数可以通过函数字面量、局部函数和对象表达式进行嵌套。  
一个带有限定的 `return` 允许你从外部函数返回。

最常见的用例是从 lambda 表达式中返回。要从 lambda 表达式中返回，
需要为其打上标签并限定 `return`：

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

这意味着 “在标签 `@a` 处返回 `1`”，而不是 “返回一个标记的表达式 `(@a 1)`”。

> 在某些情况下，你可以在不使用标签的情况下从 lambda 表达式中返回。
> 这种 *非局部* 返回位于 lambda 中，但会退出封闭的 [内联函数](inline-functions.md#returns)。
>
{style="note"}
