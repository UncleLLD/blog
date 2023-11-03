---
layout: '../../layouts/MarkdownPost.astro'
title: 'Python项目结果布局'
pubDate: 2023-11-03
description: 'Python项目结构布局，便于阅读和开发'
author: 'UncleLLD'
cover:
    url: 'https://i0.imgs.ovh/2023/11/03/Zfkl9.th.png'
    alt: 'cover'
tags: ["编程语言"]
theme: 'dark'
featured: true
---

## Python项目结构布局

通过“结构”，指的是在项目中为实现其目标所做的决策。需要考虑如何充分利用Python的特性来创建清晰、高效的代码。从实际角度来看，“结构”意味着创建清晰的代码，其逻辑和依赖关系清晰明了，以及文件和文件夹在文件系统中的组织方式。

哪些函数应该放入哪些模块？数据如何在项目中流动？哪些功能和函数可以被分组并隔离？通过回答这些问题，就可以开始在广义上规划最终产品的外观。

这里将更详细地讨论Python的模块和导入系统，因为它们是强制项目结构的核心元素。然后，将讨论如何构建可扩展和可靠测试的代码的各种观点。"

同样重要的是，代码风格、API设计和自动化，仓库结构是健康开发周期的至关重要组成部分。当潜在用户或贡献者访问您的仓库页面时，他们会看到以下内容：

- 项目名称
- 项目描述
- 一大堆文件

只有当他们滚动页面并查看下面的内容时，用户才会看到项目的自述文件。如果您的仓库是一个大杂烩的文件堆或混乱的嵌套目录，可能会在阅读自述文件之前就关闭页面，而不愿意继续了解您的项目。第一印象虽然不是一切，但您和您的同事将花费无数小时与这个仓库一起工作，最终对每一个角落和缝隙都非常熟悉，因此仓库的布局非常重要。以下是一些常见的示例：

### 示例仓库

```
README.rst
LICENSE
setup.py
requirements.txt
sample/__init__.py
sample/core.py
sample/helpers.py
docs/conf.py
docs/index.rst
tests/test_basic.py
tests/test_advanced.py
```

这个项目是一个Python软件包的常见结构，通常用于分发库或框架。下面是对这个结构的简要解释：

1. **README.rst:** 项目的说明文档，通常包括项目的描述、用法、安装指南等。

2. **LICENSE:** 项目的许可证文件，说明项目的开源许可类型。

3. **setup.py:** Python包的安装和分发配置文件，通常包括项目的元数据和依赖项。

4. **requirements.txt:** 项目的依赖包列表，用于构建虚拟环境或部署项目。

5. **sample/__init__.py:** Python包的初始化文件，通常为空。

6. **sample/core.py:** 项目的核心代码文件，包含主要功能和类。

7. **sample/helpers.py:** 包含辅助功能的文件，可能被核心代码引用。

8. **docs/conf.py:** 项目文档的配置文件，通常与Sphinx文档生成工具一起使用。

9. **docs/index.rst:** 项目文档的主页，通常包括项目的概述和索引。

10. **tests/test_basic.py:** 项目的基本单元测试。

11. **tests/test_advanced.py:** 项目的高级单元测试，通常包括更复杂的测试用例。

这个结构适用于库或框架的项目，它允许您创建和维护Python包，方便分发和共享。如果正在开发一个独立应用程序而不是库，可以考虑添加一个应用程序的入口点，例如`main.py`或`app.py`，以便用户可以运行您的应用程序。

#### 实际模块

模块包是存储库的核心焦点。它不应该被藏起来：

```
./sample/
```

如果模块只包含一个文件，则可以将其直接放在存储库的根目录中：

```
./sample.py
```

#### License

```
./LICENSE
```

除了源代码本身之外，这可以说是存储库中最重要的部分。完整的许可文本和版权声明应存在于此文件中。适当选择和明确指定许可证可以确保项目的法律和道德合规性，并告诉其他人如何使用、修改和分发您的代码。它也有助于明确项目的开放性和使用限制，确保意图得以尊重。开源社区提供了许多常见的开源许可证，供开发人员选择，如MIT许可证、Apache许可证、GPL等。选择最适合您项目需求的许可证是至关重要的。同时，除了存储库的LICENSE文件，许可证信息通常也包含在代码文件的顶部注释中，以提醒开发人员和用户知晓项目的许可条件。

