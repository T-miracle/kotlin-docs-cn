[//]: # (title: 与 JavaScript 的互操作性)

[//]: # (TODO 等待校验)

Kotlin/Wasm 允许你在 Kotlin 中使用 JavaScript 代码，也可以在 JavaScript 中使用 Kotlin 代码。

与 [Kotlin/JS](js-overview.md) 一样，Kotlin/Wasm 编译器也支持与 JavaScript 的互操作性。
如果你熟悉 Kotlin/JS 的互操作性，你会发现 Kotlin/Wasm 的互操作性是相似的。
然而，仍然有一些关键的不同点需要注意。

> Kotlin/Wasm 目前是 [Alpha](components-stability.md) 版本。
> 它可能会随时改变。请不要在生产环境中使用。
> 我们非常感谢你在 [YouTrack](https://youtrack.jetbrains.com/issue/KT-56492) 上的反馈。
>
{style="note"}

## 在 Kotlin 中使用 JavaScript 代码 {id=use-javascript-code-in-kotlin}

学习如何通过使用 `external` 声明、包含 JavaScript 代码片段的函数
以及 `@JsModule` 注解，在 Kotlin 中使用 JavaScript 代码。

### 外部声明 {id=external-declarations}

默认情况下，外部 JavaScript 代码在 Kotlin 中是不可见的。
要在 Kotlin 中使用 JavaScript 代码，可以使用 `external` 声明来描述其 API。

#### JavaScript 函数 {id=javascript-functions}

考虑以下 JavaScript 函数：

```javascript
function greet (name) {
    console.log("Hello, " + name + "!");
}
```

你可以在 Kotlin 中将其声明为 `external` 函数：

```kotlin
external fun greet(name: String)
```

外部函数没有函数体，你可以像调用常规 Kotlin 函数一样调用它：

```kotlin
fun main() {
    greet("Alice")
}
```

#### JavaScript 属性 {id=javascript-properties}

考虑以下全局 JavaScript 变量：

```javascript
let globalCounter = 0;
```

你可以在 Kotlin 中使用外部 `var` 或 `val` 属性来声明它：

```kotlin
external var globalCounter: Int
```

这些属性在外部进行初始化。属性不能在 Kotlin 代码中使用 `= value` 进行初始化。

#### JavaScript 类 {id=javascript-classes}

考虑以下 JavaScript 类：

```javascript
class Rectangle {
    constructor (height, width) {
        this.height = height;
        this.width = width;
    }

    area () {
        return this.height * this.width;
    }
}
```

你可以在 Kotlin 中将其作为外部类使用：

```kotlin
external class Rectangle(height: Double, width: Double) : JsAny {
    val height: Double
    val width: Double
    fun area(): Double
}
```

所有 `external` 类中的声明都被隐式地视为外部的。

#### 外部接口 {id=external-interfaces}

你可以在 Kotlin 中描述 JavaScript 对象的形状。考虑以下 JavaScript 函数及其返回值：

```javascript
function createUser (name, age) {
    return { name: name, age: age };
}
```

可以看到如何在 Kotlin 中用 `external interface User` 类型来描述它的形状：

```kotlin
external interface User : JsAny {
    val name: String
    val age: Int
}

external fun createUser(name: String, age: Int): User
```

外部接口没有运行时类型信息，仅是一个编译时概念。
因此，外部接口相比于常规接口有一些限制：
* 不能在 `is` 检查的右侧使用它们。
* 不能在类字面表达式中使用它们（如 `User::class`）。
* 不能将它们作为具体类型参数传递。
* 对外部接口的 `as` 强制转换总是会成功。

#### 外部对象 {id=external-objects}

考虑以下持有对象的 JavaScript 变量：

```javascript
let Counter = {
    value: 0,
    step: 1,
    increment () {
        this.value += this.step;
    }
};
```

你可以在 Kotlin 中将它们作为外部对象使用：

```kotlin
external object Counter : JsAny {
    fun increment()
    val value: Int
    var step: Int
}
```

#### 外部类型层次结构 {id=external-type-hierarchy}

与常规类和接口类似，你可以声明外部声明以扩展其他外部类和实现外部接口。
然而，你不能在同一类型层次结构中混合外部声明和非外部声明。

### Kotlin 函数与 JavaScript 代码 {id=kotlin-functions-with-javascript-code}

你可以通过定义一个带有 `= js("code")` 函数体的函数，将 JavaScript 代码片段添加到 Kotlin/Wasm 代码中：

```kotlin
fun getCurrentURL(): String =
    js("window.location.href")
```

如果你想运行一块 JavaScript 语句，将你的代码放在花括号 `{}` 中：

```kotlin
fun setLocalSettings(value: String): Unit = js(
    """{
        localStorage.setItem('settings', value);
}"""
)
```

如果你想返回一个对象，将花括号 `{}` 用括号 `()` 括起来：

```kotlin
fun createJsUser(name: String, age: Int): JsAny =
    js("({ name: name, age: age })")
```

Kotlin/Wasm 对 `js()` 函数的调用进行特殊处理，且其实现有一些限制：
* `js()` 函数调用必须提供一个字符串字面量参数。
* `js()` 函数调用必须是函数体中的唯一表达式。
* `js()` 函数仅允许在包级函数中调用。
* 函数的返回类型必须显式提供。
* [类型](#type-correspondence) 受限，与 `external fun` 类似。

Kotlin 编译器将代码字符串放入生成的 JavaScript 文件中的函数中，并将其导入 WebAssembly 格式。
Kotlin 编译器不会验证这些 JavaScript 代码片段。
如果存在 JavaScript 语法错误，会在运行 JavaScript 代码时报告。

> `@JsFun` 注解具有类似功能，可能会被弃用。
>
{style="note"}

### JavaScript 模块 {id=javascript-modules}

默认情况下，外部声明对应于 JavaScript 全局作用域。如果你使用
[`@JsModule` 注解](js-modules.md#jsmodule-annotation) 注解 Kotlin 文件，则该文件中的所有外部声明都从指定的模块导入。

考虑以下 JavaScript 代码示例：

```javascript
// users.mjs
export let maxUsers = 10;

export class User {
    constructor (username) {
        this.username = username;
    }
}
```

在 Kotlin 中使用这些 JavaScript 代码时，加上 `@JsModule` 注解：

```kotlin
// Kotlin
@file:JsModule("./users.mjs")

external val maxUsers: Int

external class User : JsAny {
    constructor(username: String)

    val username: String
}
```

## 在 JavaScript 中使用 Kotlin 代码 {id=use-kotlin-code-in-javascript}

学习如何通过使用 `@JsExport` 注解在 JavaScript 中使用你的 Kotlin 代码。

### 带有 @JsExport 注解的函数 {id=functions-with-the-jsexport-annotation}

要使 Kotlin/Wasm 函数对 JavaScript 代码可用，请使用 `@JsExport` 注解：

```kotlin
// Kotlin/Wasm

@JsExport
fun addOne(x: Int): Int = x + 1
```

带有 `@JsExport` 注解的 Kotlin/Wasm 函数会作为生成的 `.mjs` 模块的 `default` 导出的属性显示。
然后，你可以在 JavaScript 中使用这个函数：

```javascript
// JavaScript

import exports from "./module.mjs"

exports.addOne(10)
```

Kotlin/Wasm 编译器能够从 Kotlin 代码中的任何 `@JsExport` 声明生成 TypeScript 定义。
这些定义可以被 IDE 和 JavaScript 工具使用，以提供代码自动完成、帮助类型检查，并使从 JavaScript 和 TypeScript 中使用 Kotlin 代码变得更容易。

Kotlin/Wasm 编译器会收集所有标记为 `@JsExport` 注解的顶级函数，并自动生成 `.d.ts` 文件中的 TypeScript 定义。

要生成 TypeScript 定义，在 `build.gradle.kts` 文件的 `wasmJs{}` 块中，添加 `generateTypeScriptDefinitions()` 函数：

```kotlin
kotlin {
    wasmJs {
        binaries.executable()
        browser {
        }
        generateTypeScriptDefinitions()
    }
}
```

> 在 Kotlin/Wasm 中生成 TypeScript 声明文件是 [实验性的](components-stability.md#stability-levels-explained)。
> 它可能会随时被删除或更改。
>
{style="warning"}

## 类型对应关系 {id=type-correspondence}

Kotlin/Wasm 仅允许某些类型在 JavaScript 互操作声明的签名中。
这些限制适用于带有 `external`、`= js("code")` 或 `@JsExport` 的声明。

请查看 Kotlin 类型与 JavaScript 类型的对应关系：

| Kotlin                                                     | JavaScript                        |
|------------------------------------------------------------|-----------------------------------|
| `Byte`, `Short`, `Int`, `Char`, `UByte`, `UShort`, `UInt`, | `Number`                          |
| `Float`, `Double`,                                         | `Number`                          |
| `Long`, `ULong`,                                           | `BigInt`                          |
| `Boolean`,                                                 | `Boolean`                         |
| `String`,                                                  | `String`                          |
| `Unit` 在返回位置                                           | `undefined`                       |
| 函数类型，例如 `(String) -> Int`                            | Function                          |
| `JsAny` 和其子类型                                              | 任何 JavaScript 值                 |
| `JsReference`                                              | 不透明的 Kotlin 对象引用          |
| 其他类型                                                       | 不支持                             |

你也可以使用这些类型的可空版本。

### JsAny 类型 {id=jsany-type}

JavaScript 值在 Kotlin 中使用 `JsAny` 类型及其子类型进行表示。

标准库为一些类型提供了表示：
* 包 `kotlin.js`：
  * `JsAny`
  * `JsBoolean`，`JsNumber`，`JsString`
  * `JsArray`
  * `Promise`
* 包 `org.khronos.webgl`：
  * 类型化数组，如 `Int8Array`
  * WebGL 类型
* 包 `org.w3c.dom.*`：
  * DOM API 类型

你也可以通过声明 `external` 接口或类来创建自定义的 `JsAny` 子类型。

### JsReference 类型 {id=jsreference-type}

Kotlin 值可以通过 `JsReference` 类型作为不透明引用传递给 JavaScript。

例如，如果你想将这个 Kotlin 类 `User` 暴露给 JavaScript：

```kotlin
class User(var name: String)
```

你可以使用 `toJsReference()` 函数创建 `JsReference<User>` 并将其返回给 JavaScript：

```kotlin
@JsExport
fun createUser(name: String): JsReference<User> {
    return User(name).toJsReference()
}
```

这些引用在 JavaScript 中并不直接可用，并且表现得像是空的冻结 JavaScript 对象。
要操作这些对象，你需要使用 `get()` 方法导出更多函数到 JavaScript，以解包引用值：

```kotlin
@JsExport
fun setUserName(user: JsReference<User>, name: String) {
    user.get().name = name
}
```

你可以在 JavaScript 中创建一个类并更改它的名称：

```javascript
import UserLib from "./userlib.mjs"

let user = UserLib.createUser("Bob");
UserLib.setUserName(user, "Alice");
```

### 类型参数 {id=type-parameters}

JavaScript 互操作声明可以具有类型参数，前提是它们的上界是 `JsAny` 或其子类型。例如：

```kotlin
external fun <T : JsAny> processData(data: JsArray<T>): T
```

## 异常处理 {id=exception-handling}

Kotlin 的 `try-catch` 表达式无法捕获 JavaScript 异常。

如果你尝试使用 JavaScript 的 `try-catch` 表达式来捕获 Kotlin/Wasm 异常，它看起来像是一个通用的
`WebAssembly.Exception`，没有直接可访问的消息和数据。

## Kotlin/Wasm 和 Kotlin/JS 互操作性差异

尽管 Kotlin/Wasm 互操作性与 Kotlin/JS 互操作性有相似之处，但有一些关键差异需要考虑：

|                 | **Kotlin/Wasm**                                                                                                       | **Kotlin/JS**                                                                                             |
|-----------------|-----------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|
| **外部枚举**        | 不支持外部枚举类。                                                                                                             | 支持外部枚举类。                                                                                                  |
| **类型扩展**        | 不支持非外部类型扩展外部类型。                                                                                                       | 支持非外部类型。                                                                                                  |
| **`JsName` 注解** | 仅在注解外部声明时有效。                                                                                                          | 可用于更改常规非外部声明的名称。                                                                                          |
| **`js()` 函数**   | `js("code")` 函数调用仅允许作为包级函数的单一表达式体。                                                                                    | `js("code")` 函数可以在任何上下文中调用，并返回 `dynamic` 值。                                                               |
| **模块系统**        | 仅支持 ES 模块。没有 `@JsNonModule` 注解的类似功能。将其导出作为 `default` 对象上的属性。仅允许导出包级函数。                                                | 支持 ES 模块和传统模块系统。提供命名的 ESM 导出。允许导出类和对象。                                                                    |
| **类型**          | 对所有互操作声明 `external`、`= js("code")` 和 `@JsExport` 应用更严格的类型限制。允许使用有限的 [Kotlin 内置类型和 `JsAny` 子类型](#type-correspondence)。 | 允许在 `external` 声明中使用所有类型。限制 [在 `@JsExport` 中可以使用的类型](js-to-kotlin-interop.md#kotlin-types-in-javascript)。 |
| **Long**        | 类型对应于 JavaScript `BigInt`。                                                                                            | 在 JavaScript 中可见为自定义类。                                                                                    |
| **数组**          | 互操作中尚不直接支持。可以使用新的 `JsArray` 类型代替。                                                                                     | 实现为 JavaScript 数组。                                                                                        |
| **其他类型**        | 需要 `JsReference<>` 以将 Kotlin 对象传递给 JavaScript。                                                                        | 允许在外部声明中使用非外部 Kotlin 类类型。                                                                                 |
| **异常处理**        | 无法捕获 JavaScript 异常。                                                                                                   | 可以通过 `Throwable` 类型捕获 JavaScript `Error`。可以使用 `dynamic` 类型捕获任何 JavaScript 异常。                             |
| **动态类型**        | 不支持 `dynamic` 类型。改为使用 `JsAny` 类型（见下方示例代码）。                                                                            | 支持 `dynamic` 类型。                                                                                          |

> Kotlin/Wasm 不支持 Kotlin/JS 的 [动态类型](dynamic-type.md)，用于与未类型化或松散类型的对象互操作。
> 你可以使用 `JsAny` 类型代替 `dynamic` 类型：
>
> ```kotlin
> // Kotlin/JS
> fun processUser(user: dynamic, age: Int) {
>     // ...
>     user.profile.updateAge(age)
>     // ...
> }
>
> // Kotlin/Wasm
> private fun updateUserAge(user: JsAny, age: Int): Unit =
>     js("{ user.profile.updateAge(age); }")
>
> fun processUser(user: JsAny, age: Int) {
>     // ...
>     updateUserAge(user, age)
>     // ...
> }
> ```
>
{style="note"}
