[//]: # (title:  Kotlin 发布)

我们发布不同类型的版本：

* **功能发布**（1.**x**）引入了语言的重大变化。
* **增量发布**（1.**x**.**y**）在功能发布之间发布，包括工具更新、性能改进和错误修复。
* **错误修复发布**（1.**x**.**yz**）包含增量发布的错误修复。

例如，对于功能发布 1.3，我们有多个增量发布，包括 1.3.10、1.3.20 和 1.3.70。
对于 1.3.70，我们有两个错误修复发布 – 1.3.71 和 1.3.72。

对于每个增量和功能发布，我们还发布了几个预览（**EAP**）版本供您在正式发布之前尝试新功能。详细信息请参阅 [Early Access Preview](eap.md)。

了解更多关于 [Kotlin 发布的类型和兼容性](kotlin-evolution.md#feature-releases-and-incremental-releases)。

## 升级到新版本 {id=升级到新版本}

IntelliJ IDEA 和 Android Studio 在新版本发布后建议进行更新。当您接受建议时，它会自动将 Kotlin 插件更新到新版本。
您可以在 **工具** | **Kotlin** | **配置 Kotlin 插件更新** 中检查 Kotlin 版本。

如果您有使用较早版本 Kotlin 创建的项目，请在项目中更改 Kotlin 版本，并在需要时更新 kotlinx 库。

如果您正在迁移到新的功能发布版本，Kotlin 插件的迁移工具将帮助您完成迁移。

## IDE 支持 {id=ide-support}

对于语言的最新版本，以下版本的 IntelliJ IDEA 和 Android Studio 提供 IDE 支持：

* IntelliJ IDEA:
  * 最新稳定版
  * 上一个稳定版
  * [早期访问](https://www.jetbrains.com/resources/eap/) 版本
* Android Studio:
  * [最新发布](https://developer.android.com/studio) 版本
  * [早期访问](https://developer.android.com/studio/preview) 版本

> 在 [IntelliJ IDEA 新功能页面](https://www.jetbrains.com/idea/whatsnew/) 的 **Kotlin** 部分了解有关最新 Kotlin 相关更新的更多信息。


## 发布详情 {id=release-details}

以下表格列出了最新 Kotlin 发布的详细信息。

您还可以使用 [Kotlin 预览版本](eap.md#build-details)。

<table>
    <tr>
        <th>构建信息</th>
        <th>构建亮点</th>
    </tr>
    <tr>
      <td><strong>1.9.21</strong>
          <p>发布于： <strong>2023年11月23日</strong></p>
          <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.9.21" target="_blank">GitHub 上的发布</a></p>
      </td>
      <td>
          <p>Kotlin 1.9.20 的一个 bug 修复版本。</p>
          <p>了解有关 Kotlin 1.9.20 的更多信息： <a href="whatsnew1920.md" target="_blank">Kotlin 1.9.20 的新增功能</a>.</p>
      </td>
  </tr>
    <tr>
        <td><strong>1.9.20</strong>
            <p>发布于: <strong>2023 年 11 月 1 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.9.20" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>包含 Beta 版 Kotlin K2 编译器和稳定 Kotlin 多平台的功能版本。</p>
            <p>了解更多信息：</p>
            <list>
                <li><a href="whatsnew1920.md" target="_blank">Kotlin 1.9.20 的新增功能</a>,</li>
            </list>
        </td>
    </tr>
    <tr>
        <td><strong>1.9.10</strong>
            <p>发布于: <strong>2023 年 8 月 23 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.9.10" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.9.0 的错误修复版本。</p>
            <p>要详细了解 Kotlin 1.9.0，请参阅 <a href="whatsnew19.md" target="_blank">Kotlin 1.9.0 的新增功能</a>。</p>
            <note>对于 Android Studio Giraffe 和 Hedgehog，Kotlin 插件 1.9.10 将随即将发布的 Android Studio 更新一起提供。</note>
        </td>
    </tr>
    <tr>
        <td><strong>1.9.0</strong>
            <p>发布于: <strong>2023 年 7 月 6 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.9.0" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>一个功能发布，包括 Kotlin K2 编译器的更新、新的枚举类值函数、开放区间的新操作符、Kotlin Multiplatform 中 Gradle 配置缓存的预览、Kotlin 多平台 中 Android 目标支持的更改，以及 Kotlin/Native 中自定义内存分配器的预览。</p>
            <p>了解更多信息：</p>
            <list>
                <li><a href="whatsnew19.md" target="_blank">Kotlin 1.9.0 的新增功能</a></li>
                <li><a href="https://www.youtube.com/embed/fvwTZc-dxsM" target="_blank">Kotlin YouTube 视频中的新增功能</a></li>
            </list>
        </td>
    </tr>
    <tr>
        <td><strong>1.8.22</strong>
            <p>发布于: <strong>2023 年 6 月 8 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.8.22" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.8.20 的错误修复版本。</p>
            <p>要详细了解 Kotlin 1.8.20，请参阅 <a href="whatsnew1820.md" target="_blank">Kotlin 1.8.20 的新增功能</a>。</p>
        </td>
    </tr>
    <tr>
        <td><strong>1.8.21</strong>
            <p>发布于: <strong>2023 年 4 月 25 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.8.21" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.8.20 的错误修复版本。</p>
            <p>要详细了解 Kotlin 1.8.20，请参阅 <a href="whatsnew1820.md" target="_blank">Kotlin 1.8.20 的新增功能</a>。</p>
            <note>对于 Android Studio Flamingo 和 Giraffe，Kotlin 插件 1.8.21 将随即将发布的 Android Studio 更新一起提供。</note>
        </td>
    </tr>
    <tr>
        <td><strong>1.8.20</strong>
            <p>发布于: <strong>2023 年 4 月 3 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.8.20" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>一个功能发布，包括 Kotlin K2 编译器的更新、在 stdlib 中添加 AutoCloseable 接口和 Base64 编码、默认启用的新 JVM 增量编译，以及新的 Kotlin/Wasm 编译器后端。</p>
            <p>了解更多信息：</p>
            <list>
                <li><a href="whatsnew1820.md" target="_blank">Kotlin 1.8.20 的新增功能</a></li>
                <li><a href="https://youtu.be/R1JpkpPzyBU" target="_blank">Kotlin YouTube 视频中的新增功能</a></li>
            </list>
        </td>
    </tr>
    <tr>
        <td><strong>1.8.10</strong>
            <p>发布于: <strong>2023 年 2 月 2 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.8.10" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.8.0 的错误修复版本。</p>
            <p>详细了解 <a href="https://github.com/JetBrains/kotlin/releases/tag/v1.8.0" target="_blank">Kotlin 1.8.0</a>。</p>
            <note>对于 Android Studio Electric Eel 和 Flamingo，Kotlin 插件 1.8.10 将随即将推出的 Android Studio 更新一起提供。</note>
        </td>
    </tr>
    <tr>
        <td><strong>1.8.0</strong>
            <p>发布于: <strong>2022 年 12 月 28 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.8.0" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>一个功能发布，包括改进的 kotlin-reflect 性能、JVM 上新的递归复制或删除目录内容的实验性函数，以及改进的 Objective-C/Swift 互操作性。</p>
            <p>了解更多信息：</p>
            <list>
                <li><a href="whatsnew18.md" target="_blank">Kotlin 1.8.0 的新增功能</a></li>
                <li><a href="compatibility-guide-18.md" target="_blank">Kotlin 1.8.0 兼容性指南</a></li>
            </list>
        </td>
    </tr>
    <tr>
        <td><strong>1.7.21</strong>
            <p>发布于: <strong>2022 年 11 月 9 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.7.21" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.7.20 的错误修复版本。</p>
            <p>要详细了解 Kotlin 1.7.20，请参阅 <a href="whatsnew1720.md" target="_blank">Kotlin 1.7.20 的新增功能</a>。</p>
            <note>对于 Android Studio Dolphin、Electric Eel 和 Flamingo，Kotlin 插件 1.7.21 将随即将发布的 Android Studio 更新一起提供。</note>
        </td>
    </tr>
    <tr>
        <td><strong>1.7.20</strong>
            <p>发布于: <strong>2022 年 9 月 29 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.7.20" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
          <p>一个增量发布，包括新的语言特性、在 Kotlin K2 编译器中对多个编译器插件的支持，默认启用的新的 Kotlin/Native 内存管理器，以及对 Gradle 7.1 的支持。</p>
            <p>了解更多信息：</p>
            <list>
                <li><a href="whatsnew1720.md" target="_blank">Kotlin 1.7.20 的新增功能</a></li>
                <li><a href="https://youtu.be/OG9npowJgE8" target="_blank">Kotlin YouTube 视频中的新增功能</a></li>
                <li><a href="compatibility-guide-1720.md" target="_blank">Kotlin 1.7.20 兼容性指南</a></li>
            </list>
            <p>详细了解 <a href="https://github.com/JetBrains/kotlin/releases/tag/v1.7.20" target="_blank">Kotlin 1.7.20</a>。</p>
        </td>
    </tr>
    <tr>
        <td><strong>1.7.10</strong>
            <p>发布于: <strong>2022 年 7 月 7 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.7.10" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.7.0 的错误修复版本。</p>
            <p>详细了解 <a href="https://github.com/JetBrains/kotlin/releases/tag/v1.7.0" target="_blank">Kotlin 1.7.0</a>。</p>
            <note>对于 Android Studio Dolphin（213）和 Android Studio Electric Eel（221），Kotlin 插件 1.7.10 将随即将发布的 Android Studio 更新一起提供。</note>
        </td>
    </tr>
    <tr>
        <td><strong>1.7.0</strong>
            <p>发布于: <strong>2022 年 6 月 9 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.7.0" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>一个功能发布，包括 JVM 上的 Kotlin K2 编译器的 Alpha 版本、稳定的语言特性、性能改进以及稳定实验性 API 等进化性变化。</p>
            <p>了解更多信息：</p>
            <list>
                <li><a href="whatsnew17.md" target="_blank">Kotlin 1.7.0 的新增功能</a></li>
                <li><a href="https://youtu.be/54WEfLKtCGk" target="_blank">Kotlin YouTube 视频中的新增功能</a></li>
                <li><a href="compatibility-guide-17.md" target="_blank">Kotlin 1.7.0 兼容性指南</a></li>
            </list>
        </td>
    </tr>
    <tr>
        <td><strong>1.6.21</strong>
            <p>发布于: <strong>2022 年 4 月 20 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.6.21" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.6.20 的错误修复版本。</p>
            <p>详细了解 <a href="whatsnew1620.md" target="_blank">Kotlin 1.6.20</a>。</p>
        </td>
    </tr>
    <tr>
        <td><strong>1.6.20</strong>
            <p>发布于: <strong>2022 年 4 月 4 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.6.20" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>具有各种改进的增量版本，例如：</p>
            <list>
                <li>上下文接收器的原型</li>
                <li>对功能接口构造函数的可调用引用</li>
                <li>Kotlin/Native：新内存管理器的性能改进</li>
                <li>多平台：默认分层项目结构</li>
                <li>Kotlin/JS：IR 编译器改进</li>
                <li>Gradle：编译器执行策略</li>
            </list>
            <p>详细了解 <a href="whatsnew1620.md" target="_blank">Kotlin 1.6.20</a>。</p>
        </td>
    </tr>
    <tr>
        <td><strong>1.6.10</strong>
            <p>发布于: <strong>2021 年 12 月 14 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.6.10" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.6.0 的错误修复版本。</p>
            <p>详细了解 <a href="https://github.com/JetBrains/kotlin/releases/tag/v1.6.0" target="_blank">Kotlin 1.6.0</a>。</p>
        </td>
    </tr>
    <tr>
        <td><strong>1.6.0</strong>
            <p>发布于: <strong>2021 年 11 月 16 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.6.0" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>一个功能发布，包括新的语言特性、性能改进以及稳定实验性 API 等进化性变化。</p>
            <p>了解更多信息：</p>
            <list>
                <li><a href="https://blog.jetbrains.com/kotlin/2021/11/kotlin-1-6-0-is-released/" target="_blank">发布博文</a></li>
                <li><a href="whatsnew16.md" target="_blank">Kotlin 1.6.0 的新增功能</a></li>
                <li><a href="compatibility-guide-16.md" target="_blank">兼容性指南</a></li>
            </list>
        </td>
    </tr>
    <tr>
        <td><strong>1.5.32</strong>
            <p>发布于: <strong>2021 年 11 月 29 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.5.32" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.5.31 的错误修复版本。</p>
            <p>详细了解 <a href="whatsnew1530.md" target="_blank">Kotlin 1.5.30</a>。</p>
        </td>
    </tr>
    <tr>
        <td><strong>1.5.31</strong>
            <p>发布于: <strong>2021 年 9 月 20 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.5.31" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.5.30 的错误修复版本。</p>
            <p>详细了解 <a href="whatsnew1530.md" target="_blank">Kotlin 1.5.30</a>。</p>
        </td>
    </tr>
    <tr>
        <td><strong>1.5.30</strong>
            <p>发布于: <strong>2021 年 8 月 23 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.5.30" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>具有各种改进的增量版本，例如：</p>
            <list>
                <li>注解类在JVM上的实例化</li>
                <li>改进的选择加入要求机制和类型推断</li>
                <li>Beta 版 Kotlin/JS IR 后端</li>
                <li>支持 Apple Silicon 目标</li>
                <li>改进了 CocoaPods 支持</li>
                <li>Gradle：Java 工具链支持和改进的守护进程配置</li>
            </list>
            <p>了解更多信息：</p>
            <list>
                <li><a href="https://blog.jetbrains.com/kotlin/2021/08/kotlin-1-5-30-released/" target="_blank">发布博文</a></li>
                <li><a href="whatsnew1530.md" target="_blank">What's new in Kotlin 1.5.30</a></li>
            </list>
        </td>
    </tr>
    <tr>
        <td><strong>1.5.21</strong>
            <p>发布于: <strong>2021 年 7 月 13 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.5.21" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.5.20 的错误修复版本。</p>
            <p>详细了解 <a href="whatsnew1520.md" target="_blank">Kotlin 1.5.20</a>。</p>
        </td>
    </tr>
    <tr>
        <td><strong>1.5.20</strong>
            <p>发布于: <strong>2021 年 6 月 24 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.5.20" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>具有各种改进的增量版本，例如：</p>
            <list>
                <li>默认情况下通过 JVM 上的 <code>invokedynamic</code> 进行字符串连接</li>
                <li>改进了对 Lombok 的支持和对 JSpecify 的支持</li>
                <li>Kotlin/Native：KDoc 导出到 Objective-C 标头并在一个数组中更快地使用 <code>Array.copyInto()</code></li>
                <li>Gradle：注释处理器的类加载器的缓存以及对 <code>--parallel</code> Gradle 属性的支持</li>
                <li>stdlib 函数跨平台的一致行为</li>
            </list>
            <p>了解更多信息：</p>
            <list>
                <li><a href="https://blog.jetbrains.com/kotlin/2021/06/kotlin-1-5-20-released/" target="_blank">发布博文</a></li>
                <li><a href="whatsnew1520.md" target="_blank">Kotlin 1.5.20 的新增功能</a></li>
            </list>
        </td>
    </tr>
    <tr>
        <td><strong>1.5.10</strong>
            <p>发布于: <strong>2021 年 5 月 24 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.5.10" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.5.0 的错误修复版本。</p>
            <p>详细了解 <a href="https://blog.jetbrains.com/kotlin/2021/04/kotlin-1-5-0-released/" target="_blank">Kotlin 1.5.0</a>。</p>
        </td>
    </tr>
    <tr>
        <td><strong>1.5.0</strong>
            <p>发布于: <strong>2021 年 5 月 5 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.5.0" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>一个功能发布，包括新的语言特性、性能改进以及稳定实验性 API 等进化性变化。</p>
            <p>了解更多信息：</p>
            <list>
                <li><a href="https://blog.jetbrains.com/kotlin/2021/04/kotlin-1-5-0-released/" target="_blank">发布博文</a></li>
                <li><a href="whatsnew15.md" target="_blank">Kotlin 1.5.0 的新增功能</a></li>
                <li><a href="compatibility-guide-15.md" target="_blank">兼容性指南</a></li>
            </list>
        </td>
    </tr>
    <tr>
        <td><strong>1.4.32</strong>
            <p>发布于: <strong>2021 年 3 月 22 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.4.32" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.4.30 的错误修复版本。</p>
            <p>详细了解 <a href="whatsnew1430.md" target="_blank">Kotlin 1.4.30</a>。</p>
        </td>
    </tr>
    <tr>
        <td><strong>1.4.31</strong>
            <p>发布于: <strong>2021 年 2 月 25 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.4.31" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.4.30 的错误修复版本</p>
            <p>详细了解 <a href="whatsnew1430.md" target="_blank">Kotlin 1.4.30</a>。</p>
        </td>
    </tr>
    <tr>
        <td><strong>1.4.30</strong>
            <p>发布于: <strong>2021 年 2 月 3 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.4.30" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>具有各种改进的增量版本，例如：</p>
            <list>
                <li>新的 JVM 后端，现已推出 Beta 版</li>
                <li>新语言功能预览</li>
                <li>改进了 Kotlin/Native 性能</li>
                <li>标准库 API 改进</li>
            </list>
            <p>了解更多信息：</p>
            <list>
                <li><a href="https://blog.jetbrains.com/kotlin/2021/01/kotlin-1-4-30-released/" target="_blank">发布博文</a></li>
                <li><a href="whatsnew1430.md" target="_blank">Kotlin 1.4.30 的新增功能</a></li>
            </list>
        </td>
    </tr>
    <tr>
        <td><strong>1.4.21</strong>
            <p>发布于: <strong>2020 年 12 月 7 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.4.21" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.4.20 的错误修复版本</p>
            <p>详细了解 <a href="whatsnew1420.md" target="_blank">Kotlin 1.4.20</a>。</p>
        </td>
    </tr>
    <tr>
        <td><strong>1.4.20</strong>
            <p>发布于: <strong>2020 年 11 月 23 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.4.20" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>具有各种改进的增量版本，例如：</p>
            <list>
                <li>支持新的 JVM 功能，例如通过 <code>invokedynamic</code> 进行字符串连接</li>
                <li>改进了 Kotlin 多平台移动项目的性能和异常处理</li>
                <li>JDK 路径扩展：<code>Path("dir") / "file.txt"</code></li>
            </list>
            <p>了解更多信息：</p>
            <list>
                <li><a href="https://blog.jetbrains.com/kotlin/2020/11/kotlin-1-4-20-released/" target="_blank">发布博文</a></li>
                <li><a href="whatsnew1420.md" target="_blank">Kotlin 1.4.20 的新增功能</a></li>
            </list>
        </td>
    </tr>
    <tr>
        <td><strong>1.4.10</strong>
            <p>发布于: <strong>2020 年 9 月 7 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.4.10" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.4.0 的错误修复版本。</p>
            <p>了解有关 <a href="https://blog.jetbrains.com/kotlin/2020/08/kotlin-1-4-released-with-a-focus-on-quality-and-performance/" target=" 的更多信息_blank">Kotlin 1.4.0</a>。</p>
         </td>
    </tr>
    <tr>
        <td><strong>1.4.0</strong>
            <p> 发布于: <strong>2020 年 8 月 17 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.4.0" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>该功能版本包含许多功能和改进，主要关注质量和性能。</p>
            <p>了解更多信息：</p>
            <list>
                <li><a href="https://blog.jetbrains.com/kotlin/2020/08/kotlin-1-4-released-with-a-focus-on-quality-and-performance/" target="_blank">发布博文</a></li>
                <li><a href="whatsnew14.md" target="_blank">Kotlin 1.4.0 的新增功能</a></li>
                <li><a href="compatibility-guide-14.md" target="_blank">兼容性指南</a></li>
                <li><a href="whatsnew14.md#migrating-to-kotlin-1-4-0" target="_blank">迁移到 Kotlin 1.4.0</a></li>
            </list>
        </td>
    </tr>
    <tr>
        <td><strong>1.3.72</strong>
            <p> 发布于: <strong>2020 年 4 月 15 日</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.3.72" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.3.70 的错误修复版本。</p>
            <p>详细了解 <a href="https://blog.jetbrains.com/kotlin/2020/03/kotlin-1-3-70-released/" target="_blank">Kotlin 1.3.70</a>。</p>
        </td>
    </tr>
</table>
