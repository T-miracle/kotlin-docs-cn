[//]: # (title: 定义文件)

[//]: # (TODO 待校对)

Kotlin/Native 允许你使用 C 和 Objective-C 库，从而在 Kotlin 中调用它们的功能。  
一个名为 cinterop 的特殊工具会处理 C 或 Objective-C 库，并生成相应的 Kotlin 绑定，  
这样你就可以像平常一样在 Kotlin 代码中调用该库的方法。

为了生成这些绑定，每个库都需要一个定义文件，通常与库同名。  
这是一个属性文件，用于精确描述库的使用方式。查看完整的 [可用属性列表](#properties)。

以下是项目开发时的一般工作流程：

1. 创建一个 `.def` 文件，描述要在绑定中包含的内容。
2. 在你的 Kotlin 代码中使用生成的绑定。
3. 运行 Kotlin/Native 编译器以生成最终的可执行文件。

## 创建并配置定义文件 {id=create-and-configure-a-definition-file}

让我们创建一个定义文件并为 C 库生成绑定：

1. 在你的 IDE 中，选中 `src` 文件夹并通过 **File | New | Directory** 创建一个新目录。
2. 将新目录命名为 `nativeInterop/cinterop`。
   
   这是 `.def` 文件存放的默认约定路径，但如果你使用不同位置，  
   可以在 `build.gradle.kts` 文件中覆盖该设置。
3. 选中新建的子文件夹，并通过 **File | New | File** 创建一个 `png.def` 文件。
4. 添加必要的属性：

   ```none
   headers = png.h
   headerFilter = png.h
   package = png
   
   compilerOpts.linux = -I/usr/include -I/usr/include/x86_64-linux-gnu
   linkerOpts.osx = -L/opt/local/lib -L/usr/local/opt/png/lib -lpng
   linkerOpts.linux = -L/usr/lib/x86_64-linux-gnu -lpng
   ```

   * `headers` 是要为其生成 Kotlin 存根的头文件列表。你可以在此条目中添加多个文件，  
     用空格分隔。本例中仅包含 `png.h`。引用的文件必须位于指定路径（本例为 `/usr/include/png`）。
   * `headerFilter` 指定实际包含的内容。在 C 中，当一个文件通过 `#include` 指令引用另一个文件时，  
     所有头文件也会被包含。有时这并非必要，你可以[使用 glob 模式](https://en.wikipedia.org/wiki/Glob_(programming))  
     添加此参数进行调整。

     如果不希望将外部依赖（如系统 `stdint.h` 头文件）引入交互库，可使用 `headerFilter`。  
     它也有助于优化库大小，并修复系统与提供的 Kotlin/Native 编译环境之间的潜在冲突。

   * 如需针对特定平台修改行为，可使用 `compilerOpts.osx` 或 `compilerOpts.linux` 等格式  
     提供平台特定的选项值。本例中针对 macOS（`.osx` 后缀）和 Linux（`.linux` 后缀）。  
     无后缀的参数（如 `linkerOpts=`）适用于所有平台。

5. 要生成绑定，点击通知中的 **立即同步** 以同步 Gradle 文件。

   ![同步 Gradle 文件](gradle-sync.png)

绑定生成后，IDE 可将其作为原生库的代理视图使用。

> 你也可以使用命令行中的 [cinterop 工具](#generate-bindings-using-command-line) 来配置绑定生成。
>
{style="tip"}

## 属性 {id=properties}

以下是可在定义文件中使用的完整属性列表，用于调整生成的二进制文件内容。  
更多信息请参阅下方对应章节。

| **属性**                                                                              | **描述**                                                                                               |
|-------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| [`headers`](#import-headers)                                                        | 要包含在绑定中的库头文件列表。                                                                                      |
| [`modules`](#import-modules)                                                        | 要包含在绑定中的 Objective-C 库的 Clang 模块列表。                                                                  |
| `language`                                                                          | 指定语言。默认使用 C；必要时可改为 `Objective-C`。                                                                    |
| [`compilerOpts`](#pass-compiler-and-linker-options)                                 | cinterop 工具传递给 C 编译器的编译选项。                                                                           |
| [`linkerOpts`](#pass-compiler-and-linker-options)                                   | cinterop 工具传递给链接器的链接选项。                                                                              |
| [`excludedFunctions`](#ignore-specific-functions)                                   | 应忽略的函数名称列表（以空格分隔）。                                                                                   |                                              
| `excludedMacros`                                                                    | 应忽略的宏定义列表。                                                                                           |
| [`staticLibraries`](#include-a-static-library)                                      | [实验性](components-stability.md#stability-levels-explained)。将静态库包含到 `.klib` 中。                         |
| [`libraryPaths`](#include-a-static-library)                                         | [实验性](components-stability.md#stability-levels-explained)。cinterop 工具搜索要包含到 `.klib` 中的库的目录列表（以空格分隔）。 |
| `packageName`                                                                       | 生成的 Kotlin API 的包名前缀。                                                                                |
| [`headerFilter`](#filter-headers-by-globs)                                          | 通过 glob 模式过滤头文件，仅导入匹配的头文件。                                                                           |
| [`excludeFilter`](#exclude-headers)                                                 | 导入库时排除特定头文件，优先级高于 `headerFilter`。                                                                    |
| [`strictEnums`](#configure-enums-generation)                                        | 应生成为 [Kotlin 枚举类](enum-classes.md) 的枚举列表（以空格分隔）。                                                     |
| [`nonStrictEnums`](#configure-enums-generation)                                     | 应生成为整数值的枚举列表（以空格分隔）。                                                                                 |
| [`noStringConversion`](#set-up-string-conversion)                                   | 不自动将 `const char*` 参数转换为 Kotlin `String` 的函数列表（以空格分隔）。                                               |
| `allowedOverloadsForCFunctions`                                                     | 默认假定 C 函数具有唯一名称。如果有多个同名函数，则只选取一个。但可以通过在 `allowedOverloadsForCFunctions` 中指定这些函数来更改此行为。               |
| [`disableDesignatedInitializerChecks`](#allow-calling-a-non-designated-initializer) | 禁用编译器检查，允许将非指定 Objective-C 初始化器作为 `super()` 构造函数调用。                                                  |
| [`foreignExceptionMode`](#handle-objective-c-exceptions)                            | 将 Objective-C 代码中的异常包装为 Kotlin 的 `ForeignException` 类型异常。                                            |
| [`userSetupHint`](#help-resolve-linker-errors)                                      | 添加自定义消息（例如帮助用户解决链接错误）。                                                                               |

<!-- | `excludedMacros`                                                                    |                                                                                                                                                                                                                          |
| `objcClassesIncludingCategories`                                                    |                                                                                                                                                                                                                          | -->

除了属性列表外，你还可以在定义文件中包含[自定义声明](#add-custom-declarations)。

### 导入头文件 {id=import-headers}

如果 C 库没有 Clang 模块，而是由一组头文件组成，可以使用 `headers` 属性指定要导入的头文件：

```none
headers = curl/curl.h
```

#### 使用 glob 模式过滤头文件 {id=filter-headers-by-globs}

你可以通过 `.def` 文件中的过滤属性使用 glob 模式来过滤头文件。要包含头文件中的声明，请使用 `headerFilter` 属性。
如果头文件匹配任何 glob 模式，则其声明将被包含在绑定中。

这些 glob 模式会应用于相对于适当包含路径元素的头文件路径，例如 `time.h` 或 `curl/curl.h`。
因此，如果库通常通过 `#include <SomeLibrary/Header.h>` 包含，你可以使用以下过滤器来过滤头文件：

```none
headerFilter = SomeLibrary/**
```

如果未提供 `headerFilter`，则会包含所有头文件。
但我们建议你使用 `headerFilter` 并尽可能精确地指定 glob 模式。
在这种情况下，生成的库仅包含必要的声明。这有助于避免在升级 Kotlin 或开发环境中的工具时出现各种问题。

#### 排除头文件 {id=exclude-headers}

要排除特定头文件，可使用 `excludeFilter` 属性。
该属性有助于移除冗余或问题头文件并优化编译，因为指定头文件的声明不会包含在绑定中：

```none
excludeFilter = SomeLibrary/time.h
```

> 若同一头文件同时被 `headerFilter` 包含又被 `excludeFilter` 排除，则该头文件最终不会包含在绑定中。
>
{style="note"}

### 导入模块 {id=import-modules}

若 Objective-C 库包含 Clang 模块，可使用 `modules` 属性指定要导入的模块：

```none
modules = UIKit
```

### 传递编译器和链接器选项 {id=pass-compiler-and-linker-options}

使用 `compilerOpts` 属性向 C 编译器传递选项，该编译器在底层用于分析头文件。  
要向链接器传递选项（用于链接最终可执行文件），请使用 `linkerOpts`。例如：

```none
compilerOpts = -DFOO=bar
linkerOpts = -lpng
```

你也可以指定仅适用于特定目标的选项：

```none
compilerOpts = -DBAR=bar
compilerOpts.linux_x64 = -DFOO=foo1
compilerOpts.macos_x64 = -DFOO=foo2
```

在此配置下，Linux 上会使用 `-DBAR=bar -DFOO=foo1` 分析头文件，而 macOS 上会使用 `-DBAR=bar -DFOO=foo2`。  
请注意，任何定义文件选项都可以同时包含通用部分和平台特定部分。

### 忽略特定函数 {id=ignore-specific-functions}

使用 `excludedFunctions` 属性指定应忽略的函数名称列表。  
如果头文件中声明的函数无法保证可调用，且难以或无法自动确定时，这会很有用。  
你也可以使用该属性来解决互操作本身的 bug。

### 包含静态库 {id=include-a-static-library}

> 此功能为[实验性](components-stability.md#stability-levels-explained)功能，可能随时会被移除或更改。
> 请仅用于评估目的。
>
{style="warning"}

有时将静态库与产品一起分发比假设它在用户环境中可用更为方便。
要在`.klib`中包含静态库，请使用`staticLibrary`和`libraryPaths`属性：

```none
headers = foo.h
staticLibraries = libfoo.a
libraryPaths = /opt/local/lib /usr/local/opt/curl/lib
```

当给定上述代码片段时，cinterop工具会在`/opt/local/lib`和`/usr/local/opt/curl/lib`中搜索`libfoo.a`，
如果找到，则将该库二进制文件包含在`klib`中。

当在程序中使用这样的`klib`时，库会自动链接。

### 配置枚举生成方式 {id=configure-enums-generation}

使用 `strictEnums` 属性将枚举生成 Kotlin 枚举类型，或使用 `nonStrictEnums` 将其生成为整数值。  
若枚举未包含在这些列表中，则基于启发式方法生成。

### 设置字符串转换 {id=set-up-string-conversion}

使用 `noStringConversion` 属性禁用将 `const char*` 函数参数自动转换为 Kotlin `String` 的功能。

### 允许调用非指定初始化器 {id=allow-calling-a-non-designated-initializer}

默认情况下，Kotlin/Native 编译器不允许将非指定的 Objective-C 初始化器作为 `super()` 构造函数调用。  
如果库中未正确标记指定的 Objective-C 初始化器，此行为可能会带来不便。  
要禁用这些编译器检查，请使用 `disableDesignatedInitializerChecks` 属性。

### 处理 Objective-C 异常 {id=handle-objective-c-exceptions}

默认情况下，当 Objective-C 异常到达 Objective-C 与 Kotlin 的互操作边界并进入 Kotlin 代码时，程序会崩溃。

要将 Objective-C 异常传播到 Kotlin 代码，请启用 `foreignExceptionMode = objc-wrap` 属性进行包装处理。
在此模式下，Objective-C 异常会被转换为 Kotlin 异常，其类型为 `ForeignException`。

#### 解决链接器错误 {id=help-resolve-linker-errors}

当 Kotlin 库依赖 C 或 Objective-C 库（例如使用 [CocoaPods 集成](native-cocoapods.md)）时，可能会出现链接器错误。
如果依赖库未在本地机器上安装或在项目构建脚本中显式配置，则会出现 "Framework not found" 错误。

如果您是库作者，可以通过自定义消息帮助用户解决链接器错误。
为此，请在 `.def` 文件中添加 `userSetupHint=message` 属性，或向 `cinterop` 传递 `-Xuser-setup-hint` 编译器选项。

### 添加自定义声明 {id=add-custom-declarations}

有时需要在生成绑定前向库中添加自定义 C 声明（例如用于[宏](native-c-interop.md#macros)）。  
无需创建额外的头文件，您可以直接在 `.def` 文件末尾添加这些声明，只需用分隔符序列 `---` 作为分界线：

```none
headers = errno.h
---

static inline int getErrno() {
    return errno;
}
```

请注意，`.def` 文件的这部分内容会被视为头文件的一部分，因此带函数体的函数应声明为 `static`。  
这些声明会在包含 `headers` 列表中的文件后被解析。

## 通过命令行生成绑定 {id=generate-bindings-using-command-line}

除了定义文件外，您还可以通过在 `cinterop` 调用中传递相应属性作为选项来指定绑定包含的内容。

以下是生成 `png.klib` 编译库的命令示例：

```bash
cinterop -def png.def -compiler-option -I/usr/local/include -o png
```

请注意生成的绑定通常是平台特定的，因此如果为多目标开发，需要重新生成绑定。

* 对于未包含在 sysroot 搜索路径中的宿主库，可能需要头文件
* 对于带配置脚本的标准 UNIX 库，`compilerOpts` 可能需要包含配置脚本 `--cflags` 选项的输出（可能不含精确路径）
* 配置脚本 `--libs` 的输出可以传递给 `linkerOpts` 属性

## 后续步骤 {id=whats-next}

* [C 互操作性绑定](native-c-interop.md#bindings)
* [与 Swift/Objective-C 的互操作性](native-objc-interop.md)