---
layout: '../../layouts/MarkdownPost.astro'
title: 'Python程序打包指南：手把手教你一步步完成'
pubDate: 2023-11-15
description: 'Python项目打包并发布'
author: 'UncleLLD'
cover:
    url: 'https://i.imgs.ovh/2023/11/16/ngsZl.th.png'
    alt: 'cover'
tags: ["编程语言"]
theme: 'dark'
featured: true
---

## Python程序打包指南：手把手教你一步步完成

最近感兴趣想将开发的项目转成Package，研究了一下相关文章，并且自己跑通了，走了一下弯路，这里记录一下如何打包一个简单的Python项目，展示如何添加必要的文件和结构来创建包，如何构建包，以及如何将其上传到Python包索引（PyPI）。

首先要确保安装最新版本：

```shell
# Unix/macOS
python3-m pip install--upgrade pip

# windows
py-m pip install--upgrade pip
```

## 一个简单的项目

本教程使用一个名为`example_package_YOUR_USERNAME_HERE`的简单项目。如果你的用户名是`me`，那么包将会是`example_package_me`；另外确保有一个唯一的包名，不会与遵循本教程的其他人上传的包冲突。建议在打包自己的项目之前，按照本教程的原样使用这个项目。

在本地创建以下文件结构：

```shell
packaging_tutorial/
└── src/
    └── example_package_YOUR_USERNAME_HERE/
        ├── __init__.py
        └── example.py
```

包含Python文件的目录应该与项目名称匹配。这简化了配置，对于安装包的用户来说更加明显。

`__init__. py`是将目录导入为包所必需的，即使在本教程中，该文件是空的。

`example.py`是包内模块的示例，该模块可能包含包的逻辑（函数、类、常量等）。打开该文件并输入以下内容：

```python
def add_one(number):
    return number + 1
```

创建此结构后，需要在`packaging_tutorial`目录中运行本教程中的所有命令。

## 创建包文件

现在将添加用于准备项目以进行分发的文件。完成后，项目结构将如下所示：

```shell
packaging_tutorial/
├── LICENSE
├── pyproject.toml
├── README.md
├── src/
│   └── example_package_YOUR_USERNAME_HERE/
│       ├── __init__.py
│       └── example.py
└── tests/
```

## 创建测试目录

`test/`是测试文件的占位符，这里暂时将其留空。

## 选择构建后端

