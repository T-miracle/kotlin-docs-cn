# 为什么 List&lt;? extends Foo&gt; 不能调用 add() 或 set() 方法

`List<? extends Foo>` 之所以不能调用 `add()` 或 `set()` 方法，涉及到通配符的协变性和泛型的设计原则：

1. **协变性：**
    - `List<? extends Foo>` 是一个协变类型，表示这个列表中包含的元素都是 `Foo` 或者 `Foo` 的子类。这是为了提供更灵活的类型匹配。
    - 如果允许调用 `add()` 或 `set()` 方法，可能会引入类型不一致的问题。例如，如果你有一个 `List<? extends Foo>`，你不能确定这个列表到底是 `List<Foo>`、`List<SubFoo>` 还是其他子类的列表。

2. **泛型设计原则：**
    - 泛型的目标之一是提供类型安全。通过限制对协变类型的写入操作，可以避免在运行时引入不安全的类型转换或不一致的类型关系。
    - 如果允许在 `List<? extends Foo>` 上调用 `add()` 或 `set()`，可能导致在编译时看似正确的代码，在运行时引发类型错误。

因此，为了保持类型安全性，Java 中规定在协变类型（生产者类型，通常使用 `extends` 关键字）上不允许进行写入操作。

如果你需要写入操作，可以使用逆变类型（消费者类型，通常使用 `super` 关键字，如 `List<? super Foo>`）。

总之，这是为了确保在使用泛型时能够更好地遵循类型安全和一致性的原则。