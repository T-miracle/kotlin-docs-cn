[//]: # (title: 类型安全的构建器)

通过使用良好命名的函数作为构建器，并结合[带接受者的函数字面值](lambdas.md#function-literals-with-receiver)，可以在
Kotlin 中创建类型安全、静态类型的构建器。

类型安全的构建器允许创建基于 Kotlin 的领域特定语言（DSL），适合以半声明方式构建复杂的层次数据结构。构建器的示例用例包括：

* 使用 Kotlin 代码生成标记语言，例如[HTML](https://github.com/Kotlin/kotlinx.html) 或 XML
* 配置 Web 服务器的路由：[Ktor](https://ktor.io/docs/routing.html)

请考虑以下代码：

```kotlin
import com.example.html.* // 参见下方声明

fun result() =
    html {
        head {
            title {+"用 Kotlin 进行 XML 编码"}
        }
        body {
            h1 {+"用 Kotlin 进行 XML 编码"}
            p  {+"这种格式可以作为 XML 的替代标记"}

            // 一个具有属性和文本内容的元素
            a(href = "https://kotlinlang.org") {+"Kotlin"}

            // 混合内容
            p {
                +"这是一些"
                b {+"混合"}
                +"文本。更多内容请参见"
                a(href = "https://kotlinlang.org") {+"Kotlin"}
                +"项目"
            }
            p {+"一些文本"}

            // 由内容生成
            p {
                for (arg in args)
                    +arg
            }
        }
    }
```

这是完全合法的 Kotlin 代码。
你可以[在这里在线尝试这段代码（修改并在浏览器中运行）](https://play.kotlinlang.org/byExample/09_Kotlin_JS/06_HtmlBuilder)。

## 原理 {id=how-it-works}

假设你需要在 Kotlin 中实现一个类型安全的构建器。
首先，定义你要构建的模型。在这种情况下，你需要建模 HTML 标签。
这可以通过一组类轻松完成。
例如，`HTML` 是一个描述 `<html>` 标签的类，定义了诸如 `<head>` 和 `<body>` 的子元素。
（参见其声明 [下方](#full-definition-of-the-com-example-html-package)。）

现在，让我们回想一下为什么你可以在代码中这样说：

```kotlin
html {
 // ...
}
```

`html` 实际上是一个函数调用，它接受一个 [lambda 表达式](lambdas.md) 作为参数。
此函数定义如下：

```kotlin
fun html(init: HTML.() -> Unit): HTML {
    val html = HTML()
    html.init()
    return html
}
```

此函数接受一个名为 `init` 的参数，该参数本身是一个函数。
函数的类型是 `HTML.() -> Unit`，这是一个**具备接收者的函数类型**。
这意味着你需要将一个 `HTML` 类型的实例（**接收者**）传递给该函数，
并且你可以在函数内部调用该实例的成员。

接收者可以通过 `this` 关键字访问：

```kotlin
html {
    this.head { ... }
    this.body { ... }
}
```

（`head` 和 `body` 是 `HTML` 的成员函数。）

现在，`this` 可以像往常一样省略，你得到的代码看起来已经非常像一个构建器了：

```kotlin
html {
    head { ... }
    body { ... }
}
```

那么，这个调用会做什么呢？让我们看看上面定义的 `html` 函数的主体。
它创建了一个 `HTML` 的新实例，然后通过调用作为参数传递的函数来初始化它
（在这个例子中，这归结为在 `HTML` 实例上调用 `head` 和 `body`），然后返回这个实例。
这正是构建器应该做的。

`HTML` 类中的 `head` 和 `body` 函数的定义与 `html` 类似。
唯一的区别是它们将构建的实例添加到外部 `HTML` 实例的 `children` 集合中：

```kotlin
fun head(init: Head.() -> Unit): Head {
    val head = Head()
    head.init()
    children.add(head)
    return head
}

fun body(init: Body.() -> Unit): Body {
    val body = Body()
    body.init()
    children.add(body)
    return body
}
```

实际上，这两个函数做的是相同的事情，所以你可以有一个泛型版本，即 `initTag`：

```kotlin
protected fun <T : Element> initTag(tag: T, init: T.() -> Unit): T {
    tag.init()
    children.add(tag)
    return tag
}
```

所以，现在你的函数非常简单：

```kotlin
fun head(init: Head.() -> Unit) = initTag(Head(), init)

fun body(init: Body.() -> Unit) = initTag(Body(), init)
```

你可以用它们来构建 `<head>` 和 `<body>` 标签。

这里还需要讨论的另一个问题是如何向标签主体添加文本。在上面的例子中，你可以这样说：

```kotlin
html {
    head {
        title {+"用 Kotlin 进行 XML 编码"}
    }
    // ...
}
```

基本上，你只需将一个字符串放入标签主体，但在它前面有一个小小的 `+`，这实际上是一个函数调用，调用的是前缀 `unaryPlus()` 操作。
该操作实际上是通过扩展函数 `unaryPlus()` 定义的，该函数是 `TagWithText` 抽象类（`Title` 的父类）的成员：

```kotlin
operator fun String.unaryPlus() {
    children.add(TextElement(this))
}
```

所以，前缀 `+` 在这里的作用是将字符串包装成一个 `TextElement` 实例，并将其添加到 `children` 集合中，使其成为标签树的适当部分。

所有这些都定义在 `com.example.html` 包中，该包在上述构建器示例的顶部导入。
在最后一节，你可以阅读该包的完整定义。

## 作用域控制：`@DslMarker` {id=scope-control}

在使用 DSL（领域特定语言）时，你可能遇到过一个问题，即在上下文中可以调用太多函数。
你可以调用每个可用的隐式接收者的方法，在 lambda 内部因此得到一个不一致的结果，比如在 `head` 内部调用另一个 `head`：

```kotlin
html {
    head {
        head {} // 应该被禁止
    }
    // ...
}
```

在这个例子中，只有最近的隐式接收者 `this@head` 的成员应该是可用的；`head()` 是外部接收者 `this@html` 的成员，因此调用它必须是非法的。

为了解决这个问题，有一种特殊的机制来控制接收者的作用域。

要让编译器开始控制作用域，你只需要用相同的标记注解标注 DSL 中使用的所有接收者的类型。
例如，对于 HTML 构建器，你可以声明一个注解 `@HtmlTagMarker`：

```kotlin
@DslMarker
annotation class HtmlTagMarker
```

一个带有 `@DslMarker` 注解的注解类被称为 DSL 标记。

在我们的 DSL 中，所有的标签类都继承同一个超类 `Tag`。
只需要用 `@HtmlTagMarker` 注解超类即可，之后 Kotlin 编译器将会将所有继承的类视为已注解：

```kotlin
@HtmlTagMarker
abstract class Tag(val name: String) { ... }
```

你不需要为 `HTML` 或 `Head` 类添加 `@HtmlTagMarker` 注解，因为它们的超类已经被注解过了：

```kotlin
class HTML() : Tag("html") { ... }

class Head() : Tag("head") { ... }
```

添加了这个注解后，Kotlin 编译器就知道哪些隐式接收者属于同一个 DSL，并且只允许调用最近接收者的成员：

```kotlin
html {
    head {
        head { } // 错误：外部接收者的成员
    }
    // ...
}
```

注意，仍然可以调用外部接收者的成员，但需要显式指定该接收者：

```kotlin
html {
    head {
        this@html.head { } // 可行
    }
    // ...
}
```

## `com.example.html` 包的完整定义 {id=full-definition-of-the-com-example-html-package}

这是 `com.example.html` 包的定义（仅包含上述示例中使用的元素）。
它构建了一个 HTML 树。
它大量使用了 [扩展函数](extensions.md) 和 [具有接收者的 lambda 表达式](lambdas.md#function-literals-with-receiver)。

```kotlin
package com.example.html

interface Element {
    fun render(builder: StringBuilder, indent: String)
}

class TextElement(val text: String) : Element {
    override fun render(builder: StringBuilder, indent: String) {
        builder.append("$indent$text\n")
    }
}

@DslMarker
annotation class HtmlTagMarker

@HtmlTagMarker
abstract class Tag(val name: String) : Element {
    val children = arrayListOf<Element>()
    val attributes = hashMapOf<String, String>()

    protected fun <T : Element> initTag(tag: T, init: T.() -> Unit): T {
        tag.init()
        children.add(tag)
        return tag
    }

    override fun render(builder: StringBuilder, indent: String) {
        builder.append("$indent<$name${renderAttributes()}>\n")
        for (c in children) {
            c.render(builder, indent + "  ")
        }
        builder.append("$indent</$name>\n")
    }

    private fun renderAttributes(): String {
        val builder = StringBuilder()
        for ((attr, value) in attributes) {
            builder.append(" $attr=\"$value\"")
        }
        return builder.toString()
    }

    override fun toString(): String {
        val builder = StringBuilder()
        render(builder, "")
        return builder.toString()
    }
}

abstract class TagWithText(name: String) : Tag(name) {
    operator fun String.unaryPlus() {
        children.add(TextElement(this))
    }
}

class HTML : TagWithText("html") {
    fun head(init: Head.() -> Unit) = initTag(Head(), init)

    fun body(init: Body.() -> Unit) = initTag(Body(), init)
}

class Head : TagWithText("head") {
    fun title(init: Title.() -> Unit) = initTag(Title(), init)
}

class Title : TagWithText("title")

abstract class BodyTag(name: String) : TagWithText(name) {
    fun b(init: B.() -> Unit) = initTag(B(), init)
    fun p(init: P.() -> Unit) = initTag(P(), init)
    fun h1(init: H1.() -> Unit) = initTag(H1(), init)
    fun a(href: String, init: A.() -> Unit) {
        val a = initTag(A(), init)
        a.href = href
    }
}

class Body : BodyTag("body")
class B : BodyTag("b")
class P : BodyTag("p")
class H1 : BodyTag("h1")

class A : BodyTag("a") {
    var href: String
        get() = attributes["href"]!!
        set(value) {
            attributes["href"] = value
        }
}

fun html(init: HTML.() -> Unit): HTML {
    val html = HTML()
    html.init()
    return html
}
```

