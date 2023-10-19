---
layout: '../../layouts/MarkdownPost.astro'
title: '面向开发者的提示工程'
pubDate: 2023-06-15
description: 'chatGPT聊天界面可能大家都会用，但基于API的功能可能就只有开发者使用，本文是面向开发者的提示工程教程，由吴恩达联合OpenAI联合出品。'
author: 'UncleLLD'
cover:
    url: 'https://i.imgloc.com/2023/06/15/Vb4vIz.png'
    alt: 'cover'
tags: ["AIGC", "Prompt", "ChatGPT"]
theme: 'dark'
featured: true
---



# 面向开发者的提示工程

## 1 简介

随着大型语言模型（LLM）的发展，LLM 大致可以分为两种类型：

* 基础LLM
* 指令微调LLM

基础LLM是基于文本训练数据，训练出预测下一个单词能力的模型，其通常是在互联网和其他来源的大量数据上训练的。

指令调整的 LLMs 已经被训练来遵循指令。使用输入是指令、输出是其应该返回的结果的数据集来对其进行微调，要求它遵循这些指令。然后通常使用一种称为 RLHF（reinforcement learning from human feedback，人类反馈强化学习）的技术进行进一步改进，使系统更能够有帮助地遵循指令。

对于今天的大多数实际应用，我们建议将注意力集中在指令调整的LLMs上，这些LLMs更容易使用，而且由于OpenAI和其他LLM公司的工作，它们变得更加安全和更加协调。

当您使用指令调整 LLM 时，请类似于考虑向另一个人提供指令，假设它是一个聪明但不知道您任务的具体细节的人。当 LLM 无法正常工作时，有时是因为指令不够清晰。

```shell
pip install openai

pip install -U python-dotenv
```



```python
import openai
import os
from dotenv import load_dotenv, find_dotenv
# 导入第三方库

_ = load_dotenv(find_dotenv())
# 读取系统中的环境变量

openai.api_key  = os.getenv('OPENAI_API_KEY')
# 设置 API_KEY

# 一个封装 OpenAI 接口的函数，参数为 Prompt，返回对应结果
def get_completion(prompt, model="gpt-3.5-turbo"):
    '''
    prompt: 对应的提示
    model: 调用的模型，默认为 gpt-3.5-turbo(ChatGPT)，有内测资格的用户可以选择 gpt-4
    '''
    messages = [{"role": "user", "content": prompt}]
    response = openai.ChatCompletion.create(
        model=model,
        messages=messages,
        temperature=0, # 模型输出的温度系数，控制输出的随机程度
    )
    # 调用 OpenAI 的 ChatCompletion 接口
    return response.choices[0].message["content"]
```





---

## 2 编写Prompt的原则

### 原则一： 编写清晰、具体的指令

通过提供尽可能清晰和具体的指令来表达您希望模型执行的操作，将引导模型给出正确的输出，并减少你得到无关或不正确响应的可能。编写清晰的指令不意味着简短的指令，因为在许多情况下，更长的提示实际上更清晰且提供了更多上下文，这实际上可能导致更详细更相关的输出。

* **策略一：使用分隔符清晰地表示输入的不同部分，分隔符可以是：```，""，<>，<tag>，<\tag>等**

可以使用任何明显的标点符号将特定的文本部分与提示的其余部分分开。这可以是任何可以使模型明确知道这是一个单独部分的标记。使用分隔符是一种可以避免提示注入的有用技术。提示注入是指如果用户将某些输入添加到提示中，则可能会向模型提供与您想要执行的操作相冲突的指令，从而使其遵循冲突的指令而不是执行您想要的操作。

```python
text = f"""
你应该提供尽可能清晰、具体的指示，以表达你希望模型执行的任务。\
这将引导模型朝向所需的输出，并降低收到无关或不正确响应的可能性。\
不要将写清晰的提示与写简短的提示混淆。\
在许多情况下，更长的提示可以为模型提供更多的清晰度和上下文信息，从而导致更详细和相关的输出。
"""
# 需要总结的文本内容
prompt = f"""
把用三个反引号括起来的文本总结成一句话。
​```{text}```
"""
# 指令内容，使用 ``` 来分隔指令和待总结的内容
response = get_completion(prompt)
print(response)
```

> ```python
> 提供清晰具体的指示，避免无关或不正确响应，不要混淆写清晰和写简短，更长的提示可以提供更多清晰度和上下文信息，导致更详细和相关的输出。
> ```

* **策略二：要求一个结构化的输出**，可以是 Json、HTML 等格式

要求生成一个结构化的输出，这可以使模型的输出更容易被解析。

```python
prompt = f"""
请生成包括书名、作者和类别的三本虚构书籍清单，\
并以 JSON 格式提供，其中包含以下键:book_id、title、author、genre。
"""
response = get_completion(prompt)
print(response)
```

> ```json
> {
> "books": [
>  {
>    "book_id": 1,
>    "title": "The Shadow of the Wind",
>    "author": "Carlos Ruiz Zafón",
>    "genre": "Mystery"
>  },
>  {
>    "book_id": 2,
>    "title": "The Name of the Wind",
>    "author": "Patrick Rothfuss",
>    "genre": "Fantasy"
>  },
>  {
>    "book_id": 3,
>    "title": "The Hitchhiker's Guide to the Galaxy",
>    "author": "Douglas Adams",
>    "genre": "Science Fiction"
>  }
> ]
> }
> ```

* **策略三：要求模型检查是否满足条件**

如果任务做出的假设不一定满足，可以告诉模型先检查这些假设，如果不满足，指示并停止执行。还可以考虑潜在的边缘情况以及模型应该如何处理它们，以避免意外的错误或结果。

将分别给模型两段文本，分别是制作茶的步骤以及一段没有明确步骤的文本。我们将要求模型判断其是否包含一系列指令，如果包含则按照给定格式重新编写指令，不包含则回答未提供步骤。

```python
# 有步骤的文本
text_1 = f"""
泡一杯茶很容易。首先，需要把水烧开。\
在等待期间，拿一个杯子并把茶包放进去。\
一旦水足够热，就把它倒在茶包上。\
等待一会儿，让茶叶浸泡。几分钟后，取出茶包。\
如果你愿意，可以加一些糖或牛奶调味。\
就这样，你可以享受一杯美味的茶了。
"""
prompt = f"""
您将获得由三个引号括起来的文本。\
如果它包含一系列的指令，则需要按照以下格式重新编写这些指令：

第一步 - ...
第二步 - …
…
第N步 - …

如果文本中不包含一系列的指令，则直接写“未提供步骤”。"
\"\"\"{text_1}\"\"\"
"""
response = get_completion(prompt)
print("Text 1 的总结:")
print(response)
```

> ```python
> Text 1 的总结:
> 第一步 - 把水烧开。
> 第二步 - 拿一个杯子并把茶包放进去。
> 第三步 - 把烧开的水倒在茶包上。
> 第四步 - 等待几分钟，让茶叶浸泡。
> 第五步 - 取出茶包。
> 第六步 - 如果你愿意，可以加一些糖或牛奶调味。
> 第七步 - 就这样，你可以享受一杯美味的茶了。
> ```

```python
# 无步骤的文本
text_2 = f"""
今天阳光明媚，鸟儿在歌唱。\
这是一个去公园散步的美好日子。\
鲜花盛开，树枝在微风中轻轻摇曳。\
人们外出享受着这美好的天气，有些人在野餐，有些人在玩游戏或者在草地上放松。\
这是一个完美的日子，可以在户外度过并欣赏大自然的美景。
"""
prompt = f"""
您将获得由三个引号括起来的文本。\
如果它包含一系列的指令，则需要按照以下格式重新编写这些指令：

第一步 - ...
第二步 - …
…
第N步 - …

如果文本中不包含一系列的指令，则直接写“未提供步骤”。"
\"\"\"{text_2}\"\"\"
"""
response = get_completion(prompt)
print("Text 2 的总结:")
print(response)
```

> ```python
> Text 2 的总结:
> 未提供步骤。
> ```

* **策略四：提供少量示例**

即在要求模型执行实际任务之前，提供给它少量成功执行任务的示例。

例如，在以下的示例中，我们告诉模型其任务是以一致的风格回答问题，并先给它一个孩子和一个祖父之间的对话的例子。孩子说，“教我耐心”，祖父用这些隐喻回答。因此，由于我们已经告诉模型要以一致的语气回答，现在我们说“教我韧性”，由于模型已经有了这个少样本示例，它将以类似的语气回答下一个任务。

```python
prompt = f"""
你的任务是以一致的风格回答问题。

<孩子>: 教我耐心。

<祖父母>: 挖出最深峡谷的河流源于一处不起眼的泉眼；最宏伟的交响乐从单一的音符开始；最复杂的挂毯以一根孤独的线开始编织。

<孩子>: 教我韧性。
"""
response = get_completion(prompt)
print(response)
```

> ```python
> <祖父母>: 韧性就像是一棵树，需要经历风雨的洗礼才能生长得更加坚强。在面对困难和挑战时，坚持不懈、努力奋斗是培养韧性的关键。记住，失败并不意味着终点，而是一个学习和成长的机会。相信自己的能力，保持积极的心态，勇敢地面对困难，你将逐渐培养出内心的韧性。不要忘记，每一次挫折都是你成为更好的自己的一部分。
> ```

### 原则二：给模型时间去思考

如果模型匆忙地得出了错误的结论，您应该尝试重新构思查询，请求模型在提供最终答案之前进行一系列相关的推理。给模型一个在短时间或用少量文字无法完成的任务，它可能会猜测错误。这种情况对人来说也是一样的。在这些情况下，您可以指示模型花更多时间思考问题，这意味着它在任务上花费了更多的计算资源。

* **策略一：指定完成任务所需的步骤**

通过给定一个复杂任务，给出完成该任务的一系列步骤，来展示这一策略的效果。

首先描述了杰克和吉尔的故事，并给出一个指令。该指令是执行以下操作。首先，用一句话概括三个反引号限定的文本。第二，将摘要翻译成法语。第三，在法语摘要中列出每个名称。第四，输出包含以下键的 JSON 对象：法语摘要和名称数。然后要用换行符分隔答案。

```python
text = f"""
在一个迷人的村庄里，兄妹杰克和吉尔出发去一个山顶井里打水。\
他们一边唱着欢乐的歌，一边往上爬，\
然而不幸降临——杰克绊了一块石头，从山上滚了下来，吉尔紧随其后。\
虽然略有些摔伤，但他们还是回到了温馨的家中。\
尽管出了这样的意外，他们的冒险精神依然没有减弱，继续充满愉悦地探索。
"""
# example 1
prompt_1 = f"""
执行以下操作：
1-用一句话概括下面用三个反引号括起来的文本。
2-将摘要翻译成法语。
3-在法语摘要中列出每个人名。
4-输出一个 JSON 对象，其中包含以下键：French_summary，num_names。

请用换行符分隔您的答案。

Text:
​```{text}```
"""
response = get_completion(prompt_1)
print("prompt 1:")
print(response)
```

> ```python
> 1- 欢乐的兄妹冒险中，杰克和吉尔在山上打水时发生意外，但他们的冒险精神没有受到影响，继续探索。
> 2- Dans un charmant village, les frère et sœur Jack et Jill partent chercher de l'eau dans un puits au sommet d'une montagne. Ils chantent joyeusement en escaladant, mais malheureusement, Jack trébuche sur une pierre et dévale la montagne, suivi de près par Jill. Bien qu'ils aient quelques blessures, ils retournent chez eux chaleureusement. Malgré cet accident, leur esprit d'aventure ne faiblit pas et ils continuent à explorer avec plaisir.
> 3- Noms des personnages dans le résumé en français : Jack et Jill.
> 4- JSON 输出：
> {
> "French_summary": "Dans un charmant village, les frère et sœur Jack et Jill partent chercher de l'eau dans un puits au sommet d'une montagne. Ils chantent joyeusement en escaladant, mais malheureusement, Jack trébuche sur une pierre et dévale la montagne, suivi de près par Jill. Bien qu'ils aient quelques blessures, ils retournent chez eux chaleureusement. Malgré cet accident, leur esprit d'aventure ne faiblit pas et ils continuent à explorer avec plaisir.",
> "num_names": 2
> }
> ```

上述输出仍然存在一定问题，例如，键“姓名”会被替换为法语，因此，我们给出一个更好的 Prompt，该 Prompt 指定了输出的格式:

```python
prompt_2 = f"""
1-用一句话概括下面用<>括起来的文本。
2-将摘要翻译成英语。
3-在英语摘要中列出每个名称。
4-输出一个 JSON 对象，其中包含以下键：English_summary，num_names。

请使用以下格式：
文本：<要总结的文本>
摘要：<摘要>
翻译：<摘要的翻译>
名称：<英语摘要中的名称列表>
输出 JSON：<带有 English_summary 和 num_names 的 JSON>

Text: <{text}>
"""
response = get_completion(prompt_2)
print("\nprompt 2:")
print(response)
```

> ```python
> 文本：<在一个迷人的村庄里，兄妹杰克和吉尔出发去一个山顶井里打水。他们一边唱着欢乐的歌，一边往上爬，然而不幸降临——杰克绊了一块石头，从山上滚了下来，吉尔紧随其后。虽然略有些摔伤，但他们还是回到了温馨的家中。尽管出了这样的意外，他们的冒险精神依然没有减弱，继续充满愉悦地探索。>
> 
> 摘要：<在迷人的村庄中，兄妹杰克和吉尔一起去山顶井打水。他们唱歌并往上爬，不幸地，杰克绊倒了，从山上滚下来，吉尔紧随其后。尽管受了些伤，他们还是平安回到了家。尽管遇到意外，但他们的冒险精神依然坚定，继续愉快地探索。>
> 
> 翻译：<In a charming village, siblings Jack and Jill set off to fetch water from a well on the mountaintop. They climb while singing joyful songs, but unfortunately, Jack stumbles on a stone and rolls down the mountain, closely followed by Jill. Despite some minor injuries, they make it back to their cozy home. Despite the mishap, their adventurous spirit remains undiminished as they continue to explore with joy.>
> 
> 名称：<Jack, Jill>
> 
> 输出 JSON：<{"English_summary": "In a charming village, siblings Jack and Jill set off to fetch water from a well on the mountaintop. They climb while singing joyful songs, but unfortunately, Jack stumbles on a stone and rolls down the mountain, closely followed by Jill. Despite some minor injuries, they make it back to their cozy home. Despite the mishap, their adventurous spirit remains undiminished as they continue to explore with joy.", "num_names": 2}>
> ```

* **策略二：指导模型在下结论之前找出一个自己的解法**

有时候，在明确指导模型在做决策之前要思考解决方案时，我们会得到更好的结果。接下来会给出一个问题和一个学生的解答，要求模型判断解答是否正确

```python
prompt = f"""
判断学生的解决方案是否正确。

问题:
我正在建造一个太阳能发电站，需要帮助计算财务。

    土地费用为 100美元/平方英尺
    我可以以 250美元/平方英尺的价格购买太阳能电池板
    我已经谈判好了维护合同，每年需要支付固定的10万美元，并额外支付每平方英尺10美元
    作为平方英尺数的函数，首年运营的总费用是多少。

学生的解决方案：
设x为发电站的大小，单位为平方英尺。
费用：

    土地费用：100x
    太阳能电池板费用：250x
    维护费用：100,000美元+100x
    总费用：100x+250x+100,000美元+100x=450x+100,000美元
"""
response = get_completion(prompt)
print(response)
```

> ```python
> 学生的解决方案是正确的。
> 
> 根据学生的解决方案，首年运营的总费用是 450x + 100,000 美元，其中 x 是发电站的大小，单位为平方英尺。
> 
> 费用的计算如下：
> - 土地费用：100x 美元
> - 太阳能电池板费用：250x 美元
> - 维护费用：100,000 美元 + 100x 美元
> - 总费用：100x 美元 + 250x 美元 + 100,000 美元 + 100x 美元 = 450x 美元 + 100,000 美元
> 
> 因此，学生的解决方案是正确的，并给出了首年运营的总费用。
> ```

其实上述方案是错误的，可以通过指导模型先自行找出一个解法来解决这个问题。

在接下来这个 Prompt 中，我们要求模型先自行解决这个问题，再根据自己的解法与学生的解法进行对比，从而判断学生的解法是否正确。同时，我们给定了输出的格式要求。通过明确步骤，让模型有更多时间思考，有时可以获得更准确的结果。

```python
请判断学生的解决方案是否正确，请通过如下步骤解决这个问题：

