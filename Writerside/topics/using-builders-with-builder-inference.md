[//]: # (title: 使用带有构建器类型推断的构建器)

Kotlin 支持**构建器类型推断**（或构建器推断），这在使用泛型构建器时非常有用。它帮助编译器根据其 lambda 参数内部其他调用的类型信息来推断构建器调用的类型实参。

考虑这个 [`buildMap()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/build-map.html) 使用示例：

```kotlin
fun addEntryToMap(baseMap: Map<String, Number>, additionalEntry: Pair<String, Int>?) {
   val myMap = buildMap {
       putAll(baseMap)
       if (additionalEntry != null) {
           put(additionalEntry.first, additionalEntry.second)
       }
   }
}
```

这里没有足够的类型信息来以常规方式推断类型实参，但构建器推断可以分析 lambda 参数内部的调用。
基于 `putAll()` 和 `put()` 调用的类型信息，编译器可以自动将 `buildMap()` 调用的类型实参推断为 `String` 和 `Number`。
构建器推断允许在使用泛型构建器时省略类型实参。

## 编写你自己的构建器 {id=writing-your-own-builders}

### 启用构建器推断的要求 {id=requirements-for-enabling-builder-inference}

> 在 Kotlin 1.7.0 之前，启用构建器函数的构建器推断需要 `-Xenable-builder-inference` 编译器选项。
> 在 1.7.0 中，该选项默认启用。
>
{style="note"}

为了确保构建器能够在你自己的构建器中正常工作，请确保构建器的 lambda 参数声明了接收者的函数类型。
接收者类型还有两个要求：

1. 它应该使用构建器推断要推断的类型实参。例如：
   ```kotlin
   fun <V> buildList(builder: MutableList<V>.() -> Unit) { ... }
   ```

   > 请注意，直接传递类型参数的类型，如 `fun <T> myBuilder(builder: T.() -> Unit)` 目前尚不支持。
   >
   {style="note"}

2. 它应该提供包含相应类型参数的签名的公共成员或扩展。例如：
   ```kotlin
   class ItemHolder<T> {
       private val items = mutableListOf<T>()

       fun addItem(x: T) {
           items.add(x)
       }

       fun getLastItem(): T? = items.lastOrNull()
   }
   
   fun <T> ItemHolder<T>.addAllItems(xs: List<T>) {
       xs.forEach { addItem(it) }
   }

   fun <T> itemHolderBuilder(builder: ItemHolder<T>.() -> Unit): ItemHolder<T> = 
       ItemHolder<T>().apply(builder)

   fun test(s: String) {
       val itemHolder1 = itemHolderBuilder { // itemHolder1 的类型是 ItemHolder<String>
           addItem(s)
       }
       val itemHolder2 = itemHolderBuilder { // itemHolder2 的类型是 ItemHolder<String>
           addAllItems(listOf(s)) 
       }
       val itemHolder3 = itemHolderBuilder { // itemHolder3 的类型是 ItemHolder<String?>
           val lastItem: String? = getLastItem()
           // ...
       }
   }
   ```

### 支持的功能 {id=supported-features}

构建器推断支持以下功能：

* 推断多个类型实参
  ```kotlin
  fun <K, V> myBuilder(builder: MutableMap<K, V>.() -> Unit): Map<K, V> { ... }
  ```
* 在一次调用中推断多个构建器 lambda 表达式的类型参数，包括它们之间相互依赖的情况
  ```kotlin
  fun <K, V> myBuilder(
      listBuilder: MutableList<V>.() -> Unit,
      mapBuilder: MutableMap<K, V>.() -> Unit
  ): Pair<List<V>, Map<K, V>> =
      mutableListOf<V>().apply(listBuilder) to mutableMapOf<K, V>().apply(mapBuilder)
  
  fun main() {
      val result = myBuilder(
          { add(1) },
          { put("key", 2) }
      )
      // result 的类型是 Pair<List<Int>, Map<String, Int>>
  }
  ```
* 推断类型实参，其类型参数是 lambda 的参数或返回类型
  ```kotlin
  fun <K, V> myBuilder1(
      mapBuilder: MutableMap<K, V>.() -> K
  ): Map<K, V> = mutableMapOf<K, V>().apply { mapBuilder() }
  
  fun <K, V> myBuilder2(
      mapBuilder: MutableMap<K, V>.(K) -> Unit
  ): Map<K, V> = mutableMapOf<K, V>().apply { mapBuilder(2 as K) }
  
  fun main() {
      // result1 的类型被推断为 Map<Long, String>
      val result1 = myBuilder1 {
          put(1L, "value")
          2
      }
      val result2 = myBuilder2 {
          put(1, "value 1")
          // 你可以将 `it` 用作“推迟类型变量”类型
          // 详情见下节
          put(it, "value 2")
      }
  }
  ```

## 构建器推断如何工作 {id=how-builder-inference-works}

### 推迟类型变量 {id=postponed-type-variables}

构建器推断通过**推迟类型变量**来工作，这些变量在构建器推断分析期间，出现在构建器 lambda 内部。
推迟类型变量是一种类型实参的类型，在推断过程中被使用。编译器使用它来收集关于类型实参的类型信息。

考虑 [`buildList()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/build-list.html) 的这个示例：

```kotlin
val result = buildList {
    val x = get(0)
}
```

这里 `x` 的类型是推迟类型变量：`get()` 调用返回一个类型为 `E` 的值，但 `E` 本身尚未确定。此时，`E` 的具体类型是未知的。

构造器推断会在延迟类型变量的值与具体类型关联时，收集这些信息。
最终，在构造器推断分析结束时，利用收集到的信息推断出相应类型实参的结果类型。
例如：

```kotlin
val result = buildList {
    val x = get(0)
    val y: String = x
} // result 的类型被推断为 List<String>
```

当延迟类型变量被赋值给 `String` 类型的变量后，构造器推断会得出 `x` 是 `String` 的子类型。
这条赋值语句是构造器 lambda 中的最后一条语句，因此，构造器推断分析会结束，并将类型实参 `E` 推断为 `String`。

请注意，您始终可以调用 `equals()`、`hashCode()` 和 `toString()` 函数，并将推迟类型变量作为接收者。

### 对构建器推断结果的贡献 {id=contributing-to-builder-inference-results}

构建器推断可以收集不同类型的信息，这些信息都会对结果的分析产生贡献。它考虑以下几种情况：

* 调用 lambda 接收者的方法时使用了类型参数的类型
  ```kotlin
  val result = buildList {
      // 类型实参根据传递的 "value" 参数被推断为 String
      add("value")
  } // result 的类型被推断为 List<String>
  ```
* 为返回类型参数类型的调用指定预期类型
  ```kotlin
  val result = buildList {
      // 类型实参数根据预期类型被推断为 Float
      val x: Float = get(0)
  } // result 的类型是 List<Float>
  ```
  ```kotlin
  class Foo<T> {
      val items = mutableListOf<T>()
  }

  fun <K> myBuilder(builder: Foo<K>.() -> Unit): Foo<K> = Foo<K>().apply(builder)

  fun main() {
      val result = myBuilder {
          val x: List<CharSequence> = items
          // ...
      } // result 的类型是 Foo<CharSequence>
  }
  ```
* 将推迟类型变量的类型传递给期望具体类型的方法
  ```kotlin
  fun takeMyLong(x: Long) { ... }

  fun String.isMoreThat3() = length > 3

  fun takeListOfStrings(x: List<String>) { ... }

  fun main() {
      val result1 = buildList {
          val x = get(0)
          takeMyLong(x)
      } // result1 的类型被推断为 List<Long>

      val result2 = buildList {
          val x = get(0)
          val isLong = x.isMoreThat3()
          // ...
      } // result2 的类型被推断为 List<String>
  
      val result3 = buildList {
          takeListOfStrings(this)
      } // result3 的类型被推断为 List<String>
  }
  ```
* 获取 lambda 接收者成员的可调用引用
  ```kotlin
  fun main() {
      val result = buildList {
          val x: KFunction1<Int, Float> = ::get
      } // result 的类型被推断为 List<Float>
  }
  ```
  ```kotlin
  fun takeFunction(x: KFunction1<Int, Float>) { ... }

  fun main() {
      val result = buildList {
          takeFunction(::get)
      } // result 的类型被推断为 List<Float>
  }
  ```

分析结束时，构建器推断会考虑所有收集到的类型信息，并尝试将其合并为最终的类型。参见以下示例。

```kotlin
val result = buildList { // 推断推迟的类型变量 E
    // 认为 E 是 Number 或其子类型
    val n: Number? = getOrNull(0)
    // 认为 E 是 Int 或其超类型
    add(1)
    // E 被推断为 Int
} // result 的类型为 List<Int>
```

最终的类型是分析过程中收集到的类型信息所对应的最具体的类型。如果给定的类型信息存在矛盾且无法合并，编译器会报错。

请注意，只有在常规类型推断无法推断出类型实参时，Kotlin 编译器才会使用构建器推断。
这意味着你可以在构建器 lambda 外部提供类型信息，这样就不需要进行构建器推断分析。
参见以下示例：

```kotlin
fun someMap() = mutableMapOf<CharSequence, String>()

fun <E> MutableMap<E, String>.f(x: MutableMap<E, String>) { ... }

fun main() {
    val x: Map<in String, String> = buildMap {
        put("", "")
        f(someMap()) // 类型不匹配（要求 String，找到 CharSequence）
    }
}
```

这里出现类型不匹配的原因是 map 的预期类型是在构建器 lambda 外部指定的。
编译器分析 lambda 内的所有语句，并将接收者类型固定为 `Map<in String, String>`。