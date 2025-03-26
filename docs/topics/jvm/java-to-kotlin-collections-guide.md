[//]: # (title: Java 与 Kotlin 中的集合)
[//]: # (description: 学习如何从 Java 集合迁移到 Kotlin 集合。本指南涵盖了 Kotlin 和 Java 中的列表、ArrayList、映射、集合以及其他数据结构。)

_集合_（_Collections_）是包含变量数量（可能为零）并且对解决问题有意义的项目的元素集，通常用于进行各种操作。 
本指南解释并比较了 Java 和 Kotlin 中的集合概念和操作。
它将帮助你从 Java 迁移到 Kotlin，并以地道的 Kotlin 方式编写代码。

本指南的第一部分包含了 Java 和 Kotlin 中相同集合操作的简要词汇表。
它被分为 [相同的操作](#operations-that-are-the-same-in-java-and-kotlin)
和 [仅在 Kotlin 中存在的操作](#operations-that-don-t-exist-in-java-s-standard-library)。
指南的第二部分，从 [可变性](#mutability) 开始，通过具体案例解释了一些差异。

有关集合的介绍，请参阅 [集合概述](collections-overview.md)，或观看
Sebastian Aigner 的 [视频](https://www.youtube.com/watch?v=F8jj7e-_jFA)，他是 Kotlin 开发者倡导者。

> 以下所有示例仅使用 Java 和 Kotlin 标准库的 API。
>
{style="note"}

## 在 Java 和 Kotlin 中相同的操作 {id=operations-that-are-the-same-in-java-and-kotlin}

在 Kotlin 中，许多集合操作与 Java 中的操作完全相同。

### 对列表（lists）、集合（sets）、队列（queues）和双端队列（deques）的操作 {id=operations-on-lists-sets-queues-and-deques}

| 描述 | 常见操作 | 更多 Kotlin 替代方法 |
|-------------|-----------|---------------------|
| 添加一个元素或多个元素 | `add()`，`addAll()` | 使用 [`plusAssign`(`+=`) 运算符](collection-plus-minus.md)：`collection += element`，`collection += anotherCollection`。 |
| 检查集合是否包含某个元素或多个元素 | `contains()`，`containsAll()` | 使用 [`in` 关键字](collection-elements.md#check-element-existence) 来调用 `contains()` 的运算符形式：`element in collection`。 |
| 检查集合是否为空 | `isEmpty()` | 使用 [`isNotEmpty()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/is-not-empty.html) 来检查集合是否非空。 |
| 根据条件移除元素 | `removeIf()` | |
| 仅保留选定元素 | `retainAll()` | |
| 从集合中移除所有元素 | `clear()` | |
| 从集合中获取流 | `stream()` | Kotlin 有自己处理流的方式：[序列](#sequences) 和像 [`map()`](collection-filtering.md) 和 [`filter()`](#filter-elements) 这样的函数。 |
| 从集合中获取迭代器 | `iterator()` | |

### 对映射（maps）的操作 {id=operations-on-maps}

| 描述 | 常见操作 | 更多 Kotlin 替代方法 |
|-------------|-----------|---------------------|
| 添加一个元素或多个元素 | `put()`，`putAll()`，`putIfAbsent()` | 在 Kotlin 中，`map[key] = value` 的行为与 `put(key, value)` 相同。此外，你可以使用 [`plusAssign`(`+=`) 运算符](collection-plus-minus.md)：`map += Pair(key, value)` 或 `map += anotherMap`。 |
| 替换一个元素或多个元素 | `put()`，`replace()`，`replaceAll()` | 使用索引运算符 `map[key] = value` 替代 `put()` 和 `replace()`。 |
| 获取一个元素 | `get()` | 使用索引运算符获取元素：`map[index]`。 |
| 检查映射是否包含某个元素或多个元素 | `containsKey()`，`containsValue()` | 使用 [`in` 关键字](collection-elements.md#check-element-existence) 来调用 `contains()` 的运算符形式：`element in map`。 |
| 检查映射是否为空 | `isEmpty()` | 使用 [`isNotEmpty()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/is-not-empty.html) 来检查映射是否非空。 |
| 移除一个元素 | `remove(key)`，`remove(key, value)` | 使用 [`minusAssign`(`-=`) 运算符](collection-plus-minus.md)：`map -= key`。 |
| 移除映射中的所有元素 | `clear()` | |
| 从映射中获取流 | `stream()`（针对条目、键或值） | |

### 仅在列表（lists）中存在的操作 {id=operations-that-exist-only-for-lists}

| 描述 | 常见操作 | 更多 Kotlin 替代方法 |
|-------------|-----------|---------------------|
| 获取元素的索引 | `indexOf()` | |
| 获取元素的最后索引 | `lastIndexOf()` | |
| 获取一个元素 | `get()` | 使用索引运算符获取元素：`list[index]`。 |
| 获取子列表 | `subList()` | |
| 替换一个元素或多个元素 | `set()`，`replaceAll()` | 使用索引运算符替代 `set()`：`list[index] = value`。 |

## 略有不同的操作 {id=operations-that-differ-a-bit}

### 对任何集合类型的操作 {id=operations-on-any-collection-type}

| 描述 | Java | Kotlin |
|-------------|------|--------|
| 获取集合的大小 | `size()` | `count()`，`size` |
| 获取嵌套集合元素的平坦访问 | `collectionOfCollections.forEach(flatCollection::addAll)` 或 `collectionOfCollections.stream().flatMap().collect()` | [`flatten()`](collection-transformations.md#flatten) 或 [`flatMap()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/flat-map.html) |
| 对每个元素应用给定的函数 | `stream().map().collect()` | [`map()`](collection-filtering.md) |
| 按顺序对集合元素应用提供的操作并返回累积结果 | `stream().reduce()` | [`reduce()`，`fold()`](collection-aggregate.md#fold-and-reduce) |
| 按分类器对元素分组并计数 | `stream().collect(Collectors.groupingBy(classifier, counting()))` | [`eachCount()`](collection-grouping.md) |
| 根据条件过滤 | `stream().filter().collect()` | [`filter()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter.html) |
| 检查集合元素是否满足条件 | `stream().noneMatch()`，`stream().anyMatch()`，`stream().allMatch()` | [`none()`，`any()`，`all()`](collection-filtering.md) |
| 排序元素 | `stream().sorted().collect()` | [`sorted()`](collection-ordering.md#natural-order) |
| 获取前 N 个元素 | `stream().limit(N).collect()` | [`take(N)`](collection-parts.md#take-and-drop) |
| 根据谓词获取元素 | `stream().takeWhile().collect()` | [`takeWhile()`](collection-parts.md#take-and-drop) |
| 跳过前 N 个元素 | `stream().skip(N).collect()` | [`drop(N)`](collection-parts.md#take-and-drop) |
| 跳过符合谓词的元素 | `stream().dropWhile().collect()` | [`dropWhile()`](collection-parts.md#take-and-drop) |
| 从集合元素和与其相关联的特定值构建映射 | `stream().collect(toMap(keyMapper, valueMapper))` | [`associate()`](collection-transformations.md#associate) |

要在映射（maps）上执行上述所有操作，你需要首先获取映射（maps）的 `entrySet`。

### 列表（lists）上的操作 {id=operations-on-lists}

| 描述 | Java | Kotlin |
|-------------|------|--------|
| 将列表按自然顺序排序 | `sort(null)` | `sort()` |
| 将列表按降序排序 | `sort(comparator)` | `sortDescending()` |
| 从列表中移除一个元素 | `remove(index)`，`remove(element)` | `removeAt(index)`，`remove(element)` 或 [`collection -= element`](collection-plus-minus.md) |
| 用某个特定值填充列表中的所有元素 | `Collections.fill()` | [`fill()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/fill.html) |
| 获取列表中的唯一元素 | `stream().distinct().toList()` | [`distinct()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/distinct.html) |

## Java 标准库中不存在的操作 {id=operations-that-don-t-exist-in-java-s-standard-library}

* [`zip()`，`unzip()`](collection-transformations.md) – 转换集合。
* [`aggregate()`](collection-grouping.md) – 根据条件分组。
* [`takeLast()`，`takeLastWhile()`，`dropLast()`，`dropLastWhile()`](collection-parts.md#take-and-drop) – 根据谓词获取或丢弃元素。
* [`slice()`，`chunked()`，`windowed()`](collection-parts.md) – 获取集合的部分。
* [加 (`+`) 和减 (`-`) 运算符](collection-plus-minus.md) – 添加或移除元素。

如果你想深入了解 `zip()`，`chunked()`，`windowed()` 和其他操作，可以观看这段由 Sebastian Aigner
制作的关于 Kotlin 中高级集合操作的视频：

<video src="https://www.youtube.com/v/N4CpLxGJlq0" title="高级集合操作"/>

## 可变性 {id=mutability}

在 Java 中，有可变集合：

```java
// Java
// 这个列表是可变的！
public List<Customer> getCustomers() { ... }
```
{id="mutability-java"}

部分可变的集合：

```java
// Java
List<String> numbers = Arrays.asList("one", "two", "three", "four");
numbers.add("five"); // 运行时会抛出 `UnsupportedOperationException`
```
{id="mutability-partly-java"}

以及不可变的集合：

```java
// Java
List<String> numbers = new LinkedList<>();
// 这个列表是不可变的！
List<String> immutableCollection = Collections.unmodifiableList(numbers);
immutableCollection.add("five"); // 运行时会抛出 `UnsupportedOperationException`
```
{id="immutable-java"}

如果你在 IntelliJ IDEA 中编写最后两段代码，IDE 会警告你正在尝试修改一个不可变对象。
这段代码会编译通过，但在运行时会抛出 `UnsupportedOperationException`。你无法仅通过查看集合的类型来判断它是否可变。

与 Java 不同，在 Kotlin 中，你需要根据需求明确声明可变集合或只读集合。
如果你尝试修改只读集合，代码将无法编译：

```kotlin
// Kotlin
val numbers = mutableListOf("one", "two", "three", "four")
numbers.add("five")            // 这是 OK
val immutableNumbers = listOf("one", "two")
//immutableNumbers.add("five") // 编译错误 - Unresolved reference: add
```
{id="mutability-kotlin"}

在 [Kotlin 编码规范](coding-conventions.md#immutability) 页面上了解更多关于不可变性的内容。

## 协变 {id=covariance}

在 Java 中，你不能将一个包含子类类型的集合传递给一个接受父类类型集合的函数。
例如，如果 `Rectangle` 继承自 `Shape`，你不能将一个包含 `Rectangle` 元素的集合传递给一个接受 `Shape` 元素集合的函数。
为了使代码能够编译，可以使用 `? extends Shape` 类型，这样函数就可以接受任何 `Shape` 的子类集合：

```java
// Java
class Shape {}

class Rectangle extends Shape {}

public void doSthWithShapes(List<? extends Shape> shapes) {
/* 如果使用 List<Shape>，当传递 List<Rectangle> 作为参数时，代码将无法编译，如下所示 */
}

public void main() {
    var rectangles = List.of(new Rectangle(), new Rectangle());
    doSthWithShapes(rectangles);
}
```
{id="covariance-java"}

在 Kotlin 中，只有只读集合类型是 [协变的](generics.md#型变)。这意味着，如果 `Rectangle` 类继承自 `Shape` 类，
你可以在需要 `List<Shape>` 类型的地方使用 `List<Rectangle>` 类型。
换句话说，集合类型具有与元素类型相同的子类型关系。映射是对值类型协变的，但不是对键类型。
可变集合不是协变的——这会导致运行时错误。

```kotlin
// Kotlin
open class Shape(val name: String)

class Rectangle(private val rectangleName: String) : Shape(rectangleName)

fun doSthWithShapes(shapes: List<Shape>) {
    println("The shapes are: ${shapes.joinToString { it.name }}")
}

fun main() {
    val rectangles = listOf(Rectangle("rhombus"), Rectangle("parallelepiped"))
    doSthWithShapes(rectangles)
}
```
{kotlin-runnable="true" id="covariance-kotlin"}

了解更多关于 [集合类型](collections-overview.md#collection-types) 的内容。

## 区间和数列 {id=ranges-and-progressions}

在 Kotlin 中，你可以使用 [区间](ranges.md) 创建一个范围。
例如，`Version(1, 11)..Version(1, 30)` 包含了从 `1.11` 到 `1.30` 的所有版本。
你可以使用 `in` 操作符检查某个版本是否在区间内：`Version(0, 9) in versionRange`。

在 Java 中，你需要手动检查 `Version` 是否满足上下边界条件：

```java
// Java
class Version implements Comparable<Version> {

    int major;
    int minor;

    Version(int major, int minor) {
        this.major = major;
        this.minor = minor;
    }

    @Override
    public int compareTo(Version o) {
        if (this.major != o.major) {
            return this.major - o.major;
        }
        return this.minor - o.minor;
    }
}

public void compareVersions() {
    var minVersion = new Version(1, 11);
    var maxVersion = new Version(1, 31);

   System.out.println(
           versionIsInRange(new Version(0, 9), minVersion, maxVersion));
   System.out.println(
           versionIsInRange(new Version(1, 20), minVersion, maxVersion));
}

public Boolean versionIsInRange(Version versionToCheck, Version minVersion, 
                                Version maxVersion) {
    return versionToCheck.compareTo(minVersion) >= 0 
            && versionToCheck.compareTo(maxVersion) <= 0;
}
```
{id="ranges-java"}

在 Kotlin 中，你可以将区间作为一个整体对象来操作。你不需要创建两个变量并将 Version 与它们进行比较：

```kotlin
// Kotlin
class Version(val major: Int, val minor: Int): Comparable<Version> {
    override fun compareTo(other: Version): Int {
        if (this.major != other.major) {
            return this.major - other.major
        }
        return this.minor - other.minor
    }
}

fun main() {
    val versionRange = Version(1, 11)..Version(1, 30)

    println(Version(0, 9) in versionRange)
    println(Version(1, 20) in versionRange)
}
```
{kotlin-runnable="true" id="ranges-kotlin"}

当您需要排除其中一个边界时，例如检查版本是否大于或等于 (`>=`) 最低版本
且小于 (`<`) 最高版本，这些闭区间就派不上用场了。

## 多条件比较 {id=comparison-by-several-criteria}

在 Java 中，要通过多个条件比较对象，您可以使用 [`Comparator`](https://docs.oracle.com/javase/8/docs/api/java/util/Comparator.html)
接口中的 [`comparing()`](https://docs.oracle.com/javase/8/docs/api/java/util/Comparator.html#comparing-java.util.function.Function-)  
和 [`thenComparingX()`](https://docs.oracle.com/javase/8/docs/api/java/util/Comparator.html#thenComparing-java.util.Comparator-) 方法。
例如，要按姓名和年龄比较人员：

```java
class Person implements Comparable<Person> {
    String name;
    int age;

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return this.name + " " + age;
    }
}

public void comparePersons() {
    var persons = List.of(new Person("Jack", 35), new Person("David", 30), 
            new Person("Jack", 25));
    System.out.println(persons.stream().sorted(Comparator
            .comparing(Person::getName)
            .thenComparingInt(Person::getAge)).collect(toList()));
}
```
{id="comparison-java"}

在 Kotlin 中，您只需枚举需要比较的字段：

```kotlin
data class Person(
    val name: String,
    val age: Int
)

fun main() {
    val persons = listOf(Person("Jack", 35), Person("David", 30), 
        Person("Jack", 25))
    println(persons.sortedWith(compareBy(Person::name, Person::age)))
}
```
{kotlin-runnable="true" id="comparison-kotlin"}

## 序列 {id=sequences}

在 Java 中，可以通过以下方式生成数字序列：

```java
// Java
int sum = IntStream.iterate(1, e -> e + 3)
    .limit(10).sum();
System.out.println(sum); // 打印 145
```
{id="sequences-java"}

在 Kotlin 中，则使用_[序列](sequences.md)_。序列的多步处理会尽可能延迟执行 ——
只有当整个处理链的结果被请求时，才会进行实际计算。

```kotlin
fun main() {
//sampleStart
    // Kotlin
    val sum = generateSequence(1) {
        it + 3
    }.take(10).sum()
    println(sum) // 输出 145
//sampleEnd
}
```
{kotlin-runnable="true" id="sequences-kotlin"}

序列可以减少执行某些过滤操作所需的步骤数。  
参阅[序列处理示例](sequences.md#sequence-processing-example)，了解 `Iterable` 和 `Sequence` 的区别。

## 列表（list）元素移除 {id=removal-of-elements-from-a-list}

在 Java 中，[`remove()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/List.html#remove(int))
方法接收要移除元素的索引。

当需要移除整数元素时，应使用 `Integer.valueOf()` 方法作为 `remove()` 的参数：

```java
// Java
public void remove() {
    var numbers = new ArrayList<>();
    numbers.add(1);
    numbers.add(2);
    numbers.add(3);
    numbers.add(1);
    numbers.remove(1); // 按索引移除
    System.out.println(numbers); // [1, 3, 1]
    numbers.remove(Integer.valueOf(1));
    System.out.println(numbers); // [3, 1]
}
```
{id="remove-elements-java"}

在 Kotlin 中，元素移除分为两种方式：
通过 [`removeAt()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/remove-at.html) 按索引移除
和通过 [`remove()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/remove.html) 按值移除。

```kotlin
fun main() {
//sampleStart
    // Kotlin
    val numbers = mutableListOf(1, 2, 3, 1)
    numbers.removeAt(0)  // 移除索引0的元素
    println(numbers) // 输出 [2, 3, 1]
    numbers.remove(1)  // 移除值为1的元素
    println(numbers) // 输出 [2, 3]
//sampleEnd
}
```
{kotlin-runnable="true" id="remove-elements-kotlin"}

## 遍历映射（Map） {id=traverse-a-map}

在 Java 中，可以通过 [`forEach`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Map.html#forEach(java.util.function.BiConsumer)) 遍历 Map：

```java
// Java
numbers.forEach((k,v) -> System.out.println("Key = " + k + ", Value = " + v));
```
{id="traverse-map-java"}

在 Kotlin 中，可以使用 `for` 循环或类似 Java `forEach` 的 `forEach` 方法来遍历 Map：

```kotlin
// Kotlin
for ((k, v) in numbers) {
    println("Key = $k, Value = $v") 
}
// 或者
numbers.forEach { (k, v) -> println("Key = $k, Value = $v") }
```
{id="traverse-map-kotlin"}

## 获取可能为空的集合中的第一项和最后一项 {id=获取可能为空的集合中的第一项和最后一项}

## 获取集合首尾元素 {id=get-first-and-last-elements}

在 Java 中，可以通过检查集合大小并使用索引安全地获取首尾元素：

```java
// Java
var list = new ArrayList<>();
//...
if (list.size() > 0) {
    System.out.println(list.get(0));  // 获取第一个元素
    System.out.println(list.get(list.size() - 1));  // 获取最后一个元素
}
```
{id="list-get-first-last-java"}

对于 [`Deque`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Deque.html) 及其子类，
还可以使用 [`getFirst()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Deque.html#getFirst())
和 [`getLast()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Deque.html#getLast()) 方法：

```java
// Java
var deque = new ArrayDeque<>();
//...
if (deque.size() > 0) {
    System.out.println(deque.getFirst());  // 获取队首元素
    System.out.println(deque.getLast());  // 获取队尾元素
}
```
{id="deque-get-first-last-java"}

在 Kotlin 中，提供了专门的
[`firstOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/first-or-null.html)
和 [`lastOrNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/last-or-null.html) 方法。
结合使用 [Elvis 操作符](null-safety.md#elvis-operator)，可以立即根据方法结果执行后续操作。
例如使用 `firstOrNull()`：

```kotlin
// Kotlin
val emails = listOf<String>() // 可能是空集合
val theOldestEmail = emails.firstOrNull() ?: "" // 获取第一个元素，若为空则返回默认值
val theFreshestEmail = emails.lastOrNull() ?: "" // 获取最后一个元素，若为空则返回默认值
```
{id="get-first-last-kotlin"}

## 从列表（list）创建集合（set） {id=create-a-set-from-a-list}

在 Java 中，要从 [`List`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/List.html) 创建
[`Set`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Set.html)，可以使用
[`Set.copyOf`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Set.html#copyOf(java.util.Collection)) 方法：

```java
// Java
public void listToSet() {
    var sourceList = List.of(1, 2, 3, 1);  // 创建包含重复元素的列表
    var copySet = Set.copyOf(sourceList);  // 自动去重创建集合
    System.out.println(copySet);  // 输出 [1, 2, 3]
}
```
{id="list-to-set-java"}

在 Kotlin 中，直接使用 `toSet()` 方法即可：

```kotlin
fun main() {
//sampleStart
    // Kotlin
    val sourceList = listOf(1, 2, 3, 1)  // 创建包含重复元素的列表
    val copySet = sourceList.toSet()  // 自动去重创建集合
    println(copySet)  // 输出 [1, 2, 3]
//sampleEnd
}
```
{kotlin-runnable="true" id="list-to-set-kotlin"}

## 分组元素 {id=group-elements}

在 Java 中，你可以使用 [集合](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/stream/Collectors.html)
方法 `groupingBy()` 来分组元素：

```java
// Java
public void analyzeLogs() {
    var requests = List.of(
        new Request("https://kotlinlang.org/docs/home.html", 200),
        new Request("https://kotlinlang.org/docs/home.html", 400),
        new Request("https://kotlinlang.org/docs/comparison-to-java.html", 200)
    );
    var urlsAndRequests = requests.stream().collect(
            Collectors.groupingBy(Request::getUrl));
    System.out.println(urlsAndRequests);
}
```
{id="group-elements-java"}

在 Kotlin 中，使用 [分组方法 `groupBy()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/group-by.html) ：

```kotlin
data class Request(
    val url: String,
    val responseCode: Int
)

fun main() {
//sampleStart
    // Kotlin
    val requests = listOf(
        Request("https://kotlinlang.org/docs/home.html", 200),
        Request("https://kotlinlang.org/docs/home.html", 400),
        Request("https://kotlinlang.org/docs/comparison-to-java.html", 200)
    )
    println(requests.groupBy(Request::url))
//sampleEnd
}
```
{kotlin-runnable="true" id="group-elements-kotlin"}

## 过滤元素 {id=filter-elements}

在 Java 中，要从集合中过滤元素，你需要使用 [流式（Stream） API](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/stream/package-summary.html)。
流式 API 包含 `intermediate`（中间操作）和 `terminal`（终止操作）。`filter()` 是一个中间操作，它会返回一个新的流。
要获得集合作为输出结果，你需要使用终止操作，例如 `collect()`。
例如，要筛选出键以 `1` 结尾且值大于 `10` 的键值对：

```java
// Java
public void filterEndsWith() {
    var numbers = Map.of("key1", 1, "key2", 2, "key3", 3, "key11", 11);
    var filteredNumbers = numbers.entrySet().stream()
        .filter(entry -> entry.getKey().endsWith("1") && entry.getValue() > 10)
        .collect(Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue));
    System.out.println(filteredNumbers);
}
```
{id="filter-elements-java"}

在 Kotlin 中，过滤操作直接内置于集合中，`filter()` 方法会返回与被过滤集合相同的类型。
因此，你只需要编写 `filter()` 及其谓词条件即可：

```kotlin
fun main() {
//sampleStart
    // Kotlin
    val numbers = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key11" to 11)
    val filteredNumbers = numbers.filter { (key, value) -> key.endsWith("1") && value > 10 }
    println(filteredNumbers)
//sampleEnd
}
```
{kotlin-runnable="true" id="filter-elements-kotlin"}

了解更多关于[过滤映射（map）](map-operations.md#filter)的信息。

### 按类型过滤元素 {id=filter-elements-by-type}

在 Java 中，要按类型过滤元素并对其执行操作，你需要使用
[`instanceof`](https://docs.oracle.com/en/java/javase/17/language/pattern-matching-instanceof-operator.html) 操作符检查类型，然后进行类型转换：

```java
// Java
public void objectIsInstance() {
    var numbers = new ArrayList<>();
    numbers.add(null);
    numbers.add(1);
    numbers.add("two");
    numbers.add(3.0);
    numbers.add("four");
    System.out.println("All String elements in upper case:");
    numbers.stream().filter(it -> it instanceof String)
        .forEach( it -> System.out.println(((String) it).toUpperCase()));
}
```
{id="filter-by-type-java"}

在 Kotlin 中，你只需在集合上调用 [`filterIsInstance<NEEDED_TYPE>()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-is-instance.html)，
而类型转换则由[智能转换](typecasts.md#smart-casts)自动完成：

```kotlin
// Kotlin
fun main() {
//sampleStart
    // Kotlin
    val numbers = listOf(null, 1, "two", 3.0, "four")
    println("All String elements in upper case:")
    numbers.filterIsInstance<String>().forEach {
        println(it.uppercase())
    }
//sampleEnd
}
```
{kotlin-runnable="true" id="filter-by-type-kotlin"}

### 测试谓词条件 {id=test-predicates}

某些任务需要检查所有、没有或任一元素是否满足条件。
在 Java 中，你可以通过 [流式 API](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/stream/package-summary.html)
方法 [`allMatch()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/stream/Stream.html#allMatch(java.util.function.Predicate))、
[`noneMatch()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/stream/Stream.html#noneMatch(java.util.function.Predicate)) 和
[`anyMatch()`](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/stream/Stream.html#anyMatch(java.util.function.Predicate)) 来完成这些检查：

```java
// Java
public void testPredicates() {
    var numbers = List.of("one", "two", "three", "four");
    System.out.println(numbers.stream().noneMatch(it -> it.endsWith("e"))); // false
    System.out.println(numbers.stream().anyMatch(it -> it.endsWith("e"))); // true
    System.out.println(numbers.stream().allMatch(it -> it.endsWith("e"))); // false
}
```
{id="test-predicates-java"}

在 Kotlin 中，每个 [可迭代对象](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-iterable/#kotlin.collections.Iterable)
都提供了 [扩展函数](extensions.md) `none()`、`any()` 和 `all()`：

```kotlin
fun main() {
//sampleStart
// Kotlin
    val numbers = listOf("one", "two", "three", "four")
    println(numbers.none { it.endsWith("e") })  // 检查是否没有元素以"e"结尾
    println(numbers.any { it.endsWith("e") })   // 检查是否有任一元素以"e"结尾
    println(numbers.all { it.endsWith("e") })   // 检查是否所有元素都以"e"结尾
//sampleEnd
}
```
{kotlin-runnable="true" id="test-predicates-kotlin"}

了解更多关于[测试谓词条件](collection-filtering.md#test-predicates)的内容。

## 集合转换操作 {id=collection-transformation-operations}

### 元素配对 {id=zip-elements}

在 Java 中，你可以通过同时遍历两个集合来创建相同位置元素的配对：

```java
// Java
public void zip() {
    var colors = List.of("red", "brown");
    var animals = List.of("fox", "bear", "wolf");

    for (int i = 0; i < Math.min(colors.size(), animals.size()); i++) {
        String animal = animals.get(i);
        System.out.println("The " + animal.substring(0, 1).toUpperCase()
               + animal.substring(1) + " is " + colors.get(i));
   }
}
```
{id="zip-elements-java"}

如果需要进行比简单打印元素对更复杂的操作，可以使用 [记录类](https://blogs.oracle.com/javamagazine/post/records-come-to-java)。
在上例中，记录类可以是 `record AnimalDescription(String animal, String color) {}`。

在 Kotlin 中，使用 [`zip()`](collection-transformations.md#zip) 函数实现相同功能：

```kotlin
fun main() {
//sampleStart
    // Kotlin
    val colors = listOf("red", "brown")
    val animals = listOf("fox", "bear", "wolf")

    println(colors.zip(animals) { color, animal -> 
        "The ${animal.replaceFirstChar { it.uppercase() }} is $color" })
//sampleEnd
}
```
{kotlin-runnable="true" id="zip-elements-kotlin"}

`zip()` 返回包含 [Pair](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-pair/) 对象的列表。

> 如果集合大小不同，`zip()` 的结果取较小的大小。较大集合的最后几个元素不会包含在结果中。
>
{style="note"}

### 关联元素 {id=associate-elements}

在 Java 中，你可以使用 [流式 API](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/stream/package-summary.html)
来关联元素与其特征：

```java
// Java
public void associate() {
    var numbers = List.of("one", "two", "three", "four");
    var wordAndLength = numbers.stream()
        .collect(toMap(number -> number, String::length));
    System.out.println(wordAndLength);
}
```
{id="associate-elements-java"}

在 Kotlin 中，使用 [`associate()`](collection-transformations.md#associate) 函数：

```kotlin
fun main() {
//sampleStart
    // Kotlin
    val numbers = listOf("one", "two", "three", "four")
    println(numbers.associateWith { it.length })
//sampleEnd
}
```
{kotlin-runnable="true" id="associate-elements-kotlin"}

## 下一步 {id=whats-next}

* 尝试 [Kotlin 练习](koans.md) - 通过完成练习学习 Kotlin 语法。每个练习都是一个失败的单元测试，你的任务是让它通过
* 浏览其他 [Kotlin 惯用法](idioms.md)
* 学习如何使用 [Java 转 Kotlin 转换器](mixing-java-kotlin-intellij.md#converting-an-existing-java-file-to-kotlin-with-j2k) 将现有 Java 代码转换为 Kotlin
* 探索 [Kotlin 中的集合](collections-overview.md)

如果你有喜欢的惯用法，欢迎通过提交 Pull Request 来分享。