步骤：

    首先，自己解决问题。
    然后将你的解决方案与学生的解决方案进行比较，并评估学生的解决方案是否正确。在自己完成问题之前，请勿决定学生的解决方案是否正确。

使用以下格式：

    问题：问题文本
    学生的解决方案：学生的解决方案文本
    实际解决方案和步骤：实际解决方案和步骤文本
    学生的解决方案和实际解决方案是否相同：是或否
    学生的成绩：正确或不正确

问题：

    我正在建造一个太阳能发电站，需要帮助计算财务。 
    - 土地费用为每平方英尺100美元
    - 我可以以每平方英尺250美元的价格购买太阳能电池板
    - 我已经谈判好了维护合同，每年需要支付固定的10万美元，并额外支付每平方英尺10美元
    作为平方英尺数的函数，首年运营的总费用是多少。

学生的解决方案：

    设x为发电站的大小，单位为平方英尺。
    费用：
    1. 土地费用：100x
    2. 太阳能电池板费用：250x
    3. 维护费用：100,000+100x
    总费用：100x+250x+100,000+100x=450x+100,000

实际解决方案和步骤：
```

> ```python
> 正确的解决方案和步骤：
>     1. 计算土地费用：100美元/平方英尺 * x平方英尺 = 100x美元
>     2. 计算太阳能电池板费用：250美元/平方英尺 * x平方英尺 = 250x美元
>     3. 计算维护费用：10万美元 + 10美元/平方英尺 * x平方英尺 = 10万美元 + 10x美元
>     4. 计算总费用：100x美元 + 250x美元 + 10万美元 + 10x美元 = 360x + 10万美元
> 
> 学生的解决方案和实际解决方案是否相同：否
> 
> 学生的成绩：不正确
> ```

### 局限性

**虚假知识**：模型偶尔会生成一些看似真实实则编造的知识

**如果模型在训练过程中接触了大量的知识，它并没有完全记住所见的信息，因此它并不很清楚自己知识的边界。这意味着它可能会尝试回答有关晦涩主题的问题，并编造听起来合理但实际上并不正确的答案。称这些编造的想法为幻觉。**

例如在如下示例中，要求告诉 Boie 公司生产的 AeroGlide UltraSlim Smart Toothbrush 产品的信息，事实上，这个公司是真实存在的，但产品是编造的，模型则会一本正经地告诉我们编造的知识。

```python
prompt = f"""
告诉我 Boie 公司生产的 AeroGlide UltraSlim Smart Toothbrush 的相关信息
"""
response = get_completion(prompt)
print(response)
```

> ```python
> Boie公司生产的AeroGlide UltraSlim Smart Toothbrush是一款智能牙刷，具有以下特点：
> 
> 1. 超薄设计：刷头仅有0.8毫米的厚度，可以更容易地进入口腔深处，清洁更彻底。
> 
> 2. 智能感应：牙刷配备了智能感应技术，可以自动识别刷头的位置和方向，确保每个部位都得到充分的清洁。
> 
> 3. 高效清洁：牙刷采用了高速振动技术，每分钟可达到40000次，可以有效去除牙菌斑和污渍。
> 
> 4. 轻松携带：牙刷采用了便携式设计，可以轻松放入口袋或旅行包中，随时随地进行口腔清洁。
> 
> 5. 环保材料：牙刷采用了环保材料制造，不含有害物质，对环境友好。
> 
> 总之，Boie公司生产的AeroGlide UltraSlim Smart Toothbrush是一款高效、智能、环保的牙刷，可以帮助用户轻松保持口腔健康。
> ```

模型会输出看上去非常真实的编造知识，这有时会很危险。因此，请确保使用在本节中介绍的一些技巧，以尝试在构建自己的应用程序时避免这种情况。这是模型已知的一个弱点，也是正在积极努力解决的问题。在你希望模型根据文本生成答案的情况下，另一种减少幻觉的策略是先要求模型找到文本中的任何相关引用，然后要求它使用这些引用来回答问题，这种追溯源文档的方法通常对减少幻觉非常有帮助。

> 说明：在本教程中，我们使用 \ 来使文本适应屏幕大小以提高阅读体验，GPT 并不受 \ 的影响，但在你调用其他大模型时，需额外考虑 \ 是否会影响模型性能

---

## 3 迭代优化

当使用 LLM 构建应用程序时，从来没有在第一次尝试中就成功使用最终应用程序中所需的 Prompt。但这并不重要，只要有一个好的迭代过程来不断改进 Prompt，那么就能够得到一个适合任务的 Prompt。认为在提示方面，第一次成功的几率可能会高一些，但正如上所说，第一个提示是否有效并不重要。最重要的是为您的应用程序找到有效提示的过程。

可以尝试编写第一个 Prompt，满足上一章说过的两个原则：清晰明确，并且给系统足够的时间思考。然后您可以运行它并查看结果。如果第一次效果不好，那么迭代的过程就是找出为什么指令不够清晰或为什么没有给算法足够的时间思考，以便改进想法、改进提示等等，循环多次，直到找到适合您的应用程序的 Prompt。

### 任务-从产品说明书生成一份营销产品描述

有一个椅子的产品说明书，描述说它是一个中世纪灵感家族的一部分，讨论了构造、尺寸、椅子选项、材料等等，产地是意大利。假设想要使用这份说明书帮助营销团队为在线零售网站撰写营销式描述：

```python
# 示例：产品说明书
fact_sheet_chair = """
概述
    美丽的中世纪风格办公家具系列的一部分，包括文件柜、办公桌、书柜、会议桌等。
    多种外壳颜色和底座涂层可选。
    可选塑料前后靠背装饰（SWC-100）或10种面料和6种皮革的全面装饰（SWC-110）。
    底座涂层选项为：不锈钢、哑光黑色、光泽白色或铬。
    椅子可带或不带扶手。
    适用于家庭或商业场所。
    符合合同使用资格。
结构
    五个轮子的塑料涂层铝底座。
    气动椅子调节，方便升降。
尺寸
    宽度53厘米|20.87英寸
    深度51厘米|20.08英寸
    高度80厘米|31.50英寸
    座椅高度44厘米|17.32英寸
    座椅深度41厘米|16.14英寸
选项
    软地板或硬地板滚轮选项。
    两种座椅泡沫密度可选：中等（1.8磅/立方英尺）或高（2.8磅/立方英尺）。
    无扶手或8个位置PU扶手。
材料
外壳底座滑动件
    改性尼龙PA6/PA66涂层的铸铝。
    外壳厚度：10毫米。
    座椅
    HD36泡沫
原产国
    意大利
"""
```

```python
# 提示：基于说明书创建营销描述
prompt = f"""
你的任务是帮助营销团队基于技术说明书创建一个产品的营销描述。

根据```标记的技术说明书中提供的信息，编写一个产品描述。

技术说明: ```{fact_sheet_chair}```
"""
response = get_completion(prompt)
print(response)
```

> ```python
> 产品描述：
> 
> 引领中世纪风格的办公家具系列，我们自豪地推出美丽而功能齐全的产品，满足您对高品质办公环境的需求。我们的中世纪风格办公家具系列包括文件柜、办公桌、书柜、会议桌等，以其独特的设计和多样的选项，为您的办公室增添了一丝优雅和魅力。
> 
> 这个系列的设计灵感源自中世纪的美学，注重细节和精致的工艺。您可以选择多种外壳颜色和底座涂层，根据您的喜好和办公室装饰风格来打造独一无二的工作空间。无论您偏爱塑料前后靠背装饰还是面料和皮革的全面装饰，我们都提供了多种选择，以满足您的个性化需求。
> 
> 我们注重产品的实用性和舒适性。底座采用五个轮子的塑料涂层铝底座，带有气动椅子调节功能，使您能够轻松调整座椅的高度，确保最佳的工作姿势和舒适度。此外，我们还提供软地板或硬地板滚轮选项，让您根据办公室地面的不同选择适合的滚轮类型。
> 
> 在尺寸方面，我们的产品经过精心设计，确保合适的尺寸和舒适的使用体验。宽度、深度和高度的合理比例，以及座椅的高度和深度，都经过精确计算，为您提供舒适的工作环境。
> 
> 我们选用优质材料和精细工艺来打造这个系列的产品。外壳底座采用改性尼龙PA6/PA66涂层的铸铝，确保耐用性和稳定性。座椅使用高密度泡沫，提供出色的舒适感和支持力。我们坚持在制造过程中注重细节，以确保每个产品都达到最高的质量标准。
> 
> 无论您是在家办公还是在商业场所，我们的中世纪风格办公家具系列都能为您营造一个高品质和独特的工作空间。我们的产品不仅仅是家具，更是一种表达您品味和个性的方式。选择我们的中世纪风格办公家具系列，
> ```

### 问题1：生成的文本太长

它似乎很好地写了一个描述，介绍了一个惊人的中世纪灵感办公椅，很好地完成了要求，即从技术说明书开始编写产品描述。但是当我看到这个时，我会觉得这个太长了。

所以有了一个想法。我写了一个提示，得到了结果。但是我对它不是很满意，因为它太长了，所以我会澄清我的提示，并说最多使用50个字。

因此，我通过要求它限制生成文本长度来解决这一问题

```python
# 优化后的 Prompt，要求生成描述不多于 50 词
prompt = f"""
您的任务是帮助营销团队基于技术说明书创建一个产品的零售网站描述。

根据```标记的技术说明书中提供的信息，编写一个产品描述。

使用最多50个词。

技术规格：```{fact_sheet_chair}```
"""
response = get_completion(prompt)
print(response)
```

> ```python
> 优雅中世纪风格办公家具系列，包括文件柜、办公桌等。多种颜色和底座选择。舒适座椅，调节功能。适用于家庭或商业场所。高质量材料和精细工艺。营造独特办公环境。
> ```

LLM在遵循非常精确的字数限制方面表现得还可以，**但并不那么出色。有时它会输出60或65个单词的内容，但这还算是合理的。这原因是 LLM 解释文本使用一种叫做分词器的东西，但它们往往在计算字符方面表现一般般**。有很多不同的方法来尝试控制你得到的输出的长度。

### 问题2： 文本关注在错误的细节上

这个网站并不是直接向消费者销售，它**实际上旨在向家具零售商销售家具，他们会更关心椅子的技术细节和材料**。在这种情况下，你可以修改这个提示，让它**更精确地描述椅子的技术细节**。

解决方法：要求它专注于与目标受众相关的方面。

```python
# 优化后的 Prompt，说明面向对象，应具有什么性质且侧重于什么方面
prompt = f"""
您的任务是帮助营销团队基于技术说明书创建一个产品的零售网站描述。

根据```标记的技术说明书中提供的信息，编写一个产品描述。

该描述面向家具零售商，因此应具有技术性质，并侧重于产品的材料构造。

使用最多50个单词。

技术规格： ```{fact_sheet_chair}```
"""
response = get_completion(prompt)
print(response)
```

> ```python
> 引领中世纪风格，我们的办公家具系列是独一无二的选择。结构稳固，采用铝底座和改性尼龙外壳。座椅舒适，座椅泡沫密度可选。多种颜色和底座涂层选择。为您的客户提供高品质、耐用和精细工艺的办公家具。
> ```

可能进一步想要在描述的结尾包括产品ID。因此，可以进一步改进这个提示，要求在描述的结尾，包括在技术说明中的每个7个字符产品ID。

```python
# 更进一步
prompt = f"""
您的任务是帮助营销团队基于技术说明书创建一个产品的零售网站描述。

根据```标记的技术说明书中提供的信息，编写一个产品描述。

该描述面向家具零售商，因此应具有技术性质，并侧重于产品的材料构造。

在描述末尾，包括技术规格中每个7个字符的产品ID。

使用最多50个单词。

技术规格： ```{fact_sheet_chair}```
"""
response = get_completion(prompt)
print(response)
```

> ```python
> 探索中世纪风格办公家具系列。铝底座和改性尼龙外壳提供结构稳固。多色可选，座椅舒适，泡沫密度多样。适用于家庭和商业场所。高品质、耐用，细致工艺。技术规格：宽53cm，深51cm，高80cm，座椅高44cm，ID: A1B2C3D。满足家具零售商需求的理想选择。
> ```

### 问题3： 需要一个表格形式的描述

Prompt 应该保持清晰和明确，并在必要时给模型一些思考时间。在这些要求的基础上，通常值得首先尝试编写 Prompt ，看看会发生什么，然后从那里开始迭代地完善 Prompt，以逐渐接近所需的结果。因此，许多成功的Prompt都是通过这种迭代过程得出的。

添加了一些额外的说明，要求它抽取信息并组织成表格，并指定表格的列、表名和格式，还要求它将所有内容格式化为可以在网页使用的 HTML。

```python
# 要求它抽取信息并组织成表格，并指定表格的列、表名和格式
prompt = f"""
您的任务是帮助营销团队基于技术说明书创建一个产品的零售网站描述。

根据```标记的技术说明书中提供的信息，编写一个产品描述。

该描述面向家具零售商，因此应具有技术性质，并侧重于产品的材料构造。

在描述末尾，包括技术规格中每个7个字符的产品ID。

在描述之后，包括一个表格，提供产品的尺寸。表格应该有两列。第一列包括尺寸的名称。第二列只包括英寸的测量值。

给表格命名为“产品尺寸”。

将所有内容格式化为可用于网站的HTML格式。将描述放在<div>元素中。

技术规格：```{fact_sheet_chair}```
"""

