---
layout: '../../layouts/MarkdownPost.astro'
title: '理解人脸识别中的训练集Train Set、画廊集Gallery Set和探针集Probe Set'
pubDate: 2023-12-25
description: '本文介绍人脸识别中的训练集Train Set、画廊集Gallery Set和探针集Probe Set'
author: 'UncleLLD'
cover:
    url: 'https://i.imgs.ovh/2023/12/26/keitW.th.png'
    alt: 'cover'
tags: ["计算机视觉"]
theme: 'dark'
featured: true
---


## 理解人脸识别中的训练集Train Set、画廊集Gallery Set和探针集Probe Set

在构建使用深度学习的人脸识别模型时，需要构建一个训练集、画廊集Gallery set和探针集Probe Set来评估模型的性能。

在本教程中，将介绍这三个集合。

### Train Set|训练集

训练集通常用于训练模型，并通常被分为三个部分。

例如：这里的数据是整个训练集，它将被分割为训练集、验证集和测试集。

![train set|inline](https://www.tutorialexample.com/wp-content/uploads/2021/09/split-data-into-train-validation-and-test-set.png)

对于训练集、验证集和测试集，应该按照以下方式使用它们：

* 训练集：用于训练模型。

* 验证集：用于选择超参数，如学习率、批量大小等。

* 测试集：用于计算最终指标。通常，会基于验证集上的最佳结果选择，并在测试集上的结果作为模型的最终结果。

例如，对于分类问题，在训练模型时每隔 25 steps计算一次验证集和测试集的准确率。验证集的最佳准确率为98%，在4000 steps时测试集的最佳准确率为96%。即使在5000步时测试集的准确率为98%，模型的准确率仍然是96%。

### Gallery Set|画廊集

假设有一个包含 500 人的黑名单，可以使用每个人的一张、两张或更多张人脸照片来构建这个黑名单。

例如，可能选择使用每个人的两张人脸照片来构建这个黑名单，这样这个黑名单将包含 1,000 个条目。

![Gallery set|inline](https://www.tutorialexample.com/wp-content/uploads/2021/09/a-blacklist-example.png)

这个黑名单就是一个画廊集（gallery set），你将使用一个模型来判断一个人是否在这个黑名单中。很容易理解，我们不能使用图像集中的数据来训练模型。

### Probe Set|探针集

Probe set 也不能用于训练模型。它通常包括两个部分：

**第一部分： 画廊集中的数据。**

例如，探针集和画廊集中都有 250 个人，然而，他们的人脸图像是不同的。模型应该通过他的人脸图像识别判断探针集中的一个人是否也在画廊集中。

**第二部分：不在画廊集中的数据。**

如上面提到的黑名单，模型应该判断一个不在黑名单中的人确实不在黑名单中。

如果一个人在黑名单中，模型无法在黑名单中找到他，这表明模型出错，这种情况被称为虚警率（False Rejection Rate，FRR）。 如果一个人不在黑名单中，但是模型通过他的人脸图像找到了一个相似的人，并假设他是一个不可靠的人，模型这种结果也是错误的，这种情况被称为虚警率（False Acceptance Rate，FAR）。

在论文《The CAS-PEAL large-scale Chinese face database and baseline evaluations》中，定义了训练集、画廊集和测试集，它们分别是：

* Training set：一个训练集是一组用于生成通用表示并调整算法参数的图像。在该协议中，训练集包含1,200张图像（从CAS-PEAL-R1数据库的1,040个主题中随机选择300个主题，每个主题包含从CAS-PEAL-R1数据库的前瞻子集中随机选择的四张图像）。
* Gallery set：一个画廊集是已知个体图像的集合，用于与测试图像进行匹配。在该协议中，画廊集包含1,040个主题的1,040张图像（每个主题在正常条件下有一张图像）。
* Probe set：一个探针集是需要被识别的未知个体的探针图像的集合。在该协议中，来自CAS-PEAL-R1数据库的九个探针集被组成。其中，六个探针集对应于正面子集中的六个子集：表情、光照、配饰、背景、距离和老化。另外三个探针集对应于姿势子集中主题的图像：向上看、正对摄像机C4（中间那个）和向下看。所有出现在训练集中的图像都被排除在这些探针集之外。

### 总结

在计算机视觉人脸识别中，gallery set（画廊集）和probe set（探测集）是两个重要的概念。

* Gallery set（画廊集）是指一个包含已知身份的人脸图像集合。通常，这个集合是由预先收集的、已经进行身份验证的人脸图像组成。Gallery set 作为一个参考库，用于与待识别的人脸图像进行比对，以确定其身份。

* Probe set（探测集）是指待识别的人脸图像集合。这个集合中的人脸图像需要通过与 Gallery set 进行比对，来推断其身份。Probe set 通常包含需要进行身份验证或识别的未知人脸图像。

在人脸识别任务中，使用 gallery set 中的已知身份图像作为参考，通过比对 probe set 中的待识别图像与 gallery set 中的图像相似度来进行身份验证或识别。通过建立 gallery set 和 probe set 的对应关系，可以通过人脸特征的匹配来进行人脸识别。

总结来说，gallery set 是已知身份的人脸图像集合，而 probe set 是待识别的人脸图像集合。它们在人脸识别中起到了关键的作用，用于推断和验证人脸的身份。
