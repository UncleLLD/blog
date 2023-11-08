---
layout: '../../layouts/MarkdownPost.astro'
title: 'Python中的数据增强技术'
pubDate: 2023-11-08
description: '用imgaug快速观察Python中的数据增强技术'
author: 'UncleLLD'
cover:
    url: 'https://i0.imgs.ovh/2023/11/08/lSHcd.th.png'
    alt: 'cover'
tags: ["编程语言", "工具"]
theme: 'dark'
featured: true
---


## Python中的数据增强技术

使用imgaug快速观察Python中的数据增强技术

在本文中，我们将使用imgaug库来探索Python中不同的数据增强技术

### 什么是图像增强

图像增强是一种强大的技术，用于在现有图像中人为地创建变化以扩展图像数据集。这是通过应用不同的变换技术来实现的，例如缩放、旋转、剪切或裁剪现有图像。目标是创建一组全面的可能图像，代表各种变化。

### 为什么需要图像增强

图像增强在深度学习卷积神经网络 (CNN) 背景下至关重要，因为它有助于满足有效模型训练对大型且多样化数据集的需求。 CNN 需要大量图像才能有效训练，而图像增强提供了一种人为扩展现有数据集的方法。通过缩放、旋转、剪切或裁剪等技术创建图像变化，图像增强有助于生成更全面的可能图像集。这种多样化的数据集使模型能够更好地泛化，减少过度拟合，并在测试或验证过程中对以前未见过的数据进行评估时提高其性能。因此，图像增强对于提高训练数据的质量和数量至关重要，最终导致更强大和更准确的 CNN 模型。

### 什么时候使用图像增强

图像增强可以作为训练模型之前的预处理步骤，也可以在训练过程中实时应用。当用作预处理步骤时，应用增强来增加数据集的大小，特别是在处理需要扩展的小型训练数据集时。这种方法称为离线或预处理增强，涉及生成现有图像的变体以创建更多样化的数据集。应用图像增强时仔细考虑问题领域非常重要，因为某些增强策略可能与特定任务无关或无用。例如，在对不同类型的汽车进行分类时，垂直翻转汽车可能不会为数据集增加价值。因此，图像增强的应用应根据问题领域的具体要求进行定制。

### 离线或预处理增强

离线或预处理增强是指应用图像增强作为预处理步骤来增加数据集的大小。这种方法通常在处理需要扩展的小型训练数据集时使用。通过生成现有图像的变化（例如翻转、旋转或缩放），离线增强可以为训练机器学习模型创建更加多样化的数据集。应用离线增强时，特别是在处理较大的数据集时，考虑磁盘空间非常重要。这种方法允许在训练过程开始之前创建一组全面的可能图像，最终提高训练数据的质量和数量。

### 在线或实时增强

在线或实时增强涉及在训练过程中实时应用增强技术。这种方法通常用于较大的数据集，因为它不需要将增强图像保存在磁盘上。通过实时应用增强，模型在每个时期看到不同的图像，从而有助于训练数据的多样性。实时增强在处理较大的数据集时特别有用，因为它减少了与保存增强图像相关的存储要求。这种方法允许在训练过程中动态应用增强技术，有助于模型从各种图像变化中学习的能力。



我们将使用imgaug类来演示图像增强。imgaug支持广泛的数据增强技术

### 基本数据增强技术

* 翻转（Flipping）：垂直或水平翻转图像

* 旋转（Rotation）：按指定的程度旋转图像

* 剪切（Shearing）：像平行四边形一样移动图像的一部分

* 裁剪（Cropping）：对象在图像中以不同的比例出现在不同的位置上

* 放大缩小（Zoom in, Zoom out）

* 改变亮度或对比度

现在将使用imgaug库探索这些数据增强技术



### 实践Imgaug

imgaug是一个用于图像增强的库，包括关键点/地标、边界框、热图和分割图。