response = get_completion(prompt)
print(response)

# 表格是以 HTML 格式呈现的，加载出来
from IPython.display import display, HTML

display(HTML(response))
```

![|inline](https://i.imgs.ovh/2023/10/19/2Hs7N.png)

本章的主要内容是 LLM 在开发应用程序中的迭代式提示开发过程。开发者需要先尝试编写提示，然后通过迭代逐步完善它，直至得到需要的结果。关键在于拥有一种有效的开发Prompt的过程，而不是知道完美的Prompt。对于一些更复杂的应用程序，可以对多个样本进行迭代开发提示并进行评估。最后，可以在更成熟的应用程序中测试多个Prompt在多个样本上的平均或最差性能

---

## 4 文本概括

### 单一文本概括Prompt实验

对于电商平台来说，网站上往往存在着海量的商品评论，这些评论反映了所有客户的想法。如果我们拥有一个工具去概括这些海量、冗长的评论，便能够快速地浏览更多评论，洞悉客户的偏好，从而指导平台与商家提供更优质的服务。

```python
prod_review_zh = """
这个熊猫公仔是我给女儿的生日礼物，她很喜欢，去哪都带着。
公仔很软，超级可爱，面部表情也很和善。但是相比于价钱来说，
它有点小，我感觉在别的地方用同样的价钱能买到更大的。
快递比预期提前了一天到货，所以在送给女儿之前，我自己玩了会。
"""

