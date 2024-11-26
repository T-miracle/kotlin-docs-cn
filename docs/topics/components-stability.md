[//]: # (title: Kotlin 组件的稳定性)

Kotlin 语言和工具集分为多个组件，例如用于 JVM、JS 和 Native 目标的编译器、标准库、各种附带工具等。  
其中许多组件已正式发布为 **稳定版**，这意味着它们在演化过程中遵循了
[_舒适更新_ 和 _保持语言现代化_](kotlin-evolution-principles.md) 的原则，以向后兼容的方式进行改进。

根据 _反馈循环_ 原则，我们早期发布了许多功能供社区试用，因此有一些组件尚未发布为 **稳定版**。  
其中一些处于非常早期的阶段，而一些则更为成熟。  
我们根据每个组件的演化速度和用户采用时面临的风险，将它们标记为 **实验性**、**Alpha** 或 **Beta**。

## 稳定性级别说明 {id=stability-levels-explained}

以下是对这些稳定性级别及其含义的简要说明：

**实验性 (Experimental)** 表示“仅在玩具项目中尝试”：
* 我们只是在尝试一个想法，希望一些用户参与并提供反馈。如果效果不好，我们可能随时放弃它。

**Alpha** 表示“自担风险使用，预期会有迁移问题”：
* 我们打算将这个想法产品化，但它还没有达到最终形态。

**Beta** 表示“可以使用，我们将尽最大努力减少迁移问题”：
* 它几乎完成，现在用户反馈尤为重要。
* 仍然不是 100% 完成，因此可能会有变化（包括基于你的反馈做出的变化）。
* 为了获得最佳的更新体验，请提前留意弃用警告。

我们统称 _实验性_、_Alpha_ 和 _Beta_ 为 **预稳定** 级别。

<a name="stable"/>

**稳定版 (Stable)** 表示“即使在最保守的场景下也可以使用”：
* 它已经完成。我们将根据我们的严格 [向后兼容规则](https://kotlinfoundation.org/language-committee-guidelines/) 进行演进。

请注意，稳定性级别并不意味着组件何时会发布为稳定版。它们也不表示在发布之前该组件会发生多少变化。
它们只说明了一个组件的变化速度以及用户在使用时面临的更新风险。

## Kotlin 组件的 GitHub 徽章 {id=github-badges-for-kotlin-components}

[Kotlin GitHub 组织](https://github.com/Kotlin) 托管了多个与 Kotlin 相关的项目。  
其中一些是我们全职开发的项目，而另一些则是侧重的项目。

每个 Kotlin 项目都有两个 GitHub 徽章，描述其稳定性和支持状态：

* **稳定性** 状态。该徽章显示每个项目的演进速度以及用户在采用它时所面临的风险。  
  稳定性状态与 [Kotlin 语言特性及其组件的稳定性级别](#stability-levels-explained) 完全一致：
  * ![Experimental stability level](https://kotl.in/badges/experimental.svg){type="joined"} 表示 **实验性**
  * ![Alpha stability level](https://kotl.in/badges/alpha.svg){type="joined"} 表示 **Alpha**
  * ![Beta stability level](https://kotl.in/badges/beta.svg){type="joined"} 表示 **Beta**
  * ![Stable stability level](https://kotl.in/badges/stable.svg){type="joined"} 表示 **稳定版**

* **支持** 状态。该徽章显示我们对维护项目的承诺，以及帮助用户解决问题的态度。  
  支持级别对于所有 JetBrains 产品都是统一的。  
  [查看 JetBrains 开源文档了解更多](https://github.com/JetBrains#jetbrains-on-github)。

## 子组件的稳定性 {id=stability-of-subcomponents}

一个稳定的组件可能包含实验性的子组件，例如：
* 一个稳定的编译器可能有一个实验性特性；
* 一个稳定的 API 可能包含实验性的类或函数；
* 一个稳定的命令行工具可能有实验性的选项。

我们确保精确记录哪些子组件不是 **稳定版**。  
我们还会尽最大努力提醒用户，并在可能的情况下要求用户显式选择加入，以避免意外使用尚未发布为稳定版的功能。

## 当前 Kotlin 组件的稳定性 {id=current-stability-of-kotlin-components}

> 默认情况下，所有新组件的状态为实验性。

{style="note"}

### Kotlin 编译器

| **组件**                                                        | **状态** | **自版本起生效** | **备注** |
|---------------------------------------------------------------|--------|------------|--------|
| Kotlin/JVM                                                    | 稳定版    | 1.0.0      |        |
| Kotlin/Native                                                 | 稳定版    | 1.9.0      |        |
| Kotlin/JS                                                     | 稳定版    | 1.3.0      |        |
| Kotlin/Wasm                                                   | Alpha  | 1.9.20     |        |
| [分析 API](https://kotlin.github.io/analysis-api/index_md.html) | 稳定版    |            |        |

### 核心编译器插件 {id=core-compiler-plugins}

| **组件**                                           | **状态** | **自版本起生效** | **备注** |
|--------------------------------------------------|--------|------------|--------|
| [All-open](all-open-plugin.md)                   | 稳定版    | 1.3.0      |        |
| [No-arg](no-arg-plugin.md)                       | 稳定版    | 1.3.0      |        |
| [SAM-with-receiver](sam-with-receiver-plugin.md) | 稳定版    | 1.3.0      |        |
| [kapt](kapt.md)                                  | 稳定版    | 1.3.0      |        |
| [Lombok](lombok.md)                              | 实验性    | 1.5.20     |        |
| [Power-assert](power-assert.md)                  | 实验性    | 2.0.0      |        |

### Kotlin 库 {id=kotlin-libraries}

| **组件**                | **状态** | **自版本起生效** | **备注** |
|-----------------------|--------|------------|--------|
| kotlin-stdlib (JVM)   | 稳定版    | 1.0.0      |        |
| kotlinx-coroutines    | 稳定版    | 1.3.0      |        |
| kotlinx-serialization | 稳定版    | 1.0.0      |        |
| kotlin-reflect (JVM)  | Beta   | 1.0.0      |        |
| kotlinx-datetime      | Alpha  | 0.2.0      |        |
| kotlinx-io            | Alpha  | 0.2.0      |        |

### Kotlin 跨平台 {id=kotlin-multiplatform}

| **组件**                        | **状态** | **自版本起生效** | **备注**                                        |
|-------------------------------|--------|------------|-----------------------------------------------|
| Kotlin 跨平台                    | 稳定版    | 1.9.20     |                                               |
| Android Studio 的 Kotlin 跨平台插件 | Beta   | 0.8.0      | [与语言版本分开发布](multiplatform-plugin-releases.md) |

### Kotlin/Native {id=kotlin-native}

| **组件**                               | **状态** | **自版本起生效** | **备注**                 |
|--------------------------------------|--------|------------|------------------------|
| Kotlin/Native 运行时                    | 稳定版    | 1.9.20     |                        |
| Kotlin/Native 与 C 和 Objective-C 的互操作 | Beta   | 1.3.0      |                        |
| klib 二进制文件                           | 稳定版    | 1.9.20     | 不包括 cinterop klibs，见下文 |
| cinterop klib 二进制文件                  | Beta   | 1.3.0      |                        |
| CocoaPods 集成                         | 稳定版    | 1.9.20     |                        |

> 有关 Kotlin/Native 目标支持的详细信息，请参阅 [](native-target-support.md)。

### 语言工具 {id=language-tools}

| **组件**      | **状态** | **自版本起生效** | **备注**                         |
|-------------|--------|------------|--------------------------------|
| 脚本语法和语义     | Alpha  | 1.2.0      |                                |
| 脚本嵌入和扩展 API | Beta   | 1.5.0      |                                |
| 脚本 IDE 支持   | Beta   |            | 自 IntelliJ IDEA 2023.1 及以后版本可用 |
| 命令行脚本       | Alpha  | 1.2.0      |                                |

## 语言特性和设计提案 {id=language-features-and-design-proposals}

有关语言特性和新设计提案，请参阅 [](kotlin-language-features-and-proposals.md)。