* git项目地址：[https://github.com/aleju/imgaug](https://github.com/aleju/imgaug)
* 项目文档地址：[https://imgaug.readthedocs.io/en/latest](https://imgaug.readthedocs.io/en/latest/source/installation.html)

![examples](https://i0.imgs.ovh/2023/11/08/lSFhp.png)

#### 安装

```shell
pip install imageio imgaug 
pip install imgaug — upgrade — no-deps  # 出现错误时候采用这种
```

#### 导入相关的依赖包

```python
import imageio
import imgaug as ia
import imgaug.augmenters as iaa
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import matplotlib.patches as patches
import matplotlib
%matplotlib inline
```

#### 展示原有图像

```python
image = imageio.v2.imread('./Lenna.jpg')
ia.imshow(image)
```

![Lenna.png](https://i0.imgs.ovh/2023/11/08/lSHcd.png)

#### 旋转图像Rotating

可以通过指定旋转度来旋转图像，随机将图像旋转-50度到30度之间，旋转角度可以是负值，表示逆时针旋转，也可以是正值，表示顺时针旋转

```python
rotate = iaa.Affine(rotate=(-50, 30))
rotated_image = rotate.augment_image(image)
ia.imshow(rotated_image)
```

![rotate.png](https://i0.imgs.ovh/2023/11/08/lSM9K.png)

#### 图像添加噪声Noise

将不同的从高斯分布元素采样的噪声值添加到图像中，参数`10`和`20`分别表示噪声的均值和标准差。高斯噪声是一种常见的随机噪声，它的数值符合高斯分布（也称为正态分布）。均值参数决定了噪声的中心位置，标准差参数决定了噪声的强度。

```python
gaussian_noise = iaa.AdditiveGaussianNoise(10, 20)
noise_image = gaussian_noise.augment_image(image)
ia.imshow(noise_image)
```

![noise.png](https://i0.imgs.ovh/2023/11/08/lSwM2.png)

#### 图像裁剪Cropping

裁剪将删除图像两侧的列/行像素。在下面的例子中，将图像的一侧随机裁剪0~30%， 随机的裁剪，使图像具有更多的多样性和变化。裁剪操作可以用于去除图像边缘的无用信息、调整图像的组成或者创建更密集的图像副本等。

```python
crop = iaa.Crop(percent=(0, 0.3)) # crop image
corp_image = crop.augment_image(image)
ia.imshow(corp_image)
```

![crop.png](https://i0.imgs.ovh/2023/11/08/lS3Sj.png)

#### 图像剪切Shearing

剪切图像随机0到40度，错切变换会将图像的一部分按照一定角度进行平移，从而改变图像的形状和内容。可以用于创建透视效果、扭曲图像的形状或者模拟某些特定场景下的形变。

```python
shear = iaa.Affine(shear=(0, 40))
shear_image=shear.augment_image(image)
ia.imshow(shear_image)
```

![shearing.png](https://i0.imgs.ovh/2023/11/08/lS6FV.png)

#### 图像翻转Flipping

可以垂直地或水平地翻转图像， 这里展示Fliplr水平翻转图像。水平翻转可以用于纠正图像的方向、增加训练数据的多样性或者模拟镜像对称的场景。

```python
#flipping image horizontally
flip_hr = iaa.Fliplr(p=1.0)  # 翻转概率100%， 小于1.0时候是随机翻转，可能不翻转
flip_hr_image = flip_hr.augment_image(image)
ia.imshow(flip_hr_image)
```

![fliplr.png](https://i0.imgs.ovh/2023/11/08/lSWhJ.png)

Filpud垂直翻转图像

```python
flip_vr = iaa.Flipud(p=1.0)  # 翻转概率为100%
flip_vr_image = flip_vr.augment_image(image)
ia.imshow(flip_vr_image)
```

![flipud.png](https://i0.imgs.ovh/2023/11/08/lSkUW.png)

#### 图像改变亮度brightness

通过缩放像素值来调整图像的亮度。在Gamma=(0.5, 2.0)范围内的值是合理的。也可以使用符号对比度或线性对比度来改变图像的亮度。伽马对比度增强器会应用一个伽马变换到图像上，通过调整图像的亮度和对比度来改变图像的外观。伽马值决定了变换的强度，较高的伽马值会增加图像的对比度，使得图像的暗部更暗，亮部更亮。

```python
contrast = iaa.GammaContrast(gamma=2.0)
contrast_image =contrast.augment_image(image)
ia.imshow(contrast_image)
```

![brightness.png](https://i0.imgs.ovh/2023/11/08/lSBCv.png)

#### 图像缩放Scale

可以使用比例尺放大或缩小图像。下面的图像缩放到图像高度/宽度的150%到80%。此外也可以独立地缩放每个轴。函数会随机选择一个缩放比例，范围在1.0到1.5之间，并分别应用于图像的x和y方向。较小的缩放比例会使图像变小，而较大的缩放比例会使图像变大。缩放变换可以用于调整图像的尺寸、改变物体的比例或者模拟不同距离下的图像。

```python
scale_im = iaa.Affine(scale={"x": (1.5, 1.0), "y": (1.5, 1.0)})
scale_image = scale_im.augment_image(image)
ia.imshow(scale_image)
```

![scale.png](https://i0.imgs.ovh/2023/11/08/lSI6e.png)

#### 对目标检测的增强

为目标检测绘制边界框。当增强图像时，希望边界框也被相应地更新。imgaug为边界框提供了支持。当旋转、剪切或裁剪图像时，对象周围的边界框也会相应地更新。

##### 导入边界框

```python
from imgaug.augmentables.bbs import BoundingBox, BoundingBoxesOnImage
```

##### 原图画出框

```python
bbs = BoundingBoxesOnImage([BoundingBox(x1=80, x2=140, y1=70, y2=150)], shape=image.shape)
ia.imshow(bbs.draw_on_image(image, size=2))
```

![detection.png](https://i0.imgs.ovh/2023/11/08/lShe3.png)

在下面的代码中，使用平移百分比来移动图像，扩大边界框，并将其应用到图像上

```python
move = iaa.Affine(translate_percent={"x": 0.1}, scale=0.8)
image_aug, bbs_aug = move(image=image, bounding_boxes=bbs)
ia.imshow(bbs_aug.draw_on_image(image_aug, size=2))
```

![move.png](https://i0.imgs.ovh/2023/11/08/lSs99.png)

##### 在应用图像增强功能后，处理图像外部的边界框

边界框有时可能会在图像之外，需要额外的代码来处理这样的情况，旋转图像并尝试在对象周围绘制边界框。

```python
rotate_bb = iaa.Affine(rotate=(-50, 30))
image_aug, bbs_aug = rotate_bb(image=image, bounding_boxes=bbs)
ia.imshow(bbs_aug.draw_on_image(image_aug, size=2))
```

![rotate.png](https://i0.imgs.ovh/2023/11/08/lSbuH.png)

边界框的某些部分都在图像的外部。在下面的代码中，会看到这些问题:

* 完全或部分删除图像外部的边界框
* 剪切部分在外部的边界框，使它们完全在图像内部

创建一个填充功能，用1像素白色和1像素黑色边框填充图像：

```python
# 填充
def pad(image, by):
    image_border1 = iaa.size.pad(image, top=1, right=1, bottom=1, left=1, mode="constant", cval=255)
    image_border2 = iaa.size.pad(image_border1, top=by-1, right=by-1, bottom=by-1, left=by-1, mode="constant", cval=0)
    return image_border2
```

然后，在图像上绘制边界框。首先通过边界像素扩展图像平面，然后在图像平面内标记边界框

```python
def draw_bbs(image, bbs, border):
    GREEN = [0, 255, 0]
    ORANGE = [255, 140, 0]
    RED = [255, 0, 0]
    image_border = pad(image, border)
    for bb in bbs.bounding_boxes:
        if bb.is_fully_within_image(image.shape):
            color = GREEN
        elif bb.is_partly_within_image(image.shape):
            color = ORANGE
        else:
            color = RED
        image_border = bb.shift(left=border, top=border).draw_on_image(image_border, size=2, color=color)
        return image_border
```

现在，对图像应用相同的旋转，并绘制边界框

```python
rotate = iaa.Affine(rotate=(-50, 30))
image_aug, bbs_aug = rotate(image=image, bounding_boxes=bbs)
image_after = draw_bbs(image_aug,
bbs_aug.remove_out_of_image().clip_out_of_image(), 100)
ia.imshow(image_after)
```

![pad-rotate.png](https://i0.imgs.ovh/2023/11/08/lSodD.png)

### 参考

* [https://imgaug.readthedocs.io/en/latest/index.html](https://imgaug.readthedocs.io/en/latest/index.html)
* [https://towardsdatascience.com/data-augmentation-techniques-in-python-f216ef5eed69](https://towardsdatascience.com/data-augmentation-techniques-in-python-f216ef5eed69)
* [https://blog.keras.io/building-powerful-image-classification-models-using-very-little-data.html](https://blog.keras.io/building-powerful-image-classification-models-using-very-little-data.html)
* [https://nanonets.com/blog/data-augmentation-how-to-use-deep-learning-when-you-have-limited-data-part-2/](https://nanonets.com/blog/data-augmentation-how-to-use-deep-learning-when-you-have-limited-data-part-2/)
* [https://github.com/Lexie88rus/augmentation-packages-overview/blob/master/data-augmentation-packages-overview.ipynb](https://github.com/Lexie88rus/augmentation-packages-overview/blob/master/data-augmentation-packages-overview.ipynb)
* [A survey on Image Data Augmentation for deep learning.pdf](https://link.springer.com/content/pdf/10.1186%2Fs40537-019-0197-0.pdf)

