[//]: # (title: 创建你的第一个 Kotlin Notebook)

<tldr>  
   <p>这是<strong>Kotlin Notebook 入门</strong>教程的第二部分。在继续之前，请确保已完成前一步骤。</p>  
   <p><img src="icon-1-done.svg" width="20" alt="第一步"/> <a href="kotlin-notebook-set-up-env.md">设置环境</a><br/>  
      <img src="icon-2.svg" width="20" alt="第二步"/> <strong>创建 Kotlin Notebook</strong><br/>  
      <img src="icon-3-todo.svg" width="20" alt="第三步"/> 向 Kotlin Notebook 添加依赖项<br/>  
  </p>  
</tldr>  

在这里，你将学习如何创建你的第一个 [Kotlin Notebook](kotlin-notebook-overview.md)，执行简单操作，并运行代码单元。

## 创建空项目 {id=create-an-empty-project}

1. 在 IntelliJ IDEA 中，选择 **File | New | Project**。
2. 在左侧面板选择 **New Project**。
3. 为新项目命名，必要时可更改存储位置。

   > 勾选 **Create Git repository** 复选框可将新项目置于版本控制下。
   > 你也可以随时后续添加版本控制。
   >
   {style="tip"}

4. 在 **Language** 列表中选择 **Kotlin**。

   ![创建新的 Kotlin Notebook 项目](new-notebook-project.png){width=700}

5. 选择 **IntelliJ** 构建系统。
6. 从 **JDK list** 中选择要用于项目的 [JDK](https://www.oracle.com/java/technologies/downloads/)。
7. 启用 **Add sample code** 选项来创建包含示例 `"Hello World!"` 应用的文件。

   > 你还可以启用 **Generate code with onboarding tips** 选项为示例代码添加有用的注释说明。
   >
   {style="tip"}

8. 点击 **Create**。

## 创建 Kotlin Notebook {id=create-a-kotlin-notebook}

1. 要创建新笔记本，选择 **File | New | Kotlin Notebook**，或右键点击文件夹选择 **New | Kotlin Notebook**。

   ![创建新的 Kotlin Notebook](new-notebook.png){width=700}

2. 设置新笔记本名称（例如 **first-notebook**）后按 **Enter**。
   将打开名为 **first-notebook.ipynb** 的 Kotlin Notebook 新标签页。
3. 在打开的标签页中，在代码单元格输入以下代码：

   ```kotlin
   println("Hello, this is a Kotlin Notebook!")
   ```
4. 要运行代码单元格，点击 **Run Cell and Select Below** ![运行单元格并选择下方](run-cell-and-select-below.png){width=30}{type="joined"} 按钮或按 **Shift** + **Enter**。
5. 点击 **Add Markdown Cell** 按钮添加 Markdown 单元格。
6. 在单元格中输入 `# Example operations`，并以运行代码单元格的相同方式运行它以渲染内容。
7. 在新代码单元格中输入 `10 + 10` 并运行。
8. 在代码单元格中定义变量，例如 `val a = 100`。

   > 当你运行包含已定义变量的代码单元格后，这些变量将在所有其他代码单元格中可用。
   >
   {style="tip"}

9. 创建新代码单元格并添加 `println(a * a)`。
10. 使用 **Run All** ![全部运行按钮](run-all-button.png){width=30}{type="joined"} 按钮运行笔记本中的所有代码和 Markdown 单元格。

    ![第一个笔记本](first-notebook.png){width=700}

恭喜！你已成功创建第一个 Kotlin Notebook。

## 创建临时 Kotlin Notebook {id=create-a-scratch-kotlin-notebook}

从 IntelliJ IDEA 2024.1.1 开始，你也可以将 Kotlin Notebook 创建为临时文件。

[临时文件](https://www.jetbrains.com/help/idea/scratches.html#create-scratch-file)
允许你测试小型代码片段，而无需创建新项目或修改现有项目。

创建临时 Kotlin Notebook 的步骤：

1. 点击 **File | New | Scratch File**
2. 从 **New Scratch File** 列表中选择 **Kotlin Notebook**

   ![临时笔记本](kotlin-notebook-scratch-file.png){width=400}

## 下一步 {id=next-step}

在教程的下一部分，你将学习如何向 Kotlin Notebook 添加依赖项。

**[进入下一章](kotlin-notebook-add-dependencies.md)**
