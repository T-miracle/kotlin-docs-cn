[//]: # (title: 异步编程技术)

几十年来，作为开发者，我们一直面临一个需要解决的问题——如何防止我们的应用程序发生阻塞。
无论我们是在开发桌面、移动还是服务器端应用程序，我们都希望避免让用户等待，或者更糟的是，造成瓶颈，限制了应用程序的扩展。

有许多方法可以解决这个问题，包括：

* [线程](#threading)
* [回调](#callbacks)
* [Futures、promises 和其他](#futures-promises-and-others)
* [响应式扩展](#reactive-extensions)
* [协程](#coroutines)

在解释什么是协程之前，我们先简要回顾一下其他一些解决方案。

## 线程 {id=threading}

线程无疑是避免应用程序阻塞最为人熟知的方法。

```kotlin
fun postItem(item: Item) {
    val token = preparePost()
    val post = submitPost(token, item)
    processPost(post)
}

fun preparePost(): Token {
    // 发起请求，从而阻塞主线程
    return token
}
```

假设上面的代码中 `preparePost` 是一个耗时的过程，因此会阻塞用户界面。
我们可以将其启动在一个单独的线程中。这样就可以避免阻塞 UI。这是一种非常常见的技术，但也有一系列的缺点：

* 线程并不低廉。线程需要上下文切换，代价较高。
* 线程不是无限的。可以启动的线程数量受限于底层操作系统。在服务器端应用程序中，这可能会导致重大瓶颈。
* 线程并不总是可用的。有些平台，如 JavaScript，甚至不支持线程。
* 线程并不容易。调试线程和避免竞态条件是多线程编程中常见的问题。 

## 回调 {id=callbacks}

使用回调，思想是将一个函数作为参数传递给另一个函数，并在进程完成后调用它。

```kotlin
fun postItem(item: Item) {
    preparePostAsync { token -> 
        submitPostAsync(token, item) { post -> 
            processPost(post)
        }
    }
}

fun preparePostAsync(callback: (Token) -> Unit) {
    // 发起请求并立即返回
    // 安排稍后调用回调
}
```

这从原则上看是一个更优雅的解决方案，但同样也存在一些问题：

* 嵌套回调的难题。通常用作回调的函数，往往最终需要自己的回调。
这导致了一系列嵌套的回调，导致代码难以理解。这个模式通常被称为“圣诞树代码”（花括号表示树的分支）。
* 错误处理变得复杂。嵌套模型使得错误处理和这些错误的传播更加复杂。

在事件循环架构中，如 JavaScript，回调非常常见。然而，即便在这种情况下，人们也越来越倾向于使用其他方法，如 promises 或 响应式编程。

## Future、Promise 和 其他 {id=futures-promises-and-others}

Future 或 Promise（根据语言/平台的不同，可能还有其他术语）的思想理念是，当我们发起调用时，
我们被承诺在某个时刻会返回一个名为 Promise 的对象，然后可以对其进行操作。

```kotlin
fun postItem(item: Item) {
    preparePostAsync() 
        .thenCompose { token -> 
            submitPostAsync(token, item)
        }
        .thenAccept { post -> 
            processPost(post)
        }
         
}

fun preparePostAsync(): Promise<Token> {
    // 发起请求并返回一个稍后完成的 promise
    return promise 
}
```

这种方法需要在编程方式上做一系列的改变，特别是：

* 不同的编程模型。类似于回调，编程模型从一个自上而下的命令式方法转向一个由链式调用组成的组合模型。
在这个模型中，传统的程序结构，如循环、异常处理等，通常已经无效。
* 不同的 API。通常需要学习一个全新的 API，如 `thenCompose` 或 `thenAccept`，这些用法或写法在不同的平台之间可能有一定的差异。
* 特定的返回类型。返回类型从我们需要的实际数据转向一个新的类型 `Promise`，需要进行内省。
* 错误处理可能变得复杂。错误的传播和链式调用通常不会很直观。

## 响应式扩展 {id=reactive-extensions}

响应式扩展（Rx）由 [Erik Meijer](https://en.wikipedia.org/wiki/Erik_Meijer_(computer_scientist)) 引入到 C# 中。
尽管它在 .NET 平台上确实使用过，但直到 Netflix 将其移植到 Java 并命名为 RxJava 后，它才真正获得了主流的接受。
从那时起，许多平台都提供了 Rx 的移植版，包括 JavaScript（RxJS）。

Rx 背后的理念是转向所谓的 `可观察流`，在这种模型中，我们将数据视为流（无限量的数据），这些流可以被观察。
从实际应用来看，Rx 实质上是带有一系列扩展的 [观察者模式](https://en.wikipedia.org/wiki/Observer_pattern)，这些扩展允许我们对数据进行操作。

在方法上，它与 Future 类似，但可以将 Future 视为返回一个离散的元素，而 Rx 返回一个流。
然而，与之前的方法类似，它也引入了一种全新的编程模型思维方式，如下名言：

    "一切都是流，且可以被观察"
    
这意味着在解决问题时采用不同的方法，与我们在编写同步代码时的习惯有明显的变化。
与 Future 相比的一个好处是，由于它被移植到许多平台，我们通常可以在使用 C#、Java、JavaScript 或任何其他支持 Rx 的语言时找到一致的 API 体验。

此外，Rx 也引入了一种更友好的错误处理方式。

## 协程 {id=coroutines}

Kotlin 处理异步代码的方法是使用协程，协程的核心理念是可挂起的计算，即函数可以在某个时间点挂起执行，并在之后某个时间点恢复。

然而，协程有一个优势，对于开发者而言，编写非阻塞代码与编写阻塞代码几乎没有区别。
编程模型本身并没有发生什么改变。

例如，以下代码：

```kotlin
fun postItem(item: Item) {
    launch {
        val token = preparePost()
        val post = submitPost(token, item)
        processPost(post)
    }
}

suspend fun preparePost(): Token {
    // 发起请求并挂起协程
    return suspendCoroutine { /* ... */ } 
}
```

这段代码将在不阻塞主线程的情况下启动一个长期运行的操作。
`preparePost` 是所谓的 `挂起函数`，因此在其前面有 `suspend` 关键字。
这意味着如前所述，该函数会先开始执行，然后暂停执行，并在某个时间点又恢复执行。

* 函数签名保持完全不变。唯一的区别是添加了 `suspend` 关键字。但返回类型仍然是我们希望返回的类型。
* 代码仍然以编写同步代码的方式编写，自上而下，不需要任何特殊的语法，除了调用一个名为 `launch` 的函数，它本质上启动协程（在其他教程中会介绍）。
* 编程模型和 API 保持不变。我们可以继续使用循环、异常处理等，无需学习一整套新的 API。
* 它是平台无关的。无论我们是针对 JVM、JavaScript 还是其他平台，所编写的代码都是相同的。在后台，编译器负责将其适配到每个平台。

协程并不是一个新概念，更不是 Kotlin 发明的。它们已经存在了几十年，并在其他一些编程语言中（如 Go）很受欢迎。
值得注意的是，Kotlin 中的协程实现方式是将大部分功能委托给库。
实际上，除了 `suspend` 关键字外，语言中没有添加其他关键字。
这与 C# 等语言有所不同，C# 的语法中有 `async` 和 `await`。在 Kotlin 中，这些只是库函数。

有关更多信息，请参见 [协程参考](coroutines-overview.md)。
