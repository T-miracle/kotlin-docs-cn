[//]: # (title: 基本语法)

这是一个包含基本语法元素及示例的集合。每个部分末尾都有一个链接，指向相关主题的详细描述。

你也可以通过 JetBrains Academy 的免费
[Kotlin Core track](https://hyperskill.org/tracks?category=4&utm_source=jbkotlin_hs&utm_medium=referral&utm_campaign=kotlinlang-docs&utm_content=button_1&utm_term=22.03.23)
学习所有 Kotlin 的基本知识。

## 包定义和导入

源文件顶部应包含包规范。

```kotlin
package my.demo

import kotlin.text.*

// ...
```

不需要匹配目录和包：源文件可以随意放置在文件系统中。

请参阅 [包](packages.md)。

## 程序入口点

Kotlin 应用的入口是 `main` 函数。

```kotlin
fun main() {
    println("Hello world!")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-hello-world"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6mG1ADgE4EAuANkeSABIzPMQYDuEOsygBCdsUoBfEABoQjHHQDmMRgAVmORkiFYEIAFY4AbjjngIWGnmYw6ANXsBnPBDQGAjADoAnN4AmAAYQKSA%3D?_gl=1*3tw9ap*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.83338003.408218914.1700050452-2060274793.1694940376)

`main` 的另一种形式接受可变数量的 `String` 参数。

```kotlin
fun main(args: Array<String>) {
    println(args.contentToString())
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFDgE4DmAzvBgIIkk4CeAPAMoAuJBZAfAJQZgAHUwYxABy5p2AGyKlKAOkjSY0gCoQOUsoT58RAXxAAaEOwUx2ABRk52SCCSwIQAKxwA3HKfAQs4ngyMCQAaiEUeBBorgCMigCcigBMAAwghkA%3D%3D%3D?_gl=1*1syatli*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.112577281.408218914.1700050452-2060274793.1694940376)

## 打印到标准输出

`print` 将其参数打印到标准输出。

```kotlin
fun main() {
    print("Hello ")
    print("world!")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-print"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGADgE4EAuh5IAEjADbcQbti1WoxZsQAdwgNuUAISDKlAL4gANCGY4GAcxjMACtxzMk0rAhAArHADcc68BCx083GAwBqHgM54IaJYAjAB0AJwhAEwADCDKQA%3D?_gl=1*1syatli*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.112577281.408218914.1700050452-2060274793.1694940376)

`println` 打印其参数并添加换行，使得下一个打印的内容出现在下一行。

```kotlin
fun main() {
    println("Hello world!")
    println(42)
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-println"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGADgE4EAuANkeSABIyusQYA7hAasoAQk7FqtRi3aEALACZpVNAF8QAGhDMcDAOYxmABVY5mSEVgQgAVjgBuOHeAhY6eVjAYA1XwBnPAg0OwBGADoATkjlAAYQDSA?_gl=1*1syatli*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.112577281.408218914.1700050452-2060274793.1694940376)

## 函数

一个具有两个 `Int` 参数和 `Int` 返回类型的函数。

```kotlin
fun sum(a: Int, b: Int): Int {
    return a + b
}

fun main() {
    print("sum of 3 and 5 is ")
    println(sum(3, 5))
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-return-int"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIA6A7AZgVxQAgM4YFsAKAQ3mwEkUAXAGmwCNyrqBKZm7YVbX7AJxjUM%2FHCWwBqRqgC%2BqeeizYCJAJYoirLjz4AHfuupEkIfAWwQ02AMzYSKKNgCs2VbmwnWO3vsMAbDTMia3onVi8UGRBaEGoSfgBzIQAFPxJqNAh%2BAgQQACsSADcSaPAIAl1VPxh%2BADUa3FUIFFyARgA6AE52gCYABhAZIA%3D%3D?_gl=1*1syatli*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.112577281.408218914.1700050452-2060274793.1694940376)

函数体可以是一个表达式。
其返回类型是自动推断出来的。

```kotlin
fun sum(a: Int, b: Int) = a + b

fun main() {
    println("sum of 19 and 23 is ${sum(19, 23)}")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-function-expression"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIA6A7AZgVxQAgM4YFsAKAQ3mwEkUAXAGmwCNyrqBKbAXmxOwGpHUg9FmwESASxRF2wVNnnYADgCdJ1ADZSkIfAWwQ02AIwBObiijYATAGZs43NgAkwXUVP1brAL7bWqbxBaEGoSZQBzGGoABXUSajQIZQIEEAArEgA3EiDwCAJFcXUYZQA1EtxxCBRUowA6EzqrAAYQbyA?_gl=1*13fqpvw*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.11970865.408218914.1700050452-2060274793.1694940376)

一个不返回任何有意义值的函数（返回类型为 `Unit`）。

```kotlin
fun printSum(a: Int, b: Int): Unit {
    println("sum of $a and $b is ${a + b}")
}

fun main() {
    printSum(-1, 8)
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-return-unit"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIA6A7AZgVxQAgA4CcBLFAFwGUMBbACgEN5sBJUgGmwCMHmSBKBgVRSES2YKmwS8RUgBsU1JCADOVbBDTYAJLWy0UULe2yElW4DoDUHAL6KeqWylSpMOSrWLUeo8ZILFyKmoAWgBGNgAOexRrEBYQElp8AHMYEgAFGVoSNAh8SgQQACtaADdaOPAISlxCGRh8ADUGpUIIFELQgDoATi6AJgAGEGsgA%3D?_gl=1*13fqpvw*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.11970865.408218914.1700050452-2060274793.1694940376)

`Unit` 返回类型可以省略。

```kotlin
fun printSum(a: Int, b: Int) {
    println("sum of $a and $b is ${a + b}")
}

fun main() {
    printSum(-1, 8)
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-function-omit-unit"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIA6A7AZgVxQAgA4CcBLFAFwGUMBbACgEN5sBJUgGmwCMHmSBKbYVNiF4ipADYpqSEAGcq2CGmwASWtloooK9tkIyVwNQGoOAX2k9U5lKlSYclWsWp8BOYQWLkq1ALQBGNgAOSxRTEBYQElp8AHMYEgAFMVoSNAh8SgQQACtaADdaCPAISlxCMRh8ADUqmUIIFGz%2FADoAThaAJgAGEFMgA%3D?_gl=1*9jv3s2*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.111545089.408218914.1700050452-2060274793.1694940376)

请参阅 [函数](functions.md)。

## 变量 {id=变量}

使用关键字 `val` 定义只读本地变量。
它们只能被赋值一次。

```kotlin
fun main() {
    val a: Int = 1  // 立即分配
    val b = 2   // 推断出“Int”类型
    val c: Int  // 未提供初始值设定项时所需的类型
    c = 3       // 延期分配
    println("a = $a, b = $b, c = $c")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-val"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGAbjgDYY7wYCSaALhgLwYBGGgHphGPFiwwoeHFxgsAzorwBzNFO7VaDZgCM%2BGAEy1RGAAacu5jFwCeABwV5F4tEhgAnT9O01dGGBsViJiACqOCj4Ajih4PlAYAO4AFjCYaBBueFyyjHgAXl7irg6eEHR4sFB%2BgYYAzLRNZrAe3tJKKuqaXJS1ZQRcjETkIDiGACQ4ADQYBvwTerNgk2CjxJQAviDTIFw4nqowXAAKjHJIEJ5YCCAAVjgMO%2BAQWA54jF4Aal4qEGi3AQAOgAnECjAAGECbIA%3D?_gl=1*9jv3s2*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.111545089.408218914.1700050452-2060274793.1694940376)

可重新分配的变量使用关键字 `var`。

```kotlin
fun main() {
    var x = 5 // 推断出“Int”类型
    x += 1
    println("x = $x")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-var"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGAbjgE4YAeGAvBgKwYD0vGAAYBJNABdBGMQE8ADjAx4AzorRIYjRjCjVabANScAjJV01ZjAmIA2RciDacAJC3vFKAXxAAaEGKYA5jBiAArWOGJIEIxYCCAAVjgMPuAQWLJ41hoAahpKeBBocUYAdACcJQBMAAwgHkA%3D%3D?_gl=1*9jv3s2*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.111545089.408218914.1700050452-2060274793.1694940376)

你可以在顶层声明变量。

```kotlin
val PI = 3.14
var x = 0

fun incrementX() { 
    x += 1 
}

fun main() {
    println("x = $x; PI = $PI")
    incrementX()
    println("incrementX()")
    println("x = $x; PI = $PI")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-variable-top-level"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIA6A7AbgQwDYAIAKAkjgLw4DMAdAIwAsqmATjgB4k4AMqqAZgK4o4AlijAMYAWxgoALgA0AFAEocwHKhwaWOANSkqalAF8uKHvxziMwpSvWaADg2HSsKeUhCtSAEmYBufCIfQg9FOw1hUQkpOSVwnEdnV3cQSLFJGQUwkDCBBycZZI8vHF8AwjZvEJzUQxAAGhBpDAYAcxhpPCwMaW4IBnEEEAArDEwG8Ahxe0EsGAYANXmAZ0EIFCGqCgBOCgAmdhBDIA?_gl=1*9jv3s2*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.111545089.408218914.1700050452-2060274793.1694940376)

请参阅 [属性](properties.md)。

## 创建类和实例

要定义一个类，使用关键字 `class`。

```kotlin
class Shape
```

类的属性可以在其声明或主体中列出。

```kotlin
class Rectangle(var height: Double, var length: Double) {
    var perimeter = (height + length) * 2 
}
```

默认构造函数，其中参数在类声明中列出，将自动可用。

```kotlin
class Rectangle(var height: Double, var length: Double) {
    var perimeter = (height + length) * 2 
}
fun main() {
    val rectangle = Rectangle(5.0, 2.0)
    println("The perimeter is ${rectangle.perimeter}")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-class-constructor"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBc4A2BDAzmgBAJRmALigHYDmSMAFAG4oBOmAFjAJYkP7yYAiEArgEbkANJhr1ypfA048B5AJSZgAHSKZ1oupgAOMWswC2MfHswBeTBSat2mANSYJJKYoBUmAEyZVAX1UAzXjUDFGYiCkUVIlUNTSRMWjxCUnJzHCTiMkoAVgA6AAYRDwL5GI1tfSJ8JHDlEAAVJh09Q2NTZiwAEmBEgkzyXN19IxNaPxBS6KIfECEQQloSYwAFVHx%2FCFoDBBAAKxQaWfAIA21mcloANT00ZggiHYBGXIBOXI98kB8gA%3D?_gl=1*9jv3s2*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.111545089.408218914.1700050452-2060274793.1694940376)

类之间的继承由冒号（`:`）声明。
类默认为 final；
要使类可继承，将其标记为 `open`。

```kotlin
open class Shape

class Rectangle(var height: Double, var length: Double): Shape() {
    var perimeter = (height + length) * 2 
}
```

详情请参阅 [类](classes.md) 和 [对象和实例](object-declarations.md)。

## 注释

与大多数现代语言一样，Kotlin 支持单行（或 _行尾_）和多行（_块_）注释。

```kotlin
// 这是一个行尾注释。

/* 这是一个跨多行的
    块注释。 */
```

Kotlin 中的块注释可以嵌套。

```kotlin
/* 评论从这里开始
/* 包含嵌套注释 *&#8288;/     
并在此结束。 */
```

查看 [记录 Kotlin 代码](kotlin-doc.md) 以获取有关文档注释语法的信息。

## 字符串模板

```kotlin
fun main() {
    var a = 1
    // 模板中的简单名称：
    val s1 = "a is $a" 
    
    a = 2
    // 模板中的任意表达式：
    val s2 = "${s1.replace("is", "was")}, but now is $a"
    println(s2)
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-string-templates"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGAbjgE4Y4YC8GAjNbQPS8YAzniwAHADYwMaHFikEMAFxhjxOZfB40G4oZ3YZyIVnkEYAJDiOHMtLSwMAme%2FxaMARnkWMmATwwwAB6ijDCCwhCYCsqq6jCatto4uoKOBkbmwIKcAHShEjhgMIRGpkYANIYgAO44gkbEAL6V7iiK0hDVGKYWViCU9iEEiuJEqcSUjSDlIIpMAOYwigAKaopIEIxYCCAAVjgM0%2BAQYniSjABqMIwRaDu5AJw5jgAMII1AA%3D?_gl=1*jilf08*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.101642116.408218914.1700050452-2060274793.1694940376)

查看 [字符串模板](strings.md#字符串模板) 以获取详细信息。

## 条件表达式

```kotlin
fun maxOf(a: Int, b: Int): Int {
    if (a > b) {
        return a
    } else {
        return b
    }
}

fun main() {
    println("max of 0 and 42 is ${maxOf(0, 42)}")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-conditional-expressions"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIA6A7AZgVxQAgLYEMAPAeTQAp95sBJFAFwBpsAjK2ugSjfu2FWwHYAlmmwVsAPhYde%2FQfIBOMOhgU58cgQF9sMADYBnGLJzzBSlWpabsW1HZSpUmHASEoyMvqYEAHBe50eh5IIASE2BCiAAzY%2BChQ2AAsAEzCBtgAJMDhpGTRTKkcdiAc9iAMIHT4CgDmygAKevh0aBAKuAggAFb4AG74FeAQuL5CejAKAGqTBkIQKF0AjAB0AJwrKdEgWkA%3D%3D?_gl=1*jilf08*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.101642116.408218914.1700050452-2060274793.1694940376)

在 Kotlin 中，`if` 也可以作为一个表达式使用。

```kotlin
fun maxOf(a: Int, b: Int) = if (a > b) a else b

fun main() {
    println("max of 0 and 42 is ${maxOf(0, 42)}")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-if-expression"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIA6A7AZgVxQAgLYEMAPAeTQAp95sBJFAFwBpsAjK2ugSmwF5sBLNNgrYAfCy75sMADYBnGC1RL0WPPj4oyXYKmx7sABwBOGutM1IQBQtgiCADNnwoo2ACwAmfrOwASYNakZPZMnhwAvpYcqOEgDCB0%2BEYA5jB0AArS%2BHRoEEa4CCAAVvgAbvhx4BC4BnzSMEYAag2yfBAohQCMAHQAnN0e9iDhQA%3D%3D?_gl=1*jilf08*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.101642116.408218914.1700050452-2060274793.1694940376)

查看 [`if`-表达式](control-flow.md#if表达式)。

## for 循环

```kotlin
fun main() {
    val items = listOf("apple", "banana", "kiwifruit")
    for (item in items) {
        println(item)
    }
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-for-loop"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGAbjgDYZ4AuMWAzhgLwaN5OrAPJJC5EDgAOUxjAkAaDBIBGONOpyLlIANZ4A7niQAnFGwnFqtJBBMZCbDi0xOupCplrepJgq0YiNysvGgBfSkowkAUQVhwTAHMYVgAFRhxWWxMsBBAAKxwGGPAILCk8ORMANRgTTjwINDyARgA6AE42gCYABhAwoA%3D%3D%3D?_gl=1*f6bo6l*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.115404547.408218914.1700050452-2060274793.1694940376)

或

```kotlin
fun main() {
    val items = listOf("apple", "banana", "kiwifruit")
    for (index in items.indices) {
        println("item at $index is ${items[index]}")
    }
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-for-loop-indices"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGAbjgDYZ4AuMWAzhgLwaN5OrAPJJC5EDgAOUxjAkAaDBIBGONOpyLlIANZ4A7niQAnFGwnFqtJBBMZCBWAA8WmNh04A6J3jAxOUgpMWlCpEwJWRiIJDywMHFYMABInGFdBFOA4zgBtNOcAXQBfS2saUqo0YpAFEFYcEwBzGFYABUZE2xMsBBAAKxwGWvAILCk8ORMANRgTTjwIND6ARi8ATi8AJgAGEGKgA%3D?_gl=1*f6bo6l*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.115404547.408218914.1700050452-2060274793.1694940376)

查看 [for 循环](control-flow.md#for循环)。

## while 循环

```kotlin
fun main() {
    val items = listOf("apple", "banana", "kiwifruit")
    var index = 0
    while (index < items.size) {
        println("item at $index is ${items[index]}")
        index++
    }
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-while-loop"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGAbjgDYZ4AuMWAzhgLwaN5OrAPJJC5EDgAOUxjAkAaDBIBGONOpyLlIANZ4A7niQAnFGwnFqtBiZZpYAD14YADNZoGAFnjkZCBE4YADws7FwAdJx4AF4wpBSYtMlSJgSsjEQSbBwYOKwYACSBMM6CRcA5XADaJY4AugC%2Blh7J9k4A1B2tzVRojSAKIKw4JgDmMKwACoz5SBAmWAggAFY4DIPgEFhSvjAmAGr70RBoywCMEQCcEQBMriCNQA%3D?_gl=1*f6bo6l*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.115404547.408218914.1700050452-2060274793.1694940376)

请参阅 [while 循环](control-flow.md#while循环)。

## when 表达式

```kotlin
fun describe(obj: Any): String =
    when (obj) {
        1          -> "One"
        "Hello"    -> "Greeting"
        is Long    -> "Long"
        !is String -> "Not a string"
        else       -> "Unknown"
    }

fun main() {
    println(describe(1))
    println(describe("Hello"))
    println(describe(1000L))
    println(describe(2))
    println(describe("other"))
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-when-expression"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIA6A7AZgVxQAlgZzACcBLAIxgAoJSArebAQRQE8BKegZQBcSUBzbAF5U2UdgDuACxg4qtVtmAixKgIwqN2ALQA%2BbEhAB5FDAPLNBgBIwANjYgGVu%2FSADihGDC7F%2BZnJuxiPGwAGQh%2BJz0DMN9kf00AQiDsbl4BZwMAOQgubABDbDweHz4%2FAOxbPBgAjJAAVRQAaxQIcRQysQBfVB70LGwAWzyfCgUleOwAB14uGxQKfCIySlVWVnMpmbmFmAIScgorW3sDNY3pn1n5xf2VgAYHkLOJi5Qrnb3ligAmZ5VX943L4GHLSQindYoTogAA0IC4eUIfC8AAUbHkuGgIIQBggQDQ8gA3PKw8AQAaTYg2GCEABqNLwxHCeNUADoAJys753ECdIA%3D%3D?_gl=1*f6bo6l*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.115404547.408218914.1700050452-2060274793.1694940376)

请参阅 [when 表达式](control-flow.md#when表达式)。

## Ranges（区间）

使用 `in` 运算符检查数字是否在指定区间内。

```kotlin
fun main() {
    val x = 10
    val y = 9
    if (x in 1..y+1) {
        println("fits in range")
    }
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-range-in"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGAbjgDYYAeGAvBgIwAM1tDZgE8OGAJz8aeJBkJsC3AHSKhAai6kKmWjoAOAJwIAXRkXLI8RgM4YF%2BnGgDmMc8UkYAvpUoeQAGhAjHH1nIwAFRhwjJAh9LAQQACscBn9wCCxdPEYYfQA1XKs8CDQErkUxRQAmHhAPIA%3D%3D?_gl=1*mwj4zb*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.77466640.408218914.1700050452-2060274793.1694940376)

检查数字是否超出区间。

```kotlin
fun main() {
    val list = listOf("a", "b", "c")
    
    if (-1 !in 0..list.lastIndex) {
        println("-1 is out of range")
    }
    if (list.size !in list.indices) {
        println("list size is out of valid list indices range, too")
    }
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-out-of-range"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGAbjgDYaN4DOALhgLwvscB5JIXIgcogDQZRAI0nTwo4tVoqaeJBkIBaAIwYAhAQwAGAHRnWnSzk4BJNLAAepCploeADgCcCHRkSiehjsGBAoXBCa3jhoAOYwSmoYAL7JGlpWHGZseABeMIbGWWYEUHhgMGyuyV6%2BaP6BIFkYuQUhbGERYZoMrFB8nCGOFVUYMfEwUhwQEEnuNGlUaCkgEiAcON4JHAAKjDgcSBDeWAggAFY4DGvgEFieeIww3gBqL7kQaOe6ZgCcZgATCYQCkgA%3D?_gl=1*mwj4zb*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.77466640.408218914.1700050452-2060274793.1694940376)

迭代一个区间。

```kotlin
fun main() {
    for (x in 1..5) {
        print(x)
    }
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-iterate-range"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGSEAThoQB4YEYCMAdNwKykKmWiIAODAgBdWxarQC%2BlSvJAAaEJJwMA5jEkAFADY5J9BlgQgAVjgBuONeAhZReQzAYA1DwGc8ENEseAE5uACYABhB5IA%3D?_gl=1*mwj4zb*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.77466640.408218914.1700050452-2060274793.1694940376)

或者迭代一个数列。

```kotlin
fun main() {
    for (x in 1..10 step 2) {
        print(x)
    }
    println()
    for (x in 9 downTo 0 step 3) {
        print(x)
    }
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-iterate-progression"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGSEAThoQB4YEYCMAdN5wAwYAzgBcYABwwAmUhUy0F4hgRGti1WgF8NNJSoA2RdfJr0mrdpgCcGKBADuaACoQMg0RIwBmWToUY9NFUWYwVtKjRNEAAaEBEcBgBzGBEABX0cETMsBBAAKxwANxwY8AgscTx9GAYANRqhPAg0XJ4rbil%2BEE0gA%3D?_gl=1*mwj4zb*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.77466640.408218914.1700050452-2060274793.1694940376)

请参阅 [区间和数列](ranges.md)。

## 集合

迭代集合。

```kotlin
fun main() {
    val items = listOf("apple", "banana", "kiwifruit")
    for (item in items) {
        println(item)
    }
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-iterate-collection"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6mG1AbjgDYZ4AuMWAzhgLwb17vMA8kkLkQOAA4T6MMQBoMYgEY40qnPMUgA1ngDueJACcULMcUqVq1JBCMZCLNk0xOOpClWvWJRgs3oiNwsvDABfSzQwkDkQZhwjAHMYZgAFehxmWyMsBBAAKxw6GPAILAk8GSMANRgjdjwINDyARgA6AE42gCYABhAwoA%3D%3D%3D?_gl=1*mwj4zb*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.77466640.408218914.1700050452-2060274793.1694940376)

使用 `in` 运算符检查集合是否包含一个对象。

```kotlin
fun main() {
    val items = setOf("apple", "banana", "kiwifruit")
    when {
        "orange" in items -> println("juicy")
        "apple" in items -> println("apple is fine too")
    }
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-collection-in"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6mG1AbjgDYZ4AuMWAzhgLwbszMB5JIXIgcAB3H0YogDQZRAIxxoVOOQpABrPAHc8SAE4oWo4pUrVqugBYxMFKlauiIhlQHMZIJphZtOAFoAPgxxQwJmeiJRACsTMABPM0tnFzFJaVFfJlYODBCwiLQomIypGCZOJAJK5ggIFKcMAF8LNBaQWRBmHEMvZgAFehxmJDcsBBBYnDou8AgscTxpQwA1GEN2PAg0KYBGADoATkOAJgAGEBagA%3D?_gl=1*1jglexf*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.105874566.408218914.1700050452-2060274793.1694940376)

使用 lambda 表达式来过滤和映射集合：

```kotlin
fun main() {
    val fruits = listOf("banana", "avocado", "apple", "kiwifruit")
    fruits
      .filter { it.startsWith("a") }
      .sortedBy { it }
      .map { it.uppercase() }
      .forEach { println(it) }
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-collection-filter-map"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgLYEMCWaAFAJQbAA6alGNGAbjgDYZIBOKeALgM4YC8GRnm6cA8kkLkQAIxxo5OKQBoMUnHQhgcUCMtUgcAB0OMYeqQGs8AdzxsOnKcWq17Xbi9oYAdEjyNOGFYyDC5vERxWHgB1LgALSQMnDABfT1pwiCiYKAAhAE8QrlT0mm9cQyLObxRjIK1uGBISzC8fJCyAURwwOJDDVgJORiIuUjSqNBSQJRBOSIBzGE4ABUYcTg7WLAQQACt1HBnwCCxDfyCANSDuPAg0XYBGbwBObwAmAAYQFKA%3D%3D%3D?_gl=1*1jglexf*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.105874566.408218914.1700050452-2060274793.1694940376)

请参阅 [集合概述](collections-overview.md)。

## 可空值和空值检查

当可能存在 `null` 值时，引用必须显式标记为可空。可空类型的名称末尾带有 `?`。

如果 `str` 不包含整数，则返回 `null`：

```kotlin
fun parseInt(str: String): Int? {
    // ...
}
```

使用返回可空值的函数：

```kotlin
fun parseInt(str: String): Int? {
    return str.toIntOrNull()
}

fun printProduct(arg1: String, arg2: String) {
    val x = parseInt(arg1)
    val y = parseInt(arg2)

    // 使用 `x * y` 会导致错误，因为它们可能包含 null 值。
    if (x != null && y != null) {
        // 在空检查后，`x` 和 `y` 会自动转换为非空类型。
        println(x * y)
    }
    else {
        println("'$arg1' or '$arg2' is not a number")
    }    
}

fun main() {
    printProduct("6", "7")
    printProduct("a", "7")
    printProduct("a", "b")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-function-nullable-value"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgA4EMBOAzjAJJoAuAFAWXvBgMo0CWaA5gJR2lkD8GwAHUwYReGGRR5M1PADoyEbgHk8AORQAbDRXZCAvkMNpUmLHhZkACnmgowlfKwCMdRubYAaDI4BMr5mzs%2FEIiIgBuOBoYAB4YALzY%2BETcFI5OusLhkRgAnvGJhCTkqXisPhkhoQD0VRgAqgQsrBgABrEAVLktuUwwGlAEGDB4NngYAEYwYDgoRBhkABYweQC2OHkLEP0YaJoaBLKVIkxIGBSxAIQJu1oYAGR3uRhXO3tBgpmhIjUx3mhQT3wMG8KAUazITGmWjy02o8wgOwgaAAtDcNDhxhpgTgkGRhq9bmAlmAANZHL7YdxkDRoc4YTo5DJfAyfPpzD4U0JmCw0igCEAAcgAJGkBRgIGNhb4xUxBmgIGRvK8VpM8PymaE9F99EYhCYMGsWDpgp9ueRrLZ7HyQAA2flefkAdnV5LNVhsUDslH5OHtGCdLtNVItnqtPr9%2FPGgb0IA8IDIjnElnRZCQEpWCBAACscBFY%2BAICssEwsXgAGrDRpIzNOWQATlkPgADCA9EA%3D%3D%3D?_gl=1*1jglexf*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.105874566.408218914.1700050452-2060274793.1694940376)

或

```kotlin
fun parseInt(str: String): Int? {
    return str.toIntOrNull()
}

fun printProduct(arg1: String, arg2: String) {
    val x = parseInt(arg1)
    val y = parseInt(arg2)
    
    // ...
    if (x == null) {
        println("Wrong number format in arg1: '$arg1'")
        return
    }
    if (y == null) {
        println("Wrong number format in arg2: '$arg2'")
        return
    }

    // 在空检查后，`x` 和 `y` 会自动转换为非空类型。
    println(x * y)
}

fun main() {
    printProduct("6", "7")
    printProduct("a", "7")
    printProduct("99", "b")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-function-null-check"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIBmBXAdgAgA4EMBOAzjAJJoAuAFAWXvBgMo0CWaA5gJR2lkD8GwAHUwYReGGRR5M1PADoyEbgHk8AORQAbDRXZCAvkKGpMWPCzIAFPNBRhK%2BVgEY6jM2wA0GBwCYXzNuz8QiIiAG44GhgAHhgAvNj4RNwUDo66wmERGACecQmEJOQpeKze6SEYhhkYAPQ1GLKNwSFMSBgUMbHxaJoagYLVFabmGmgUAiAA6tZsGD0AtgBGMHgYSBB48zhkGCxeJc4YAOQAJKlHE%2BUVIWISUs0iBtWt7bldc739D9fD5KPjUxmrA%2BSxWaw2Wx2ex8dFOPguICu1wwt0kaG%2BT2%2BdWiXjQUBy%2BxgXhQCkhTDAEQ0uQp1AwCjmEDQAFoelocIsNEScEgyGDWZEwAALGBgADW31%2BZH%2BMQAVDl0voqsYMFsWDogtVJVYbHYAQA2CaeCYAdkuErclmsUFslAmOENlRApsR5vM2utuomAE4vQ6JoszWg9CB3CAyA5xBYNNt1psECAAFY4cIh8AQeZYJicvAANRWBCYjPjjlkXtk3gADCA9EA%3D%3D?_gl=1*19gmio9*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.83168019.408218914.1700050452-2060274793.1694940376)

请参阅 [空值安全](null-safety.md)。

## 类型检查和自动转换

`is` 运算符用于检查表达式是否是某种类型的实例。
如果对不可变局部变量或属性进行特定类型的检查，就无需显式进行转换：

```kotlin
fun getStringLength(obj: Any): Int? {
    if (obj is String) {
        // 在此分支中，`obj` 会自动转换为 `String`。
        return obj.length
    }

    // 在类型检查分支外部，`obj` 仍然是 `Any` 类型。
    return null
}

fun main() {
    fun printLength(obj: Any) {
        println("Getting the length of '$obj'. Result: ${getStringLength(obj) ?: "Error: The object is not a string"} ")
    }
    printLength("Incomprehensibilities")
    printLength(1000)
    printLength(listOf(Any()))
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-is-operator"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIA6A7AZgVxQAgOYwBcBlAgJwEsVcAZGKggCwAoIAjAK3mwEEUBPAJRcAkigIB%2BbMFTZZ2cmmwsO8gM7YSFKgKky5%2BgPQHsAAzbsTa7AEMMBCAFtrBcmGsAbd32xvVBbPammpS4lpQBDOTqrKTWKGAMevqypIQYpDjmAHTudLiMSdgAvqiFRqbmYep%2B5J7YEIoEfAAOMKa8fJYQdqrksPWNDG1NrQC0CTBgANYwUNgxcQmFqQTpOCgYnqglKKXoWNhOlEw60jhymDjNWgS09MzmXB2nhfrXlATuKExIIADihBcVAibVy9wG2AA5AAScyQrLYABKMFUmwIXGhwHwxDIITu%2BQeHB04i4vwAoqRSBBSFwACpDeocSb%2BKLYFAQfzWbB%2BLS4X5FbC%2FASFHZvG74xg%2FECiSAOa4wIYoXqsWrkFwooWFd5iCXMACMAAYjcLzrJtbc8pL3FECAB5NBMDonAQmoogAA0IAI1lI2IACu5nGhqQ4ECB2NYAG7WD3gRzNWowUgANSTvQgKDDeqyAE4sgAmA0gIpAA%3D%3D?_gl=1*19gmio9*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.83168019.408218914.1700050452-2060274793.1694940376)

或

```kotlin
fun getStringLength(obj: Any): Int? {
    if (obj !is String) return null

    // 在此分支中，`obj` 会自动转换为 `String`。
    return obj.length
}

fun main() {
    fun printLength(obj: Any) {
        println("Getting the length of '$obj'. Result: ${getStringLength(obj) ?: "Error: The object is not a string"} ")
    }
    printLength("Incomprehensibilities")
    printLength(1000)
    printLength(listOf(Any()))
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-is-operator-expression"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIA6A7AZgVxQAgOYwBcBlAgJwEsVcAZGKggCwAoIAjAK3mwEEUBPAJRcAkigIB%2BbMFTZZ2cmmwsO2AITkAzthIUqA7KUIZSOFBgA251DLkB6W9gAGbdo%2FlaAhhgIQAth4JyMA9LPmxgjQJsHycdSlw3SmiGTWxWUg8UMAYbWUMCYxwXADpzOlxGVABfaxRUTBx%2FSiZ9aRw5BuwAB10CWnpmFy5eQSlcuVkeygJzFCYkEABxQkCqZJhsMoHsCEUAcgASFz3i7AAlGA0LAi4D4HxiMnj%2BisGOfXEuBYBRUlIIUhcAAqDA2LhgYCiqRQECiHmwkV0uAWVWwCwE4xq7UmvRejHmIFEkF8PRgoJQGnIrHI5nIgUu6PGUzEeOYAEYAAxcjHY7q48r42mRADyaCYIxaAh5VRAABoQAQPKQHgAFcwBNAA3wIEDsDwANw8cvAfi6NJgpAAahbKRAUDq2cUAJzFABMHJAVSAA?_gl=1*19gmio9*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.83168019.408218914.1700050452-2060274793.1694940376)

甚至可以：

```kotlin
fun getStringLength(obj: Any): Int? {
    // 在 `&&` 的右侧，`obj` 会自动转换为 `String`。
    if (obj is String && obj.length > 0) {
        return obj.length
    }

    return null
}

fun main() {
    fun printLength(obj: Any) {
        println("Getting the length of '$obj'. Result: ${getStringLength(obj) ?: "Error: The object is not a string"} ")
    }
    printLength("Incomprehensibilities")
    printLength("")
    printLength(1000)
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-basic-syntax-is-operator-logic"}
[**打开训练场>>>**](https://play.kotlinlang.org/editor/v1/N4Igxg9gJgpiBcIA6A7AZgVxQAgOYwBcBlAgJwEsVcAZGKggCwAoIAjAK3mwEEUBPAJRcAkigIB%2BbMFTZZ2APTzsAAzbtl2cgGdsAQwwEIAW10FyYXQBtLfbBa0FshlSQpUNEHIxjYKuBgQAtAy6KFDYWuSw2BBoKgBk8coycuRxLByaOq6UuNiJMRwAdJZ0uIzYAHzYAAwCUilycqSEGKQ4aiVljI3YAL6ovS0EbTgoGNaoAyiD6FjYJpRM9dI4cpg4AA5uBLT0zGpcvIINa02y25QElihMSCAA4oRmVE4MPqX7MXEA5AAkah%2BRWwACUYFoJgQuH9gPhiGRcntygcOPVxFx7gBRUikCCkLgAFXehXYMDAjm02BQEEcugiCKo9z62HuAl60yalzESMYdxAokgRm2MHeKEirHIlnIZnBrN6XN23WY9zlZ2wCp5zAAjDVdWyUH0QAAaEAEXSkOEABUspjQeKMCBA7F0ADddMbwMZNpKYKQAGq%2ByKeR1aooATiKACYaiA%2BkA%3D%3D%3D?_gl=1*19gmio9*_ga*MjA2MDI3NDc5My4xNjk0OTQwMzc2*_ga_9J976DJZ68*MTcwMDEwMjYyNi4yOC4wLjE3MDAxMDI2MjYuNjAuMC4w&_ga=2.83168019.408218914.1700050452-2060274793.1694940376)

请参阅 [类](classes.md) 和 [类型转换](typecasts.md)。

