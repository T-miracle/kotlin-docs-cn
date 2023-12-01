[//]: # (title: 继承)

所有 Kotlin 中的类都有一个共同的超类：`Any`，它是没有声明超类型的类的默认超类：

```kotlin
class Example // 隐式继承自 Any
```

`Any` 类在 Kotlin 中有三个方法，它们分别是 `equals()`、`hashCode()` 和 `toString()`。
因此，所有 Kotlin 类都会继承这些方法。

默认情况下，Kotlin 类是 final 的，它们不能被继承。要使类可继承，使用 `open` 关键字标记它：

```kotlin
open class Base // 类是可继承的
```

如果你想为一个类显式地声明超类型，只需在类头的冒号后面指定相应的类型：

```kotlin
open class Base(p: Int)

class Derived(p: Int) : Base(p)
```

当派生类拥有一个主构造函数时，其基类可以（且必须）在主构造函数中使用传递的参数进行初始化。

但是，如果派生类没有主构造函数，那么每个次构造函数都必须使用 `super` 关键字来初始化基类，或者它必须委托给另一个能够执行此初始化操作的构造函数。
这确保了基类在派生类的构造过程中得到适当的初始化。

值得注意的是，在这种情况下，不同的次构造函数可以调用基类的不同构造函数，以满足特定的初始化需求：

```kotlin
class MyView : View {
    constructor(ctx: Context) : super(ctx)

    constructor(ctx: Context, attrs: AttributeSet) : super(ctx, attrs)
}
```

## 重写方法 {id=重写方法}

在 Kotlin 中，对可重写成员和重写方法需要显式添加修饰符：

```kotlin
open class Shape {
    open fun draw() { /*...*/ }
    fun fill() { /*...*/ }
}

class Circle() : Shape() {
    override fun draw() { /*...*/ }
}
```

对于 `Circle.draw()`，需要添加 `override` 修饰符。
如果省略了它，编译器会报错。
如果一个函数没有 `open` 修饰符，比如 `Shape.fill()`，在子类中声明具有相同签名的方法是不允许的，无论是否使用 `override` 修饰符。
在 final 类（即没有 `open` 修饰符的类）的成员上添加 `open` 修饰符是没有效果的。

被标记为 `override` 的成员本身是可继承的，因此它可以在子类中被重写。
如果要禁止再次重写，可以使用 `final` 关键字：

```kotlin
open class Rectangle() : Shape() {
    final override fun draw() { /*...*/ }
}
```

## 重写属性 {id=重写属性}

重写属性的机制与方法类似。
如果在超类中声明了一个属性，然后在派生类中重新声明，必须使用 `override` 关键字，同时确保它们的类型是兼容的。
每个被声明的属性可以被 具有初始化程序的属性 或 带有 `get` 方法的属性 重写：

```kotlin
open class Shape {
    open val vertexCount: Int = 0
}

class Rectangle : Shape() {
    override val vertexCount = 4
}
```

你还可以使用 `var` 属性来重写 `val` 属性，但是反过来是不允许的。
这是因为 `val` 属性本质上声明了一个 `get` 方法，而将其重写为 `var` 就在派生类中额外声明了一个 `set` 方法。

值得注意的是，你可以在主构造函数中的属性声明中使用 `override` 关键字：

```kotlin
interface Shape {
    val vertexCount: Int
}

class Rectangle(override val vertexCount: Int = 4) : Shape // 总是有4个顶点

class Polygon : Shape {
    override var vertexCount: Int = 0  // 以后可以设置为任意数字
}
```

## 派生类初始化顺序 {id=派生类初始化顺序}

在构造派生类的新实例期间，基类初始化是第一步完成的（仅在基类构造函数的参数求值之前），这意味着它在派生类的初始化逻辑运行之前发生。

```kotlin
open class Base(val name: String) {

    init { println("初始化基类") }

    open val size: Int = 
        name.length.also { println("在基类中初始化 size: $it") }
}

class Derived(
    name: String,
    val lastName: String,
) : Base(name.replaceFirstChar { it.uppercase() }.also { println("基类参数: $it") }) {

    init { println("初始化派生类") }

    override val size: Int =
        (super.size + lastName.length).also { println("在派生类中初始化 size: $it") }
}

fun main() {
    println("构造派生类(\"hello\", \"world\")")
    Derived("hello", "world")
}
```
{kotlin-runnable="true"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIA6A7CAHGKAEYA2AhgM5HYBCxMAFAG4F7YoEC2M82AygC4BOAligDmASmzBUqbFOwC%2BXMdnT8UXPCipIQASRRy%2B9PgC8Bg7AWwAjSrkIlNogL4Sc0jFmx0GRI22w75ALzYktKhTKwAdHhYglwAFhH0RBAKSgKq6po6egbGQtjehjAyOPHFVkTF%2BMRE7AAkcvbYTigtqNUk2AAiMPw0MFAaLlLhvtzKggA0IVKe2LZcAHIsY7wm0yii7BSVVKMRPDDohGAwAGJ8PERcAMJxBDwKchEAruiYPGCUVI6JeMmpZQZDQgACCPEEL1YKmwADMII8ypZrB1atgGlwmg5ROIUDMSnJAek1CDslx9HgjCYzNhYH0BjYaljnKEIP0ePxYB56AUfOx%2FNgAvjQlQiG9ehFCsUANTzYhLFZRGLxYR%2FAHARRAklZXTk3LUqUlbBIul8fpQRkkeqNECOVBtPEoWEvHDMAgCH5ifFpFTakA3CAoa48F5gcn5E29M0M1EaJCaOIwPB4CDxzSTYLxkAAdwReCgadt9nxPXpgwTSZT6eCObzBaLrRAkxAXAeghgXAACoQuPCeMwECAAFYEOhN8AQZjoPjRHgANV63kDg4AjBEAJwRABMAAYQA4gA%3D?_gl=1*tqulsc*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMTM5MjQzMS41Mi4xLjE3MDEzOTY2NDMuNTkuMC4w&_ga=2.246417028.1244175791.1701270364-2060274793.1694940376)

这意味着在执行基类构造函数时，派生类中声明或重写的属性尚未初始化。
在基类初始化逻辑中（直接或间接通过另一个重写的 `open` 成员实现）使用这些属性可能导致不正确的行为或运行时失败。
因此，在设计基类时，应避免在构造函数、属性初始化程序或 `init` 块中使用 `open` 成员。

## 调用超类实现 {id=调用超类实现}

在派生类中，可以使用 `super` 关键字调用其超类的函数和属性访问器实现：

```kotlin
open class Rectangle {
    open fun draw() { println("绘制矩形") }
    val borderColor: String get() = "黑色"
}

class FilledRectangle : Rectangle() {
    override fun draw() {
        super.draw()
        println("填充矩形")
    }

    val fillColor: String get() = super.borderColor
}
```

在内部类中，访问外部类的超类是通过带有外部类名称的 `super` 关键字来完成的：`super@Outer`：

```kotlin
open class Rectangle {
    open fun draw() { println("绘制矩形") }
    val borderColor: String get() = "黑色"
}

class FilledRectangle: Rectangle() {
    override fun draw() {
        val filler = Filler()
        filler.drawAndFill()
    }
    
    inner class Filler {
        fun fill() { println("填充") }
        fun drawAndFill() {
            super@FilledRectangle.draw() // 调用 Rectangle 的 draw() 实现
            fill()
            println("绘制带颜色的填充矩形，颜色为 ${super@FilledRectangle.borderColor}") // 使用 Rectangle 的 borderColor 的 get() 实现
        }
    }
}

fun main() {
    val fr = FilledRectangle()
    fr.draw()
}
```
{kotlin-runnable="true"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIIAcYDsAEYA2BDAzvhgEoxgAuuaA5tjBsADqYasroYBmArplAE64A7gAoAlAwzJ%2BASzTlsaEYxAARQULnUMuDPzKUadFRIC%2BzVqwBuubBgBGEfrH4BhCNifwMAZXKyaDGoYcnEMAF4MFXs8MABrFWZzNGZmHAIiADEZbDooUgoqWhhvAsNisKYWNisYfllYLl4MAWFKi0tOmztOHLp%2BCIxs3LrxDs7WXpH%2BADpWoQBBNChh7DHqjGTO8Yw5NDqsPEIhvoOqicseTCm1iWApAIUlFVWtE02dzquWjSWVvvaGwurHw3FQ%2FAAAqsYPkDEU6HMNGEAPTIjCuWzYIhleEwADkRBkAFtkHQiehKOQZBBMBBOD82mJPsCbutgRNpHInso1BpMLobjC9HCjPRNOQABZYDxODAAEmAoPBUNOsMKopmjmcdXcnn45hAElRGAAqvgYNiRcUCbsSWSKbgqTSMHSHE4XLqnDbgqEmUDLFsA0lUik0N8ibg5ICurYuANItC1eU6GyWbN5utTCAADQgSj8H0ABTw5E4TiJCBAACtcDYc%2BAICScnUAGp1fDUtCVgCMMwAnDMAEwABhApiAA%3D%3D%3D?_gl=1*tqulsc*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMTM5MjQzMS41Mi4xLjE3MDEzOTY2NDMuNTkuMC4w&_ga=2.246417028.1244175791.1701270364-2060274793.1694940376)

## 重写规则 {id=重写规则}

在 Kotlin 中，实现继承必须遵循一些规则：
如果一个类从其直接超类继承了相同成员的多个实现，那么该类必须重写这个成员，并提供自己的实现（可以选择使用其中某个超类的实现）。

为了明确表示继承的实现来自哪个超类型，可以使用 `super` 后跟尖括号和超类型的名称，例如 `super<Base>`。
这样可以清楚地指定使用哪个超类的实现。

```kotlin
open class Rectangle {
    open fun draw() { /* ... */ }
}

interface Polygon {
    fun draw() { /* ... */ } // 接口成员默认为 'open'
}

class Square() : Rectangle(), Polygon {
    // 编译器要求必须重写 draw()：
    override fun draw() {
        super<Rectangle>.draw() // 调用 Rectangle.draw()
        super<Polygon>.draw() // 调用 Polygon.draw()
    }
}
```

`Square` 类可以同时继承 `Rectangle` 和 `Polygon`。
虽然这是允许的，但由于这两者都有各自的 `draw()` 实现，因此在 `Square` 中必须重新定义 `draw()` 并提供一个独立的实现，以消除潜在的歧义。