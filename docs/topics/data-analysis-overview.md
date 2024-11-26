[//]: # (title: Kotlin 用于数据分析)

探索和分析数据可能不是你每天都做的事情，但它是作为软件开发人员所需的一个关键技能。

让我们思考一些数据分析在软件开发中的关键作用：在调试时分析集合中的实际内容，深入研究内存转储或数据库，或在处理
REST APIs 时接收包含大量数据的 JSON 文件，这些只是其中的一些例子。

使用 Kotlin 的探索性数据分析（EDA）工具，例如 [Kotlin notebooks](#notebooks)，[Kotlin DataFrame](#kotlin-dataframe)
和 [Kandy](#kandy)，你可以利用一系列丰富的功能来提升你的分析技能，并在不同的场景中支持你：

* **加载、转换和可视化各种格式的数据：** 使用我们的 Kotlin EDA 工具，你可以执行如过滤、排序和聚合数据等任务。
我们的工具可以无缝地从不同的文件格式（包括 CSV、JSON 和 TXT）中读取数据，直接在 IDE 中处理。

  我们的绘图工具 Kandy 允许你创建各种图表，以可视化数据集并获得洞察。

* **高效分析存储在关系数据库中的数据：** Kotlin DataFrame 无缝集成数据库，并提供类似于 SQL 查询的功能。
你可以直接从各种数据库中检索、操作和可视化数据。

* **从 Web API 获取和分析实时及动态数据集：** EDA 工具的灵活性允许通过诸如 OpenAPI 等协议与外部 API 集成。
此功能帮助你从 Web API 获取数据，然后根据需要清洗和转换数据。

你想尝试我们的 Kotlin 数据分析工具吗？

<a href="get-started-with-kotlin-notebooks.md"><img src="kotlin-notebooks-button.svg" width="600" style="block" alt="开始使用 Kotlin Notebook" style="block"/></a>

我们的 Kotlin 数据分析工具让你能够从头到尾流畅地处理数据。
只需简单的拖放功能，就能在 Kotlin Notebook 中轻松获取数据。使用几行代码即可对数据进行清理、转换和可视化。
此外，还可以在几次点击内导出你的输出图表。

![Kotlin Notebook](data-analysis-notebook.gif){width=700}

## Notebooks {id=notebooks}

_Notebooks_ 是将代码、图形和文本集成在一个环境中的编辑器。
在使用 Notebooks 时，您可以运行代码单元并立即查看输出。

Kotlin 提供了不同的 Notebooks  解决方案，例如 [Kotlin Notebook](#kotlin-notebook)、[Datalore](#kotlin-notebooks-in-datalore)
和 [Kotlin-Jupyter Notebook](#jupyter-notebook-with-kotlin-kernel)，这些解决方案提供了便捷的数据检索、转换、探索、建模等功能。
这些 Kotlin Notebooks 解决方案基于我们的 [Kotlin Kernel](https://github.com/Kotlin/kotlin-jupyter)。

您可以在 Kotlin Notebook、Datalore 和 Kotlin-Jupyter Notebook 之间无缝地共享代码。
在其中一个 Kotlin  Notebooks  中创建一个项目，并在另一个编辑器中继续工作，且不会遇到兼容性问题。

利用我们强大的 Kotlin Notebooks 的功能，以及使用 Kotlin 编码的好处。
Kotlin 与这些编辑器集成，帮助您管理数据，并与同事分享您的发现，同时提升数据科学和机器学习技能。

探索我们不同的 Kotlin Notebooks 解决方案的功能，并选择最符合您项目需求的方案。

![Kotlin Notebook](kotlin-notebook.png){width=700}

### Kotlin Notebook {id=kotlin-notebook}

[Kotlin Notebook](kotlin-notebook-overview.md) 是一个 IntelliJ IDEA 的插件，允许您在 Kotlin 中创建 Notebooks。
它提供了我们 IDE 的体验，具有所有常见的 IDE 特性，提供实时代码洞察和项目集成。

### Datalore 中的 Kotlin Notebooks {id=kotlin-notebooks-in-datalore}

通过 [Datalore](https://datalore.jetbrains.com/)，您可以直接在浏览器中使用 Kotlin，无需额外安装。
您还可以共享您的 Notebooks 并远程运行它们，与其他 Kotlin Notebooks 实时协作，
在编写代码时获得智能编码辅助，并通过交互式或静态报告导出结果。

### 带 Kotlin 内核的 Jupyter Notebook {id=jupyter-notebook-with-kotlin-kernel}

[Jupyter Notebook](https://jupyter.org/) 是一个开源的 web 应用程序，允许您创建和分享包含代码、可视化和 Markdown 文本的文档。
[Kotlin-Jupyter](https://github.com/Kotlin/kotlin-jupyter) 是一个开源项目，将 Kotlin 支持引入 Jupyter Notebook，以在
Jupyter 环境中利用 Kotlin 的强大功能。

## Kotlin DataFrame {id=kotlin-dataframe}

[Kotlin DataFrame](https://kotlin.github.io/dataframe/overview.html) 库允许您在 Kotlin 项目中操作结构化数据。
从数据创建和清理到深入分析和特征工程，这个库都能满足您的需求。

使用 Kotlin DataFrame 库，您可以处理不同的文件格式，包括 CSV、JSON、XLS 和 XLSX。
该库还通过连接 SQL 数据库或 API 方便了数据检索过程。

![Kotlin DataFrame](data-analysis-dataframe-example.png){width=700}

## Kandy {id=kandy}

[Kandy](https://kotlin.github.io/kandy/welcome.html) 是一个开源的 Kotlin 库，提供了一个强大且灵活的领域特定语言 (DSL) 用于绘制各种类型的图表。
该库是一个简单、符合 Kotlin 风格、可读性强且类型安全的可视化工具。

Kandy 与 Kotlin Notebook、Datalore 和 Kotlin-Jupyter Notebook 无缝集成。
您还可以轻松地将 Kandy 和 Kotlin DataFrame 库结合使用，以完成不同的数据相关任务。

![Kandy](data-analysis-kandy-example.png){width=700}

## 接下来做什么 {id=whats-next}

* [开始使用 Kotlin Notebook。](get-started-with-kotlin-notebooks.md)
* [使用 Kotlin DataFrame 库检索和转换数据。](data-analysis-work-with-data-sources.md)
* [使用 Kandy 库可视化数据。](data-analysis-visualization.md)
* [了解更多关于 Kotlin 和 Java 数据分析库的信息。](data-analysis-libraries.md)