选择许可证是项目创建时的一项关键决策，所以确保充分了解许可证的含义，以及如何正确地使用它，对于项目的长期可持续性非常重要。如果不确定项目应该使用哪个许可证，请查看[choosealicense.com](choosealicense.com)。当然，也可以在没有许可的情况下自由发布代码，但这将阻止许多人可能使用您的代码。

#### Setup.py

```
./setup.py
```

如果模块包在存储库的根目录下，那么这显然也应该在根目录下。确保`setup.py`和其他项目文件按照规范放置，有助于项目的清晰性和易维护性，并使其对其他开发人员更加友好。

#### Requirements文件

```
./requirements.txt
```

一个pip需求文件应该被放置在存储库的根目录中。它应该指定为项目做出贡献所需的依赖关系：测试、构建和生成文档。`requirements.txt`文件包含了项目的依赖关系列表，包括运行时依赖和开发依赖。这个文件对于管理项目的依赖项非常有用，无论是在开发、测试还是部署阶段。如果您的项目没有开发依赖关系，或者更喜欢通过setup.py设置开发环境，则此文件可能没有必要。

#### 文件

```
./docs/
```

通常，`./docs/`目录用于存放项目的文档文件。这包括项目的参考文件、文档、用户手册、API文档等。文档对于项目的可维护性和可用性非常重要，因为它们帮助用户和其他开发人员了解如何使用和贡献项目。文档通常使`reStructuredText（.rst）`或Markdown等标记语言编写。使用工具如Sphinx可以将这些标记转换为漂亮的HTML文档或其他格式的文档。

维护清晰、准确和详尽的文档有助于提高项目的易用性，减少用户的困惑，以及吸引更多的贡献者。这些文档也可以作为项目的参考，帮助团队成员了解项目的架构和实现。

#### 测试单元

```
./test_sample.py
./tests/
```

首先，一个小的测试套件通常存在于一个文件中

```
./test_sample.py
```

一旦测试套件增长，就可以将测试移动到一个目录中，例如：

```
tests/test_basic.py
tests/test_advanced.py
```

显然，这些测试模块必须导入打包的模块来测试它。可以通过几种方法来做到这一点：

* 期望将该包安装在站点包中

* 使用一个简单的路径修改来正确地解析软件包

这里强烈推荐后者。要求开发人员运行`setup.py`开发来测试一个积极变化的代码库，还要求他们为代码库的每个实例有一个孤立的环境设置。若要提供单个测试的导入上下文，请创建一个`tests/context.py`文件：

```python
import os
import sys
sys.path.insert(0, os.path.abspath(os.path.join(os.path.dirname(__file__), '..')))

import sample
```

然后，在各个测试模块中，像这样导入该模块：

```python
from .context import sample
```

将测试分发到模块内部并不是一个明智的做法，因为这可能会增加用户的复杂性并引入不必要的依赖关系和运行时上下文。用户安装模块通常是为了使用其功能而不是运行模块的测试。如果测试与模块代码混合在一起，可能会导致以下问题：

* 用户的复杂性：用户安装模块时不应该受到测试的影响。
* 不必要的依赖关系：将测试与模块代码混合在一起可能会导致用户安装不必要的依赖关系，这可能会增加模块的大小和复杂性。
* 运行时上下文问题：测试通常需要特定的运行时上下文，例如测试数据库或外部服务。

最佳做法是将测试与模块代码分开，确保模块的用户能够轻松使用它，同时开发者可以专注于确保模块的正确性和质量。这有助于提高代码的可维护性和可用性。

#### Makefile

```
./Makefile
```

Makefiles是一种用于定义项目通用任务的非常有用的工具，尤其适合自动化构建、测试和部署流程。虽然Make最初是用于构建C和C++项目的，但它已经成为一个通用工具，可以用于各种项目，包括Python项目。在Python项目中使用Makefiles的好处包括：

