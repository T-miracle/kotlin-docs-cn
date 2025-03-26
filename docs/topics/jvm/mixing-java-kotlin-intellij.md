[//]: # (title: 在同一个项目中混合使用 Java 和 Kotlin - 教程)

Kotlin 提供了与 Java 的一等互操作性，现代 IDE 让它变得更好。
在本教程中，你将学习如何在 IntelliJ IDEA 中的同一个项目里同时使用 Kotlin 和 Java 源代码。
要了解如何在 IntelliJ IDEA 中启动一个新的 Kotlin 项目，请参阅
[IntelliJ IDEA 入门指南](jvm-get-started.md)。

## 向已有的 Kotlin 项目添加 Java 源代码 {id=adding-java-source-code-to-an-existing-kotlin-project}

向 Kotlin 项目中添加 Java 类非常简单。你只需要创建一个新的 Java 文件即可。
在项目中的某个目录或包上，选择 **File** | **New** | **Java Class**，或者使用 **Alt + Insert** / **Cmd + N** 快捷键。

![添加新的 Java 类](new-java-class.png){width=400}

如果你已经有了 Java 类文件，可以直接将它们复制到项目目录里。

现在，你可以在 Kotlin 中调用 Java 类，或者反过来在 Java 中调用 Kotlin 类，完全不需要额外的操作。
 
比如，添加下面这个 Java 类：

``` java
public class Customer {

    private String name;

    public Customer(String s){
        name = s;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void placeOrder() {
        System.out.println("A new order is placed by " + name);
    }
}
```

你就可以在 Kotlin 里像调用普通 Kotlin 类型一样使用它了：

```kotlin
val customer = Customer("Phase")
println(customer.name)
println(customer.placeOrder())
```

## 向已有的 Java 项目添加 Kotlin 源代码 {id=adding-kotlin-source-code-to-an-existing-java-project}

向已有的 Java 项目添加 Kotlin 文件的方式基本相同。

![添加新的 Kotlin 文件类](new-kotlin-file.png){width=400}

如果这是你第一次在项目中添加 Kotlin 文件，IntelliJ IDEA 会自动添加所需的 Kotlin 运行时库。

![打包 Kotlin 运行时](bundling-kotlin-option.png){width=350}

你也可以手动打开 Kotlin 运行时配置，路径是 **Tools** | **Kotlin** | **Configure Kotlin in Project**。

## 使用 J2K 将现有 Java 文件转换为 Kotlin {id=converting-an-existing-java-file-to-kotlin-with-j2k}

Kotlin 插件还内置了一个 Java 转 Kotlin 转换器（_J2K_），它可以自动将 Java 文件转换成 Kotlin 文件。
要对文件使用 J2K，可以在文件的右键菜单或 IntelliJ IDEA 的 **Code** 菜单中选择 **Convert Java File to Kotlin File**。

![将 Java 转换为 Kotlin](convert-java-to-kotlin.png){width=500}

虽然这个转换器并不完美，但大部分 Java 的样板代码它都能转换得相当不错。  
不过，有时可能还是需要手动调整一下。
