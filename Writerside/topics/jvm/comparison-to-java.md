[//]: # (title: Comparison to Java)

## Some Java issues addressed in Kotlin

Kotlin fixes a series of issues that Java suffers from:

* Null references are [controlled by the type system](null-safety.md).
* [No raw types](java-interop.md#java-generics-in-kotlin)
* Arrays in Kotlin are [invariant](arrays.md)
* Kotlin has proper [function types](lambdas.md#function-types), as opposed to Java's SAM-conversions
* [Use-site variance](generics.md#使用处型变-类型投影)  without wildcards
* Kotlin does not have checked [exceptions](exceptions.md)

## What Java has that Kotlin does not

* [Checked exceptions](exceptions.md)
* [Primitive types](basic-types.md) that are not classes. The byte-code uses primitives where possible, but they are not
    explicitly available.
* [Static members](classes.md) are replaced with [companion objects](object-declarations.md#companion-objects),
    [top-level functions](functions.md), [extension functions](extensions.md#扩展函数), or [@JvmStatic](java-to-kotlin-interop.md#static-methods).
* [Wildcard-types](generics.md) are replaced with [declaration-site variance](generics.md#声明处型变) and
    [type projections](generics.md#类型投影).
* [Ternary-operator `a ? b : c`](control-flow.md#if表达式) is replaced with [if expression](control-flow.md#if表达式). 


## What Kotlin has that Java does not

* [Lambda expressions](lambdas.md) + [Inline functions](inline-functions.md) = performant custom control structures
* [Extension functions](extensions.md)
* [Null-safety](null-safety.md)
* [Smart casts](typecasts.md)
* [String templates](strings.md)
* [Properties](properties.md)
* [Primary constructors](classes.md)
* [First-class delegation](delegation.md)
* [Type inference for variable and property types](basic-types.md)
* [Singletons](object-declarations.md)
* [Declaration-site variance & Type projections](generics.md)
* [Range expressions](ranges.md)
* [Operator overloading](operator-overloading.md)
* [Companion objects](classes.md#伴生对象)
* [Data classes](data-classes.md)
* [Separate interfaces for read-only and mutable collections](collections-overview.md)
* [Coroutines](coroutines-overview.md)

## What's next?

Learn how to:
* Perform [typical tasks with strings in Java and Kotlin](java-to-kotlin-idioms-strings.md).
* Perform [typical tasks with collections in Java and Kotlin](java-to-kotlin-collections-guide.md).
* [Handle nullability in Java and Kotlin](java-to-kotlin-nullability-guide.md).