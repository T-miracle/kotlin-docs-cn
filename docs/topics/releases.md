[//]: # (title:  Kotlin 发布)

Since Kotlin 2.0.0, we ship the following types of releases:

* _Language releases_ (2._x_._0_) that bring major changes in the language and include tooling updates. Released once in 6 months.
* _Tooling releases_ (2._x_._20_) that are shipped between language releases and include updates in the tooling,
    performance improvements, and bug fixes.
    Released in 3 months after corresponding _language release_.
* _Bug fix releases_ (2._x_._yz_) that include bug fixes for _tooling releases_. There is no exact release schedule for these releases.

<!-- TODO: uncomment with 2.1.0 release
> For example, for the feature release 1.8.0, we had only one tooling release 1.8.20,
> and several bugfix releases including 1.8.21, 1.8.22.
>
{type="tip"}
-->

For each language and tooling release, we also ship several preview (_EAP_) versions for you to try
new features before they are released. See [Early Access Preview](eap.md) for details.

## Update to a new release

To upgrade your project to a new release, you need to update your build script file.
For example, to update to Kotlin %kotlinVersion%, change the version of the Kotlin Gradle plugin in your
`build.gradle(.kts)` file:

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
plugins {
    // Replace `<...>` with the plugin name appropriate for your target environment
    kotlin("<...>") version "%kotlinVersion%"
    // For example, if your target environment is JVM:
    // kotlin("jvm") version "%kotlinVersion%"
    // If you target is Kotlin Multiplatform
    // kotlin("multiplatform") version "%kotlinVersion%"
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
plugins {
    // Replace `<...>` with the plugin name appropriate for your target environment
    id 'org.jetbrains.kotlin.<...>' version '%kotlinVersion%'
    // For example, if your target environment is JVM: 
    // id 'org.jetbrains.kotlin.jvm' version '%kotlinVersion%'
    // If you target is Kotlin Multiplatform
    // id 'org.jetbrains.kotlin.multiplatform' version '%kotlinVersion%'
}
```

</tab>
</tabs>

如果您有使用较早版本 Kotlin 创建的项目，请在项目中更改 Kotlin 版本，并在需要时更新 kotlinx 库。

If you are migrating to the new language release, Kotlin plugin's migration tools will help you with the migration.

## IDE 支持 {id=ide-support}

Even with the release of the K2 compiler, IntelliJ IDEA and Android Studio still use the previous compiler by default 
for code analysis, code completion, highlighting, and other IDE-related features.

Starting from 2024.1, IntelliJ IDEA can use the new K2 compiler to analyze your code with its K2 Kotlin mode.
To enable it, go to **Settings** | **Languages & Frameworks** | **Kotlin** and select the **Enable K2 Kotlin Mode** option.

> The K2 Kotlin mode is in Alpha. The performance and stability of code highlighting and code completion have been significantly improved,
> but not all IDE features are supported yet.
>
{type="warning"}

After enabling K2 mode, you may notice differences in IDE analysis due to changes in compiler behavior.
Learn how the new K2 compiler differs from the previous one in the [migration guide](k2-compiler-migration-guide.md).

## Kotlin release compatibility

Learn more about [types of Kotlin releases and their compatibility](kotlin-evolution.md#language-and-tooling-releases)

## Release details

The following table lists details of the latest Kotlin releases:

> You can also use [preview versions of Kotlin](eap.md#build-details).
> 
{type="tip"}

<table>
    <tr>
        <th>构建信息</th>
        <th>构建亮点</th>
    </tr>
    <tr>
        <td><strong>2.0.0</strong>
            <p>Released: <strong>May 21, 2024</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v2.0.0" target="_blank">Release on GitHub</a></p>
        </td>
        <td>
            <p>A language release with the Stable Kotlin K2 compiler.</p>
            <p>Learn more about Kotlin 2.0.0 in <a href="whatsnew20.md" target="_blank">What's new in Kotlin 2.0.0</a>.</p>
        </td>
    </tr>
    <tr>
        <td><strong>1.9.25</strong>
            <p>Released: <strong>July 19, 2024</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.9.25" target="_blank">Release on GitHub</a></p>
        </td>
        <td>
            <p>A bug fix release for Kotlin 1.9.20, 1.9.21, 1.9.22, 1.9.23, and 1.9.24</p>
            <p>Learn more about Kotlin 1.9.20 in <a href="whatsnew1920.md" target="_blank">What's new in Kotlin 1.9.20</a>.</p>
        </td>
    </tr>
    <tr>
        <td><strong>1.9.24</strong>
            <p>发布于： <strong>2024.5.7</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.9.24" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.9.20、1.9.21、1.9.22 和 1.9.23 的错误修复版本。</p>
            <p>了解有关 Kotlin 1.9.20 的更多信息： <a href="whatsnew1920.md" target="_blank">Kotlin 1.9.20 的新增功能</a>.</p>
        </td>
    </tr>
    <tr>
        <td><strong>1.9.23</strong>
            <p>发布于： <strong>2024.3.7</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.9.23" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.9.20、1.9.21 和 1.9.22 的错误修复版本。</p>
            <p>了解有关 Kotlin 1.9.20 的更多信息： <a href="whatsnew1920.md" target="_blank">Kotlin 1.9.20 的新增功能</a>.</p>
        </td>
    </tr>
    <tr>
        <td><strong>1.9.22</strong>
            <p>发布于： <strong>2023.12.21</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.9.22" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.9.20 和 1.9.21 的错误修复版本。</p>
            <p>了解有关 Kotlin 1.9.20 的更多信息： <a href="whatsnew1920.md" target="_blank">Kotlin 1.9.20 的新增功能</a>.</p>
        </td>
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
            <p>发布于: <strong>2023.11.1</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.9.20" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>包含 Beta 版 Kotlin K2 编译器和稳定 Kotlin 多平台的功能版本。</p>
            <p>了解更多信息：</p>
            <list>
                <li><a href="whatsnew1920.md" target="_blank">Kotlin 1.9.20 的新增功能</a></li>
            </list>
        </td>
    </tr>
    <tr>
        <td><strong>1.9.10</strong>
            <p>发布于: <strong>2023.8.23</strong></p>
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
            <p>发布于: <strong>2023.7.6</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.9.0" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>A feature release with Kotlin K2 compiler updates, new enum class values function,
                new operator for open-ended ranges, preview of Gradle configuration cache in Kotlin Multiplatform, 
                changes to Android target support in Kotlin Multiplatform, preview of custom memory allocator in Kotlin/Native.
            </p>
            <p>Learn more in:</p>
            <list>
                <li><a href="whatsnew19.md" target="_blank">Kotlin 1.9.0 的新增功能</a></li>
                <li><a href="https://www.youtube.com/embed/fvwTZc-dxsM" target="_blank">Kotlin YouTube 视频中的新增功能</a></li>
            </list>
        </td>
    </tr>
    <tr>
        <td><strong>1.8.22</strong>
            <p>发布于: <strong>2023.6.8</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.8.22" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.8.20 的错误修复版本。</p>
            <p>要详细了解 Kotlin 1.8.20，请参阅 <a href="whatsnew1820.md" target="_blank">Kotlin 1.8.20 的新增功能</a>。</p>
        </td>
    </tr>
    <tr>
        <td><strong>1.8.21</strong>
            <p>发布于: <strong>2023.4.25</strong></p>
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
            <p>发布于: <strong>2023.4.3</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.8.20" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>A feature release with Kotlin K2 compiler updates, AutoCloseable interface and Base64 encoding in stdlib,
                new JVM incremental compilation enabled by default, new Kotlin/Wasm compiler backend.
            </p>
            <p>Learn more in:</p>
            <list>
                <li><a href="whatsnew1820.md" target="_blank">Kotlin 1.8.20 的新增功能</a></li>
                <li><a href="https://youtu.be/R1JpkpPzyBU" target="_blank">Kotlin YouTube 视频中的新增功能</a></li>
            </list>
        </td>
    </tr>
    <tr>
        <td><strong>1.8.10</strong>
            <p>发布于: <strong>2023.2.2</strong></p>
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
            <p>发布于: <strong>2022.12.28</strong></p>
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
            <p>发布于: <strong>2022.11.9</strong></p>
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
            <p>发布于: <strong>2022.9.29</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.7.20" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>An incremental release with new language features, the support for several compiler plugins in the Kotlin K2 compiler,
                the new Kotlin/Native memory manager enabled by default, and the support for Gradle 7.1.
            </p>
            <p>Learn more in:</p>
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
            <p>发布于: <strong>2022.7.7</strong></p>
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
            <p>发布于: <strong>2022.6.9</strong></p>
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
            <p>发布于: <strong>2022.4.20</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.6.21" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.6.20 的错误修复版本。</p>
            <p>详细了解 <a href="whatsnew1620.md" target="_blank">Kotlin 1.6.20</a>。</p>
        </td>
    </tr>
    <tr>
        <td><strong>1.6.20</strong>
            <p>发布于: <strong>2022.4.4</strong></p>
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
            <p>发布于: <strong>2021.12.14</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.6.10" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.6.0 的错误修复版本。</p>
            <p>详细了解 <a href="https://github.com/JetBrains/kotlin/releases/tag/v1.6.0" target="_blank">Kotlin 1.6.0</a>。</p>
        </td>
    </tr>
    <tr>
        <td><strong>1.6.0</strong>
            <p>发布于: <strong>2021.11.16</strong></p>
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
            <p>发布于: <strong>2021.11.29</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.5.32" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.5.31 的错误修复版本。</p>
            <p>详细了解 <a href="whatsnew1530.md" target="_blank">Kotlin 1.5.30</a>。</p>
        </td>
    </tr>
    <tr>
        <td><strong>1.5.31</strong>
            <p>发布于: <strong>2021.9.20</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.5.31" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.5.30 的错误修复版本。</p>
            <p>详细了解 <a href="whatsnew1530.md" target="_blank">Kotlin 1.5.30</a>。</p>
        </td>
    </tr>
    <tr>
        <td><strong>1.5.30</strong>
            <p>发布于: <strong>2021.8.23</strong></p>
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
            <p>发布于: <strong>2021.7.13</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.5.21" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.5.20 的错误修复版本。</p>
            <p>详细了解 <a href="whatsnew1520.md" target="_blank">Kotlin 1.5.20</a>。</p>
        </td>
    </tr>
    <tr>
        <td><strong>1.5.20</strong>
            <p>发布于: <strong>2021.6.24</strong></p>
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
            <p>发布于: <strong>2021.5.24</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.5.10" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.5.0 的错误修复版本。</p>
            <p>详细了解 <a href="https://blog.jetbrains.com/kotlin/2021/04/kotlin-1-5-0-released/" target="_blank">Kotlin 1.5.0</a>。</p>
        </td>
    </tr>
    <tr>
        <td><strong>1.5.0</strong>
            <p>发布于: <strong>2021.5.5</strong></p>
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
            <p>发布于: <strong>2021.3.22</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.4.32" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.4.30 的错误修复版本。</p>
            <p>详细了解 <a href="whatsnew1430.md" target="_blank">Kotlin 1.4.30</a>。</p>
        </td>
    </tr>
    <tr>
        <td><strong>1.4.31</strong>
            <p>发布于: <strong>2021.2.25</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.4.31" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.4.30 的错误修复版本</p>
            <p>详细了解 <a href="whatsnew1430.md" target="_blank">Kotlin 1.4.30</a>。</p>
        </td>
    </tr>
    <tr>
        <td><strong>1.4.30</strong>
            <p>发布于: <strong>2021.2.3</strong></p>
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
            <p>发布于: <strong>2020.12.7</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.4.21" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.4.20 的错误修复版本</p>
            <p>详细了解 <a href="whatsnew1420.md" target="_blank">Kotlin 1.4.20</a>。</p>
        </td>
    </tr>
    <tr>
        <td><strong>1.4.20</strong>
            <p>发布于: <strong>2020.11.23</strong></p>
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
            <p>发布于: <strong>2020.9.7</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.4.10" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.4.0 的错误修复版本。</p>
            <p>了解有关 <a href="https://blog.jetbrains.com/kotlin/2020/08/kotlin-1-4-released-with-a-focus-on-quality-and-performance/" target=" 的更多信息_blank">Kotlin 1.4.0</a>。</p>
         </td>
    </tr>
    <tr>
        <td><strong>1.4.0</strong>
            <p> 发布于: <strong>2020.8.17</strong></p>
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
            <p> 发布于: <strong>2020.4.15</strong></p>
            <p><a href="https://github.com/JetBrains/kotlin/releases/tag/v1.3.72" target="_blank">在 GitHub 上发布</a></p>
        </td>
        <td>
            <p>Kotlin 1.3.70 的错误修复版本。</p>
            <p>详细了解 <a href="https://blog.jetbrains.com/kotlin/2020/03/kotlin-1-3-70-released/" target="_blank">Kotlin 1.3.70</a>。</p>
        </td>
    </tr>
</table>
