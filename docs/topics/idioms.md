[//]: # (title: 惯用语法)

这是一个包含 Kotlin 中随机和常用语法习惯的集合。如果你有自己喜欢的语法习惯和风格，请通过发送 pull request 贡献给我们~

## 创建 DTOs (POJOs/POCOs)

```kotlin
data class Customer(val name: String, val email: String)
```

提供一个具有以下功能的 `Customer` 类：

* 所有属性的 getters（对于 `var`，还包括 setters）
* `equals()`
* `hashCode()`
* `toString()`
* `copy()`
* `component1()`、`component2()` 等，对应于所有属性（参见[数据类](data-classes.md)）

## 函数参数的默认值

```kotlin
fun foo(a: Int = 0, b: String = "") { ... }
```

## 过滤列表

```kotlin
val positives = list.filter { x -> x > 0 }
```

或者，甚至更短：

```kotlin
val positives = list.filter { it > 0 }
```

了解 [Java 和 Kotlin 过滤](java-to-kotlin-collections-guide.md#filter-elements) 之间的区别。

## 检查集合中元素是否存在

```kotlin
if ("john@example.com" in emailsList) { ... }

if ("jane@example.com" !in emailsList) { ... }
```

## 字符串插值

```kotlin
println("Name $name")
```

了解 [Java 和 Kotlin 字符串连接](java-to-kotlin-idioms-strings.md#concatenate-strings) 之间的区别。

## 实例检查

```kotlin
when (x) {
    is Foo -> ...
    is Bar -> ...
    else   -> ...
}
```

## 只读列表

```kotlin
val list = listOf("a", "b", "c")
```
## 只读映射

```kotlin
val map = mapOf("a" to 1, "b" to 2, "c" to 3)
```

## 访问映射条目

```kotlin
println(map["key"])
map["key"] = value
```

## 遍历映射或键值对列表。

```kotlin
for ((k, v) in map) {
    println("$k -> $v")
}
```

`k` 和 `v` 可以是任何方便的名称，比如 `name` 和 `age`。

## 迭代一个区间

```kotlin
for (i in 1..100) { ... }  // 封闭区间：包括 100
for (i in 1..<100) { ... } // 开放区间：不包括 100
for (x in 2..10 step 2) { ... }
for (x in 10 downTo 1) { ... }
(1..10).forEach { ... }
```

## 延迟属性

```kotlin
val p: String by lazy { // 该值仅在第一次访问时计算
    // 计算字符串
}
```

## 扩展功能

```kotlin
fun String.spaceToCamelCase() { ... }

"Convert this to camelcase".spaceToCamelCase()
```

## 创建单例

```kotlin
object Resource {
    val name = "Name"
}
```

## 使用内联值类实现类型安全的值 {id=use-inline-value-classes-for-type-safe-values}

```kotlin
@JvmInline
value class EmployeeId(private val id: String)

@JvmInline
value class CustomerId(private val id: String)
```

如果你不小心混淆了 `EmployeeId` 和 `CustomerId`，将会触发编译错误。

> `@JvmInline` 注解仅用于 JVM 后端。
>
{style="note"}

## 实例化抽象类 {id=instantiate-an-abstract-class}

```kotlin
abstract class MyAbstractClass {
    abstract fun doSomething()
    abstract fun sleep()
}

fun main() {
    val myObject = object : MyAbstractClass() {
        override fun doSomething() {
            // ...
        }

        override fun sleep() { // ...
        }
    }
    myObject.doSomething()
}
```

## If-not-null （非空）简写 {id=if-not-null简写}

```kotlin
val files = File("Test").listFiles()

println(files?.size) // 如果文件不为空，则打印大小
```

## If-not-null-else （非空否则）简写

```kotlin
val files = File("Test").listFiles()

// 对于简单的备用值：
println(files?.size ?: "empty") // 如果 `files` 为 null，则打印 "empty"。

// 要在代码块中计算更复杂的备用值，请使用 `run`。
val filesSize = files?.size ?: run { 
    val someSize = getSomeSize()
    someSize * 2
}
println(filesSize)
```

## If-null（空值）执行语句

```kotlin
val values = ...
val email = values["email"] ?: throw IllegalStateException("Email is missing!")
```

## 获取可能为空的集合的第一个元素

```kotlin
val emails = ... // 可能是空的集合
val mainEmail = emails.firstOrNull() ?: ""
```

了解 [Java 和 Kotlin 第一个元素获取](java-to-kotlin-collections-guide.md#获取可能为空的集合中的第一项和最后一项) 之间的区别。

## If-not-null（非空）执行语句 {id=非空执行语句}

```kotlin
val value = ...

value?.let {
    ... // 如果不为空则执行此块
}
```

## 映射可空值 If-not-null（非空）转换 {id=映射可空值If-not-null转换}

```kotlin
val value = ...

val mapped = value?.let { transformValue(it) } ?: defaultValue 
// 如果值或转换结果为 null，则返回 defaultValue。
```

## 返回 when 表达式

```kotlin
fun transform(color: String): Int {
    return when (color) {
        "Red" -> 0
        "Green" -> 1
        "Blue" -> 2
        else -> throw IllegalArgumentException("Invalid color param value")
    }
}
```

## try-catch 表达式

```kotlin
fun test() {
    val result = try {
        count()
    } catch (e: ArithmeticException) {
        throw IllegalStateException(e)
    }

    // 处理结果
}
```

## if 表达式

```kotlin
val y = if (x == 1) {
    "one"
} else if (x == 2) {
    "two"
} else {
    "other"
}
```

## 返回 Unit 类型的方法的构建器风格用法

```kotlin
fun arrayOfMinusOnes(size: Int): IntArray {
    return IntArray(size).apply { fill(-1) }
}
```

## 单表达式函数

```kotlin
fun theAnswer() = 42
```

这相当于

```kotlin
fun theAnswer(): Int {
    return 42
}
```

它可以与其它惯用语法有效地结合，从而使代码更简短。例如，使用 `when` 表达式：

```kotlin
fun transform(color: String): Int = when (color) {
    "Red" -> 0
    "Green" -> 1
    "Blue" -> 2
    else -> throw IllegalArgumentException("Invalid color param value")
}
```

## 在一个对象实例上调用多个方法（with）

```kotlin
class Turtle {
    fun penDown()
    fun penUp()
    fun turn(degrees: Double)
    fun forward(pixels: Double)
}

val myTurtle = Turtle()
with(myTurtle) { // 画一个100像素的正方形
    penDown()
    for (i in 1..4) {
        forward(100.0)
        turn(90.0)
    }
    penUp()
}
```

## 配置对象的属性（apply）

```kotlin
val myRectangle = Rectangle().apply {
    length = 4
    breadth = 5
    color = 0xFAFAFA
}
```

这用于配置 在对象构造函数中不存在的属性 非常有用。

## Java 7 的 try-with-resources

```kotlin
val stream = Files.newInputStream(Paths.get("/some/file.txt"))
stream.buffered().reader().use { reader ->
    println(reader.readText())
}
```

## 需要泛型类型信息的泛型函数

```kotlin
//  public final class Gson {
//     ...
//     public <T> T fromJson(JsonElement json, Class<T> classOfT) throws JsonSyntaxException {
//     ...

inline fun <reified T: Any> Gson.fromJson(json: JsonElement): T = this.fromJson(json, T::class.java)
```

## 交换两个变量

```kotlin
var a = 1
var b = 2
a = b.also { b = a }
```

## 将代码标记为不完整 (TODO)

Kotlin 的标准库有一个 `TODO()` 函数，它总是抛出一个 `NotImplementedError`。
其返回类型是 `Nothing`，因此可以在期望类型不明确的情况下使用。
还有一个接受原因参数的重载：

```kotlin
fun calcTaxes(): BigDecimal = TODO("Waiting for feedback from accounting")
```

IntelliJ IDEA 的 Kotlin 插件理解 `TODO()` 的语义，并自动在 TODO 工具窗口中添加代码指示。

## 下一步是什么？

* 用符合惯用 Kotlin 风格的方式解决 [Advent of Code 谜题](advent-of-code.md)。
* 学习如何[在 Java 和 Kotlin 中执行字符串的典型任务](java-to-kotlin-idioms-strings.md)。
* 学习如何[在 Java 和 Kotlin 中执行集合的典型任务](java-to-kotlin-collections-guide.md)。
* 学习如何[在 Java 和 Kotlin 中处理可空性](java-to-kotlin-nullability-guide.md)。