prompt = f"""
你的任务是从电子商务网站上生成一个产品评论的简短摘要。

请对三个反引号之间的评论文本进行概括，最多30个词汇。

评论: ```{prod_review_zh}```
"""

response = get_completion(prompt)
print(response)
```

> ```python
> 熊猫公仔：软萌可爱，面部和善，价值一般。快递提前一天送达。
> ```

### 关键角度侧重

有时，针对不同的业务，我们对文本的侧重会有所不同。例如对于商品评论文本，物流会更关心运输时效，商家更加关心价格与商品质量，平台更关心整体服务体验。

**可以通过增加Prompt提示，来体现对于某个特定角度的侧重。**

* 侧重于运输

```python
prompt = f"""
你的任务是从电子商务网站上生成一个产品评论的简短摘要。

请对三个反引号之间的评论文本进行概括，最多30个词汇，并且聚焦在产品运输上。

评论: ```{prod_review_zh}```
"""

response = get_completion(prompt)
print(response)
```

> ```python
> 提前到货，满意女儿的生日礼物。公仔可爱软萌，面部表情友善。运输迅速。
> ```

可以看到，输出结果以“提前到货”开头，体现了对于快递效率的侧重。

* 侧重于价格与质量

```python
prompt = f"""
你的任务是从电子商务网站上生成一个产品评论的简短摘要。

请对三个反引号之间的评论文本进行概括，最多30个词汇，并且聚焦在产品价格和质量上。

评论: ```{prod_review_zh}```
"""

response = get_completion(prompt)
print(response)
```

> ```python
> 超可爱软熊猫公仔，女儿喜欢。但有点小，同价位可买更大的。快递提前到，我玩了会。
> ```

可以看到，输出结果以超可爱软熊猫公仔，女儿喜欢。但有点小，同价位可买更大的，体现了对于产品价格与质量的侧重。



### 关键信息的提取

虽然我们通过添加关键角度侧重的Prompt，使得文本摘要更侧重于某一特定方面，但是可以发现，结果中也会保留一些其他信息，如价格与质量角度的概括中仍保留了“快递提前到货”的信息。有时这些信息是有帮助的，但如果我们只想要提取某一角度的信息，并过滤掉其他所有信息，则可以要求LLM进行“文本提取(Extract)”而非“文本概括(Summarize)”。

```python
prompt = f"""
你的任务是从电子商务网站上的产品评论中提取相关信息。

请从以下三个反引号之间的评论文本中提取产品运输相关的信息，最多30个词汇。

评论: ```{prod_review_zh}```
"""

response = get_completion(prompt)
print(response)
```

> ```python
> 提前到货，快递准时，顺利送达。
> ```

### 多条文本概况Prompt实验

在实际的工作流中，往往有许许多多的评论文本，以下展示了一个基于for循环调用“文本概括”工具并依次打印的示例。当然，在实际生产中，**对于上百万甚至上千万的评论文本，使用for循环也是不现实的，可能需要考虑整合评论、分布式等方法提升运算效率。**

```python
review_1_zh = prod_review 

# review for a standing lamp
review_2_zh = """
我的卧室需要一盏漂亮的灯，这盏有额外的储物空间，而且价格不太高。
很快就到了——两天后就到了。
灯的绳子在运输过程中断了，很高兴公司送来了一个新的，几天之内也送来了。
这很容易组合起来。然后我有一个缺失的部分，
所以我联系了他们的支持，他们很快就给了我缺失的部分！
在我看来，这是一家关心客户和产品的伟大公司。
"""

# review for an electric toothbrush
review_3_zh = """
我的牙科保健师推荐了一把电动牙刷，这就是我得到这个的原因。
到目前为止，电池寿命似乎相当可观。
在初次充电并让充电器插上电源第一周以调节电池后，我拔下了充电器的插头，
在过去的3周里每天用它刷两次，都是在同一次充电的情况下。
但是牙刷头太小了。我见过比这个大的婴儿牙刷。
我希望头部更大，刷毛长度不同，这样可以更好地夹在牙齿之间，因为这一个没有。
总的来说，如果你能在50美元左右买到这件，那就太划算了。
制造商的替换头相当昂贵，但你可以买到价格更合理的通用头。
这把牙刷让我觉得我每天都去看牙医。我的牙齿感觉干净得闪闪发光！
"""

# review for a blender
review_4_zh = """
因此，他们的17件套系统在11月仍在季节性销售，价格约为49美元，约为半价，
但由于某种原因（称之为价格欺诈），在12月的第二周左右，
同一系统的价格都上涨到了70美元至89美元之间。
11件套的价格也比之前的29美元上涨了大约10美元。
所以它看起来还可以，但如果你看一下底座，
刀片固定到位的部分看起来不像几年前的前几版那么好，
但我计划对它非常温和（
例如，我在先用搅拌机搅拌，然后在搅拌机中按我想要的份量将其粉碎，
然后换成搅打刀片以获得更细的面粉，制作冰沙时先用横切刀片，
如果我需要更细/更不浆的话，可以用平刀片）。
制作冰沙时的特别提示是，将水果和蔬菜切细并冷冻（
如果使用菠菜轻轻炖煮，软化菠菜，然后冷冻直到可以使用；
如果制作冰沙，请使用中小型食品加工机），
这样你就可以避免在制作冰沙的时候添加太多冰。
大约一年后，发动机发出奇怪的声音。
我打电话给客服，但保修期已经到期，所以我不得不再买一辆。
仅供参考：
这些类型的产品的整体质量都很好，
所以他们有点依赖品牌认可度和消费者忠诚度来维持销售额。
大约两天就搞定了。
"""

reviews_zh = [review_1_zh, review_2_zh, review_3_zh, review_4_zh]
```

```python
for i in range(len(reviews)):
    prompt = f"""
    你的任务是从电子商务网站生成一个简短的产品评论摘要。
    总结下面的评论，用最多20个字的三个反引号分隔。

    评论:  ```{reviews_zh[i]}```
    """
    response = get_completion(prompt)
    print(i, response, "\n")
