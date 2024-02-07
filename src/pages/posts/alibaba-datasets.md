---
layout: '../../layouts/MarkdownPost.astro'
title: '阿里计算巢：开启数据集市场的宝库，助力AI研究和应用'
pubDate: 2024-02-01
description: '阿里计算巢：开启数据集市场的宝库，助力AI研究和应用'
author: 'UncleLLD'
cover:
    url: 'https://i0.imgs.ovh/2024/01/29/KpYJt.th.png'
    alt: 'cover'
tags: ["AIGC"]
theme: 'dark'
featured: true

---


阿里数据巢提供了一个丰富的数据集市场，官方地址：

* [https://computenest.console.aliyun.com/dataset/service/cn-hangzhou](https://computenest.console.aliyun.com/dataset/service/cn-hangzhou)

![ |inline](https://i0.imgs.ovh/2024/01/29/Kpi5U.png)

可以看到数据集内容涵盖了多个领域，且还在不断增加中。关键是免费！且支持下载到本地。

![ |inline](https://i0.imgs.ovh/2024/01/29/KpYJt.png]

以下是一些数据集介绍：

### 中文诗歌古典文学数据服务

**简介：**

最全的中华古典文集数据库，包含 5.5 万首唐诗、26 万首宋诗、2.1 万首宋词和其他古典文集。诗人包括唐宋两朝近 1.4 万古诗人，和两宋时期 1.5 千古词人。数据来源于[互联网](https://github.com/chinese-poetry/chinese-poetry)。

含如下诗词： 1、《唐诗宋诗》 2、《全宋词》 3、《五代·花间集》 4、《五代·南唐二主词》 5、《论语》 6、《诗经》 7、《幽梦影》 8、《四书五经》 9、《蒙學》 10、《纳兰性德诗集》 ......以及等其他诗词

全唐诗 & 全宋诗：

* 《全唐诗》是清康熙四十四年（1705年），彭定求、沈三曾、杨中讷、汪士鋐、汪绎、俞梅、徐树本、车鼎晋、潘从律、查嗣瑮10人奉敕编校，“得诗四万八千九百余首，凡二千二百余人”， 共计900卷，目录12卷。

* 《全宋诗》继唐诗的高度繁荣之后，宋诗在思想内容和艺术表现上有新的开拓和创造，出现了许多优秀作家作品，形成了许多流派，对元、明、清的诗歌发展产生了深远影响。

**订阅地址：**

* [https://computenest.console.aliyun.com/dataset/service/service-4190105361e44b2daa43/detail/cn-hangzhou](https://computenest.console.aliyun.com/dataset/service/service-4190105361e44b2daa43/detail/cn-hangzhou)

**示例数据：**

> [
>   {
>     "author": "太宗皇帝",
>     "paragraphs": [
>       "秦川雄帝宅，函谷壯皇居。",
>       "綺殿千尋起，離宮百雉餘。",
>       "連甍遙接漢，飛觀迥凌虛。",
>       "雲日隱層闕，風煙出綺疎。"
>     ],
>     "note": [],
>     "title": "帝京篇十首 一"
>   }
> ]
> // 作者格式
> [
>   {
>     "name": "太宗皇帝",
>     "desc": "帝姓李氏，諱世民，神堯次子，聰明英武。貞觀之治，庶幾成康，功德兼隆。由漢以來，未之有也。而銳情經術，初建秦邸，即開文學館，召名儒十八人爲學士。既即位，殿左置弘文館，悉引內學士，番宿更休。聽朝之間，則與討論典籍，雜以文詠。或日昃夜艾，未嘗少怠。詩筆草隸，卓越前古。至於天文秀發，沈麗高朗，有唐三百年風雅之盛，帝實有以啓之焉。在位二十四年，諡曰文。集四十卷。館閣書目，詩一卷，六十九首。今編詩一卷。"
>   }
> ]

### 新闻语料 - 中文

**简介：**

新闻内容跨度：2014-2016年，包含了250万篇新闻。新闻来源涵盖了6.3万个媒体，含标题、关键词、描述、正文。可以做为【通用中文语料】，训练【词向量】或做为【预训练】的语料；也可以用于训练【标题生成】模型，或训练【关键词生成】模型（选关键词内容不同于标题的数据）; 亦可以通过新闻渠道区分出新闻的类型。数据来源[互联网](https://github.com/brightmart/nlp_chinese_corpus)。

数据结构如下： title: 英文句子 content: 中文句子 keywords: 关键词 desc: 描述 source: 新闻来源 time: 发布时间

```json
{'news_id': <news_id>,'title':<title>,'content':<content>,'source': <source>,'time':<time>,'keywords': <keywords>,'desc': <desc>, 'desc': <desc>}
```

 **订阅地址：**

* [https://computenest.console.aliyun.com/dataset/service/service-7f394b9c9b874dd4b3fc/detail/cn-hangzhou](https://computenest.console.aliyun.com/dataset/service/service-7f394b9c9b874dd4b3fc/detail/cn-hangzhou)

**示例数据：**

 ```json

{"news_id": "610130831", "keywords": "导游，门票","title": "故宫淡季门票40元 “黑导游”卖外地客140元", "desc": "近日有网友微博爆料称，故宫午门广场售票处出现“黑导游”，专门向外地游客出售高价门票。昨日，记者实地探访故宫，发现“黑导游”确实存在。窗口出售", "source": "新华网", "time": "03-22 12:00", "content": "近日有网友微博爆料称，故宫午门广场售票处出现“黑导游”，专门向外地游客出售高价门票。昨日，记者实地探访故宫，发现“黑导游”确实存在。窗口出售40元的门票，被“黑导游”加价出售，最高加到140元。故宫方面表示，请游客务必通过正规渠道购买门票，避免上当受骗遭受损失。目前单笔门票购买流程不过几秒钟，耐心排队购票也不会等待太长时间。....再反弹”的态势，打击黑导游需要游客配合，通过正规渠道购买门票。"}
 ```

### 中英文翻译数据集

**简介：**

中英文平行语料520万对。每一个对，包含一个英文和对应的中文。中文或英文，多数情况是一句带标点符号的完整的话。 对于一个平行的中英文对，中文平均有36个字，英文平均有19个单词(单词如“she”)。数据集划分：数据去重并分成三个部分。训练集：516万；验证集：3.9万；测试集，数万，不提供下载。数据来源于[互联网](https://github.com/brightmart/nlp_chinese_corpus)。

用途可以用于训练中英文翻译系统，从中文翻译到英文，或从英文翻译到中文；

由于有上百万的中文句子，可以只抽取中文的句子，做为通用中文语料，训练词向量或做为预训练的语料。英文任务也可以类似操作；

数据结构如下： english: 英文句子 chinese: 中文句子

```json
{"english": <english>, "chinese": <chinese>}
```

**订阅地址：**

* [https://computenest.console.aliyun.com/dataset/service/service-d3ed7dc9e15e4e3f9d72/detail/cn-hangzhou ](https://computenest.console.aliyun.com/dataset/service/service-d3ed7dc9e15e4e3f9d72/detail/cn-hangzhou )

**示例数据：**

```json
{"english": "In Italy, there is no real public pressure for a new, fairer tax system.", "chinese": "在意大利，公众不会真的向政府施压，要求实行新的、更公平的税收制度。"}
```

### 社区问答类 - 中文

**简介：**

含有410万个预先过滤过的、高质量问题和回复。每个问题属于一个【话题】，总共有2.8万个各式话题，话题包罗万象。从1400万个原始问答中，筛选出至少获得3个点赞以上的的答案，代表了回复的内容比较不错或有趣，从而获得高质量的数据集。除了对每个问题对应一个话题、问题的描述、一个或多个回复外，每个回复还带有点赞数、回复ID、回复者的标签，[数据来源于互联网](https://github.com/brightmart/nlp_chinese_corpus)。用途：

* 1）构建百科类问答：输入一个问题，构建检索系统得到一个回复或生产一个回复；或根据相关关键词从，社区问答库中筛选出你相关的领域数据

* 2）训练话题预测模型：输入一个问题(和或描述)，预测属于话题。

* 3）训练社区问答(cQA)系统：针对一问多答的场景，输入一个问题，找到最相关的问题，在这个基础上基于不同答案回复的质量、问题与答案的相关性，找到最好的答案。

* 4）做为通用中文语料，做大模型预训练的语料或训练词向量。其中类别信息也比较有用，可以用于做监督训练，从而构建更好句子表示的模型、句子相似性任务等。

* 5）结合点赞数量这一额外信息，预测回复的受欢迎程度或训练答案评分系统。

数据结构如下： qid: 问题类型， title: 问题标题， desc: 问题描述，可以为空或与标题内容一致 topic: 问题所属的话题 star: 回复的点赞个数 content: 回复的内容 answer_id: 回复的ID answerer_tags: 回复者所携带的标签

```json
{"qid":<qid>,"title":<title>,"desc":<desc>,"topic":<topic>,"star":<star>,"content":<content>,"answer_id":<answer_id>,"answerer_tags":<answerer_tags>}
```

**订阅地址：**

* [https://computenest.console.aliyun.com/dataset/service/service-da8a5b4ed4994ddc8890/detail/cn-hangzhou ](https://computenest.console.aliyun.com/dataset/service/service-da8a5b4ed4994ddc8890/detail/cn-hangzhou)

**示例数据：**

```json

{"qid": 65618973, "title": "AlphaGo只会下围棋吗？阿法狗能写小说吗？", "desc": "那么现在会不会有智能机器人能从事文学创作？<br>如果有，能写出什么水平的作品？", "topic": "机器人", "star": 3, "content": "AlphaGo只会下围棋，因为它的设计目的，架构，技术方案以及训练数据，都是围绕下围棋这个核心进行的。它在围棋领域的突破，证明了深度学习深度强化学习MCTS技术在围棋领域的有效性，并且取得了重大的PR效果。AlphaGo不会写小说，它是专用的，不会做跨出它领域的其它事情，比如语音识别，人脸识别，自动驾驶，写小说或者理解小说。如果要写小说，需要用到自然语言处理（NLP））中的自然语言生成技术，那是人工智能领域一个", "answer_id": 545576062, "answerer_tags": "人工智能@游戏业"
```

### 百科类问答 - 中文

**简介：**

含有150万个预先过滤过的、高质量问题和答案，每个问题属于一个类别。总共有492个类别，其中频率达到或超过10次的类别有434个。 可以做为通用中文语料，训练词向量或做为预训练的语料；也可以用于构建百科类问答；其中类别信息比较有用，可以用于做监督训练，从而构建更好句子表示的模型、句子相似性任务等。[数据来源于互联网](https://github.com/brightmart/nlp_chinese_corpus)。

数据结构如下： **category: 问题类型， title: 问题标题， desc: 问题描述，可以为空或与标题内容一致**

```json
{"qid":<qid>, "category":<category>, "title":<title>, "desc":<desc>, "answer":<answer>}
```

**订阅地址：**

* [https://computenest.console.aliyun.com/dataset/service/service-c0d46ce948b44f0f99d2/detail/cn-hangzhou ](https://computenest.console.aliyun.com/dataset/service/service-c0d46ce948b44f0f99d2/detail/cn-hangzhou )

**示例数据：**

```json

{"qid": "qid_2540946131115409959", "category": "生活知识", "title": "冬天进补好一些呢，还是夏天进步好啊？ ", "desc": "", "answer": "你好！\r\r当然是冬天进补好的了，夏天人体的胃处于收缩状态，不适宜大量的进补，所以我们有时候说：“夏天就要吃些清淡的，就是这个道理的。”\r\r不过，秋季进补要注意“四忌” 一忌多多益善。任何补药服用过量都有害。认为“多吃补药，有病治病，无病强身”是不的。过量进补会加重脾胃、肝脏负担。在夏季里，人们由于喝冷饮，常食冻品，多有脾胃功能减弱的现象，这时候如果突然大量进补，会骤然加重脾胃及肝脏的负担，使长期处于疲弱的消化器官难于承受，导致消化器官功能紊乱。 \r\r二忌以药代食。重药物轻食物的做法是不科学的，许多食物也是好的滋补品。如多吃荠菜可治疗高血压；多吃萝卜可健胃消食，顺气宽胸；多吃山药能补脾胃。日常食用的胡桃、芝麻、花生、红枣、扁豆等也是进补的佳品。\r\r三忌越贵越好。每个人的身体状况不同，因此与之相适应的补品也是不同的。价格昂贵的补品如燕窝、人参之类并非对每个人都适合。每种进补品都有一定的对象和适应症，应以实用有效为滋补原则，缺啥补啥。 \r\r四忌只补肉类。秋季适当食用牛羊肉进补效果好。但经过夏季后，由于脾胃尚未完全恢复到正常功能，因此过于油腻的食品不易消化吸收。另外，体内过多的脂类、糖类等物质堆积可能诱发心脑血管病。"}
```

### 维基百科 - 中文

**简介：**

104万个词条(1,043,224条; 原始文件大小1.6G，压缩文件519M；数据更新时间：2019.2.7) ，可以做为通用中文语料，做预训练的语料或构建词向量，也可以用于构建知识问答。 [数据来源于互联网](https://github.com/brightmart/nlp_chinese_corpus)。

数据格式如下：

```json
{"id":<id>,"url":<url>,"title":<title>,"text":<text>}
```

其中，title是词条的标题，text是正文；通过"\n\n"换行。



**订阅地址：**

* [https://computenest.console.aliyun.com/dataset/service/service-90c10fd0845e464daf17/detail/cn-hangzhou ](https://computenest.console.aliyun.com/dataset/service/service-90c10fd0845e464daf17/detail/cn-hangzhou)

**示例数据：**

```json
{"id": "53", "url": "https://zh.wikipedia.org/wiki?curid=53", "title": "经济学", "text": "经济学\n\n经济学是一门对产品和服务的生产、分配以及消费进行研究的社会科学。西方语言中的“经济学”一词源于古希腊的。\n\n经济学注重的是研究经济行为者在一个经济体系下的行为，以及他们彼此之间的互动。在现代，经济学的教材通常将这门领域的研究分为总体经济学和个体经济学。微观经济学检视一个社会里基本层次的行为，包括个体的行为者（例如个人、公司、买家或卖家）以及与市场的互动。而宏观经济学则分析整个经济体和其议题，包括失业、通货膨胀、经济成长、财政和货币政策等。..."}
```

### 流萤Firefly微调firefly-train-1.1M

**简介：**

收集了23个常见的中文数据集，对于每个任务，由人工书写若干种指令模板，保证数据的高质量与丰富度，数据量为115万本，分布如下：

![https://computenest-service-deploy-document-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/dataset-firefly/1.png|inline](https://computenest-service-deploy-document-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/dataset-firefly/1.png#id=qCeF3&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

数据应用于项目：Firefly（流萤）: 中文对话式大语言模型 ，训练后得到的模型firefly-1b4，[数据来源于互联网](YeungNLP/firefly-train-1.1M)。

**订阅地址：**

* [https://computenest.console.aliyun.com/dataset/service/service-b16c8bb46bb44db9b49b/detail/cn-hangzhou](https://computenest.console.aliyun.com/dataset/service/service-b16c8bb46bb44db9b49b/detail/cn-hangzhou)

**示例数据：**

每条数据的格式如下，包含任务类型、输入、目标输出：

```json
{
  "kind": "ClassicalChinese", 
  "input": "将下面句子翻译成现代文：\n石中央又生一树，高百余尺，条干偃阴为五色，翠叶如盘，花径尺余，色深碧，蕊深红，异香成烟，著物霏霏。",
  "target": "大石的中央长着一棵树，一百多尺高，枝干是彩色的，树叶有盘子那样大，花的直径有一尺宽，花瓣深蓝色，花中飘出奇异的香气笼罩着周围，如烟似雾。"
}
```

### 中国科技文献数据集CSL 

**简介：**

提供首个中文科学文献数据集（CSL），包含396,209篇中文核心期刊论文元信息（标题、摘要、关键词、学科、门类）。CSL数据集可以作为训练预语料，也可以构建许多NLP任务，例如文本摘要（标题预测）、关键词生成和文本分类等。

![https://computenest-service-deploy-document-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/dataset-csl/1.jpeg#id=qCeF3&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=](https://computenest-service-deploy-document-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/dataset-csl/1.jpeg#id=qCeF3&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

CSL 数据获取自 [国家科技资源共享服务工程技术研究中心](https://nstr.escience.net.cn/)， 包含 2010-2020 年发表的期刊论文元信息（标题、摘要和关键词）。根据中文核心期刊目录进行筛选， 并标注学科和门类标签，分为 13 个门类（一级标签）和 67 个学科（二级标签）。 数据总量为 396,209 条，分布如下表所示：

![ inline](https://i0.imgs.ovh/2024/01/29/KW0Be.png)

**订阅地址：**

* [https://computenest.console.aliyun.com/dataset/service/service-3db968e842274600b0bd/detail/cn-hangzhou](https://computenest.console.aliyun.com/dataset/service/service-3db968e842274600b0bd/detail/cn-hangzhou)

**示例数据：**

```json
{ 
  "prompt": "to title",
  "text_a": "多个相邻场景同时进行干涉参数外定标的过程称为联合定标,联合定标能够 \
            保证相邻场景的高程衔接性,能够实现无控制点场景的干涉定标.该文提出了 \
            一种适用于机载InSAR系统的联合定标算法...",
  "text_b": "基于加权最优化模型的机载InSAR联合定标算法"
}
```

### CMMLU - 中文多任务语言理解评估

**简介：**

CMMLU是针对中国的语言和文化背景设计的评测集，用来评估LLM的知识蕴含和推理能力。该评测集跨多个学科，由67个主题组成。其中大多数任务的答案都是专门针对中国的文化背景设计，不适用于其它国家的语言。如下图所示，除了涵盖人文科学、社会科学、STEM(科学、技术、工程和数学)以及其他在人类日常生活中很重要的四个通用领域的知识外，还涵盖一些特定领域的知识，用于验证模型的中国知识的蕴含能力以及对中文的理解和适应能力。

![https://computenest-service-deploy-document-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/dataset-cmmlu/1.jpeg#id=qCeF3&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=](https://computenest-service-deploy-document-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/dataset-cmmlu/1.jpeg#id=qCeF3&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

数据集中的每个问题都是一个多项选择题，有4个选项，只有一个选项是正确答案。数据以逗号分隔的.csv文件形式存在。[数据来源于互联网](https://github.com/haonan-li/CMMLU)。

**订阅地址：**

https://computenest.console.aliyun.com/dataset/service/service-007b4d87338548279dba/detail/cn-hangzhou 

**示例数据：**

数据集中的每个问题都是一个多项选择题，有4个选项，只有一个选项是正确答案。数据以逗号分隔的.csv文件形式存在

```csv
Question	A	B	C	D	Answer
0	商业伦理学最早出现在	英国	法国	美国	意大利	C
1	伦理学的核心是	宗教伦理学	描述伦理学	元伦理学	规范伦理学	D
2	下列不属于人的基本权利的时	人格平等	独立	生命	自由	B
3	关税的课税客体是	进出境的货物	消费者	海关	进出口商	A
4	区域经济从本质上讲是	商品经济	一体化经济	信用经济	市场经济	D
```

### MNIST手写数字体数据集

**简介：**

MNIST 的手写数字数据库有60,000个训练例子和10,000个测试例子。MNIST 数据库中包含的每个例子都是一个28x28的手写数字的灰度图像及其相应的标签(0-9)。

![https://computenest-service-deploy-document-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/dataset-mnist/0.png|inline](https://computenest-service-deploy-document-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/dataset-mnist/0.png)

**订阅地址：**

* [https://computenest.console.aliyun.com/dataset/service/service-9fe302a11b5c4b588c8b/detail/cn-hangzhou](https://computenest.console.aliyun.com/dataset/service/service-9fe302a11b5c4b588c8b/detail/cn-hangzhou)

**示例数据：**

手写体图片的形式存在，样式如下：

```
数据集mnist.pkl共包含4个numpy数组：

x_train : 60,000x784 numpy array that each row contains flattened version of training images.
t_train : 1x60,000 numpy array that each component is true label of the corresponding training images.
x_test : 10,000x784 numpy array that each row contains flattened version of test images.
t_test : 1x10,000 numpy array that each component is true label of the corresponding test images.
```

### OpenAI HumanEval数据集

**简介：**

OpenAI 发布的 HumanEval 数据集包括164个编程问题，包括函数 sig-nature、 docstring、 body 和几个单元测试。数据字段:

- name: task_id dtype: string
- name: prompt dtype: string
- name: canonical_solution
- name: test
- name: entry_point

字段含义：

* task_id: identifier for the data sample
* prompt: input for the model containing function header and docstrings canonical_solution: solution for the problem in the prompt
* test: contains function to test generated code for correctness
* entry_point: entry point for test

**订阅地址：**

* [https://computenest.console.aliyun.com/dataset/service/service-81fb2d1366874b428177/detail/cn-hangzhou](https://computenest.console.aliyun.com/dataset/service/service-81fb2d1366874b428177/detail/cn-hangzhou)

**示例数据：**

| task_id     | prompt                                                       | **canonical_solution**                                       | **test**                                                     | **entry_point**       |
| ----------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------------- |
| HumanEval/0 | from typing import List def has_close_elements(numbers: List[float], threshold: float) -> bool: """ Check if in given list of numbers, are any two numbers closer to each other than given threshold. >>> has_close_elements([1.0, 2.0, 3.0], 0.5) False >>> has_close_elements([1.0, 2.8, 3.0, 4.0, 5.0, 2.0], 0.3) True """ | for idx, elem in enumerate(numbers): for idx2, elem2 in enumerate(numbers): if idx != idx2: distance = abs(elem - elem2) if distance < threshold: return True return False | METADATA = { 'author': 'jt', 'dataset': 'test' } def check(candidate): assert candidate([1.0, 2.0, 3.9, 4.0, 5.0, 2.2], 0.3) == True assert candidate([1.0, 2.0, 3.9, 4.0, 5.0, 2.2], 0.05) == False assert candidate([1.0, 2.0, 5.9, 4.0, 5.0], 0.95) == True assert candidate([1.0, 2.0, 5.9, 4.0, 5.0], 0.8) == False assert candidate([1.0, 2.0, 3.0, 4.0, 5.0, 2.0], 0.1) == True assert candidate([1.1, 2.2, 3.1, 4.1, 5.1], 1.0) == True assert candidate([1.1, 2.2, 3.1, 4.1, 5.1], 0.5) == False | has_close_elements    |
| HumanEval/1 | from typing import List def separate_paren_groups(paren_string: str) -> List[str]: """ Input to this function is a string containing multiple groups of nested parentheses. Your goal is to separate those group into separate strings and return the list of those. Separate groups are balanced (each open brace is properly closed) and not nested within each other Ignore any spaces in the input string. >>> separate_paren_groups('( ) (( )) (( )( ))') ['()', '(())', '(()())'] """ | result = [] current_string = [] current_depth = 0 for c in paren_string: if c == '(': current_depth += 1 current_string.append(c) elif c == ')': current_depth -= 1 current_string.append(c) if current_depth == 0: result.append(''.join(current_string)) current_string.clear() return result | METADATA = { 'author': 'jt', 'dataset': 'test' } def check(candidate): assert candidate('(()()) ((())) () ((())()())') == [ '(()())', '((()))', '()', '((())()())' ] assert candidate('() (()) ((())) (((())))') == [ '()', '(())', '((()))', '(((())))' ] assert candidate('(()(())((())))') == [ '(()(())((())))' ] assert candidate('( ) (( )) (( )( ))') == ['()', '(())', '(()())'] | separate_paren_groups |

### GSM8K数据服务

**简介：**

GSM8K (Grade School Math 8K)是一个8.5 K 的高质量语言多样化的小学数学词汇问题数据集。该数据集用于支持需要多步推理的基本数学问题的问题回答任务。

数据字段:

- name: question dtype: string
- name: answer dtype: string

**订阅地址：**

* [https://computenest.console.aliyun.com/dataset/service/service-7ba26107f69b4ed3914d/detail/cn-hangzhou](https://computenest.console.aliyun.com/dataset/service/service-7ba26107f69b4ed3914d/detail/cn-hangzhou)

**示例数据：**

| **question**                                                 | **answer**                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Natalia sold clips to 48 of her friends in April, and then she sold half as many clips in May. How many clips did Natalia sell altogether in April and May? | Natalia sold 48/2 = <<48/2=24>>24 clips in May. Natalia sold 48+24 = <<48+24=72>>72 clips altogether in April and May. #### 72 |
| Weng earns $12 an hour for babysitting. Yesterday, she just did 50 minutes of babysitting. How much did she earn? | Weng earns 12/60 = <<12/60=0.2>>0.2 per minute. Working 50 minutes, she earned 0.2 x 50 = ​<<0.2*50=10>>10.  #### 10 |

### 知乎问题答案数据集

**简介：**

知乎问题答案，一个问题，多个答案，根据赞同数量排序

数据字段:

- name: INSTRUCTION dtype: string
- name: RESPONSE dtype: string
- name: SOURCE dtype: string
- name: METADATA dtype: string

**订阅地址：**

* [https://computenest.console.aliyun.com/dataset/service/service-63a78222b0d1499ca75c/detail/cn-hangzhou](https://computenest.console.aliyun.com/dataset/service/service-63a78222b0d1499ca75c/detail/cn-hangzhou)

**示例数据：**

| **INSTRUCTION**                                  | **RESPONSE**                                                 | **SOURCE** | **METADATA**                                                 |
| ------------------------------------------------ | ------------------------------------------------------------ | ---------- | ------------------------------------------------------------ |
| 怎么说服男朋友买烤箱？                           | emmmmm，首先想说的是，我买厨房用品一般是不用「说服」的，只是在厨房堆的满满当当的情况下会象征性的问一下我老公，他就会回答我说：你看看你还有地方放吗。然后我会思考一下，如果是特别想买的，就不会问他了。自己决定就好。 比如，前几天我又买了两个盘子~~~~他还不知道。 可以给题主看看我有多少的锅具：自家炒菜用什么锅好？各有什么优缺点？ 说回烤箱的问题，买的时候处于热恋期，我告诉他我有一个买烤箱的计划。虽然他基本不吃点心，也不喜欢烘焙，但那个时期的他欣然同意并热情洋溢的给我选烤箱。可能是他有憧憬我会给他做什么好吃的吧。又因为我是一个不怎么吃甜食的湖南人，烤箱在我家烘焙的使用率很低。 但是！！你还是可以告诉他烤箱的作用是可以烤制各种肉类！！！我不相信有不喜欢吃肉的男生！！烤箱真的是可以烤一切的肉类，熟悉之后会觉得非常简单。 我很久以前用烤箱做的最多的就是烤羊排和烤鸡翅，我老公不怎么吃羊肉和鸡翅。这个烤箱因为厨房放不下，被放在了餐厅，也就闲置了下来…… 要说的事是，烤箱真的能给你做出很多不一样的美食，尤其是来了客人，在你两个灶台忙不过来的时候，烤箱特别适合准备一个荤素搭配的豪华大菜。在烹饪其他需要爆炒的菜肴的空档去处理一下就可以了。 总结来说理由如下： 1、如果你家是你做饭多，那么为什么有这么多话说， 也不是他用，等着吃就好了。 2、工欲善其事，必先利其器。没有好的工具怎么能吃到更好的美食。 3、我要我喜欢，不要你喜欢。我还不能有个爱好吗？ | Zhihu      | {"question_id": 357137111.0, "answer_id": 914332816.0, "url": "https://www.zhihu.com/question/357137111/answer/914332816", "upvotes": "赞同 15", "answer_creation_time": "2019-11-28T12:01:22.000Z"} |
| 国内企业去Oracle的话，首选MySQL 还是PostgreSQL？ | 最新稳定版本下载链接：https://github.com/IvorySQL/IvorySQL/releases/tag/Ivory_REL_1_4 发行日期：2022年6月28日 IvorySQL 1.4基于PostgreSQL 14.4并包含IvorySQL 1.3的问题修复。此版本与IvorySQL 1.3兼容，并且不需要为运行 IvorySQL 1.X 的用户进行数据备份/恢复。 它包含从14.3开始的各种修复，特别是修复了CREATE INDEX CONCURRENT 和REINDEX CONCURRENT，这可能会导致索引的无提示数据损坏。在修复之前，CREATE INDEX CONCURRENT和REINDEX CONCURRENT可以构建缺少条目的索引，导致使用该索引的 SELECT 查询找不到某些行。更多细节可以在14.4版本发行中找到。 如果您有任何使用 14.X 下的 CONCURRENTLY 选项创建的索引，您应该在更新后重新索引它们。请参阅 PostgreSQL 14.4 发行说明的第一个变更条目。 有关PostgreSQL 14.4的更新和错误修复，请参阅官方 PostgreSQL14.4 发行说明。 https://www.postgresql.org/docs/release/14.4/ 问题详细说明： https://github.com/IvorySQL/IvorySQL/pull/139 以下人员作为补丁的作者、提交者、审阅者、测试人员或问题报告者对这个版本做出了贡献。 https://github.com/IvorySQL/IvorySQL/commits/Ivory_REL_1_4 | Zhihu      | {"question_id": 533346959.0, "answer_id": 2557549069.0, "url": "https://www.zhihu.com/question/533346959/answer/2557549069", "upvotes": "赞同 1", "answer_creation_time": "2022-07-04T01:50:28.000Z"} |

### Math-QA数据集

**简介：**

数学应用题数据集是通过使用新的表示语言通过完全指定的操作程序对 AQuA-RAT 数据集进行注释来收集的。 AQuA-RAT 提供了问题、选项、理由和正确选项，如

- 问题：一列火车以48 公里/小时的速度运行，在9 秒内穿过一根电线杆。火车的长度是多少？
- 原理：速度= (48 x 5 / 18) 米/秒= (40 / 3) 米/秒。火车的长度=（速度x时间）。火车长度 = ( 40 / 3 x 9 ) m = 120 m 。答案是c。
- 选项：a) 140, b) 130, c) 120, d) 170, e) 160
- 正确选项是：C

数据字段:

- Problem String
- Rationale String
- options String
- correct String
- annotated_formula String
- linear_formula String
- category String

**订阅地址：**

* [https://computenest.console.aliyun.com/dataset/service/service-de582de378db4b84ab68/detail/cn-hangzhou](https://computenest.console.aliyun.com/dataset/service/service-de582de378db4b84ab68/detail/cn-hangzhou)

**示例数据：**

```json
{
    "Problem": "a multiple choice test consists of 4 questions , and each question has 5 answer choices . in how many r ways can the test be completed if every question is unanswered ?",
    "Rationale": "\"5 choices for each of the 4 questions , thus total r of 5 * 5 * 5 * 5 = 5 ^ 4 = 625 ways to answer all of them . answer : c .\"",
    "annotated_formula": "power(5, 4)",
    "category": "general",
    "correct": "c",
    "linear_formula": "power(n1,n0)|",
    "options": "a ) 24 , b ) 120 , c ) 625 , d ) 720 , e ) 1024"
}
```



### 中文医学数据集

**简介：**

该数据集医学知识库围绕疾病、药物、检查指标等构建，字段包括并发症，高危因素，组织学检查，临床症状，药物治疗，辅助治疗等，可以利用该数据集对ChatGLM或者LLaMA模型进行训练，提高模型在医疗领域的问答效果

数据集构建采用了公开和自建的中文医学知识库，主要参考了[cMeKG](https://github.com/king-yyf/CMeKG_tools) 医学知识库围绕疾病、药物、检查指标等构建，字段包括并发症，高危因素，组织学检查，临床症状，药物治疗，辅助治疗等。

**订阅地址：**

* [https://computenest.console.aliyun.com/dataset/service/service-b23ee2aeb8fa4784bd31/detail/cn-hangzhou ](https://computenest.console.aliyun.com/dataset/service/service-b23ee2aeb8fa4784bd31/detail/cn-hangzhou)

**示例数据：**

```json
{"中心词": "偏头痛", "相关疾病": ["妊娠合并偏头痛", "恶寒发热"], "相关症状": ["皮肤变硬", "头部及眼后部疼痛并能听到连续不断的隆隆声", "晨起头痛加重"], "所属科室": ["中西医结合科", "内科"], "发病部位": ["头部"]}
```



