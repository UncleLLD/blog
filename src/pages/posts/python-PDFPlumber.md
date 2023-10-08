---
layout: '../../layouts/MarkdownPost.astro'
title: '当涉及到PDF中的数据挖掘，PDFPlumber是您的得力助手'
pubDate: 2023-10-08
description: '本文介绍如何通过pdfplumber从pdf中提取文本'
author: 'UncleLLD'
cover:
    url: 'https://i.imgs.ovh/2023/10/08/L4jUI.png'
    alt: 'cover'
tags: ["工具"]
theme: 'dark'
featured: true

---



![|inline](https://i.imgs.ovh/2023/10/08/L4jUI.png)

当涉及到处理PDF中的信息时，数据科学家们常常需要面临一项挑战。有些人可能会采用一种可怕的方法，即手动复制和粘贴所需的数据。这种方法不仅效率低下，而且对于长期工作来说是最慢和最低效的方式之一。此外，有些PDF文件可能不容易进行这种手动操作。

然而，有幸的是，有一种强大而简单的工具可以改善这个问题，那就是[PDFPlumber](https://github.com/jsvine/pdfplumber)，这是一个开源的Python包。它的出现使得从PDF中提取信息变得更加容易和高效。在下文中，我们将详细介绍如何使用PDFPlumber来处理PDF文件，以及示例PDF文件的用法。

通过使用PDFPlumber，数据科学家可以更轻松地解析PDF文件，提取所需的信息，从而提高工作效率并避免繁琐的手动操作。接下来将深入研究如何使用这个强大的工具。

在本教程中使用的工具是[PDFPlumber](https://github.com/jsvine/pdfplumber)，一个开源的python包，它很棒，简单而强大。使用的样例pdf如下：

![|inline](https://i.imgs.ovh/2023/10/08/L45PV.png)

### 安装并导入模块

```shell
pip install pdfplumber -q
```

```python
import pdfplumber
```

现在来看看PDF Plumber的主要功能：

### open函数

此函数将打开作为参数传递目录的文件，假设上述样例的文件名为`file.pdf`的变量，调用该函数后得到的返回结果命名为`pdf`， 包含文件目录

```python
pdf = pdfplumber.open('/content/file.pdf')
```

### pages[]

打开文件后，想选择要提取要查找的信息的页面，假设想要的信息在第一页，索引将为0:

```python
page = pdf.pages[0]
```

想象正在阅读一本书，第一步是打开这本书，然后寻找想阅读的页面，然后阅读它（即从中提取信息），Python的工作方式相同。

### extract_text()

现在已经打开了一个页面，需要从中提取文本：

```python
text = page.extract_text()
```

用`print`打印出变量`text`，将得到如下输出：

```python
SIGMOIDAL 
   
Relatório Diário 
 
Data: 10/08/2020 
 
RECEITA: R$ 1.397,00 
DADOS ATUALIZADOS POR CARLOS MELO
 
 
Visitantes: 1367 
A quantidade de visitantes diz respeito a visitantes únicos visitando qualquer 
página do domínio ou subdomínio sigmoidal.ai. Compreende, então, cursos, 
blogs e landing pages. 
 Inscritos: 33 
É considerado aqui o número de leads gerados por meio de cadastro 
voluntário nos formulários do cabeçalho, rodapé ou materiais ricos (como 
eBook, infográficos, entre outros). 
 Assinantes: 6 
Clientes assinantes da Escola de Data Science, considerando-se o plano 
renovável de assinatura mensal. 
```

print函数将'\n'识别为换行符，'\t'识别为制表符，因此文本已经被格式化过的。顺便说一句，这是用来写这篇文章的提取文本，直接调用`text`的话，输出是不一样的：

```python
SIGMOIDAL \n \nRelatório Diário \n \nData: 10/08/2020 \n \nRECEITA: R$ 1.397,00 \nDADOS ATUALIZADOS POR CARLOS MELO\n \n \n Visitantes: 1367 \nA quantidade de visitantes diz respeito a visitantes únicos visitando qualquer \npágina do domínio ou subdomínio sigmoidal.ai. Compreende, então, cursos, \nblogs e landing pages. \n Inscritos: 33 \nÉ considerado aqui o número de leads gerados por meio de cadastro \nvoluntário nos formulários do cabeçalho, rodapé ou materiais ricos (como \neBook, infográficos, entre outros). \n Assinantes: 6 \nClientes assinantes da Escola de Data Science, considerando-se o plano \nrenovável de assinatura mensal. \n \n \n
```

上述内容就是想要开始处理文本的最初的方式。现在假设想要提取这个文件中包含的利润值，即`1397,00`，必须进一步的对这个输出进行处理，直到得到“1397.00”作为字符串，然后将其转换为浮点数。提取代码如下：

```python
float(text.split("\n")[5].replace("\t", "").split("R$")[1])

#1397.00
```



如果有很多遵循相同文本模式的文件，就可以写一个“for循环”，然后代码会遍历所有这些文件，并返回每个文件的利润值。

```python
sum = 0

# making the function
for reports in week_files:
     report = pdfplumber.open(reports)
     page = report.pages[0]
     text = page.extract_text()  # extracting the text
     value = text.split("\n")[6].replace("\t", "").split("R$")[1]
     value = float(value)
     sum += value
print("{} ----> {}".format(reports, value))
```

### 参考

* [https://github.com/jsvine/pdfplumber](https://github.com/jsvine/pdfplumber)

