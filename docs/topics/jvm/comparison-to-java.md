[//]: # (title: 与 Java 的比较)

## 一些 Kotlin 解决的 Java 问题 {id=some-java-issues-addressed-in-kotlin}

Kotlin 修复了 Java 中存在的一系列问题：

* 空引用由 [类型系统控制](null-safety.md)。
* [没有原始类型](java-interop.md#java-generics-in-kotlin)
* Kotlin 中的数组是 [不可变的](arrays.md)
* Kotlin 具有适当的 [函数类型](lambdas.md#function-types)，而不是 Java 的 SAM 转换
* [使用处型变](generics.md#use-site-variance-type-projections) 无需通配符
* Kotlin 没有检查 [异常](exceptions.md)
* [将只读集合和可变集合分开](collections-overview.md)

## Java 有而 Kotlin 没有的功能 {id=what-java-has-that-kotlin-does-not}

* [受检异常](exceptions.md)
* [不是类的原始类型](types-overview.md)。字节码尽可能使用原始类型，但它们并不能显式使用。
* [静态成员](classes.md) 被替换为 [伴生对象](object-declarations.md#companion-objects)、[顶级函数](functions.md)、[扩展函数](extensions.md#extension-functions) 或 [@JvmStatic](java-to-kotlin-interop.md#static-methods)。
* [通配符类型](generics.md) 被 [声明处型变](generics.md#declaration-site-variance) 和 [类型投影](generics.md#type-projections) 取代。
* [三元运算符 `a ? b : c`](control-flow.md#if-expression) 被 [if 表达式](control-flow.md#if-expression) 取代。
* [记录类型](https://openjdk.org/jeps/395)
* [模式匹配](https://openjdk.org/projects/amber/design-notes/patterns/pattern-matching-for-java)
* 包私有 [可见性修饰符](visibility-modifiers.md)

## Kotlin 有而 Java 没有的功能 {id=what-kotlin-has-that-java-does-not}

* [Lambda expressions](lambdas.md) + [Inline functions](inline-functions.md) = performant custom control structures
* [Extension functions](extensions.md)
* [Null-safety](null-safety.md)
* [Smart casts](typecasts.md) (**Java 16**: [Pattern Matching for instanceof](https://openjdk.org/jeps/394))
* [String templates](strings.md)
* [Properties](properties.md)
* [Primary constructors](classes.md)
* [First-class delegation](delegation.md)
* [Type inference for variable and property types](types-overview.md) (**Java 10**: [Local-Variable Type Inference](https://openjdk.org/jeps/286))
* [Singletons](object-declarations.md)
* [Declaration-site variance & Type projections](generics.md)
* [Range expressions](ranges.md)
* [Operator overloading](operator-overloading.md)
* [Companion objects](classes.md#companion-objects)
* [Data classes](data-classes.md)
* [Coroutines](coroutines-overview.md)
* [Top-level functions](functions.md)
* [Parameters with default values](functions.md#parameters-with-default-values)
* [Named parameters](functions.md#named-arguments)
* [Infix functions](functions.md#infix-notation)
* [Expect and actual declarations](https://kotlinlang.org/docs/multiplatform/multiplatform-expect-actual.html)
* [Explicit API mode](whatsnew14.md#explicit-api-mode-for-library-authors) and [better control of API surface](opt-in-requirements.md)

## 接下来做什么？ {id=whats-next}

学习如何：
* 执行 [Java 和 Kotlin 中的典型字符串任务](java-to-kotlin-idioms-strings.md)。
* 执行 [Java 和 Kotlin 中的典型集合任务](java-to-kotlin-collections-guide.md)。
* [处理 Java 和 Kotlin 中的可空性](java-to-kotlin-nullability-guide.md)。
