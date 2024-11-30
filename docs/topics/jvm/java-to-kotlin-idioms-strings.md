[//]: # (title: Java 和 Kotlin 中的字符串)
[//]: # (description: 学习如何从 Java 的 String 迁移到 Kotlin 的 String。 本指南涵盖了 Java StringBuilder、字符串拼接、字符串拆分、多行字符串、流等主题。)

本指南包含了如何在 Java 和 Kotlin 中处理字符串的常见任务示例。  
它将帮助你从 Java 迁移到 Kotlin，并以真正的 Kotlin 风格编写代码。

## 字符串拼接 {id="concatenate-strings"}

在 Java 中，你可以通过以下方式实现：

```java
// Java
String name = "Joe";
System.out.println("Hello, " + name);
System.out.println("Your name is " + name.length() + " characters long");
```
{id="concatenate-strings-java"}

在 Kotlin 中，可以在变量名前使用美元符号 (`$`)，将该变量的值插入到字符串中：

```kotlin
fun main() {
//sampleStart
    // Kotlin
    val name = "Joe"
    println("Hello, $name")
    println("Your name is ${name.length} characters long")
//sampleEnd
}
```
{kotlin-runnable="true" id="concatenate-strings-kotlin"}

你可以通过将复杂的表达式放在花括号中，像 `${name.length}` 这样，来插入其值。
有关更多信息，请参阅 [字符串模板](strings.md#string-templates)。

## 构建字符串 {id="build-a-string"}

在 Java 中，你可以使用 [StringBuilder](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/StringBuilder.html)：

```java
// Java
StringBuilder countDown = new StringBuilder();
for (int i = 5; i > 0; i--) {
    countDown.append(i);
    countDown.append("\n");
}
System.out.println(countDown);
```
{id="build-string-java"}

在 Kotlin 中，使用 [buildString()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/build-string.html) ——
一个将构建字符串的逻辑作为 lambda 参数的 [内联函数](inline-functions.md)：

```kotlin
fun main() {
//sampleStart
       // Kotlin
       val countDown = buildString {
           for (i in 5 downTo 1) {
               append(i)
               appendLine()
           }
       }
       println(countDown)
//sampleEnd
}
```
{kotlin-runnable="true" id="build-string-kotlin"}

在底层，`buildString` 使用与 Java 中相同的 `StringBuilder` 类，你可以通过
[lambda](lambdas.md#function-literals-with-receiver) 中隐式的 `this` 来访问它。

了解更多关于 [lambda 编程规范](coding-conventions.md#lambdas)。

## 从集合项创建字符串 {id="create-string-from-collection-items"}

在 Java 中，你使用 [Stream API](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/stream/package-summary.html)
来过滤、映射，然后收集项：

```java
// Java
List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6);
String invertedOddNumbers = numbers
        .stream()
        .filter(it -> it % 2 != 0)
        .map(it -> -it)
        .map(Object::toString)
        .collect(Collectors.joining("; "));
System.out.println(invertedOddNumbers);
```
{id="create-string-from-collection-java"}

在 Kotlin 中，使用 [joinToString()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/join-to-string.html) 函数，
Kotlin 为每个 List 定义了这个函数：

```kotlin
fun main() {
//sampleStart
    // Kotlin
    val numbers = listOf(1, 2, 3, 4, 5, 6)
    val invertedOddNumbers = numbers
        .filter { it % 2 != 0 }
        .joinToString(separator = ";") {"${-it}"}
    println(invertedOddNumbers)
//sampleEnd
}
```
{kotlin-runnable="true"  id="create-string-from-collection-kotlin"}

> 在 Java 中，如果你希望分隔符和后续项之间有空格，你需要显式地在分隔符中添加空格。
>
{style="note"}

了解更多关于 [joinToString()](collection-transformations.md#string-representation) 的使用。

## 如果字符串为空则设置默认值 {id="set-default-value-if-the-string-is-blank"}

在 Java 中，你可以使用 [三元运算符](https://en.wikipedia.org/wiki/%3F:)：

```java
// Java
public void defaultValueIfStringIsBlank() {
    String nameValue = getName();
    String name = nameValue.isBlank() ? "John Doe" : nameValue;
    System.out.println(name);
}

public String getName() {
    Random rand = new Random();
    return rand.nextBoolean() ? "" : "David";
}
```
{id="set-default-value-if-blank-java"}

Kotlin 提供了内联函数 [ifBlank()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/if-blank.html)，
该函数接受默认值作为参数：

```kotlin
// Kotlin
import kotlin.random.Random

//sampleStart
fun main() {
    val name = getName().ifBlank { "John Doe" }
    println(name)
}

fun getName(): String =
    if (Random.nextBoolean()) "" else "David"
//sampleEnd
```
{kotlin-runnable="true" id="set-default-value-if-blank-kotlin"}

## 替换字符串开头和结尾的字符 {id="replace-characters-at-the-beginning-and-end-of-a-string"}

在 Java 中，你可以使用 [replaceAll()](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/String.html#replaceAll(java.lang.String,java.lang.String)) 函数。  
在这种情况下，`replaceAll()` 函数接受正则表达式 `^##` 和 `##$`，分别表示以 `##` 开头和以 `##` 结尾的字符串：

```java
// Java
String input = "##place##holder##";
String result = input.replaceAll("^##|##$", "");
System.out.println(result);
```
{id="replace-characters-java"}

在 Kotlin 中，使用 [removeSurrounding()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/remove-surrounding.html)
函数，并传入字符串分隔符 `##`：

```kotlin
fun main() {
//sampleStart
    // Kotlin
    val input = "##place##holder##"
    val result = input.removeSurrounding("##")
    println(result)
//sampleEnd
}
```
{kotlin-runnable="true" id="replace-characters-kotlin"}

## 替换出现的匹配项 {id="replace-occurrences"}

在 Java 中，你可以使用 [Pattern](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/regex/Pattern.html)
和 [Matcher](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/regex/Matcher.html) 类，  
例如，用于模糊处理一些数据：

```java
// Java
String input = "login: Pokemon5, password: 1q2w3e4r5t";
Pattern pattern = Pattern.compile("\\w*\\d+\\w*");
Matcher matcher = pattern.matcher(input);
String replacementResult = matcher.replaceAll(it -> "xxx");
System.out.println("Initial input: '" + input + "'");
System.out.println("Anonymized input: '" + replacementResult + "'");
```
{id="replace-occurrences-java"}

在 Kotlin 中，你使用 [Regex](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/-regex/) 类
来简化正则表达式的使用。  
此外，使用 [多行字符串](strings.md#多行字符串) 来简化正则表达式模式，减少反斜杠的数量：

```kotlin
fun main() {
//sampleStart
    // Kotlin
    val regex = Regex("""\w*\d+\w*""") // 多行字符串
    val input = "login: Pokemon5, password: 1q2w3e4r5t"
    val replacementResult = regex.replace(input, replacement = "xxx")
    println("Initial input: '$input'")
    println("Anonymized input: '$replacementResult'")
//sampleEnd
}
```
{kotlin-runnable="true" id="replace-occurrences-kotlin"}

## 拆分字符串 {id="split-a-string"}

在 Java 中，要拆分一个包含句点字符（`.`）的字符串，你需要使用转义字符（`\\`）。  
这是因为 `String` 类的 [split()](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/String.html#split(java.lang.String))
函数接受正则表达式作为参数：

```java
// Java
System.out.println(Arrays.toString("Sometimes.text.should.be.split".split("\\.")));
```
{id="split-string-java"}

在 Kotlin 中，使用 Kotlin 函数 [split()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/split.html)，  
该函数接受可变数量的分隔符作为输入参数：

```kotlin
fun main() {
//sampleStart
    // Kotlin
    println("Sometimes.text.should.be.split".split("."))
//sampleEnd
}
```
{kotlin-runnable="true" id="split-string-kotlin"}

如果你需要使用正则表达式进行拆分，使用重载的 `split()` 版本，该版本接受 `Regex` 作为参数。

## 获取子字符串 {id="take-a-substring"}

在 Java 中，你可以使用 [substring()](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/String.html#substring(int)) 函数，  
该函数接受一个字符的起始索引（包括该索引），从这个位置开始获取子字符串。  
如果你想获取该字符后的子字符串，需要将索引加一：

```java
// Java
String input = "What is the answer to the Ultimate Question of Life, the Universe, and Everything? 42";
String answer = input.substring(input.indexOf("?") + 1);
System.out.println(answer);
```
{id="take-substring-java"}

在 Kotlin 中，使用 [substringAfter()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/substring-after.html) 函数，  
你不需要计算想要获取子字符串后面的字符的索引：

```kotlin
fun main() {
//sampleStart
    // Kotlin
    val input = "What is the answer to the Ultimate Question of Life, the Universe, and Everything? 42"
    val answer = input.substringAfter("?")
    println(answer)
//sampleEnd
}
```
{kotlin-runnable="true" id="take-substring-kotlin"}

此外，你可以获取最后一次出现的字符后的子字符串：

```kotlin
fun main() {
//sampleStart
    // Kotlin
    val input = "To be, or not to be, that is the question."
    val question = input.substringAfterLast(",")
    println(question)
//sampleEnd
}
```
{kotlin-runnable="true" id="take-substring-after-last-kotlin"}

## 使用多行字符串 {id="use-multiline-strings"}

在 Java 15 之前，有多种方式可以创建多行字符串。
例如，可以使用 `String` 类的 [join()](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/String.html#join(java.lang.CharSequence,java.lang.CharSequence...))
函数：

```java
// Java
String lineSeparator = System.getProperty("line.separator");
String result = String.join(lineSeparator,
       "Kotlin",
       "Java");
System.out.println(result);
```
{id="join-strings-11-java"}

在 Java 15 中，[文本块](https://docs.oracle.com/en/java/javase/15/text-blocks/index.html) 被引入。  
需要注意的是，如果你打印一个多行字符串，并且三引号位于下一行，结果中会出现一个额外的空行：

```java
// Java
String result = """
    Kotlin
       Java
    """;
System.out.println(result);
```
{id="join-strings-15-java"}

输出结果：

![Java 15 multiline output](java-15-multiline-output.png){width=700}

如果将三引号放在与最后一个单词相同的行上，这种行为差异就会消失。

在 Kotlin 中，你可以将引号放在新的一行中进行格式化，输出中不会出现额外的空行。  
每行的最左边字符标识该行的开始位置。与 Java 的不同之处在于，Java 会自动去除缩进，而在 Kotlin 中，你需要显式地处理缩进问题：

```kotlin
fun main() {
//sampleStart
    // Kotlin   
    val result = """
        Kotlin
           Java 
    """.trimIndent()
    println(result)
//sampleEnd
}
```
{kotlin-runnable="true" id="join-strings-kotlin"}

输出结果：

![Kotlin multiline output](kotlin-multiline-output.png){width=700}

如果你想要额外的空行，可以显式地将空行添加到你的多行字符串中。

在 Kotlin 中，你还可以使用 [trimMargin()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/trim-margin.html) 函数来定制缩进：

```kotlin
// Kotlin
fun main() {
    val result = """
       #  Kotlin
       #  Java
   """.trimMargin("#")
    println(result)
}
```
{kotlin-runnable="true" id="join-strings-trim-margin-kotlin"}

了解更多关于 [多行字符串](coding-conventions.md#字符串)。

## 接下来做什么？ {id="whats-next"}

* 浏览其他 [Kotlin 惯用语法](idioms.md)。
* 学习如何使用 [Java 到 Kotlin 转换器](mixing-java-kotlin-intellij.md#converting-an-existing-java-file-to-kotlin-with-j2k) 将现有的 Java 代码转换为 Kotlin。

如果你有自己喜欢的习惯用法，欢迎通过发送 Pull Request 分享给我们。
