[//]: # (title: 类型检查和强制转换)

在 Kotlin 中，你可以在运行时进行类型检查来确认对象的类型。
类型转换允许你将对象转换为其他类型。

> 要了解关于 **泛型** 类型检查和转换（例如 `List<T>`、`Map<K,V>`）的具体信息，请参阅
> [泛型类型检查和转换](generics.md#泛型类型检查和转换)。
> 
{style="tip"}

## `is` 和 `!is` 操作符 {id=是或不是操作符}

要在运行时检查对象是否属于某个类型，可以使用 `is` 运算符或其否定形式 `!is`：

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

## 智能转换 {id=smart-casts}

在大多数情况下，你不需要使用显式类型转换操作符，因为编译器会自动为你进行类型转换。
这称为智能类型转换。
编译器会对于不可变值进行 类型跟踪检查 和 [显式类型转换](#unsafe-cast-operator)，并在必要时自动插入隐式（安全）类型转换：

```kotlin
fun demo(x: Any) {
    if (x is String) {
        print(x.length) // x 会自动转换为 String
    }
}
```

编译器甚至可以智能地识别，当负面检查导致 return 时，类型转换是安全的：

```kotlin
if (x !is String) return

print(x.length) // x 会自动转换为 String
```

### 控制流 {id=control-flow}

智能类型转换不仅适用于 `if` 条件表达式，还适用于 [`when` 表达式](control-flow.md#when-expression)
和 [`while` 循环](control-flow.md#while-loops)：

```kotlin
when (x) {
    is Int -> print(x + 1)
    is String -> print(x.length + 1)
    is IntArray -> print(x.sum())
}
```

如果你在 `if`、`when` 或 `while`
条件中使用之前声明了 `Boolean`
类型的变量，那么编译器收集的关于该变量的信息将在相应的代码块中可用于智能类型转换。

这对想将布尔条件提取到变量时特别有用。
你可以给变量一个有意义的名字，这样不仅能提高代码的可读性，还能在代码的其他部分重用这个变量。
例如：

```kotlin
class Cat {
    fun purr() {
        println("Purr purr")
    }
}

fun petAnimal(animal: Any) {
    val isCat = animal is Cat
    if (isCat) {
        // 编译器可以访问有关
        // isCat 的信息，因此知道 animal 已经被智能转换
        // 为 Cat 类型。
        // 因此，可以调用 purr() 函数。
        animal.purr()
    }
}

fun main(){
    val kitty = Cat()
    petAnimal(kitty)
    // Purr purr
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="2.0" id="kotlin-smart-casts-local-variables" validate="false"}

### 逻辑运算符 {id=logical-operators}

如果在 `&&` 或 `||` 运算符的左侧有类型检查（不论是确认某个类型还是排除某个类型），编译器可以对右侧进行智能类型转换：

```kotlin
// 在 `||` 的右侧，x 会自动转换为 String
if (x !is String || x.length == 0) return

// 在 `&&` 的右侧，x 会自动转换为 String
if (x is String && x.length > 0) {
    print(x.length) // x 会自动转换为 String
}
```

如果你将对象的类型检查与 `||` 运算符结合使用，编译器会将对象智能地转换为它们的最接近的共同超类型：

```kotlin
interface Status {
    fun signal() {}
}

interface Ok : Status
interface Postponed : Status
interface Declined : Status

fun signalCheck(signalStatus: Any) {
    if (signalStatus is Postponed || signalStatus is Declined) {
        // signalStatus 被智能转换为共同超类型 Status
        signalStatus.signal()
    }
}
```

> 共同超类型是对 **联合类型** 的一种 **近似**。
> Kotlin [目前不支持](https://youtrack.jetbrains.com/issue/KT-13108/Denotable-union-and-intersection-types) 联合类型。
>
{style="note"}

### 内联函数 {id=inline-functions}

编译器可以对传递给 [内联函数](inline-functions.md) 的 lambda 函数中捕获的变量进行智能类型转换。

内联函数被视为具有隐式
[`callsInPlace`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.contracts/-contract-builder/calls-in-place.html) 合约。
这意味着传递给内联函数的任何 lambda 函数都是在原地调用的。
由于 lambda 函数是在原地调用的，编译器知道 lambda 函数不能泄漏对其函数体内任何变量的引用。

编译器利用这一知识点，以及其他分析，来决定是否安全地对任何捕获的变量进行智能类型转换。例如：

```kotlin
interface Processor {
    fun process()
}

inline fun inlineAction(f: () -> Unit) = f()

fun nextProcessor(): Processor? = null

fun runProcessor(): Processor? {
    var processor: Processor? = null
    inlineAction {
        // 编译器知道 processor 是一个局部变量，并且 inlineAction()
        // 是一个内联函数，因此对 processor 的引用不能泄漏。
        // 因此，智能类型转换 processor 是安全的。
      
        // 如果 processor 不为 null，processor 被智能转换
        if (processor != null) {
            // 编译器知道 processor 不为 null，所以不需要安全调用
            processor.process()
        }

        processor = nextProcessor()
    }

    return processor
}
```

### 异常处理 {id=exception-handling}

智能类型转换信息会传递到 `catch` 和 `finally` 块。
这一变化使你的代码更安全，因为编译器会跟踪你的对象是否具有可空类型。例如：

```kotlin
//sampleStart
fun testString() {
    var stringInput: String? = null
    // stringInput 被智能转换为 String 类型
    stringInput = ""
    try {
        // 编译器知道 stringInput 不是 null
        println(stringInput.length)
        // 0

        // 编译器拒绝之前关于 stringInput 的智能转换信息。
        // 现在 stringInput 的类型是 String?。
        stringInput = null

        // 触发异常
        if (2 > 1) throw Exception()
        stringInput = ""
    } catch (exception: Exception) {
        // 编译器知道 stringInput 可能为 null，
        // 所以 stringInput 保持可空类型。
        println(stringInput?.length)
        // null
    }
}
//sampleEnd
fun main() {
    testString()
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="2.0" id="kotlin-smart-casts-exception-handling"}

### 智能类型转换的前提条件 {id=smart-cast-prerequisites}

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

## "不安全" 强制转换运算符 {id=unsafe-cast-operator}

要显式地将对象转换为非空类型，可以使用 **不安全** 的类型转换操作符 `as`：

```kotlin
val x: String = y as String
```

如果无法进行转换，编译器会抛出异常。这就是为什么它被称为 **不安全** 的原因。

在前面的例子中，如果 `y` 是 `null`，上面的代码也会抛出异常。
这是因为 `null` 不能转换为 `String`，因为 `String` 不是 [可空的](null-safety.md)。
为了使示例适应可能的 null 值，在类型转换的右侧使用可空类型：

```kotlin
val x: String? = y as String?
```

## "安全"（可空）强制转换运算符 {id=安全可空强制转换运算符}

为了避免异常，使用**安全**强制转换运算符 `as?`，在失败时返回 `null`。

```kotlin
val x: String? = y as? String
```

请注意，尽管 `as?` 的右侧是非可空类型 `String`，但转换的结果是可空的。
