# 什么是隐式幕后字段

在 Kotlin 中，当你声明一个属性时，如果没有显式地为其指定幕后字段，Kotlin 会自动创建一个默认的幕后字段。
这种情况下的幕后字段就被称为隐式幕后字段（Implicit Backing Field）。

举例：

```kotlin
class Example {
    private var _value: Int = 0 // 显式定义幕后字段

    var value: Int
        get() = _value
        set(newValue) {
            _value = newValue
        }
}

class Example2 {
    var value: Int = 0 // 隐式定义幕后字段
}
```

在这个例子中，有两个类： `Example` 和 `Example2`。

将它们反编译为 Java：

```java
public final class Example {
   private int _value;

   public final int getValue() {
      return this._value;
   }

   public final void setValue(int newValue) {
      this._value = newValue;
   }
}

public final class Example2 {
   private int value;

   public final int getValue() {
      return this.value;
   }

   public final void setValue(int var1) {
      this.value = var1;
   }
}
```

我们可以发现，它们生成的结构其实都是一样的，都包括 `getter` 和 `setter`。

所以在 Kotlin 中，如果没有显示定义幕后字段的话，那么在进行编译的时候，会帮属性自动创建一个幕后字段（可以在 `getter` 或 `setter` 中通过 `field` 标识符来调用）。

```kotlin
class Example {
    var counter: Int = 0
        set(value) {
            if (value >= 0) {
                // 使用 field 来引用属性的幕后字段
                field = value
            }
        }
}
```