* **统一构建和测试任务**：Makefiles允许您定义和管理项目中的常见任务，例如安装依赖、运行测试等。这使得开发者可以轻松地运行这些任务，确保项目的一致性和可重复性。
* **自动化流程**：Makefiles可以自动化许多重复的任务，减少了手动操作的需求。这有助于提高开发效率和降低出错的风险。
* **易于维护**：Makefiles是文本文件，易于编辑和维护。可以根据项目需求添加、修改或删除任务，而无需深入了解构建工具的内部工作原理。
* **良好的可移植性**：Make是跨平台的工具，可以在多个操作系统上运行。这意味着Makefiles可以在不同环境中使用，而不需要重复编写任务。
* **社区支持**：由于Makefiles在许多项目中广泛使用，因此存在大量的文档和示例，以帮助开发者使用它们。

虽然Makefiles是一种有效的工具，但也存在其他可选项，例如管理脚本（如`manage.py`）或Fabric脚本。这些工具通常用于特定框架或库，以简化项目管理。选择使用哪种工具取决于项目的需求和开发团队的偏好。

**Makefile样例：**

```
init:
     pip install -r requirements.txt
test:
     py.test tests
.PHONY: init test
```

其他通用的管理脚本（例如，`manage.py`或`fabfile.py`）也属于存储库的根目录。

### 关于Django应用

这里注意到了Django应用程序的一个新趋势，许多开发人员由于新的捆绑应用程序模板而不合理地构建其存储库。 如何做到的呢？他们会进入他们的干净、新鲜的存储库，然后像以往一样运行以下命令：

```
$ django-admin.py startproject samplesite
```

所生成的存储库结构如下所示：

```
README.rst
samplesite/manage.py
samplesite/samplesite/settings.py
samplesite/samplesite/wsgi.py
samplesite/samplesite/sampleapp/models.py
```

不要这样做。重复的路径会让您的工具和开发人员都感到困惑。不必要的嵌套对任何人都没有帮助，可以用下面的命令进行替换：

```
$ django-admin.py startproject samplesite .
```

注意`.`,其结果结构：

```
README.rst
manage.py
samplesite/settings.py
samplesite/wsgi.py
samplesite/sampleapp/models.py
```

### 代码的结构至关重要

由于Python中的导入和模块处理方式，相对容易为Python项目创建结构。这里的“容易”意味着没有太多的约束，并且模块导入模型容易理解。因此，需要完成的是纯粹的架构任务，即创建项目的不同部分以及它们之间的交互。 简化项目的结构意味着也容易犯错误。一些项目结构不佳的迹象包括：

* **多个混乱的循环依赖**：如果`furn.py`中的`Table`和`Chair`类需要从`workers.py`中导入`Carpenter`来回答像`table.isdoneby()`这样的问题，反之亦然，`Carpenter`类需要导入`Table`和`Chair`来回答`carpenter.whatdo()`这样的问题，那么就有了循环依赖。在这种情况下，将不得不采取脆弱的解决方案，例如在方法或函数内部使用导入语句。
* **隐藏的耦合**：对`Table`实现的每一次更改都会破坏与其无关的20个测试用例，因为它会破坏`Carpenter`的代码，需要非常仔细的手术来适应更改。这意味着对`Carpenter`代码中对`Table`的假设太多，或者反过来也是如此。

* **全局状态或上下文的大量使用**：`Table`和`Carpenter`不是显式地将（高度、宽度、类型、木材等）传递给对方，而是依赖于可以被不同的模块动态修改的全局变量。需要仔细审查对这些全局变量的访问，以了解为什么一个矩形桌变成了一个正方形，以及发现远程模板代码也在修改这个上下文，干扰了桌子的尺寸。
* **面条式代码（Spaghetti code）**：多页嵌套的if子句和for循环，带有大量复制粘贴的过程式代码，没有适当的分段，被称为面条式代码。Python的有意义的缩进（它最具争议的特性之一）使得维护这种代码非常困难。所以好消息是，可能不会经常遇到这种情况。
* **馄饨式代码（Ravioli code）**：它由数百个相似的小逻辑片段组成，通常是类或对象，没有适当的结构。如果永远不记得是否要使用`FurnitureTable`、`AssetTable`还是`Table`，甚至是`TableNew`来完成手头的任务，那么可能正在应对Ravioli代码。这意味着代码可能过于碎片化，需要更好的组织和结构。