[//]: # (title: 包和导入)

一个源文件通常可能是以包声明开始：

```kotlin
package org.example

fun printMessage() { /*...*/ }
class Message { /*...*/ }

// ...
```

源文件的所有内容，如类和函数，都包含在这个包中。
因此，在上面的示例中，`printMessage()` 的全名是 `org.example.printMessage`，
而 `Message` 的全名是 `org.example.Message`。

如果没有指定包，这种文件的内容将属于没有名称的 **默认** 包。

## 默认导入

每个 Kotlin 文件默认导入了一些包：

- [kotlin.*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/index.html)
- [kotlin.annotation.*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.annotation/index.html)
- [kotlin.collections.*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/index.html)
- [kotlin.comparisons.*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.comparisons/index.html)
- [kotlin.io.*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.io/index.html)
- [kotlin.ranges.*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/index.html)
- [kotlin.sequences.*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.sequences/index.html)
- [kotlin.text.*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/index.html)

根据目标平台，还会导入一些其他的包：

- JVM:
  - java.lang.*
  - [kotlin.jvm.*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/index.html)

- JS:
  - [kotlin.js.*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.js/index.html)

## 导入 {id=导入}

除了默认导入外，每个文件都可以包含自己的 `import` 指令。

你可以导入单个名称：

```kotlin
import org.example.Message // 现在可以直接访问 Message，无需限定符
```

或者导入一个作用域范围内的所有可访问内容：包、类、对象等：

```kotlin
import org.example.* // 'org.example' 中的所有内容都变得可访问
```

如果存在名称冲突，可以使用 `as` 关键字进行消歧义，将冲突的实体在本地重命名：

```kotlin
import org.example.Message // Message 可以访问
import org.test.Message as TestMessage // TestMessage 代表 'org.test.Message'
```

`import` 关键字不仅限于导入类，还可以用于导入其他声明：

* 顶层函数和属性
* 在 [对象声明](object-declarations.md#object-declarations-overview) 中声明的函数和属性
* [枚举常量](enum-classes.md)

## 顶层声明的可见性

如果顶层声明标记为 `private`，它对声明所在的文件是私有的（参见[可见性修饰符](visibility-modifiers.md)）。
