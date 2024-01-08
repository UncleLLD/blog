---
layout: '../../layouts/MarkdownPost.astro'
title: 'Git LFS: 简单高效的大文件版本控制'
pubDate: 2024-01-08
description: 'Git LFS: 简单高效的大文件版本控制'
author: 'UncleLLD'
cover:
    url: 'https://i.imgs.ovh/2023/12/27/ktv80.th.png'
    alt: 'cover'
tags: ["Git"]
theme: 'dark'
featured: true
---


## Git Large File Storage

## 问题

在使用git上传大文件时候，git push时候会报错:

```
remote: error: File xxx.tar.gz is 135.17 MB; this exceeds GitHub's file size limit of 100 MB
```

可以看到，git限制上传大小是100MB，超过的话就会报错，找了一圈，学到了一个新东西git LFS，这里记录一下。

## 用于对大文件进行版本控制的开源 Git 扩展

Git 大文件存储 (LFS) 使用 Git 内部的文本指针替换音频样本、视频、数据集和图形等大文件，同时将文件内容存储在 GitHub.com 或 GitHub Enterprise 等远程服务器上。

![ktv80.png|inline](https://i.imgs.ovh/2023/12/27/ktv80.png)

## 入门指南

下载并安装Git命令行扩展安装。

**安装并初始化**，一旦下载并安装完成，请通过运行以下命令为您的用户帐户设置Git LFS：

```shell
git lfs install
```

只需要在每个用户帐户中运行一次此命令。

**跟踪大型文件**。在每个要使用Git LFS的Git存储库中，选择您想要Git LFS管理的文件类型（或直接编辑`.gitattributes`文件）。可以随时配置额外的文件扩展名。

```shell
git lfs track "*.psd"
git lfs track "*.pdf"
```

现在确保`.gitattributes`文件已被跟踪：

```shell
git add .gitattributes
```

请注意，仅定义Git LFS应跟踪的文件类型并不能自行将任何现有文件转换为Git LFS，比如其他分支上的文件或您之前的提交历史中的文件。为此，请使用`git lfs migrate`命令，该命令具有一系列选项，旨在适应各种潜在用例。

**提交和推送**。只需像往常一样提交和推送到GitHub；例如，如果当前的分支名为`main`：

```
git add file.psd
git commit -m "Add design file"
git push origin main
```

**下载和还原**：当其他人克隆或拉取您的仓库时，需要运行以下命令来下载和还原 LFS 文件：

```shell
git lfs fetch
git lfs checkout
```

* `git lfs fetch`：该命令用于从远程 LFS 服务器下载 LFS 文件。当其他人从远程仓库中拉取或克隆包含 LFS 文件的仓库时，他们需要运行 `git lfs fetch` 命令来获取这些文件的实际内容。这个命令会从 LFS 服务器上下载并将 LFS 文件还原到本地仓库中。
* `git lfs checkout`：该命令用于还原本地仓库中的 LFS 文件。在执行 `git lfs fetch` 命令后，LFS 文件的实际内容会被下载到本地仓库的 `.git/lfs` 目录中。然而，这些文件只是指针文件，并没有在工作目录中显示实际内容。通过运行 `git lfs checkout` 命令，LFS 文件的实际内容会被还原到工作目录中，使得能够访问和使用这些文件。

如果不想将某某文件进行Git LFS处理，比如说`readme.md`， 可以在`.gitattributes`文件中，添加一行规则来指定不将`readme.md`文件包含在Git LFS中，这将告诉Git不要将`readme.md`文件进行Git LFS处理。

```shell
readme.md !filter
```

保存并关闭`.gitattributes`文件，并将`.gitattributes`文件提交到Git版本控制中。如果之前已经将`readme.md`文件添加到Git LFS中，可以使用以下命令将其从Git LFS中移除：

```shell
git lfs untrack readme.md
```

注意，这些操作只会影响后续的Git操作，不会改变之前已经提交到Git LFS的文件。

## 其他

查看[维基](https://github.com/git-lfs/git-lfs/wiki?utm_source=gitlfs_site&utm_medium=wiki_link&utm_campaign=gitlfs)、[讨论论坛](https://github.com/git-lfs/git-lfs/discussions?utm_source=gitlfs_site&utm_medium=discussions_link&utm_campaign=gitlfs)和[文档](https://github.com/git-lfs/git-lfs/tree/main/docs?utm_source=gitlfs_site&utm_medium=docs_link&utm_campaign=gitlfs)，以获取有关可能遇到的任何问题的帮助！

Git LFS是一个开源项目 要开始讨论、提交问题或为项目做贡献，请访问存储库或阅读贡献指南。

如果对将Git LFS集成到另一个工具或产品中感兴趣，可能想阅读[API规范](https://github.com/git-lfs/git-lfs/blob/main/docs/api/README.md?utm_source=gitlfs_site&utm_medium=api_spec_link&utm_campaign=gitlfs)或查看[参考服务器实现](https://github.com/git-lfs/lfs-test-server?utm_source=gitlfs_site&utm_medium=reference_servedr&utm_campaign=gitlfs)。

## 特性

### 大文件版本控制

使用Git对大文件进行版本控制，即使这些文件的大小达到几GB。

### 更多的存储空间

在Git存储库中托管更多内容。通过外部文件存储，轻松保持存储库的可管理大小。

### 更快的克隆和提取

下载更少的数据。这意味着在处理大文件的存储库中进行更快的克隆和提取。

### 相同的Git工作流

像往常一样在Git上工作，无需额外的命令、辅助存储系统或工具集。

### 相同的访问控制和权限

在与GitHub等远程主机一起工作时，对大文件保持与Git存储库的其余部分相同的访问控制和权限。

## 参考

* [https://git-lfs.com/](https://git-lfs.com/)
* [API规范](https://github.com/git-lfs/git-lfs/blob/main/docs/api/README.md?utm_source=gitlfs_site&utm_medium=api_spec_link&utm_campaign=gitlfs)
* [文档](https://github.com/git-lfs/git-lfs/tree/main/docs?utm_source=gitlfs_site&utm_medium=docs_link&utm_campaign=gitlfs)
* [论坛](https://github.com/git-lfs/git-lfs/discussions?utm_source=gitlfs_site&utm_medium=discussions_link&utm_campaign=gitlfs)
* [https://blog.csdn.net/xixihahalelehehe/article/details/123137847](https://blog.csdn.net/xixihahalelehehe/article/details/123137847)

