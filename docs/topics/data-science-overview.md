[//]: # (title: 用于数据科学的 Kotlin)

从构建数据管道到将机器学习模型投入生产，Kotlin 可以是处理数据的一个很好的选择：
* Kotlin 简洁、可读性强，易于学习。
* 静态类型和空安全性有助于创建可靠、易于维护且易于排除故障的代码。
* 作为 JVM 语言，Kotlin 提供出色的性能，并能够利用经过验证的 Java 库的整个生态系统。

## 交互式编辑器、

笔记本，如 [Kotlin Notebook](https://plugins.jetbrains.com/plugin/16340-kotlin-notebook)、
[Jupyter Notebook](https://jupyter.org/)
和 [Datalore](http://jetbrains.com/datalore) 提供了用于数据可视化和探索性研究的便捷工具。
Kotlin 与这些工具集成，帮助你探索数据、与同事分享发现，或提升数据科学和机器学习技能。

### Kotlin Notebook

[Kotlin Notebook](https://plugins.jetbrains.com/plugin/16340-kotlin-notebook) 是 IntelliJ IDEA 的插件，允许您在 Kotlin 中创建笔记本。
它利用 [Kotlin 内核](#jupyter-kotlin-kernel) 执行单元格，并利用强大的 Kotlin IDE 支持提供实时代码洞察力。
它现在是使用 Kotlin 笔记本的首选方法。
务必查看我们的[博客文章](https://blog.jetbrains.com/kotlin/2023/07/introducing-kotlin-notebook/)了解更多信息。

![Kotlin Notebook](kotlin-notebook.png){width=800}

### Datalore 中的 Kotlin 笔记本 {id=kotlin-notebooks-in-datalore}

使用 Datalore，你可以直接在浏览器中使用 Kotlin，无需安装。
你还可以实时协作编写 Kotlin 笔记本，编写代码时获得智能代码辅助，并将结果分享为交互式或静态报告。
查看一个 [示例报告](https://datalore.jetbrains.com/view/report/9YLrg20eesVX2cQu1FKLiZ)。

![Kotlin 在 Datalore 中](kotlin-datalore.png){width=800}

[注册并使用免费的 Datalore Community 账户来使用 Kotlin](https://datalore.jetbrains.com/)。

### Jupyter Kotlin kernel

Jupyter Notebook 是一个开源的 Web 应用程序，允许您创建和共享包含代码、可视化和 Markdown 文本的文档（即“笔记本”）。
[Kotlin-jupyter](https://github.com/Kotlin/kotlin-jupyter) 是一个开源项目，将 Kotlin 支持引入到 Jupyter Notebook 中。

![Kotlin 在 Jupyter 笔记本中的应用](kotlin-jupyter-kernel.png){width=800}

请查看 Kotlin 内核的 [GitHub 仓库](https://github.com/Kotlin/kotlin-jupyter) 获取安装说明、文档和示例。

## 库 {id=libraries}

由 Kotlin 社区创建的与数据相关任务有关的库生态系统正在迅速扩展。以下是一些您可能会发现有用的库：

### Kotlin 库

* [Kotlin DataFrame](https://github.com/Kotlin/dataframe) 是一个用于结构化数据处理的库。
  它旨在通过利用 Kotlin 语言的全部功能以及在 Jupyter 笔记本和 REPL 中的间歇性代码执行，调和 Kotlin 的静态类型与数据的动态特性。

* [Kandy](https://kotlin.github.io/kandy/welcome.html) 是一个为 JVM 编写的开源绘图库，使用 Kotlin 编写。
  它提供了一个强大而灵活的 DSL 用于图表创建，
  并与 [Kotlin Notebook](https://plugins.jetbrains.com/plugin/16340-kotlin-notebook)
  和 [Kotlin DataFrame](https://kotlin.github.io/dataframe/gettingstarted.html) 无缝集成。

* [Multik](https://github.com/Kotlin/multik): Kotlin 中的多维数组库。该库提供了适用于多维数组的 Kotlin 语言惯用、类型安全和维度安全的 API，用于数学运算。Multik 提供可互换的 JVM 和本地计算引擎，以及两者的组合，以实现最佳性能。

* [KotlinDL](https://github.com/jetbrains/kotlindl) 是一个使用 Kotlin 编写、受 Keras 启发的高级深度学习 API。它提供了用于从头开始训练深度学习模型、导入现有 Keras 模型进行推断，以及利用迁移学习调整现有预训练模型以适应您的任务的简单 API。

* [Kotlin for Apache Spark](https://github.com/JetBrains/kotlin-spark-api) 在 Kotlin 和 Apache Spark 之间添加了一层缺失的兼容性。它允许 Kotlin 开发人员使用熟悉的语言特性，如数据类，以及在花括号或方法引用中的简单表达式。

* [kmath](https://github.com/mipt-npm/kmath) 是一个实验性库，最初受到 [NumPy](https://numpy.org/) 的启发，但发展出了更灵活的抽象。
  它实现了在 Kotlin 类型上的代数结构中的数学运算，定义了线性结构、表达式、直方图、流式操作的 API，
  提供了对现有 Java 和 Kotlin 库的可互换包装，包括
  [ND4J](https://github.com/eclipse/deeplearning4j/tree/master/nd4j)、[Commons Math](https://commons.apache.org/proper/commons-math/)、[Multik](https://github.com/Kotlin/multik) 等。

* [lets-plot](https://github.com/JetBrains/lets-plot) 是用 Kotlin 编写的用于统计数据的绘图库。Lets-Plot 是多平台的，不仅可以与 JVM 一起使用，还可以与 JS 和 Python 一起使用。

* [kravis](https://github.com/holgerbrandl/kravis) 是受到 R 的 [ggplot](https://ggplot2.tidyverse.org/) 启发的用于可视化表格数据的另一个库。

* [londogard-nlp-toolkit](https://github.com/londogard/londogard-nlp-toolkit/) 是一个库，提供在自然语言处理中处理诸如词/子词/句子嵌入、词频、停用词、词干化等时的实用工具。

### Java 库

由于 Kotlin 提供了与 Java 的一流互操作性，您还可以在 Kotlin 代码中使用 Java 数据科学库。以下是一些示例：

* [DeepLearning4J](https://deeplearning4j.konduit.ai) - 用于 Java 的深度学习库

* [ND4J](https://github.com/eclipse/deeplearning4j/tree/master/nd4j) - 用于 JVM 的高效矩阵数学库

* [Dex](https://github.com/PatMartin/Dex) - 一个基于 Java 的数据可视化工具

* [Smile](https://github.com/haifengl/smile) - 一个全面的机器学习、自然语言处理、线性代数、图形、插值和可视化系统。
  除了 Java API，Smile 还提供了一个功能性的 [Kotlin API](https://haifengl.github.io/api/kotlin/index.html)，以及 Scala 和 Clojure API。
  * [Smile-NLP-kt](https://github.com/londogard/smile-nlp-kt) - 一个将 Smile 的自然语言处理部分的 Scala 隐式转换重写为 Kotlin 扩展函数和接口的项目。

* [Apache Commons Math](https://commons.apache.org/proper/commons-math/) - 用于 Java 的通用数学、统计和机器学习库

* [NM Dev](https://nm.dev/) - 一个涵盖所有经典数学的 Java 数学库。

* [OptaPlanner](https://www.optaplanner.org/) - 用于优化规划问题的求解器工具

* [Charts](https://github.com/HanSolo/charts) - 一款正在开发中的科学 JavaFX 图表库

* [Apache OpenNLP](https://opennlp.apache.org/) - 用于处理自然语言文本的基于机器学习的工具包

* [CoreNLP](https://stanfordnlp.github.io/CoreNLP/) - 一个自然语言处理工具包

* [Apache Mahout](https://mahout.apache.org/) - 一个用于回归、聚类和推荐的分布式框架

* [Weka](https://www.cs.waikato.ac.nz/ml/index.html) - 用于数据挖掘任务的一组机器学习算法集合

* [Tablesaw](https://github.com/jtablesaw/tablesaw) - 一个 Java 数据框架。它包括基于 Plot.ly 的可视化库
