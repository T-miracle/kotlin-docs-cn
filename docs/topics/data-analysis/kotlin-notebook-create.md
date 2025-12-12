[//]: # (title: 创建你的第一个 Kotlin Notebook)

<tldr>  
   <p>这是<strong>Kotlin Notebook 入门</strong>教程的第二部分。在继续之前，请确保已完成前一步骤。</p>  
   <p><img src="icon-1-done.svg" width="20" alt="第一步"/> <a href="kotlin-notebook-set-up-env.md">设置环境</a><br/>  
      <img src="icon-2.svg" width="20" alt="第二步"/> <strong>创建 Kotlin Notebook</strong><br/>  
      <img src="icon-3-todo.svg" width="20" alt="第三步"/> 向 Kotlin Notebook 添加依赖项<br/>  
  </p>  
</tldr>  

You can start working with your [Kotlin Notebook](kotlin-notebook-overview.md) in three main ways:

* [Create a new project that includes a Kotlin Notebook](#create-a-new-project) 
* [Add a Kotlin Notebook to an existing project](#add-a-new-kotlin-notebook-to-your-project)
* [Create a scratch Kotlin Notebook](#create-a-scratch-kotlin-notebook)

## Create a new project 

To create a new project that includes a Kotlin Notebook:

1. In IntelliJ IDEA's welcome screen, select **Kotlin Notebook** | **New Notebook** from the panel on the left.
2. Enter a **Name** for your new notebook and select **Type** | **In Folder**.
   * **Scratch:** This option is to create a scratch notebook without adding it to a project.
   * **In Folder:** This option is to create a notebook within a project. You have to specify the project's location.
3. Click **Create**.

![Create a new Kotlin Notebook from IntelliJ IDEA](create-notebook-welcome.png){width=700}

Your new Kotlin Notebook is created within a new project.

Alternatively, create an empty project and then [add a Kotlin Notebook](#add-a-new-kotlin-notebook-to-your-project):

1. 在 IntelliJ IDEA 中，选择 **File | New | Project**。
2. 在左侧面板选择 **New Project**。
3. 为新项目命名，必要时可更改存储位置。

   > 勾选 **Create Git repository** 复选框可将新项目置于版本控制下。
   > 你也可以随时后续添加版本控制。
   >
   {style="tip"}

4. 在 **Language** 列表中选择 **Kotlin**。

   ![创建新的 Kotlin Notebook 项目](new-notebook-project.png){width=700}

5. Select the **IntelliJ** build system.
6. From the **JDK** list, select the [JDK](https://www.oracle.com/java/technologies/downloads/) that you want to use in your project.
7. Enable the **Add sample code** option to create a file with a sample `"Hello World!"` application.

   > 你还可以启用 **Generate code with onboarding tips** 选项为示例代码添加有用的注释说明。
   >
   {style="tip"}

8. 点击 **Create**。


Once your project is created, add a new Kotlin Notebook (see the next section).

## Add a new Kotlin Notebook to your project

To add a new Kotlin Notebook to an existing project: 

1. Select **File | New | Kotlin Notebook**, or right-click on a folder and select **New | Kotlin Notebook**.

   ![创建新的 Kotlin Notebook](new-notebook.png){width=700}

2. Set the name of the new notebook, for example, **first-notebook**.
3. Press **Enter**. A new tab with a Kotlin Notebook **first-notebook.ipynb** opens.

## 创建临时 Kotlin Notebook {id=create-a-scratch-kotlin-notebook}

You can also create a Kotlin Notebook as a scratch file. [Scratch files](https://www.jetbrains.com/help/idea/scratches.html#create-scratch-file) allow 
you to test small pieces of code without creating a new project or modifying an existing one.

创建临时 Kotlin Notebook 的步骤：

1. 点击 **File | New | Scratch File**
2. 从 **New Scratch File** 列表中选择 **Kotlin Notebook**

   ![临时笔记本](kotlin-notebook-scratch-file.png){width=400}

## Perform basic operations

1. In the new Kotlin Notebook, type the following code in the code cell:

   ```kotlin
   println("Hello, this is a Kotlin Notebook!")
   ```

2. To run a code cell, click the **Run Cell and Select Below** ![Run Cell and Select Below](run-cell-and-select-below.png){width=30}{type="joined"} button or press **Shift** + **Return**.
3. Add a markdown cell by clicking on the **Add Markdown Cell** button.
4. Type `# Example operations` in the cell, and run it the same way you run code cells to render it.
5. In a new code cell, type `10 + 10` and run it.
6. Define a variable in a code cell. For example, `val a = 100`.

   > Once you run a code cell with defined variables, those variables become accessible in all other code cells.
   >
   {style="tip"}

7. Create a new code cell and add `println(a * a)`.
8. Run all code and markdown cells in the notebook using the **Run All** ![Run all button](run-all-button.png){width=30}{type="joined"} button.

   ![First notebook](first-notebook.png){width=700}

Congratulations! You have just created and tried out your first Kotlin Notebook.


## Next step

在教程的下一部分，你将学习如何向 Kotlin Notebook 添加依赖项。

**[进入下一章](kotlin-notebook-add-dependencies.md)**
