[//]: # (title: This 表达式)

要表示当前 _接收者_，你使用 `this` 表达式：

* 在 [类](classes.md#继承) 的成员中，`this` 指的是该类的当前对象。
* 在 [扩展函数](extensions.md) 或 [带接收者的函数字面量](lambdas.md#function-literals-with-receiver) 中，
`this` 表示传递在点号左侧的 _接收者_ 参数。

如果 `this` 没有限定符，它指的是 _最内层的封闭范围_。要在其他范围中引用 `this`，使用 _标签限定符_：

## 带限定符的 this {id=qualified-this}

要从外部范围（[类](classes.md)、[扩展函数](extensions.md)
或带标签的 [带接收者的函数字面量](lambdas.md#function-literals-with-receiver)）访问 `this`，你需要写 `this@label`，
其中 `@label` 是范围上的一个 [标签](returns.md)，表示 `this` 所在的范围：

```kotlin
class A { // 隐式标签 @A
    inner class B { // 隐式标签 @B
        fun Int.foo() { // 隐式标签 @foo
            val a = this@A // A 的 this
            val b = this@B // B 的 this

            val c = this // foo() 的接收者，一个 Int
            val c1 = this@foo // foo() 的接收者，一个 Int

            val funLit = lambda@ fun String.() {
                val d = this // funLit 的接收者，一个 String
            }

            val funLit2 = { s: String ->
                // foo() 的接收者，因为封闭的 lambda 表达式
                // 没有任何接收者
                val d1 = this
            }
        }
    }
}
```

## 隐式 this {id=implicit-this}

当你在 `this` 上调用成员函数时，可以省略 `this.` 部分。如果有一个同名的非成员函数，请谨慎使用，因为在某些情况下它可能会被调用：

```kotlin
fun main() {
    fun printLine() { println("Local function") }
    
    class A {
        fun printLine() { println("成员函数") }

        fun invokePrintLine(omitThis: Boolean = false) { 
            if (omitThis) printLine()
            else this.printLine()
        }
    }
    
    A().invokePrintLine() // 成员函数
    A().invokePrintLine(omitThis = true) // 局部函数
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

