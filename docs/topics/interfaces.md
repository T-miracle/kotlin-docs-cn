[//]: # (title: 接口)

Kotlin 中的接口可以包含抽象方法的声明，同时也可以包含方法的实现。
与抽象类的区别在于接口不能存储状态。
接口可以有属性，但这些属性需要是抽象的或者提供访问器的实现。

使用关键字 `interface` 来定义接口：

```kotlin
interface MyInterface {
    fun bar()
    fun foo() {
        // 可选的方法体
    }
}
```

## 实现接口

一个类或对象可以实现一个或多个接口：

```kotlin
class Child : MyInterface {
    override fun bar() {
        // 具体实现
    }
}
```

## 接口中的属性

你可以在接口中声明属性。在接口中声明的属性可以是抽象的，也可以提供访问器的实现。
在接口中声明的属性不能有幕后字段，因此在接口中声明的访问器不能引用它们：

```kotlin
interface MyInterface {
    val prop: Int // 抽象

    val propertyWithImplementation: String
        get() = "foo"

    fun foo() {
        print(prop)
    }
}

class Child : MyInterface {
    override val prop: Int = 29
}
```

## 接口继承

一个接口可以继承自其他接口，这意味着它既可以为它们的成员提供实现，也可以声明新的函数和属性。
很自然地，实现这样一个接口的类只需要定义缺失的实现：

```kotlin
interface Named {
    val name: String
}

interface Person : Named {
    val firstName: String
    val lastName: String
    
    override val name: String get() = "$firstName $lastName"
}

data class Employee(
    // 不需要实现 'name'
    override val firstName: String,
    override val lastName: String,
    val position: Position
) : Person
```

## 解决覆盖冲突

当在<tooltip term="超类型列表">超类型列表</tooltip>中声明多个类型时，可能会继承相同方法的多个实现：

```kotlin
interface A {
    fun foo() { print("A") }
    fun bar()
}

interface B {
    fun foo() { print("B") }
    fun bar() { print("bar") }
}

class C : A {
    override fun bar() { print("bar") }
}

class D : A, B {
    override fun foo() {
        super<A>.foo()
        super<B>.foo()
    }

    override fun bar() {
        super<B>.bar()
    }
}
```

接口 **A** 和 **B** 都声明了 **foo()** 和 **bar()** 函数。
它们都实现了 **foo()**，但只有 **B** 实现了 **bar()**（**bar()** 在 **A** 中虽然没有标记为抽象，但是对于没有具体实现的函数，接口默认将其视为抽象）。
现在，如果你从 **A** <tooltip term="派生">派生</tooltip>一个具体类 **C**，你必须覆盖 **bar()** 并提供实现。

However, if you derive *D* from *A* and *B*, you need to implement all the methods that you have
inherited from multiple interfaces, and you need to specify how exactly *D* should implement them. This rule applies
both to methods for which you've inherited a single implementation (*bar()*) and to those for which you've inherited multiple implementations (*foo()*).

## JVM default method generation for interface functions

On the JVM, functions declared in interfaces are compiled to default methods.
You can control this behavior using the `-jvm-default` compiler option with the following values:

* `enable` (default): generates default implementations in interfaces and includes bridge functions in subclasses and `DefaultImpls` classes. Use this mode to maintain binary compatibility with older Kotlin versions.
* `no-compatibility`: generates only default implementations in interfaces. This mode skips compatibility bridges and `DefaultImpls` classes, making it suitable for new Kotlin code.
* `disable`: skips default methods and generates only compatibility bridges and `DefaultImpls` classes.

To configure the `-jvm-default` compiler option, set the `jvmDefault` property in your Gradle Kotlin DSL:

```kotlin
kotlin {
    compilerOptions {
        jvmDefault = JvmDefaultMode.NO_COMPATIBILITY
    }
}
```

