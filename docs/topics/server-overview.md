[//]: # (title: 用于服务器端的 Kotlin)

Kotlin 在开发服务器端应用程序方面表现出色。它允许你编写简洁而富有表现力的代码，同时保持与现有基于 Java 的技术堆栈的完全兼容性，而且学习曲线平缓：

- **表达力**：Kotlin 的创新语言特性，例如其对 [类型安全构建器](type-safe-builders.md) 和 [委托属性](delegated-properties.md) 的支持，有助于构建强大且易于使用的抽象。
- **可扩展性**：Kotlin 对 [协程](coroutines-overview.md) 的支持，有助于构建能够在硬件资源有限的情况下处理大量客户端的服务端应用程序。
- **互操作性**：Kotlin 与所有基于 Java 的框架完全兼容，因此你可以在保留熟悉技术栈的同时，享受到更现代语言带来的优势。
- **迁移能力**：Kotlin 支持从 Java 到 Kotlin 的大型代码库逐步迁移。你可以开始用 Kotlin 编写新代码，同时保留系统中旧有的 Java 部分。
- **工具支持**：除了广泛的 IDE 支持外，Kotlin 还在 IntelliJ IDEA Ultimate 插件中提供了针对特定框架（例如 Spring 和 Ktor）的专用工具支持。
- **学习曲线**：对于 Java 开发者来说，入门 Kotlin 十分容易。Kotlin 插件内置的 Java 到 Kotlin 自动转换器能够帮助你迈出第一步。[Kotlin 习题集](koans.md) 通过一系列交互式练习引导你掌握关键语言特性。像 [Ktor](https://ktor.io/) 这样的 Kotlin 专用框架，提供了一种简单直观的方式，避免了大型框架中隐藏的复杂性。

## 使用 Kotlin 进行服务器端开发的框架

以下是 Kotlin 的一些服务器端框架示例：

* [Spring](https://spring.io) 利用 Kotlin 的语言特性，从 5.0 版本开始提供了 [更简洁的 API](https://spring.io/blog/2017/01/04/introducing-kotlin-support-in-spring-framework-5-0)。
  [在线项目生成器](https://start.spring.io/#!language=kotlin)允许你快速生成一个新的 Kotlin 项目。

* [Ktor](https://github.com/kotlin/ktor) 是由 JetBrains 构建的用于在 Kotlin 中创建 Web 应用程序的框架，利用协程实现高可扩展性，并提供易于使用和惯用的 API。

* [Quarkus](https://quarkus.io/guides/kotlin) 对使用 Kotlin 提供了一流的支持。该框架是由 Red Hat 维护的开源框架。
  Quarkus 是从头开始为 Kubernetes 构建的，并通过利用一个不断增长的数百个最佳库的列表，提供了一个完整的协同全栈框架。

* [Vert.x](https://vertx.io) 是用于在 JVM 上构建反应式 Web 应用程序的框架，为 Kotlin 提供了 [专门的支持](https://github.com/vert-x3/vertx-lang-kotlin)，包括 [完整的文档](https://vertx.io/docs/vertx-core/kotlin/)。

* [kotlinx.html](https://github.com/kotlin/kotlinx.html) 是一种 DSL，可用于在 Web 应用程序中构建 HTML。它作为传统模板系统（如 JSP 和 FreeMarker）的替代方案。

* [Micronaut](https://micronaut.io/) 是一个现代的基于 JVM 的全栈框架，用于构建模块化、易于测试的微服务和无服务器应用程序。它具有许多有用的内置功能。

* [http4k](https://http4k.org/) 是用于 Kotlin HTTP 应用程序的功能工具包，体积很小，纯 Kotlin 编写。
  该库基于 Twitter 的 "Your Server as a Function" 论文，将建模 HTTP 服务器和客户端的概念简化为可以组合在一起的简单 Kotlin 函数。

* [Javalin](https://javalin.io) 是一个非常轻量级的 Kotlin 和 Java Web 框架，支持 WebSockets、HTTP2 和异步请求。

* 持久性的可用选项包括直接使用 JDBC 访问、JPA，以及通过它们的 Java 驱动程序使用 NoSQL 数据库。
  对于 JPA，[kotlin-jpa 编译器插件](no-arg-plugin.md#jpa-support)使 Kotlin 编译的类适应了该框架的要求。
  
> 您可以在 [https://kotlin.link/](https://kotlin.link/resources) 找到更多框架。
>
{style="note"}

## 部署 Kotlin 服务器端应用程序 {id="部署Kotlin服务器端应用程序"}

Kotlin 应用程序可以部署到支持 Java Web 应用程序的任何主机，包括Amazon Web Services、Google Cloud Platform 等。

要在 [Heroku](https://www.heroku.com) 上部署 Kotlin 应用程序，可以按照
[官方 Heroku 教程](https://devcenter.heroku.com/articles/getting-started-with-kotlin) 进行操作。

AWS Labs 提供了一个 [示例项目](https://github.com/awslabs/serverless-photo-recognition)，演示了使用 Kotlin 编写
[AWS Lambda](https://aws.amazon.com/lambda/) 函数的用法。

Google Cloud Platform 提供了一系列教程，用于将 Kotlin 应用程序部署到 GCP，包括
[Ktor 和 App Engine](https://cloud.google.com/community/tutorials/kotlin-ktor-app-engine-java8)
以及 [Spring 和 App Engine](https://cloud.google.com/community/tutorials/kotlin-springboot-app-engine-java8)。
此外，还有一个 [交互式代码实验](https://codelabs.developers.google.com/codelabs/cloud-spring-cloud-gcp-kotlin)，用于部署 Kotlin Spring 应用程序。

## 在服务器端使用 Kotlin 的产品 {id="在服务器端使用Kotlin的产品"}

[Corda](https://www.corda.net/) 是一个由主要银行支持并完全用 Kotlin 构建的开源分布式账本平台。

[JetBrains Account](https://account.jetbrains.com/) 是 JetBrains 负责整个许可证销售和验证过程的系统，完全使用 Kotlin 编写，自 2015 年以来在生产环境中运行，没有出现重大问题。

[Chess.com](https://www.chess.com/) 是一个专注于国际象棋及全球数百万棋迷的网站。
Chess.com 使用 Ktor 来无缝配置多个 HTTP 客户端。

[Adobe](https://blog.developer.adobe.com/streamlining-server-side-app-development-with-kotlin-be8cf9d8b61a) 的工程师使用 Kotlin 进行服务端应用开发，并在 Adobe Experience
Platform 中使用 Ktor 进行原型开发，该平台能够帮助组织集中并标准化客户数据，然后应用数据科学和机器学习。


## 下一步

- 若想更深入了解这门语言，请查看本站上的 Kotlin 文档以及 [Kotlin 习题集](koans.md)。
- 探索如何 [使用 Ktor 构建异步服务端应用](https://ktor.io/docs/server-create-a-new-project.html)，该框架基于 Kotlin 协程构建。
- 观看网络研讨会 [“Micronaut for microservices with Kotlin”](https://micronaut.io/2020/12/03/webinar-micronaut-for-microservices-with-kotlin/)，并查看详细的 [指南](https://guides.micronaut.io/latest/micronaut-kotlin-extension-fns.html)，了解如何在 Micronaut 框架中使用 [Kotlin 扩展函数](extensions.md#extension-functions)。
- http4k 提供了用于生成完整项目的 [命令行工具](https://toolbox.http4k.org)，以及一个 [starter 仓库](https://start.http4k.org)，通过一条 bash 命令即可使用 GitHub、Travis 和 Heroku 构建整个 CD 流水线。
- 想从 Java 迁移到 Kotlin？学习如何在 Java 与 Kotlin 中执行 [字符串的常见操作](java-to-kotlin-idioms-strings.md)。

