# 译者说

## 注意

本中文翻译文档会尽量及时同步官方文档（非Fork分支），但是不保证每次都能及时更新，如果您介意这一点，请以 [官方文档](https://kotlinlang.org/docs) 为准！

如果您觉得本文档有错误，或者有晦涩难懂的地方，请提交 [issue](https://github.com/T-miracle/kotlin-docs-cn/issues)。

您也可以 Clone [本文档项目](https://github.com/T-miracle/kotlin-docs-cn.git) 进行本地修改，然后给我的分支推送请求。

## 如何启动

使用 `IntelliJ IDEA` 安装 `WriterSide` 插件 或者 直接安装 [`WriterSide` 编辑器](https://www.jetbrains.com/writerside/)

Clone 本项目到本地，直接运行打开即可

## 如何使用 `WriterSide`

请参阅 [官方文档](https://www.jetbrains.com/help/writerside/discover-writerside.html)

## 当前同步官方提交记录 {id=current-commit-id}

非 Fork 分支，本文档的内容来自以下 5 个不同的官方仓库源，使用 `git fetch [仓库名] [分支名]` 获取最新代码，然后进行 `优选` 合并：

| 仓库                                                                       | 分支       | 当前提交版本号                   | 备注           |
|--------------------------------------------------------------------------|----------|---------------------------|--------------|
| [`kotlin-web-site`](https://github.com/JetBrains/kotlin-web-site.git)    | `master` | `%commitId%`              | 主仓库（kr）      |
| [`kotlinx.coroutines`](https://github.com/Kotlin/kotlinx.coroutines.git) | `master` | `%coroutinesCommitId%`    | 协程（kc）       |
| [`lincheck`](https://github.com/JetBrains/lincheck.git)                  | `master` | `%lincheckCommitId%`      | lincheck（kl） |
| [`api-guidelines`](https://github.com/Kotlin/api-guidelines.git)         | `main`   | `%apiGuidelinesCommitId%` | 库作者指南        |
| [`dokka`](https://github.com/Kotlin/dokka.git)                           | `main`   | `%dokkaCommitId%`         | dokka        |