```

> ```python
> 0 柔软可爱的熊猫毛绒玩具深受女儿的喜爱，但价格有点小。提前到达。
> 
> 1 价格合理的灯具，具有存储、快速运输和卓越的客户服务。易于组装和丢失的零件很快被更换。 
> 
> 2 电池寿命长，牙刷头小，但清洁效果好。如果在50美元左右买，那就太划算了。 
> 
> 3 该产品在11月以49美元的价格出售，但在12月价格上涨至70至89美元。这个底座看起来没有前几版那么好，但评论家计划对它温和一点。制作冰沙的一个特别提示是提前冷冻水果和蔬菜。一年后，这台电动机发出奇怪的噪音，保修期已经过期。整体质量下降。
> ```

---

## 5 推理

在这节课中，**将从产品评论和新闻文章中推断情感和主题**。

这些任务可以看作是模型接收文本作为输入并执行某种分析的过程。这可能涉及提取标签、提取实体、理解文本情感等等。如果想要从一段文本中提取正面或负面情感，在传统的机器学习工作流程中，需要收集标签数据集、训练模型、确定如何在云端部署模型并进行推断。这样做可能效果还不错，但是这个过程需要很多工作。而且对于每个任务，如情感分析、提取实体等等，都需要训练和部署单独的模型。

大型语言模型的一个非常好的特点是，对于许多这样的任务，只需要编写一个prompt即可开始产生结果，而不需要进行大量的工作。这极大地加快了应用程序开发的速度。还可以只使用一个模型和一个 API 来执行许多不同的任务，而不需要弄清楚如何训练和部署许多不同的模型。

### 商品评论文本

这是一盏台灯的评论。

```python
# 中文
lamp_review_zh = """
我需要一盏漂亮的卧室灯，这款灯具有额外的储物功能，价格也不算太高。\
我很快就收到了它。在运输过程中，我们的灯绳断了，但是公司很乐意寄送了一个新的。\
几天后就收到了。这款灯很容易组装。我发现少了一个零件，
于是联系了他们的客服，他们很快就给我寄来了缺失的零件！\
在我看来，Lumina 是一家非常关心顾客和产品的优秀公司！
"""
```

### 情感（正向/负向）

**现在来编写一个prompt来分类这个评论的情感**。如果想让系统告诉我这个评论的情感是什么，只需要编写 “以下产品评论的情感是什么” 这个prompt，加上通常的分隔符和评论文本等等。

然后我们运行一下。结果显示这个产品评论的情感是积极的，这似乎是非常正确的。虽然这盏台灯不完美，但这个客户似乎非常满意。这似乎是一家关心客户和产品的伟大公司，可以认为积极的情感似乎是正确的答案。

```python
# 中文
prompt = f"""
以下用三个反引号分隔的产品评论的情感是什么？

评论文本: ```{lamp_review_zh}```
"""
response = get_completion(prompt)
print(response)
```

> ```python
> 情感是积极的/正面的。
> ```

如果想要更简洁的答案以便更容易进行后处理，可以使用上面的prompt并添加另一个指令，以一个单词 “正面” 或 “负面” 的形式给出答案。这样就只会打印出 “正面” 这个单词，这使得文本更容易接受这个输出并进行处理。

```python
prompt = f"""
以下用三个反引号分隔的产品评论的情感是什么？

用一个单词回答：「正面」或「负面」。

评论文本: ```{lamp_review_zh}```
"""
response = get_completion(prompt)
print(response)
```

> ```python
> 正面
> ```

### 识别情感类型

下面看看另一个prompt，仍然使用台灯评论。这次我要让它识别出以下评论作者所表达的情感列表，不超过五个。

```python
# 中文
prompt = f"""
识别以下评论的作者表达的情感。包含不超过五个项目。将答案格式化为以逗号分隔的单词列表。

评论文本: ```{lamp_review_zh}```
"""
response = get_completion(prompt)
print(response)
```

> ```python
> 正面, 满意, 高效, 贴心, 优质
> ```

大型语言模型非常擅长从一段文本中提取特定的东西。在上面的例子中，评论可以表达情感，这可能有助于了解客户如何看待特定的产品。

### 识别愤怒

对于很多企业来说，了解某个顾客是否非常生气很重要。所以可能有一个类似这样的分类问题：以下评论的作者是否表达了愤怒情绪？因为如果有人真的很生气，那么可能值得额外关注，让客户支持或客户成功团队联系客户以了解情况，并为客户解决问题。

```python
# 中文
prompt = f"""
以下评论的作者是否表达了愤怒？评论用三个反引号分隔。给出是或否的答案。

评论文本: ```{lamp_review_zh}```
"""
response = get_completion(prompt)
print(response)
```

> ```python
> 否
> ```

上面这个例子中，客户并没有生气。如果使用常规的监督学习，如果想要建立所有这些分类器，不可能在几分钟内就做到这一点。鼓励大家尝试更改一些这样的prompt，也许询问客户是否表达了喜悦，或者询问是否有任何遗漏的部分，并看看是否可以让prompt对这个灯具评论做出不同的推论。

### 从客户评论中提取产品和公司名称

信息提取是自然语言处理（NLP）的一部分，与从文本中提取你想要知道的某些事物相关。因此，在这个prompt中，我要求它识别以下内容：**购买物品和制造物品的公司名称**。

同样，如果试图总结在线购物电子商务网站的许多评论，对于这些评论来说，弄清楚是什么物品，谁制造了该物品，弄清楚积极和消极的情感，以跟踪特定物品或特定制造商的积极或消极情感趋势，可能会很有用。

在下面这个示例中，我们要求它将响应格式化为一个 JSON 对象，其中物品和品牌是键。

```python
# 中文
prompt = f"""
从评论文本中识别以下项目：
- 评论者购买的物品
- 制造该物品的公司

评论文本用三个反引号分隔。将你的响应格式化为以 “物品” 和 “品牌” 为键的 JSON 对象。
如果信息不存在，请使用 “未知” 作为值。
让你的回应尽可能简短。
  
评论文本: ```{lamp_review_zh}```
"""
response = get_completion(prompt)
print(response)
```

> ```python
> {
> "物品": "卧室灯",
> "品牌": "Lumina"
> }
> ```

如上所示，它会说这个物品是一个卧室灯，品牌是 Luminar，你可以轻松地将其加载到 Python 字典中，然后对此输出进行其他处理。

### 一次完成多项任务

提取上面所有这些信息使用了 3 或 4 个prompt，但实际上可以编写单个prompt来同时提取所有这些信息。

```python
# 中文
prompt = f"""
从评论文本中识别以下项目：
- 情绪（正面或负面）
- 审稿人是否表达了愤怒？（是或否）
- 评论者购买的物品
- 制造该物品的公司

评论用三个反引号分隔。将您的响应格式化为 JSON 对象，以 “Sentiment”、“Anger”、“Item” 和 “Brand” 作为键。
如果信息不存在，请使用 “未知” 作为值。
让你的回应尽可能简短。
将 Anger 值格式化为布尔值。

评论文本: ```{lamp_review_zh}```
"""
response = get_completion(prompt)
print(response)
```

> ```python
> {
> "Sentiment": "正面",
> "Anger": false,
> "Item": "卧室灯",
> "Brand": "Lumina"
> }
> ```

这个例子中，告诉它将愤怒值格式化为布尔值，然后输出一个 JSON。大家可以自己尝试不同的变化，或者甚至尝试完全不同的评论，看看是否仍然可以准确地提取这些内容。

### 推断主题

大型语言模型的一个很酷的应用是推断主题。给定一段长文本，这段文本是关于什么的？有什么话题？

```python
# 中文
story_zh = """
在政府最近进行的一项调查中，要求公共部门的员工对他们所在部门的满意度进行评分。
调查结果显示，NASA 是最受欢迎的部门，满意度为 95％。

一位 NASA 员工 John Smith 对这一发现发表了评论，他表示：
“我对 NASA 排名第一并不感到惊讶。这是一个与了不起的人们和令人难以置信的机会共事的好地方。我为成为这样一个创新组织的一员感到自豪。”

NASA 的管理团队也对这一结果表示欢迎，主管 Tom Johnson 表示：
“我们很高兴听到我们的员工对 NASA 的工作感到满意。
我们拥有一支才华横溢、忠诚敬业的团队，他们为实现我们的目标不懈努力，看到他们的辛勤工作得到回报是太棒了。”

调查还显示，社会保障管理局的满意度最低，只有 45％的员工表示他们对工作满意。
政府承诺解决调查中员工提出的问题，并努力提高所有部门的工作满意度。
"""

prompt = f"""
	文本用三个反引号分隔，这个文本是关于什么的？有什么话题？

	评论文本: ```{story_zh}```
    """
response = get_completion(prompt)
print(response)
```

> ```python
> 这篇文章是关于政府部门员工对所在部门满意度的调查结果。主要话题是各部门的满意度比较，特别提及了NASA作为最受欢迎部门和社会保障管理局满意度最低的情况。
> ```

### 推断5个主题

上面是一篇虚构的关于政府工作人员对他们工作机构感受的报纸文章。我们可以让它确定五个正在讨论的主题，用一两个字描述每个主题，并将输出格式化为逗号分隔的列表。

```python
# 中文
prompt = f"""
确定以下给定文本中讨论的五个主题。

每个主题用1-2个单词概括。

输出时用逗号分割每个主题。

给定文本: ```{story_zh}```
"""
response = get_completion(prompt)
print(response)
```

> ```python
> 调查结果, NASA满意度, 社会保障管理局满意度, 员工评论, 政府承诺
> ```

### 为特定主题制作新闻提醒

假设有一个新闻网站或类似的东西，这是我们感兴趣的主题：NASA、地方政府、工程、员工满意度、联邦政府等。假设我们想弄清楚，针对一篇新闻文章，其中涵盖了哪些主题。可以使用这样的prompt：**确定以下主题列表中的每个项目是否是以下文本中的主题。以 0 或 1 的形式给出答案列表。**

```python
# 中文
prompt = f"""
判断主题列表中的每一项是否是给定文本中的一个话题，

以列表的形式给出答案，每个主题用 0 或 1。

主题列表：美国航空航天局、当地政府、工程、员工满意度、联邦政府

以json的格式输出，其中key是主题列表中的元素

给定文本: ```{story_zh}```
"""
response = get_completion(prompt)
print(response)
```

> ```python
> {
> "美国航空航天局": 1,
> "当地政府": 0,
> "工程": 0,
> "员工满意度": 1,
> "联邦政府": 1
> }
> ```

所以，这个故事是关于 NASA 的。它不是关于当地政府的，不是关于工程的。它是关于员工满意度的，它是关于联邦政府的。**这在机器学习中有时被称为 Zero-Shot 学习算法**，因为我们没有给它任何标记的训练数据。仅凭prompt，它就能确定哪些主题在新闻文章中涵盖了。

如果想生成一个新闻提醒，也可以使用这个处理新闻的过程。假设我非常喜欢 NASA 所做的工作，就可以构建一个这样的系统，每当 NASA 新闻出现时，输出提醒。

```python
topic_dict = response
if topic_dict['美国航空航天局'] == 1:
    print("提醒: 关于美国航空航天局的新消息")
