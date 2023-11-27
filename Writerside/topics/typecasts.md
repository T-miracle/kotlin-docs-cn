[//]: # (title: 类型检查和强制转换)

在 Kotlin 中，当您需要在运行时检查对象的类型时，你可以执行类型检查。
类型转换将对象转换为不同的类型。

> 要了解关于 **泛型** 类型检查和转换（例如 `List<T>`、`Map<K,V>`）的具体信息，请参阅
> [泛型类型检查和转换](generics.md#generics-type-checks-and-casts)。
> 
{type="tip"}

## `is` 和 `!is` 操作符 {id=是或不是操作符}

使用 `is` 操作符或其否定形式 `!is` 进行运行时检查，来确定对象是否符合给定的类型：

```kotlin
if (obj is String) {
    print(obj.length)
}

if (obj !is String) { // 和 !(obj is String) 一样
    print("Not a String")
} else {
    print(obj.length)
}
```

## 智能转换 {id=智能转换}

在大多数情况下，你不需要在 Kotlin 中使用显式的转换操作符，因为编译器会追踪`is`检查和[显式转换](#不安全强制转换运算符)（对于不可变值）并在必要时自动插入（安全）转换：

```kotlin
fun demo(x: Any) {
    if (x is String) {
        print(x.length) // x 会自动转换为 String
    }
}
```

假如编译器足够智能，能够理解如果进行反向检查后能够安全返回，那么转换就是安全的：

```kotlin
if (x !is String) return

print(x.length) // x 会自动转换为 String
```

或者如果它位于 `&&` 或 `||` 的右侧，并且左侧有适当的检查（正常或否定）：

```kotlin
// 在 `||` 的右侧，x 会自动转换为 String
if (x !is String || x.length == 0) return

// 在 `&&` 的右侧，x 会自动转换为 String
if (x is String && x.length > 0) {
    print(x.length) // x 会自动转换为 String
}
```

智能转换同样适用于[`when` 表达式](control-flow.md#when表达式)和[`while` 循环](control-flow.md#while循环)：

```kotlin
when (x) {
    is Int -> print(x + 1)
    is String -> print(x.length + 1)
    is IntArray -> print(x.sum())
}
```

> 注意，智能转换仅在编译器能够保证变量在检查和使用之间不会改变时起作用。
>
{style="warning"}

智能转换可以在以下情况下使用：

<table header-style="none">
        <tr>
        <td>
            <code>val</code> 局部变量
        </td>
        <td>
            始终可以，除了 <a href='delegated-properties.md'>局部委托属性</a>。
        </td>
    </tr>
        <tr>
        <td>
            <code>val</code> 属性
        </td>
        <td>
            如果属性是<code>private</code>、<code>internal</code>，或者检查是在声明属性的相同
            <a href="visibility-modifiers.md#modules">模块</a>
            内执行的，那么智能转换不能用于<code>open</code>属性或具有自定义 getter 的属性。
        </td>
    </tr>
        <tr>
        <td>
            <code>var</code> 局部变量
        </td>
        <td>
            如果变量在检查和使用之间没有被修改，在修改它的 lambda 中没有被捕获，并且不是局部委托属性。
        </td>
    </tr>
        <tr>
        <td>
            <code>var</code> 属性
        </td>
        <td>
            永远不会，因为变量可能随时被其他代码修改。
        </td>
    </tr>
</table>

## "不安全" 强制转换运算符 {id=不安全强制转换运算符}

通常，如果转换不可能，强制转换运算符会抛出异常。因此，它被称为 **unsafe**（**不安全**）的。
在 Kotlin 中，通过中缀运算符 `as` 进行不安全转换。

```kotlin
val x: String = y as String
```

请注意，`null` 不能被转换为 `String`，因为这个类型不是[可空的](null-safety.md)。
如果 `y` 为 null，上述代码会抛出异常。
为了使这样的代码对于空值是正确的，请在转换的右侧使用可空类型：

```kotlin
val x: String? = y as String?
```

## "安全"（可空）强制转换运算符 {id=安全可空强制转换运算符}

为了避免异常，使用**安全**强制转换运算符 `as?`，在失败时返回 `null`。

```kotlin
val x: String? = y as? String
```

请注意，尽管 `as?` 的右侧是非可空类型 `String`，但转换的结果是可空的。
