---
layout: '../../layouts/MarkdownPost.astro'
title: 'AWS CodeWhisperer'
pubDate: 2023-04-19
description: '现已正式推出实时 AI 编程助手 Amazon CodeWhisperer，包括 CodeWhisperer 个人套餐，所有开发人员均可免费使用'
author: 'UncleLLD'
cover:
    url: 'https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e9aee9d218fc4c1c872638057de236aa~tplv-k3u1fbpfcp-zoom-1.image'
    alt: 'cover'
tags: ["AWS", "工具", "AIGC"]
theme: 'dark'
featured: true
---



## Amazon CodeWhisperer 

现已正式推出[实时 AI 编程助手](https://aws.amazon.com/blogs/machine-learning/introducing-amazon-codewhisperer-the-ml-powered-coding-companion/) [Amazon CodeWhisperer](https://aws.amazon.com/codewhisperer/)，包括 CodeWhisperer 个人套餐，所有开发人员均可免费使用。[最初于去年推出的预览版](https://aws.amazon.com/blogs/aws/now-in-preview-amazon-codewhisperer-ml-powered-coding-companion/) CodeWhisperer 让开发人员能够保持专注、高效，帮助他们快速、安全地编写代码，无需因离开 IDE 去研究某件事而中断流程。在为复杂且不断变化的环境创建代码时，开发人员可以通过在他们最喜欢的 IDE（包括 Visual Studio Code、IntelliJ IDEA 等）中使用 CodeWhisperer 来提高工作效率并简化工作。在为常规任务或耗时、无差异的任务创建代码、使用不熟悉的 API 或 SDK、正确有效地使用 AWS API 以及其他常见的编码场景（例如读写文件、图像处理、编写单元测试等），CodeWhisperer 都可以提供帮助。

![通过 CodeWhisperer 中的注释生成代码](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3401c70b8fb64ec4b21be508aecb562c~tplv-k3u1fbpfcp-zoom-1.image)

同时也提供了参考代码追踪， 当生成与训练数据相似的代码时，它会提供额外建议数据

![CodeWhisperer 中的参考跟踪](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e9aee9d218fc4c1c872638057de236aa~tplv-k3u1fbpfcp-zoom-1.image)

CodeWhisperer 也是唯一一个具有安全扫描功能的 AI 编码助手，可以发现难以检测的漏洞并提出修复建议，扫描生成的代码和开发人员编写的代码，寻找漏洞

![在 CodeWhisperer 中扫描漏洞](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f719d4da7cd243878ca49c25a01b2804~tplv-k3u1fbpfcp-zoom-1.image)

目前，所有开发人员都可以使用 [Amazon CodeWhisperer](https://aws.amazon.com/codewhisperer/) 编写 Python、Java、JavaScript、TypeScript、C#、Go、Rust、PHP、Ruby、Kotlin、C、C++、Shell 脚本、SQL 和 Scala 代码，只需要邮箱注册一下即可。

## 使用方法

### 注册

* 首先选择IDE，支持vs code和jetbrains， 这里以vscode为例


![image-20230419104157790.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/68a23dd7e18c4ce48462ec0a339b895a~tplv-k3u1fbpfcp-watermark.image?)

* 安装aws toolkit


![image-20230419104222568.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/13234e4ed5f24cbf982a67ff664672d4~tplv-k3u1fbpfcp-watermark.image?)

* 注册认证即可使用


![image-20230419104244745.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/680936b39d26471cb8473fa7fa0448a3~tplv-k3u1fbpfcp-watermark.image?)

点击start，然后会出一个弹窗，复制然后打开链接，进行邮箱注册即可。


![image-20230419104307227.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d8134f7407584f3a9b5d63165f3f187e~tplv-k3u1fbpfcp-watermark.image?)
![image-20230419104709686.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/76028a0ceda148c0a7646877725754f5~tplv-k3u1fbpfcp-watermark.image?)


![image-20230419104720785.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/765006ff84dc4d7b905a7d6da92dade8~tplv-k3u1fbpfcp-watermark.image?)


![image-20230419104754476.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1a61aea04292440eb7feef8559bb50b5~tplv-k3u1fbpfcp-watermark.image?)


### 开始使用


![image-20230419104921215.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f42d5c49e58c4500939559baf880428c~tplv-k3u1fbpfcp-watermark.image?)

以windows为例，`alt+c`是生成代码，`tab`是接收生成的代码，左右箭头是选择前后生成的候选。这里以quicksort为例，当你输入`def quicksort()` 然后输入alt+c就能生成下面的代码，可以看到是基于递归写的一个代码。


![image-20230419105548069.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3cbdab3b6c964c4f80c8fd6b9bbae23c~tplv-k3u1fbpfcp-watermark.image?)

使用`tab`接收即可


![image-20230419104949668.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/367a2a28a2a44ab284b0f95158ab2aa3~tplv-k3u1fbpfcp-watermark.image?)

## 参考

* [https://aws.amazon.com/cn/blogs/china/amazon-codewhisperer-free-for-individual-use-is-now-generally-available/](https://aws.amazon.com/cn/blogs/china/amazon-codewhisperer-free-for-individual-use-is-now-generally-available/)

* [https://docs.aws.amazon.com/codewhisperer/latest/userguide/whisper-setup-indv-devs.html](https://docs.aws.amazon.com/codewhisperer/latest/userguide/whisper-setup-indv-devs.html)

  