```

这就是关于推断的全部内容了，仅用几分钟时间，就可以构建多个用于对文本进行推理的系统，而以前则需要熟练的机器学习开发人员数天甚至数周的时间。这非常令人兴奋，无论是对于熟练的机器学习开发人员还是对于新手来说，都可以使用prompt来非常快速地构建和开始相当复杂的自然语言处理任务。

---

## 6 文本转换

LLM非常擅长将输入转换成不同的格式，例如多语种文本翻译、拼写及语法纠正、语气调整、格式转换等。本部分将介绍如何使用编程的方式，调用API接口来实现“文本转换”功能。

### 文本翻译

* **中文转西班牙语**

```python
prompt = f"""
请将以下中文翻译成西班牙语: 
​```您好，我想订购一个搅拌机。```
"""
response = get_completion(prompt)
print(response)
```

> ```python
> "Hola, me gustaría ordenar una batidora."
> ```

* **识别语种**

```python
prompt = f"""
请告诉我以下文本是什么语种: 
​```Combien coûte le lampadaire?```
"""
response = get_completion(prompt)
print(response)
```

> ```python
> 以下文本是法语。
> ```

* **多语种翻译**

```python
prompt = f"""
请将以下文本分别翻译成中文、英文、法语和西班牙语: 
​```I want to order a basketball.```
"""
response = get_completion(prompt)
print(response)
```

> ```python
> 以下是文本的翻译：
> 
> 中文：我想订购一个篮球。
> 英文：I want to order a basketball.
> 法语：Je souhaite commander un ballon de basket.
> 西班牙语：Quiero ordenar una pelota de baloncesto.
> ```

* **翻译+正式语气**

```python
prompt = f"""
请将以下文本翻译成中文，分别展示成正式与非正式两种语气: 
​```Would you like to order a pillow?```
"""
response = get_completion(prompt)
print(response)
```

> ```python
> 以下是文本的翻译，分别展示了正式和非正式两种语气：
> 
> 正式：
> 您是否想要订购一个枕头？
> 
> 非正式：
> 你想订购一个枕头吗？
> ```

* **通用翻译器**

```python
user_messages = [
  "La performance du système est plus lente que d'habitude.",  # System performance is slower than normal         
  "Mi monitor tiene píxeles que no se iluminan.",              # My monitor has pixels that are not lighting
  "Il mio mouse non funziona",                                 # My mouse is not working
  "Mój klawisz Ctrl jest zepsuty",                             # My keyboard has a broken control key
  "我的屏幕在闪烁"                                             # My screen is flashing
]

for issue in user_messages:
    time.sleep(20)
    prompt = f"告诉我以下文本是什么语种，直接输出语种，如法语，无需输出标点符号: ```{issue}```"
    lang = get_completion(prompt)
    print(f"原始消息 ({lang}): {issue}\n")

    prompt = f"""
    将以下消息分别翻译成英文和中文，并写成
    中文翻译：xxx
    英文翻译：yyy
    的格式：
    ```{issue}```
    """
    response = get_completion(prompt)
    print(response, "\n=========================================")
```

> ```python
> 原始消息 (法语): La performance du système est plus lente que d'habitude.
> 
> 中文翻译：系统性能比平时慢。
> 英文翻译：The system performance is slower than usual. 
> =========================================
> 原始消息 (西班牙语): Mi monitor tiene píxeles que no se iluminan.
> 
> 中文翻译：我的显示器有一些像素点不亮。
> 英文翻译：My monitor has pixels that don't light up. 
> =========================================
> 原始消息 (意大利语): Il mio mouse non funziona
> 
> 中文翻译：我的鼠标不工作了。
> 英文翻译：My mouse is not working. 
> =========================================
> 原始消息 (波兰语): Mój klawisz Ctrl jest zepsuty
> 
> 中文翻译：我的Ctrl键坏了
> 英文翻译：My Ctrl key is broken. 
> =========================================
> 原始消息 (中文): 我的屏幕在闪烁
> 
> 中文翻译：我的屏幕在闪烁。
> 英文翻译：My screen is flickering. 
> =========================================
> ```

### 语气/风格调整

写作的语气往往会根据受众对象而有所调整。例如，对于工作邮件，常常需要使用正式语气与书面用词，而对同龄朋友的微信聊天，可能更多地会使用轻松、口语化的语气。

```python
prompt = f"""
将以下文本翻译成商务信函的格式: 
​```小老弟，我小羊，上回你说咱部门要采购的显示器是多少寸来着？```
"""
response = get_completion(prompt)
print(response)
```

> ```python
> 尊敬的XXX（收件人姓名）：
> 
> 您好！我是XXX（发件人姓名），在此向您咨询一个问题。上次我们交流时，您提到我们部门需要采购显示器，但我忘记了您所需的尺寸是多少英寸。希望您能够回复我，以便我们能够及时采购所需的设备。
> 
> 谢谢您的帮助！
> 
> 顺祝商祺
> XXX（发件人姓名）
> ```

### 格式转换

ChatGPT非常擅长不同格式之间的转换，例如JSON到HTML、XML、Markdown等。在下述例子中，我们有一个包含餐厅员工姓名和电子邮件的列表的JSON，我们希望将其从JSON转换为HTML。

```python
data_json = { "resturant employees" :[ 
    {"name":"Shyam", "email":"shyamjaiswal@gmail.com"},
    {"name":"Bob", "email":"bob32@gmail.com"},
    {"name":"Jai", "email":"jai87@gmail.com"}
]}

prompt = f"""
将以下Python字典从JSON转换为HTML表格，保留表格标题和列名：{data_json}
"""
response = get_completion(prompt)
print(response)
```

> ```html
> <table>
> <caption>resturant employees</caption>
> <tr>
> <th>name</th>
> <th>email</th>
> </tr>
> <tr>
> <td>Shyam</td>
> <td>shyamjaiswal@gmail.com</td>
> </tr>
> <tr>
> <td>Bob</td>
> <td>bob32@gmail.com</td>
> </tr>
> <tr>
> <td>Jai</td>
> <td>jai87@gmail.com</td>
> </tr>
> </table>
> ```

```python
from IPython.display import display, Markdown, Latex, HTML, JSON
display(HTML(response))
```

![image-20230614140023387](C:\Users\DELL-3020\AppData\Roaming\Typora\typora-user-images\image-20230614140023387.png)

### 拼写及语法纠正

拼写及语法的检查与纠正是一个十分常见的需求，特别是使用非母语语言，例如发表英文论文时，这是一件十分重要的事情。

以下给了一个例子，有一个句子列表，其中有些句子存在拼写或语法问题，有些则没有，循环遍历每个句子，要求模型校对文本，**如果正确则输出“未发现错误”，如果错误则输出纠正后的文本**。

```python
text = [ 
  "The girl with the black and white puppies have a ball.",  # The girl has a ball.
  "Yolanda has her notebook.", # ok
  "Its going to be a long day. Does the car need it’s oil changed?",  # Homonyms
  "Their goes my freedom. There going to bring they’re suitcases.",  # Homonyms
  "Your going to need you’re notebook.",  # Homonyms
  "That medicine effects my ability to sleep. Have you heard of the butterfly affect?", # Homonyms
  "This phrase is to cherck chatGPT for spelling abilitty"  # spelling
]

for i in range(len(text)):
    time.sleep(20)
    prompt = f"""请校对并更正以下文本，注意纠正文本保持原始语种，无需输出原始文本。
    如果您没有发现任何错误，请说“未发现错误”。
    
    例如：
    输入：I are happy.
    输出：I am happy.
    ```{text[i]}```"""
    response = get_completion(prompt)
    print(i, response)
```

> ```python
> 0 The girl with the black and white puppies has a ball.
> 1 未发现错误。
> 2 It's going to be a long day. Does the car need its oil changed?
> 3 Their goes my freedom. They're going to bring their suitcases.
> 4 You're going to need your notebook.
> 5 That medicine affects my ability to sleep. Have you heard of the butterfly effect?
> 6 This phrase is to check chatGPT for spelling ability.
> ```

以下是一个简单的类Grammarly纠错示例，输入原始文本，输出纠正后的文本，并基于Redlines输出纠错过程。

```python
text = f"""
Got this for my daughter for her birthday cuz she keeps taking \
mine from my room.  Yes, adults also like pandas too.  She takes \
it everywhere with her, and it's super soft and cute.  One of the \
ears is a bit lower than the other, and I don't think that was \
designed to be asymmetrical. It's a bit small for what I paid for it \
though. I think there might be other options that are bigger for \
the same price.  It arrived a day earlier than expected, so I got \
to play with it myself before I gave it to my daughter.
"""

prompt = f"校对并更正以下商品评论：```{text}```"
response = get_completion(prompt)
print(response)
```

> ```python
> I got this for my daughter's birthday because she keeps taking mine from my room. Yes, adults also like pandas too. She takes it everywhere with her, and it's super soft and cute. However, one of the ears is a bit lower than the other, and I don't think that was designed to be asymmetrical. It's also a bit smaller than I expected for the price. I think there might be other options that are bigger for the same price. On the bright side, it arrived a day earlier than expected, so I got to play with it myself before giving it to my daughter.
> ```

```python
from redlines import Redlines
from IPython.display import display, Markdown