像[pip](https://packaging.python.org/en/latest/key_projects/#pip)和[build](https://packaging.python.org/en/latest/key_projects/#build)这样的工具实际上不会将源代码转换为[分发包](https://packaging.python.org/en/latest/glossary/#term-Distribution-Package)（如轮子）；该工作由[构建后端](https://packaging.python.org/en/latest/glossary/#term-Build-Backend)执行。构建后端决定您的项目将如何指定其配置，包括元数据（有关项目的信息，例如，PyPI上显示的名称和标签）和输入文件。构建后端具有不同级别的功能，例如它们是否支持构建[扩展模块](https://packaging.python.org/en/latest/glossary/#term-Extension-Module)，应该选择适合需求和偏好的一个。

这里可以从许多后端中进行选择；本教程默认使用[Hatchling](https://packaging.python.org/en/latest/key_projects/#hatch)，但它将与支持元数据的[setuptools](https://packaging.python.org/en/latest/key_projects/#setuptools)、[Flight](https://packaging.python.org/en/latest/key_projects/#flit)、[PDM](https://packaging.python.org/en/latest/key_projects/#pdm)和其他支持`[project]`表的方法相同。

`pyproject.toml`告诉[构建前端](https://packaging.python.org/en/latest/glossary/#term-Build-Frontend)工具，如[pip](https://packaging.python.org/en/latest/key_projects/#pip)和[build](https://packaging.python.org/en/latest/key_projects/#build)，为项目使用哪个后端。以下是一些常见构建后端的示例，但请查看后端自己的留档以获取更多详细信息。

```shell
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[build-system]
requires = ["setuptools>=61.0"]
build-backend = "setuptools.build_meta"

[build-system]
requires = ["flit_core>=3.4"]
build-backend = "flit_core.buildapi"

[build-system]
requires = ["pdm-backend"]
build-backend = "pdm.backend"
```

`requires`键是构建包所需的包列表。[前端](https://packaging.python.org/en/latest/glossary/#term-Build-Frontend)应该在构建包时自动安装它们。前端通常在隔离的环境中运行构建，因此在这里省略依赖项可能会导致构建时错误。这应该始终包括后端的包，并且可能有其他构建时依赖项。

该`build-backend`键是前端用来执行构建的Python对象的名称。

这两个值都将由构建后端的留档提供，或者由其命令行界面生成，不需要自定义这些设置。

构建工具的其他配置将在`pyproject.toml`的`工具`部分或构建工具定义的特殊文件中。例如，当使用`setuptools`作为构建后端时，可以将其他配置添加到`setup.py`或`settings.cfg`文件，并指定`setuptools。build_meta`在构建中允许工具自动定位和使用这些。

### 配置元数据

打开`pyproject.toml`并输入以下内容。更改`name`以包含用户名；这可确保拥有唯一的包名，不会与遵循本教程的其他人上传的包冲突。

```shell
[project]
name = "example_package_YOUR_USERNAME_HERE"
version = "0.0.1"
authors = [
  { name="Example Author", email="author@example.com" },
]
description = "A small example package"
readme = "README.md"
requires-python = ">=3.7"
classifiers = [
    "Programming Language :: Python :: 3",
    "License :: OSI Approved :: MIT License",
    "Operating System :: OS Independent",
]

[project.urls]
"Homepage" = "https://github.com/pypa/sampleproject"
"Bug Tracker" = "https://github.com/pypa/sampleproject/issues"
```

- `name`是包的分发名称。它可以是任何名称，只要它只包含字母、数字、`.`、`_`和`-`。它也不能已经在PyPI上使用。在本教程中，**请务必使用自己的用户名更新它**，因为这可以确保不会尝试上传与已存在的名称相同的包。
- `version`是包版本。有关版本的更多详细信息，请参阅[版本说明符规范](https://packaging.python.org/en/latest/specifications/version-specifiers/#version-specifiers)。一些构建后端允许以其他方式指定它，例如从文件或git tag。
- `authors`用于标识包的作者；可以为每个作者指定姓名和电子邮件。还可以以相同的格式列出`maintainers`。
- `description`是对包装的简短的一句话总结。
- `readme`是包含包的详细描述的文件的路径。这显示在PyPI上的包详细信息页面上。在这种情况下，描述是从`README.md`加载的（这是一种常见的模式）。[项目元数据规范](https://packaging.python.org/en/latest/specifications/declaring-project-metadata/#declaring-project-metadata)中还有一种更高级的表格形式。
- `requires-python`提供项目支持的Python版本。像[pip](https://packaging.python.org/en/latest/key_projects/#pip)这样的安装程序会回顾旧版本的包，直到找到一个具有匹配Python版本的包。
- `classifiers`提供有关包的索引和[pip](https://packaging.python.org/en/latest/key_projects/#pip)一些附加元数据。在这种情况下，包仅与Python3兼容，根据MIT许可证获得许可，并且OS独立。您应该始终至少包括包适用于Python的哪个版本，包在哪个许可证下可用，以及包将适用于哪些操作系统。有关分类器的完整列表，请参阅https://pypi.org/classifiers/。
- `URL`允许列出要在PyPI上显示的任意数量的额外链接。通常这可能是指向源、留档、问题跟踪器等。

有关可以在`[project]`表中定义的这些和其他字段的详细信息，请参阅[项目元数据规范](https://packaging.python.org/en/latest/specifications/declaring-project-metadata/#declaring-project-metadata)。其他常见字段是提高可发现性的`关键字`和安装包所需的`依赖项`。

## 创建README.md

打开`README.md`并输入以下内容，也可以自定义此内容。

```markdown
# Example Package

This is a simple example package. You can use
[GitHub-flavored Markdown](https://guides.github.com/features/mastering-markdown/)
to write your content.
```

## 创建LICENSE

上传到Python包索引的每个软件包都必须包含许可证。这将告诉安装软件包的用户他们可以使用上传软件包的条款。有关选择许可证的帮助，请参阅https://choosealicense.com/。选择许可证后，打开`LICENSE`并输入许可证文本。例如，如果选择了MIT许可证：

```markdown
Copyright (c) 2018 The Python Packaging Authority

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

大多数构建后端会自动在包中包含许可证文件。有关详细信息，请参阅后端的留档。

## 包括其他文件

上面列出的文件将自动包含在[源发行版](https://packaging.python.org/en/latest/glossary/#term-Source-Distribution-or-sdist)中。如果想包含其他文件，请参阅构建后端的留档。

## 生成分发档案

下一步是为包生成[分发包](https://packaging.python.org/en/latest/glossary/#term-Distribution-Package)。这些是上传到Python包索引的存档，可以通过[pip](https://packaging.python.org/en/latest/key_projects/#pip)安装。确保安装了最新版本的PyPA[ bulid](https://packaging.python.org/en/latest/key_projects/#build)：

```shell
# Unix/macOS
python3 -m pip install --upgrade build

# Windows
py -m pip install --upgrade build
```

现在从pyproject. toml所在的同一目录运行下面的命令：

```shell
# Unix/macOS
python3 -m build

# Windows
py -m build
```

这个命令应该输出很多文本，一旦完成应该在`dist`目录中生成两个文件：

```shell
dist/
├── example_package_YOUR_USERNAME_HERE-0.0.1-py3-none-any.whl
└── example_package_YOUR_USERNAME_HERE-0.0.1.tar.gz
```

`tar.gz`文件是[源发行版](https://packaging.python.org/en/latest/glossary/#term-Source-Distribution-or-sdist)，而`.whl`文件是[构建发行版](https://packaging.python.org/en/latest/glossary/#term-Built-Distribution)。较新的[pip](https://packaging.python.org/en/latest/key_projects/#pip)版本优先安装构建发行版，但如果需要可以回退到源发行版。应该始终上传源发行版，并为项目兼容的平台提供构建发行版。在这种情况下，本文的示例包与任何平台上的Python兼容，因此只需要一个构建发行版。

## 上传分发档案

最后将打好的包上传到Python包索引，可供其它人安装。需要做的第一件事是在TestPyPI上注册一个帐户，这是一个用于测试和实验的包索引的单独实例。对于像本教程这样不一定想上传到真实索引的东西，这样的测试环境是非常好的。

首先要注册一个帐户，打开https://test.pypi.org/account/register/并完成该页面上的步骤。还需要在能够上传任何包之前验证填写的电子邮件地址。有关更多详细信息，请参阅[使用TestPyPI](https://packaging.python.org/en/latest/guides/using-testpypi/)。

要安全地上传项目，还需要一个PyPI[API令牌](https://test.pypi.org/help/#apitoken)。https://test.pypi.org/manage/account/#api-tokens创建一个，将“范围”设置为“整个帐户”。**在复制并保存令牌之前不要关闭页面——您将不会再看到该令牌。**

这一步需要QR扫描二维码，得到6位数字验证码，另外获得的令牌如何使用呢？这是踩的弯路，详细说一下：

PyPi 调整了安全策略，不再允许启用两步验证的账号使用用户名密码来上传项目了，必须使用 API 令牌来进行身份验证。登录 PyPi ，进入[账户设置](https://pypi.org/manage/account/)页，点击「添加 API 令牌」按钮创建 API 令牌。然后修改 `~/.pypirc` 配置文件， 用户名字段改为 `__token__` ，密码字段改为刚才创建的令牌：

```markdown
[testpypi]
  username = __token__
  password = pypi-AgENdGVzdC5weXBpLm9xxxxxxxxxxxxxxx
```

注册好之后就可以使用[twine](https://packaging.python.org/en/latest/key_projects/#twine)上传分发包。上传之前需要先安装Twine：

```shell
# Unix/macOS
python3 -m pip install --upgrade twine

# Windows
py -m pip install --upgrade twine
```

安装后，运行Twine上传`dist`下的所有存档：

```shell
# Unix/macOS
python3 -m twine upload --repository testpypi dist/*

# Windows
py -m twine upload --repository testpypi dist/*
```

命令完成后，您应该会看到类似于以下内容的输出：

```shell
Uploading distributions to https://test.pypi.org/legacy/
Enter your username: __token__
Uploading example_package_YOUR_USERNAME_HERE-0.0.1-py3-none-any.whl
100% ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 8.2/8.2 kB • 00:01 • ?
Uploading example_package_YOUR_USERNAME_HERE-0.0.1.tar.gz
100% ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 6.8/6.8 kB • 00:00 • ?
```

上传后，打的包应该可以在TestPyPI上查看；例如：`https://test.pypi.org/project/example_package_YOUR_USERNAME_HERE`。

![package.png|inline](https://i0.imgs.ovh/2023/11/09/lJHnT.png)

## 安装新上传的包

上传成果后，可以使用[pip](https://packaging.python.org/en/latest/key_projects/#pip)安装包并验证它是否有效。创建一个[虚拟环境](https://packaging.python.org/en/latest/tutorials/installing-packages/#creating-and-using-virtual-environments)并从TestPyPI安装上传的包：

```shell
# Unix/macOS
python3 -m pip install --index-url https://test.pypi.org/simple/ --no-deps example-package-YOUR-USERNAME-HERE

# Windows
py -m pip install --index-url https://test.pypi.org/simple/ --no-deps example-package-YOUR-USERNAME-HERE
```

pip应该从TestPyPI安装包，输出应该如下所示：

```shell
Collecting example-package-YOUR-USERNAME-HERE
  Downloading https://test-files.pythonhosted.org/packages/.../example_package_YOUR_USERNAME_HERE_0.0.1-py3-none-any.whl
Installing collected packages: example_package_YOUR_USERNAME_HERE
Successfully installed example_package_YOUR_USERNAME_HERE-0.0.1
```

> 注 此示例使用`--index-url`标志来指定TestPyPI而不是live PyPI。此外，它还指定`--no-deps`。由于TestPyPI没有与live PyPI相同的软件包，因此尝试安装依赖项可能会失败或安装意外的东西。虽然我们的示例包没有任何依赖项，但在使用TestPyPI时避免安装依赖项是一个很好的做法。

接下来就可以通过导入软件包来测试它是否已正确安装。确保仍在虚拟环境中，然后运行Python：

```python
from example_package_YOUR_USERNAME_HERE import example

example.add_one(2)
# 3
```

## 后续

**恭喜，已成果打包并分发了一个Python项目！**✨🍰✨

请记住，本教程展示了如何将包上传到Test PyPI，这不是永久存储。Test系统偶尔会删除包和帐户。最好像本教程一样使用TestPyPI进行测试和实验。

当准备好将真实包上传到Python包索引时，可以像本教程中一样执行相同的操作，但有以下重要区别：

- 为包选择一个难忘且独特的名称；
- 在[https://pypi.org](https://pypi.org/)上注册一个帐户，这是两个独立的服务器，测试服务器的登录详细信息不与主服务器共享；
- 使用`twine上传dist/*`上传自己的包，并输入正式PyPI环境上注册的帐户的凭据。在生产环境中上传包，不需要指定`--repository`；默认情况下，包将上传到https://pypi.org/；
- 使用`python3-m pip install[your-package]`从真正的PyPI安装包；

## 参考

* [Hattch](https://packaging.python.org/en/latest/key_projects/#hatch)
* [Flit](https://packaging.python.org/en/latest/key_projects/#flit)
* [pdm](https://packaging.python.org/en/latest/key_projects/#pdm)
* [poetry](https://packaging.python.org/en/latest/key_projects/#poetry)
* [PEP517](https://peps.python.org/pep-0517/)
* [PEP518](https://peps.python.org/pep-0518/)
* [https://segmentfault.com/a/1190000008663126](https://segmentfault.com/a/1190000008663126)
* [Packaging binary extensions](https://packaging.python.org/en/latest/guides/packaging-binary-extensions/)]

* [https://www.fournoas.com/posts/authentication-on-pypi-using-api-token-instead-of-password/](https://www.fournoas.com/posts/authentication-on-pypi-using-api-token-instead-of-password/)
* [https://packaging.python.org/en/latest/tutorials/packaging-projects/](https://packaging.python.org/en/latest/tutorials/packaging-projects/)
