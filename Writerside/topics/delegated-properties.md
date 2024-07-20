[//]: # (title: 委托属性)

当我们频繁使用一些常见类型的属性时，虽然我们可以每次需要时手动实现它们，但更方便的做法是将它们实现一次，添加到库中，以后可以重复使用。
例如：

* **延迟初始化**属性：值仅在首次访问时计算。
* **可观察**属性：监听器会被通知该属性的更改。
* 将属性存储在 **映射** 中，而不是为每个属性单独的字段。

为了涵盖这些（和其他）情况，Kotlin 支持 **委托属性**：

```kotlin
class Example {
    var p: String by Delegate()
}
```

语法是：`val/var <property name>: <Type> by <expression>`。
`by` 后面的表达式是一个 **委托**，因为与属性对应的 `get()`（和 `set()`）将被委托给它的 `getValue()` 和 `setValue()` 方法。
属性委托不需要实现接口，但必须提供 `getValue()` 函数（对于 `var`，还需要 `setValue()`）。

例如：

```kotlin
import kotlin.reflect.KProperty

class Delegate {
    operator fun getValue(thisRef: Any?, property: KProperty<*>): String {
        return "$thisRef, thank you for delegating '${property.name}' to me!"
    }
 
    operator fun setValue(thisRef: Any?, property: KProperty<*>, value: String) {
        println("$value has been assigned to '${property.name}' in $thisRef.")
    }
}
```

当你读取委托给 `Delegate` 实例的 `p` 时，会调用 `Delegate` 的 `getValue()` 函数。
它的第一个参数是你读取 `p` 的对象，第二个参数包含 `p` 本身的描述（例如，你可以获取它的名称）。

```kotlin
val e = Example()
println(e.p)
```

这将打印：

```
Example@33a17727, thank you for delegating 'p' to me!
```

类似地，当你给 `p` 赋值时，会调用 `setValue()` 函数。
前两个参数是相同的，第三个参数是被赋的值：

```kotlin
e.p = "NEW"
```

这将打印：

```
NEW has been assigned to 'p' in Example@33a17727.
```

关于委托对象的要求规范可以在[下面](#property-delegate-requirements)找到。

你可以在函数或代码块中声明一个委托属性；它不必是类的成员。
你可以在[此处](#local-delegated-properties)找到一个示例。

## 标准委托

Kotlin 标准库提供了几个有用的委托的工厂方法。

### 延迟初始化属性

[`lazy()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/lazy.html)
是一个函数，它接受一个 lambda 并返回一个 `Lazy<T>` 实例，可以作为实现延迟初始化属性的委托。
第一次调用 `get()` 会执行传递给 `lazy()` 的 lambda 并记住结果。
随后的 `get()` 调用只会返回记住的结果。

```kotlin
val lazyValue: String by lazy {
    println("computed!")
    "Hello"
}

fun main() {
    println(lazyValue)
    println(lazyValue)
}
```
{kotlin-runnable="true"}

默认情况下，延迟属性的计算是 **同步** 的：值只会在一个线程中计算，但所有线程都会看到相同的值。
如果不需要对初始化委托进行同步以允许多个线程同时执行它，可以将 `LazyThreadSafetyMode.PUBLICATION` 作为参数传递给 `lazy()`。

如果你确定初始化总是在使用属性的同一线程中发生，可以使用 `LazyThreadSafetyMode.NONE`。它不提供任何线程安全保证和相关开销。

### 可观察属性

[`Delegates.observable()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.properties/-delegates/observable.html) 接受两个参数：初始值和修改处理程序。

每次对属性进行赋值（*在*赋值执行之后）时，都会调用处理程序。它有三个参数：被赋值的属性、旧值和新值：

```kotlin
import kotlin.properties.Delegates

class User {
    var name: String by Delegates.observable("<no name>") {
        prop, old, new ->
        println("$old -> $new")
    }
}

fun main() {
    val user = User()
    user.name = "first"
    user.name = "second"
}
```
{kotlin-runnable="true"}

如果你想拦截赋值并进行 *否决*，请使用 [`vetoable()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.properties/-delegates/vetoable.html) 而不是 `observable()`。
传递给 `vetoable` 的处理程序将在对新属性值进行赋值之前被调用。

## 委托给另一个属性

属性可以将其 getter 和 setter 委托给另一个属性。此类委托适用于顶层属性和类属性（成员属性和扩展属性）。委托属性可以是：
* 顶层属性
* 同一类的成员或扩展属性
* 另一个类的成员或扩展属性

要将属性委托给另一个属性，请在委托名称中使用 `::` 限定符，例如 `this::delegate` 或 `MyClass::delegate`。

```kotlin
var topLevelInt: Int = 0
class ClassWithDelegate(val anotherClassInt: Int)

class MyClass(var memberInt: Int, val anotherClassInstance: ClassWithDelegate) {
    var delegatedToMember: Int by this::memberInt
    var delegatedToTopLevel: Int by ::topLevelInt
    
    val delegatedToAnotherClass: Int by anotherClassInstance::anotherClassInt
}
var MyClass.extDelegated: Int by ::topLevelInt
```

这在某些情况下可能很有用，例如当你想以向后兼容的方式重命名属性时：引入一个新属性，用 `@Deprecated` 注解旧属性，并委托其实现。

```kotlin
class MyClass {
   var newName: Int = 0
   @Deprecated("Use 'newName' instead", ReplaceWith("newName"))
   var oldName: Int by this::newName
}
fun main() {
   val myClass = MyClass()
   // Notification: 'oldName: Int' is deprecated.
   // Use 'newName' instead
   myClass.oldName = 42
   println(myClass.newName) // 42
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.4"}

## 将属性存储在映射中

一个常见的用例是将属性值存储在映射中。
这在应用程序中经常出现，比如解析 JSON 或执行其他动态任务时。
在这种情况下，你可以使用映射实例本身作为委托属性的委托者。

```kotlin
class User(val map: Map<String, Any?>) {
    val name: String by map
    val age: Int     by map
}
```

在这个例子中，构造函数接受一个映射：

```kotlin
val user = User(mapOf(
    "name" to "John Doe",
    "age"  to 25
))
```

委托属性通过字符串键从该映射获取值，这些键与属性的名称关联：

```kotlin
class User(val map: Map<String, Any?>) {
    val name: String by map
    val age: Int     by map
}

fun main() {
    val user = User(mapOf(
        "name" to "John Doe",
        "age"  to 25
    ))
//sampleStart
    println(user.name) // 输出 "John Doe"
    println(user.age)  // 输出 25
//sampleEnd
}
```
{kotlin-runnable="true"}

如果你使用可变的 `MutableMap` 而不是只读的 `Map`，这对于 `var` 属性也是适用的：

```kotlin
class MutableUser(val map: MutableMap<String, Any?>) {
    var name: String by map
    var age: Int     by map
}
```

## 局部委托属性 {id=local-delegated-properties}

你可以将局部变量声明为委托属性。
例如，你可以将局部变量声明为延迟初始化：

```kotlin
fun example(computeFoo: () -> Foo) {
    val memoizedFoo by lazy(computeFoo)

    if (someCondition && memoizedFoo.isValid()) {
        memoizedFoo.doSomething()
    }
}
```

`memoizedFoo` 变量只会在首次访问时计算。
如果 `someCondition` 失败，则根本不会计算该变量。

## 属性委托要求 {id=property-delegate-requirements}

对于*只读*属性（`val`），委托应该提供一个带有以下参数的操作符函数 `getValue()`：

* `thisRef` 必须与*属性所有者*相同类型或是其超类型（对于扩展属性，它应该是被扩展的类型）。
* `property` 必须是 `KProperty<*>` 类型或其超类型。

`getValue()` 必须返回与属性相同的类型（或其子类型）。

```kotlin
class Resource

class Owner {
    val valResource: Resource by ResourceDelegate()
}

class ResourceDelegate {
    operator fun getValue(thisRef: Owner, property: KProperty<*>): Resource {
        return Resource()
    }
}
```

对于*可变*属性（`var`），委托还必须提供一个带有以下参数的操作符函数 `setValue()`：

* `thisRef` 必须与*属性所有者*相同类型或是其超类型（对于扩展属性，它应该是被扩展的类型）。
* `property` 必须是 `KProperty<*>` 类型或其超类型。
* `value` 必须与属性相同的类型（或其超类型）。

```kotlin
class Resource

class Owner {
    var varResource: Resource by ResourceDelegate()
}

class ResourceDelegate(private var resource: Resource = Resource()) {
    operator fun getValue(thisRef: Owner, property: KProperty<*>): Resource {
        return resource
    }
    operator fun setValue(thisRef: Owner, property: KProperty<*>, value: Any?) {
        if (value is Resource) {
            resource = value
        }
    }
}
```

`getValue()` 和/或 `setValue()` 函数可以作为委托类的成员函数或扩展函数提供。
当你需要将属性委托给原始不提供这些函数的对象时，后者很方便。
这两个函数都需要用 `operator` 关键字标记。

你可以通过使用 Kotlin 标准库中的 `ReadOnlyProperty` 和 `ReadWriteProperty` 接口，将委托创建为匿名对象，而无需创建新类。
它们提供了所需的方法：`ReadOnlyProperty` 中声明了 `getValue()`；`ReadWriteProperty` 扩展了它并添加了 `setValue()`。
这意味着可以在期望 `ReadOnlyProperty` 的地方传递 `ReadWriteProperty`。

```kotlin
fun resourceDelegate(resource: Resource = Resource()): ReadWriteProperty<Any?, Resource> =
    object : ReadWriteProperty<Any?, Resource> {
        var curValue = resource 
        override fun getValue(thisRef: Any?, property: KProperty<*>): Resource = curValue
        override fun setValue(thisRef: Any?, property: KProperty<*>, value: Resource) {
            curValue = value
        }
    }

val readOnlyResource: Resource by resourceDelegate()  // ReadWriteProperty as val
var readWriteResource: Resource by resourceDelegate()
```

## 委托属性的翻译规则 {id=translation-rules-for-delegated-properties}

在底层，Kotlin编译器为某些类型的委托属性生成辅助属性，然后委托给它们。

> 为了优化目的，编译器[_在几种情况下不生成辅助属性_](#optimized-cases-for-delegated-properties)。
> 通过[委托给另一个属性的示例](#translation-rules-when-delegating-to-another-property)来了解这种优化。
>
{style="note"}

例如，对于属性`prop`，它会生成隐藏属性`prop$delegate`，并且访问器的代码简单地委托给这个额外的属性：

```kotlin
class C {
    var prop: Type by MyDelegate()
}

// 编译器生成的代码如下：
class C {
    private val prop$delegate = MyDelegate()
    var prop: Type
        get() = prop$delegate.getValue(this, this::prop)
        set(value: Type) = prop$delegate.setValue(this, this::prop, value)
}
```

Kotlin编译器在参数中提供了关于`prop`的所有必要信息：第一个参数`this`是指外部类`C`的实例，而`this::prop`是描述`prop`本身的`KProperty`类型的反射对象。

### 委托属性的优化情况 {id=optimized-cases-for-delegated-properties}

如果委托是以下情况之一，`$delegate`字段将被省略：

* 引用属性：

  ```kotlin
  class C<Type> {
      private var impl: Type = ...
      var prop: Type by ::impl
  }
  ```

* 命名对象：

  ```kotlin
  object NamedObject {
      operator fun getValue(thisRef: Any?, property: KProperty<*>): String = ...
  }

  val s: String by NamedObject
  ```

* 具有相同模块中的备份字段和默认getter的最终`val`属性：

  ```kotlin
  val impl: ReadOnlyProperty<Any?, String> = ...

  class A {
      val s: String by impl
  }
  ```

* 常量表达式、枚举项、`this`、`null`。`this`的示例：

  ```kotlin
  class A {
      operator fun getValue(thisRef: Any?, property: KProperty<*>) ...

      val s by this
  }
  ```

### 委托给另一个属性时的翻译规则 {id=translation-rules-when-delegating-to-another-property}

当委托给另一个属性时，Kotlin编译器会立即访问所引用的属性。
这意味着编译器不会生成字段`prop$delegate`。这种优化有助于节省内存。

例如，考虑以下代码：

```kotlin
class C<Type> {
    private var impl: Type = ...
    var prop: Type by ::impl
}
```

`prop`变量的属性访问器直接调用`impl`变量，跳过了委托属性的`getValue`和`setValue`操作符，因此不需要`KProperty`引用对象。

对于上述代码，编译器生成以下代码：

```kotlin
class C<Type> {
    private var impl: Type = ...

    var prop: Type
        get() = impl
        set(value) {
            impl = value
        }
    
    fun getProp$delegate(): Type = impl // 这个方法只对反射有用
}
```

## 提供委托

通过定义`provideDelegate`操作符，你可以扩展创建属性实现委托的逻辑。
如果右侧使用的对象定义了`provideDelegate`作为成员或扩展函数，那么该函数将被调用来创建属性委托实例。

`provideDelegate`的一个可能的用例是在属性初始化时检查其一致性。

例如，要在绑定之前检查属性名称，你可以这样写：

```kotlin
class ResourceDelegate<T> : ReadOnlyProperty<MyUI, T> {
    override fun getValue(thisRef: MyUI, property: KProperty<*>): T { ... }
}
    
class ResourceLoader<T>(id: ResourceID<T>) {
    operator fun provideDelegate(
            thisRef: MyUI,
            prop: KProperty<*>
    ): ReadOnlyProperty<MyUI, T> {
        checkProperty(thisRef, prop.name)
        // 创建委托
        return ResourceDelegate()
    }

    private fun checkProperty(thisRef: MyUI, name: String) { ... }
}

class MyUI {
    fun <T> bindResource(id: ResourceID<T>): ResourceLoader<T> { ... }

    val image by bindResource(ResourceID.image_id)
    val text by bindResource(ResourceID.text_id)
}
```

`provideDelegate`的参数与`getValue`相同：

* `thisRef`必须与属性的所有者类型相同或是其超类型（对于扩展属性，它应该是被扩展的类型）；
* `property`必须是`KProperty<*>`类型或其超类型。

在创建`MyUI`实例期间，`provideDelegate`方法将为每个属性调用，并立即执行必要的验证。

如果没有拦截属性和其委托之间绑定的能力，要实现相同的功能，你将不得不显式传递属性名称，这不是很方便：

```kotlin
// 没有"provideDelegate"功能时检查属性名称
class MyUI {
    val image by bindResource(ResourceID.image_id, "image")
    val text by bindResource(ResourceID.text_id, "text")
}

fun <T> MyUI.bindResource(
        id: ResourceID<T>,
        propertyName: String
): ReadOnlyProperty<MyUI, T> {
    checkProperty(this, propertyName)
    // 创建委托
}
```

在生成的代码中，将调用`provideDelegate`方法来初始化辅助的`prop$delegate`属性。
比较属性声明`val prop: Type by MyDelegate()`的生成代码和上面（当`provideDelegate`方法不存在时）生成的代码：

```kotlin
class C {
    var prop: Type by MyDelegate()
}

// 编译器生成的代码 
// 当'provideDelegate'函数可用时：
class C {
    // 调用 "provideDelegate" 来创建额外的 "delegate" 属性
    private val prop$delegate = MyDelegate().provideDelegate(this, this::prop)
    var prop: Type
        get() = prop$delegate.getValue(this, this::prop)
        set(value: Type) = prop$delegate.setValue(this, this::prop, value)
}
```

请注意，`provideDelegate`方法仅影响辅助属性的创建，不影响生成的getter或setter的代码。

使用标准库中的`PropertyDelegateProvider`接口，你可以创建委托提供者而不创建新类。

```kotlin
val provider = PropertyDelegateProvider { thisRef: Any?, property ->
    ReadOnlyProperty<Any?, Int> {_, property -> 42 }
}
val delegate: Int by provider
```