diff = Redlines(text,response)
display(Markdown(diff.output_markdown))
```

### 综合样例：文本翻译+拼写纠正+风格调整+格式转换

```python
text = f"""
Got this for my daughter for her birthday cuz she keeps taking \
mine from my room.  Yes, adults also like pandas too.  She takes \
it everywhere with her, and it's super soft and cute.  One of the \
ears is a bit lower than the other, and I don't think that was \
designed to be asymmetrical. It's a bit small for what I paid for it \
though. I think there might be other options that are bigger for \
the same price.  It arrived a day earlier than expected, so I got \
to play with it myself before I gave it to my daughter.
"""
```

```python
prompt = f"""
针对以下三个反引号之间的英文评论文本，
首先进行拼写及语法纠错，
然后将其转化成中文，
再将其转化成优质淘宝评论的风格，从各种角度出发，分别说明产品的优点与缺点，并进行总结。
润色一下描述，使评论更具有吸引力。
输出结果格式为：
【优点】xxx
【缺点】xxx
【总结】xxx
注意，只需填写xxx部分，并分段输出。
将结果输出成Markdown格式。
​```{text}```
"""
response = get_completion(prompt)
display(Markdown(response))
```

> ```python
> 【拼写及语法纠错】
> "Got this for my daughter for her birthday cuz she keeps taking mine from my room. Yes, adults also like pandas too. She takes it everywhere with her, and it's super soft and cute. One of the ears is a bit lower than the other, and I don't think that was designed to be asymmetrical. It's a bit small for what I paid for it though. I think there might be other options that are bigger for the same price. It arrived a day earlier than expected, so I got to play with it myself before I gave it to my daughter."
> 
> 【英文转中文】
> 我给女儿买了这个作为她的生日礼物，因为她总是拿走我房间里的。是的，成年人也喜欢熊猫。她把它带到任何地方，它非常柔软和可爱。其中一只耳朵比另一只耳朵低一点，我不认为这是设计上的不对称。不过，相对于我付的价格来说，它有点小。我想可能还有其他相同价格但更大的选择。它比预期提前了一天到达，所以我在给女儿之前还能亲自玩一下。
> 
> 【优质淘宝评论风格】
> 【优点】
> - 材质柔软可爱，适合孩子们抱着玩，我女儿非常喜欢。
> - 熊猫不仅适合孩子，成年人也能找到共鸣，它真的很受欢迎。
> - 提前一天送到，让我有机会先体验一下，质量和预期相符。
> 
> 【缺点】
> - 有一只耳朵比另一只低一点，这不是设计上的特点，有点让人失望。
> - 相对于价格来说，尺寸有点小了，希望能有更大的选择。
> 
> 【总结】
> 这款熊猫玩具质地柔软可爱，非常适合孩子们抱着玩。不仅小朋友喜欢，成年人也能找到共鸣。然而，有一些缺点需要注意，比如耳朵的不对称和尺寸相对较小。考虑到价格，可能有其他更大的选择可供选择。整体而言，提前送达的快递服务让人满意。
> ```

## 7 文本扩展

扩展是将短文本，例如一组说明或主题列表，输入到大型语言模型中，让模型生成更长的文本，例如基于某个主题的电子邮件或论文。这样做有一些很好的用途，例如将大型语言模型用作头脑风暴的伙伴。但这种做法也存在一些问题，例如某人可能会使用它来生成大量垃圾邮件。因此，当你使用大型语言模型的这些功能时，请仅以负责任的方式和有益于人们的方式使用它们。

本节将学会如何基于 OpenAI API 生成适用于每个客户评价的客户服务电子邮件。我们还将使用模型的另一个输入参数称为温度，这种参数允许您在模型响应中变化探索的程度和多样性。

### 定制客户邮件

将根据客户评价和情感撰写自定义电子邮件响应。因此，我们将给定客户评价和情感，并生成自定义响应即使用 LLM 根据客户评价和评论情感生成定制电子邮件。

首先给出一个示例，包括一个评论及对应的情感。

```python
# 可以在推理那章学习到如何对一个评论判断其情感倾向
sentiment = "negative"

# 一个产品的评价
review = f"""
他们在11月份的季节性销售期间以约49美元的价格出售17件套装，折扣约为一半。\
但由于某些原因（可能是价格欺诈），到了12月第二周，同样的套装价格全都涨到了70美元到89美元不等。\
11件套装的价格也上涨了大约10美元左右。\
虽然外观看起来还可以，但基座上锁定刀片的部分看起来不如几年前的早期版本那么好。\
不过我打算非常温柔地使用它，例如，\
我会先在搅拌机中将像豆子、冰、米饭等硬物研磨，然后再制成所需的份量，\
切换到打蛋器制作更细的面粉，或者在制作冰沙时先使用交叉切割刀片，然后使用平面刀片制作更细/不粘的效果。\
制作冰沙时，特别提示：\
将水果和蔬菜切碎并冷冻（如果使用菠菜，则轻轻煮软菠菜，然后冷冻直到使用；\
如果制作果酱，则使用小到中号的食品处理器），这样可以避免在制作冰沙时添加太多冰块。\
大约一年后，电机发出奇怪的噪音，我打电话给客服，但保修已经过期了，所以我不得不再买一个。\
总的来说，这些产品的总体质量已经下降，因此它们依靠品牌认可和消费者忠诚度来维持销售。\
货物在两天内到达。
"""
```

之前已经使用推断课程中学到的提取了情感，这是一个关于搅拌机的客户评价，现在我们将根据情感定制回复。

这里的指令是：假设你是一个客户服务AI助手，你的任务是为客户发送电子邮件回复，根据通过三个反引号分隔的客户电子邮件，生成一封回复以感谢客户的评价。

```python
prompt = f"""
你是一位客户服务的AI助手。
你的任务是给一位重要客户发送邮件回复。
根据客户通过“```”分隔的评价，生成回复以感谢客户的评价。提醒模型使用评价中的具体细节
用简明而专业的语气写信。
作为“AI客户代理”签署电子邮件。
客户评论：
​```{review}```
评论情感：{sentiment}
"""
response = get_completion(prompt)
print(response)
```

> ```python
> 尊敬的客户，
> 非常感谢您给出对我们产品的评价和反馈。我们非常重视您的意见，并对您的不满感到遗憾。
> 首先，对于在11月份季节性销售期间以49美元出售的17件套装，折扣约为一半的情况，
> 我们深感抱歉因价格上涨给您带来的困扰。我们会进一步核实价格变动的原因，
> 并确保以透明和公正的方式与客户交流。
> 此外，我们也注意到您提到产品的一些问题，如基座上锁定刀片的部分不如早期版本那么好，
> 以及电机在使用一年后发出奇怪的噪音。我们深表歉意，这些问题影响了您对产品的使用体验。
> 您的使用说明和温柔使用方法对我们来说非常重要，我们将会进一步改进产品的质量和性能，
> 以确保您获得更好的使用体验。
> 请允许我们再次表示诚挚的歉意，并感谢您对我们品牌的认可和消费者忠诚度。
> 我们将继续努力提供优质的产品和卓越的客户服务。
> 如果您对我们的产品或服务有任何其他问题或需要进一步的帮助，请随时与我们联系。我们将竭诚为您提供支持。
> 祝您度过愉快的一天！
> AI客户代理
> ```

### 使用温度系数

温度视为模型探索或随机性的程度。

例如，在一个特定的短语中，“我的最爱食品”最有可能的下一个词是“比萨”，其次最有可能的是“寿司”和“塔可”。因此，在温度为零时，模型将总是选择最有可能的下一个词，而在较高的温度下，它还将选择其中一个不太可能的词，在更高的温度下，它甚至可能选择塔可，而这种可能性仅为五分之一。您可以想象，随着模型继续生成更多单词的最终响应，“我的最爱食品是比萨”将会与第一个响应“我的最爱食品是塔可”产生差异。因此，随着模型的继续，这两个响应将变得越来越不同。

一般来说，**在构建需要可预测响应的应用程序时，建议使用温度为零。在所有课程中，我们一直设置温度为零，如果您正在尝试构建一个可靠和可预测的系统，我认为您应该选择这个温度**。如果您尝试以**更具创意的方式使用模型，可能需要更广泛地输出不同的结果，那么可能需要使用更高的温度**。

```python
# given the sentiment from the lesson on "inferring",
# and the original customer message, customize the email
sentiment = "negative"

# review for a blender
review = f"""
So, they still had the 17 piece system on seasonal \
sale for around $49 in the month of November, about \
half off, but for some reason (call it price gouging) \
around the second week of December the prices all went \
up to about anywhere from between $70-$89 for the same \
system. And the 11 piece system went up around $10 or \
so in price also from the earlier sale price of $29. \
So it looks okay, but if you look at the base, the part \
where the blade locks into place doesn’t look as good \
as in previous editions from a few years ago, but I \
plan to be very gentle with it (example, I crush \
very hard items like beans, ice, rice, etc. in the \ 
blender first then pulverize them in the serving size \
I want in the blender then switch to the whipping \
blade for a finer flour, and use the cross cutting blade \
first when making smoothies, then use the flat blade \
if I need them finer/less pulpy). Special tip when making \
smoothies, finely cut and freeze the fruits and \
vegetables (if using spinach-lightly stew soften the \ 
spinach then freeze until ready for use-and if making \
sorbet, use a small to medium sized food processor) \ 
that you plan to use that way you can avoid adding so \
much ice if at all-when making your smoothie. \
After about a year, the motor was making a funny noise. \
I called customer service but the warranty expired \
already, so I had to buy another one. FYI: The overall \
quality has gone done in these types of products, so \
they are kind of counting on brand recognition and \
consumer loyalty to maintain sales. Got it in about \
two days.
"""

