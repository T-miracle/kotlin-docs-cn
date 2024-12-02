[//]: # (title: 标准输入(Standard input))

> Java 的 Scanner 是一个较慢的工具。仅在需要其提供的特定功能时才有必要使用它。  
> 否则，通常更推荐使用 Kotlin 的 `readln()` 方法来[读取标准输入](basic-syntax.md#read-from-the-standard-input)。
>
{style="note"}

要读取标准输入，Java 提供了 `Scanner` 类。Kotlin 提供了两种主要方式来读取标准输入：  
与 Java 类似的 `Scanner` 类，以及 `readln()` 方法。

## 使用 Java Scanner 读取标准输入 {id=read-from-the-standard-input-with-java-scanner}

在 Java 中，标准输入通常通过 `System.in` 对象访问。您需要导入 `Scanner` 类，
创建一个对象，并使用 `.nextLine()` 和 `.nextInt()` 等方法来读取不同的数据类型：

```java
// Java 实现
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        // 读取一行输入。例如：Hi there!
        System.out.print("Enter a line: ");
        String line = scanner.nextLine();
        System.out.println("You entered: " + line);
        // 打印：You entered: Hi there!

        // 读取一个整数。例如：08081990
        System.out.print("Enter an integer: ");
        int number = scanner.nextInt();
        System.out.println("You entered: " + number);
        // 打印：You entered: 08081990

        scanner.close();
    }
}
```

### 在 Kotlin 中使用 Java Scanner {id=use-java-scanner-in-kotlin}

由于 Kotlin 与 Java 库的互操作性，您可以直接在 Kotlin 代码中访问 Java Scanner。

要在 Kotlin 中使用 Java Scanner，您需要导入 `Scanner` 类，并通过传递 `System.in` 对象来初始化它，该对象表示标准输入流并决定如何读取数据。  
您可以使用[可用的读取方法](https://docs.oracle.com/javase/8/docs/api/java/util/Scanner.html)来读取不同于字符串的值，
例如 `.nextLine()`、`.next()` 和 `.nextInt()`：

```kotlin
// 导入 Java Scanner
import java.util.Scanner

fun main() {
    // 初始化 Scanner
    val scanner = Scanner(System.`in`)

    // 读取一整行字符串。例如："Hello, Kotlin"
    val line = scanner.nextLine()
    print(line)
    // Hello, Kotlin

    // 读取一个字符串。例如："Hello"
    val string = scanner.next()
    print(string)
    // Hello

    // 读取一个数字。例如：123
    val num = scanner.nextInt()
    print(num)
    // 123
}
```

使用 Java Scanner 读取输入时，其他有用的方法包括 `.hasNext()`、`.useDelimiter()` 和 `.close()`：

* `.hasNext()` 方法检查输入中是否还有数据可用。如果输入中还有剩余元素可迭代，则返回布尔值 `true`，如果没有更多元素，则返回 `false`。

* `.useDelimiter()` 方法设置读取输入元素的分隔符。默认情况下，分隔符是空白字符，但您可以指定其他字符。例如，`.useDelimiter(",")` 将按逗号分隔输入元素。

* `.close()` 方法关闭与 Scanner 关联的输入流，防止 Scanner 继续用于读取输入。

> 使用完 Java Scanner 后，务必调用 `.close()` 方法。关闭 Java Scanner 会释放其占用的资源，确保程序行为的正确性。
>
{style="note"}

## 使用 readln() 读取标准输入 {id=read-from-the-standard-input-with-readln}

在 Kotlin 中，除了 Java Scanner 之外，还有 `readln()` 方法。它是读取输入的最简单方式。
该方法从标准输入读取一行文本，并将其作为字符串返回：

```kotlin
// 读取一个字符串。例如：Charlotte
val name = readln()

// 读取一个字符串并将其转换为整数。例如：43
val age = readln().toInt()

println("Hello, $name! You are $age years old.")
// 打印：Hello, Charlotte! You are 43 years old.
```

欲了解更多信息，请参阅 [读取标准输入](read-standard-input.md)。