prompt = f"""
你是一名客户服务的AI助手。
你的任务是给一位重要的客户发送邮件回复。
根据通过“```”分隔的客户电子邮件生成回复，以感谢客户的评价。
如果情感是积极的或中性的，感谢他们的评价。
如果情感是消极的，道歉并建议他们联系客户服务。
请确保使用评论中的具体细节。
以简明和专业的语气写信。
以“AI客户代理”的名义签署电子邮件。
客户评价：```{review}```
评论情感：{sentiment}
"""
response = get_completion(prompt, temperature=0.7)
print(response)
```

> ```python
> 尊敬的客户，
> 
> 非常感谢您对我们产品的评价。我们由衷地为您在购买过程中遇到的问题表示抱歉。我们确实在12月份的第二周调整了价格，但这是由于市场因素所致，并非价格欺诈。我们深刻意识到您对产品质量的担忧，我们将尽一切努力改进产品，以提供更好的体验。
> 
> 我们非常感激您对我们产品的使用经验和制作技巧的分享。您的建议和反馈对我们非常重要，我们将以此为基础，进一步改进我们的产品。
> 
> 如果您有任何疑问或需要进一步帮助，请随时联系我们的客户服务部门。我们将尽快回复您并提供帮助。
> 
> 最后，请再次感谢您对我们产品的评价和选择。我们期待着未来与您的合作。
> 
> 此致
> 
> 敬礼
> 
> AI客户代理
> ```

**在温度为零时，每次执行相同的提示时，应该期望获得相同的完成。而使用温度为0.7，每次都会获得不同的输出。**所以，可以看到它与我们之前收到的电子邮件不同。

因此，建议自己尝试温度，以查看输出如何变化。总之，**在更高的温度下，模型的输出更加随机**。几乎**可以将其视为在更高的温度下，助手更易分心，但也许更有创造力**。

---

## 8 聊天机器人 Chatbot

### 对话聊天

将探索如何利用聊天格式（接口）与个性化或专门针对特定任务或行为的聊天机器人进行延伸对话。

像 ChatGPT 这样的聊天模型实际上是组装成以一系列消息作为输入，并返回一个模型生成的消息作为输出的。虽然聊天格式的设计旨在使这种多轮对话变得容易，但我们通过之前的学习可以知道，它对于没有任何对话的单轮任务也同样有用。

接下来，我们将定义两个辅助函数。第一个是单轮的，我们将prompt放入看起来像是某种用户消息的东西中。另一个则传入一个消息列表。这些消息可以来自不同的角色，我们会描述一下这些角色。

第一条消息是一个系统消息，它提供了一个总体的指示，然后在这个消息之后，我们有用户和助手之间的交替。如果你曾经使用过 ChatGPT 网页界面，那么你的消息是用户消息，而 ChatGPT 的消息是助手消息。系统消息则有助于设置助手的行为和角色，并作为对话的高级指示。你可以想象它在助手的耳边低语，引导它的回应，而用户不会注意到系统消息。

因此，作为用户，如果你曾经使用过 ChatGPT，你可能不知道 ChatGPT 的系统消息是什么，这是有意为之的。系统消息的好处是为开发者提供了一种方法，在不让请求本身成为对话的一部分的情况下，引导助手并指导其回应。

这里不仅仅是一个支持单轮对话的函数`get_completion`，还需要支持多轮对话的函数`get_completion_from_messages`：

```python
def get_completion(prompt, model="gpt-3.5-turbo"):
    messages = [{"role": "user", "content": prompt}]
    response = openai.ChatCompletion.create(
        model=model,
        messages=messages,
        temperature=0, # 控制模型输出的随机程度
    )
    return response.choices[0].message["content"]

def get_completion_from_messages(messages, model="gpt-3.5-turbo", temperature=0):
    response = openai.ChatCompletion.create(
        model=model,
        messages=messages,
        temperature=temperature, # 控制模型输出的随机程度
    )
    return response.choices[0].message["content"]
```

现在尝试在对话中使用这些消息。将使用上面的函数来获取从这些消息中得到的回答，同时，使用更高的 temperature（越高生成的越多样）。

**系统消息说，你是一个说话像莎士比亚的助手。这是我们向助手描述它应该如何表现的方式**。然后，第一个用户消息是，给我讲个笑话。接下来的消息是，为什么鸡会过马路？然后最后一个用户消息是，我不知道。

```python
# 中文
messages =  [  
{'role':'system', 'content':'你是一个像莎士比亚一样说话的助手。'},    
{'role':'user', 'content':'给我讲个笑话'},   
{'role':'assistant', 'content':'鸡为什么过马路'},   
{'role':'user', 'content':'我不知道'}]

response = get_completion_from_messages(messages, temperature=1)
print(response)
```

> ```python
> 因为它要去找“母鸡”。哈哈哈！（注：此为英文双关语，"chicken"是鸡的意思，也是胆小的意思；"cross the road"是过马路的意思，也是“破坏规则”的意思。）
> ```

下一个例子，助手的消息是，你是一个友好的聊天机器人，第一个用户消息是，嗨，我叫Isa。我们想要得到第一个用户消息。

```python
# 中文
messages =  [  
{'role':'system', 'content':'你是个友好的聊天机器人。'},    
{'role':'user', 'content':'Hi, 我是Isa。'}  ]
response = get_completion_from_messages(messages, temperature=1)
print(response)
```

> ```python
> 嗨，Isa，很高兴见到你！有什么我可以帮助你的吗？
> ```

系统消息是，你是一个友好的聊天机器人，第一个用户消息是，是的，你能提醒我我的名字是什么吗？

```python
# 中文
messages =  [  
{'role':'system', 'content':'你是个友好的聊天机器人。'},    
{'role':'user', 'content':'好，你能提醒我，我的名字是什么吗？'}  ]
response = get_completion_from_messages(messages, temperature=1)
print(response)
```

> ```python
> 抱歉，我不知道您的名字，因为我们是虚拟的聊天机器人和现实生活中的人类在不同的世界中。
> ```

如上所见，模型实际上并不知道我的名字。

因此，**每次与语言模型的交互都是一个独立的交互，这意味着我们必须提供所有相关的消息，以便模型在当前对话中进行引用**。**如果想让模型引用或 “记住” 对话的早期部分，则必须在模型的输入中提供早期的交流。我们将其称为上下文。**让我们试试。

```python
# 中文
messages =  [  
{'role':'system', 'content':'你是个友好的聊天机器人。'},
{'role':'user', 'content':'Hi, 我是Isa'},
{'role':'assistant', 'content': "Hi Isa! 很高兴认识你。今天有什么可以帮到你的吗?"},
{'role':'user', 'content':'是的，你可以提醒我, 我的名字是什么?'}  ]
response = get_completion_from_messages(messages, temperature=1)
print(response)
```

> ```python
> 当然可以！您的名字是Isa。
> ```

现在已经给模型提供了上下文，也就是之前的对话中提到的我的名字，然后我们会问同样的问题，也就是我的名字是什么。因为模型有了需要的全部上下文，所以它能够做出回应，就像我们在输入的消息列表中看到的一样。

### 订餐机器人

现在，构建一个 “订餐机器人”，需要它自动收集用户信息，接受比萨饼店的订单。

下面这个函数将收集用户消息，以便可以避免手动输入，就像上面做的那样。这个函数将从下面构建的用户界面中收集提示，然后将其附加到一个名为上下文的列表中，并在每次调用模型时使用该上下文。模型的响应也会被添加到上下文中，所以模型消息和用户消息都被添加到上下文中，因此上下文逐渐变长。这样，模型就有了需要的信息来确定下一步要做什么。

```python
def collect_messages(_):
    prompt = inp.value_input
    inp.value = ''
    context.append({'role':'user', 'content':f"{prompt}"})
    response = get_completion_from_messages(context) 
    context.append({'role':'assistant', 'content':f"{response}"})
    panels.append(
        pn.Row('User:', pn.pane.Markdown(prompt, width=600)))
    panels.append(
        pn.Row('Assistant:', pn.pane.Markdown(response, width=600, style={'background-color': '#F6F6F6'})))
 
    return pn.Column(*panels)
```

将设置并运行这个 UI 来显示订单机器人。初始的上下文包含了包含菜单的系统消息。请注意，上下文会随着时间的推移而不断增长。

```python
# 中文
import panel as pn  # GUI
pn.extension()

panels = [] # collect display 

context = [{'role':'system', 'content':"""
你是订餐机器人，为披萨餐厅自动收集订单信息。
你要首先问候顾客。然后等待用户回复收集订单信息。收集完信息需确认顾客是否还需要添加其他内容。
最后需要询问是否自取或外送，如果是外送，你要询问地址。
最后告诉顾客订单总金额，并送上祝福。

请确保明确所有选项、附加项和尺寸，以便从菜单中识别出该项唯一的内容。
你的回应应该以简短、非常随意和友好的风格呈现。

菜单包括：

菜品：
意式辣香肠披萨（大、中、小） 12.95、10.00、7.00
芝士披萨（大、中、小） 10.95、9.25、6.50
茄子披萨（大、中、小） 11.95、9.75、6.75
薯条（大、小） 4.50、3.50
希腊沙拉 7.25

配料：
奶酪 2.00
蘑菇 1.50
香肠 3.00
加拿大熏肉 3.50
AI酱 1.50
辣椒 1.00

饮料：
可乐（大、中、小） 3.00、2.00、1.00
雪碧（大、中、小） 3.00、2.00、1.00
瓶装水 5.00
"""} ]  # accumulate messages


inp = pn.widgets.TextInput(value="Hi", placeholder='Enter text here…')
button_conversation = pn.widgets.Button(name="Chat!")

interactive_conversation = pn.bind(collect_messages, button_conversation)

dashboard = pn.Column(
    inp,
    pn.Row(button_conversation),
    pn.panel(interactive_conversation, loading_indicator=True, height=300),
)

dashboard
```

现在我们可以要求模型创建一个 JSON 摘要发送给订单系统。

所以我们现在追加另一个系统消息，**它是另一条prompt，我们说创建一个刚刚订单的 JSON 摘要，列出每个项目的价格，字段应包括1）披萨，包括尺寸，2）配料列表，3）饮料列表，4）辅菜列表，包括尺寸，最后是总价格。这里也可以在这里使用用户消息，不一定是系统消息。**

请注意，这里我们**使用了一个较低的temperature，因为对于这些类型的任务，我们希望输出相对可预测。**

```python
messages =  context.copy()
messages.append(
{'role':'system', 'content':'创建上一个食品订单的 json 摘要。\
逐项列出每件商品的价格，字段应该是 1) 披萨，包括大小 2) 配料列表 3) 饮料列表，包括大小 4) 配菜列表包括大小 5) 总价'},    
)

response = get_completion_from_messages(messages, temperature=0)
print(response)
```

> ```python
> {
> "pizza": {
>  "size": "large",
>  "type": "pepperoni",
>  "price": 12.95
> },
> "toppings": [
>  {"name": "extra cheese", "price": 2.00},
>  {"name": "mushrooms", "price": 1.50}
> ],
> "drinks": [
>  {"name": "coke", "size": "large", "price": 3.00},
>  {"name": "sprite", "size": "large", "price": 3.00}
> ],
> "sides": [
>  {"name": "fries", "size": "large", "price": 4.50}
> ],
> "total_price": 26.95
> }
> ```

现在，我们已经建立了自己的订餐聊天机器人。请随意自定义并修改系统消息，以更改聊天机器人的行为，并使其扮演不同的角色和拥有不同的知识。

