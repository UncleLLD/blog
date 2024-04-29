---
layout: '../../layouts/MarkdownPost.astro'
title: 'hyperopt基础进阶'
pubDate: 2024-04-29
description: 'hyperopt进阶教程'
author: 'UncleLLD'
cover:
    url: 'https://img2.imgtp.com/2024/04/29/O0MzTCRd.png'
    alt: 'cover'
tags: ["机器学习"]
theme: 'dark'
featured: true

---



# Hyperopt进阶教程

## 定义要最小化的函数

在指定要最小化的目标函数时，Hyperopt提供了几个灵活性/复杂性逐渐增加的级别。作为设计者需要考虑的问题是：

* 是否想要保存除函数返回值之外的其他信息，比如在计算目标函数时收集的其他统计和诊断信息？
* 是否想要使用需要更多信息而不仅仅是函数值的优化算法？
* 是否想要在并行进程之间进行通信？（例如，与其他工作进程或最小化算法）

接下来的几个部分将介绍实现一个目标函数的各种方法，该目标函数在单个变量上最小化二次目标函数。在每个部分中，我们将在从-10到+10的有界范围内进行搜索，我们可以用以下搜索空间来描述：

```python
space = hp.uniform('x', -10, 10)
```

### 最简单的情况

在Hyperopt的优化算法和目标函数之间进行通信的最简单协议是，目标函数接收来自搜索空间的有效点，并返回与该点相关联的浮点损失（也称为负效用）。

```python
from hyperopt import fmin, tpe, hp

best = fmin(fn=lambda x: x ** 2,
            space=hp.uniform('x', -10, 10),
            algo=tpe.suggest,
            max_evals=100)

print(best)
```

这种协议的优点是非常易读且容易实现。正如所看到的，它几乎是一行代码。这种协议的缺点是

* （1）这种类型的函数无法将有关每次评估的额外信息返回到Trials数据库
* （2）这种类型的函数无法与搜索算法或其他并发函数评估进行交互

### 通过Trials对象附加额外信息

如果目标函数很复杂且运行时间很长，几乎肯定会想保存比最后输出的一个浮点损失更多的统计和诊断信息。对于这种情况，`fmin`函数被编写为处理带有字典返回值的情况。其思想是，损失函数可以返回一个嵌套的字典，其中包含想要的所有统计和诊断信息。不过实际情况比这要灵活一些：例如，使用mongodb时，字典必须是有效的JSON文档。尽管如此，在存储领域特定的辅助结果时仍然有很大的灵活性。

当目标函数返回一个字典时，`fmin`函数会在返回值中查找一些特殊的键值对，它会将它们传递给优化算法。有两个强制性的键值对：

- `status `- 来自`hyperopt.STATUS_STRINGS`的键之一，例如'ok'表示成功完成，'fail'表示函数在某些情况下未定义。
- `loss` - 要最小化的浮点函数值，如果状态是'ok'，则必须存在。

`fmin`函数还响应一些可选键：

- `attachments` - 一个键值对的字典，其键是短字符串（如文件名），值是潜在的长字符串（如文件内容），不应在每次访问记录时从数据库加载。 （此外，MongoDB限制了正常键值对的长度，因此一旦值超过几兆字节，可能就必须将其作为附件。）
- `loss_variance` - 浮点数 - 随机目标函数的不确定性
- `true_loss` - 浮点数 - 在进行超参数优化时，如果使用此名称存储模型的泛化误差，有时可以从内置的绘图例程中获得更漂亮的输出。
- `true_loss_variance` - 浮点数 - 泛化误差的不确定性

由于字典意味着可以与各种后端存储机制一起使用，因此应确保它与JSON兼容。只要它是字典、列表、元组、数字、字符串和日期时间的树状结构图，就可以了。

>  提示：
>
> * 要存储numpy数组，将它们序列化为字符串，并考虑将它们存储为附件
> * 如果需要复制随机搜索的结果（例如进行演示），请使用`rstate`可选参数将`np.random.Generator`类型的对象传递给`fmin`函数。

将上面的函数以返回字典的方式编写，如下所示：

```python
from hyperopt import fmin, tpe, hp, STATUS_OK


def objective(x):
    return {'loss': x ** 2, 'status': STATUS_OK }

best = fmin(objective,
            space=hp.uniform('x', -10, 10),
            algo=tpe.suggest,
            max_evals=100)

print(best)
```

### Trials对象

为了真正看到返回字典的目的，修改目标函数以返回更多的信息，并将显式的trials参数传递给`fmin`。

```python
import pickle
import time
from hyperopt import fmin, tpe, hp, STATUS_OK, Trials


def objective(x):
    return {
        'loss': x ** 2,
        'status': STATUS_OK,
        # -- store other results like this
        'eval_time': time.time(),
        'other_stuff': {'type': None, 'value': [0, 1, 2]},
        # -- attachments are handled differently
        'attachments':
            {'time_module': pickle.dumps(time.time)}
        }

trials = Trials()
best = fmin(objective,
            space=hp.uniform('x', -10, 10),
            algo=tpe.suggest,
            max_evals=100,
            trials=trials)

print(best)
```

在这种情况下，对`fmin`的调用与之前一样进行，但通过直接传递trials对象，可以检查在实验期间计算的所有返回值。

因此，例如：

- `trials.trials` - 一个表示搜索所有内容的字典列表
- `trials.results` - 在搜索期间由'objective'返回的字典列表
- `trials.losses()` - 损失的列表（对于每个'ok'试验都是浮点数）
- `trials.statuses()` - 状态字符串的列表

这个trials对象可以保存下来，传递给内置的绘图例程，或者用自己的自定义代码进行分析。以下是保存和随后加载trials对象的一种简单示例。

```python
import pickle
from hyperopt import fmin, tpe, hp, Trials, STATUS_OK


def objective(x):
    return {'loss': x ** 2, 'status': STATUS_OK }

# Initialize an empty trials database
trials = Trials()

# Perform 100 evaluations on the search space
best = fmin(objective,
            space=hp.uniform('x', -10, 10),
            algo=tpe.suggest,
            trials=trials,
            max_evals=100)

# The trials database now contains 100 entries, it can be saved/reloaded with pickle or another method
pickle.dump(trials, open("my_trials.pkl", "wb"))
trials = pickle.load(open("my_trials.pkl", "rb"))

# Perform an additional 100 evaluations
# Note that max_evals is set to 200 because 100 entries already exist in the database
best = fmin(objective,
    space=hp.uniform('x', -10, 10),
    algo=tpe.suggest,
    trials=trials,
    max_evals=200)

print(best)
```

附件由一种特殊机制处理，这使得可以在Trials和MongoTrials两者上使用相同的代码。

```python
msg = trials.trial_attachments(trials.trials[5])['time_module']

time_module = pickle.loads(msg)
```

语法有点复杂，因为附件是大字符串，因此在使用`MongoTrials`时，不希望下载超过必要的内容。字符串还可以通过`trials.attachments`附加到整个trials对象，它的行为类似于一个字符串到字符串的字典。

注意：目前，Trials对象的特定于试验的附件被投放到同一个全局试验附件字典中，但这可能会在将来发生变化，并且在MongoTrials上不成立。

### Ctrl对象用于与MongoDB实时通信
`fmin()` 可以为目标函数提供一个句柄，用于访问并行实验使用的`mongodb`。这种机制使得可以更新数据库，记录部分结果，并与正在评估不同点的其他并发进程进行通信。目标函数甚至可以添加新的搜索点，就像 `rand.suggest` 一样。

基本技术包括：

- 使用 `fmin_pass_expr_memo_ctrl` 装饰器
- 在自己的函数中调用 `pyll.rec_eval` 从 `expr` 和 `memo` 构建搜索空间点。
- 使用 `ctrl`，`hyperopt.Ctrl` 的一个实例，与实时的 `trials` 对象进行通信。

 

## 定义搜索空间
搜索空间由嵌套的函数表达式组成，其中包括随机表达式。随机表达式是超参数。从这个嵌套的随机程序中采样定义了随机搜索算法。超参数优化算法通过使用自适应探索策略替换正常的“采样”逻辑来工作，这些策略不试图实际从搜索空间中指定的分布中采样。

最好将搜索空间视为随机参数采样程序。例如：

```python
from hyperopt import hp

space = hp.choice('a',
    [
        ('case 1', 1 + hp.lognormal('c1', 0, 1)),
        ('case 2', hp.uniform('c2', -10, 10))
    ])
```

运行此代码片段的结果是一个变量 `space`，它引用了一个包含表达式标识符和它们的参数的图。实际上并没有进行采样，它只是一个描述如何采样一个点的图。处理这种类型的表达式图的代码位于 `hyperopt.pyll` 中，将称这些图为 `pyll` 图或 `pyll` 程序。也可以通过对其进行采样来评估样本空间：

```python
import hyperopt.pyll.stochastic

print(hyperopt.pyll.stochastic.sample(space))
```

这个由 `space` 描述的搜索空间有3个参数：

- 'a' - 选择不同的情况
- 'c1' - 用于 'case 1' 的正值参数
- 'c2' - 用于 'case 2' 的有界实值参数

需要注意的一件事是，每个可优化的随机表达式都有一个标签作为第一个参数。这些标签用于将参数选择返回给调用方，并在内部的各种方式中使用。

第二个要注意的是，在图的中间部分（'case 1' 和 'case 2' 附近）使用了元组。列表、字典和元组都被升级为“确定性函数表达式”，以便它们可以成为搜索空间随机程序的一部分。

第三个要注意的事情是数值表达式 `1 + hp.lognormal('c1', 0, 1)`，它嵌入到搜索空间的描述中。就优化算法而言，在搜索空间中直接添加1和在目标函数逻辑中添加1之间没有区别。作为设计者，可以选择在哪里放置这种处理，以实现想要的模块化。另外，在使用 mongodb 进行并行优化时，搜索空间中的中间表达式结果可以是任意的 Python 对象。可以很容易地在搜索空间描述中添加新类型的非随机表达式。

第四个要注意的是 'c1' 和 'c2' 是我们将称之为条件参数的示例。对于 'c1' 和 'c2' 中的每个参数，它们只在 'a' 的特定值的返回样本中起作用。如果 'a' 是 0，则使用 'c1' 但不使用 'c2'。如果 'a' 是 1，则使用 'c2' 但不使用 'c1'。每当有意义时，应该将参数编码为这种条件参数，而不是在目标函数中简单地忽略参数。如果揭示了 'c1' 有时对目标函数没有影响（因为它对目标函数的参数没有影响），则搜索在分配积分方面可以更有效。

### 参数表达式  

搜索空间由嵌套的函数表达式组成，包括随机表达式。随机表达式是超参数。从这个嵌套的随机程序中采样定义了随机搜索算法。超参数优化算法通过使用自适应探索策略替换正常的“采样”逻辑来工作，这些策略不试图实际从搜索空间中指定的分布中采样。

以下是 hyperopt 优化算法当前支持的随机表达式：

- `hp.choice(label, options)`: 从 options 中返回一个值，options 应该是一个列表或元组。options 的元素本身可以是[嵌套的]随机表达式。在这种情况下，只在某些选项中出现的随机选择会变成条件参数。
- `hp.pchoice(label, p_list)`: 从 options 中返回一个值，其中 p_list 是一个(probability, option)对的列表。options 的元素本身可以是[嵌套的]随机表达式。在这种情况下，只在某些选项中出现的随机选择会变成条件参数。
- `hp.randint(label[, low], upper)`: 返回范围在 `[low, upper)` 中的随机整数。默认的 low 值是 0。该分布的语义是在损失函数中，相邻整数值之间的相关性与更远整数值相比没有更多的相关性。这是描述随机种子的合适分布，如果损失函数对相邻整数值的相关性可能更大，则应该使用其中一个“量化”的连续分布，例如 `quniform`、`qloguniform`、`qnormal `或 `qlognormal`。
- `hp.uniform(label, low, high)`: 返回一个在` low` 和 `high` 之间均匀分布的值。在优化时，此变量被限制在一个双边区间内。
- `hp.quniform(label, low, high, q)`: 返回类似于 `round(uniform(low, high) / q) * q` 的值。适用于离散值，对于该值，目标函数仍然相对“平滑”，但上下都应该有界。
- `hp.quniformint(label, low, high)` 或 `hp.uniformint(label, low, high, q)`: 返回类似于 `round(uniform(low, high) / q) * q` 的值。参数 q 将始终设置为 1.0。适用于离散值，对于该值，目标函数仍然相对“平滑”，但上下都应该有界。
- `hp.loguniform(label, low, high)`: 返回根据 `exp(uniform(low, high))` 绘制的值，以使返回值的对数均匀分布。在优化时，此变量被限制在区间 `[exp(low), exp(high)]`。
- `hp.qloguniform(label, low, high, q)`: 返回类似于 `round(exp(uniform(low, high)) / q) * q` 的值。适用于与该值相关的离散变量，目标函数相对“平滑”，且随着值的大小而变得更平滑，但上下都应该有界。
- `hp.normal(label, mu, sigma)`: 返回一个实数，其均值为 mu，标准差为 sigma 的正态分布。在优化时，这是一个无约束的变量。
- `hp.qnormal(label, mu, sigma, q)`: 返回类似于 `round(normal(mu, sigma) / q) * q` 的值。适用于在目标函数中可能在 mu 附近取值的离散变量，但基本上是无界的。
- `hp.lognormal(label, mu, sigma)`: 返回根据 `exp(normal(mu, sigma))` 绘制的值，以使返回值的对数正态分布。在优化时，此变量被限制为正数。
- `hp.qlognormal(label, mu, sigma, q):`返回一个类似于 `round(exp(normal(mu, sigma)) / q) * q` 的值。适用于与目标函数相对平滑且随着变量大小变得更平滑的离散变量，该变量从一侧有界。

### 一个搜索空间示例：scikit-learn
为了看到所有这些可能性的实际应用，看看如何描述 scikit-learn 中分类算法的超参数空间。 

```python
from hyperopt import hp

space = hp.choice('classifier_type', [
    {
        'type': 'naive_bayes',
    },
    {
        'type': 'svm',
        'C': hp.lognormal('svm_C', 0, 1),
        'kernel': hp.choice('svm_kernel', [
            {'ktype': 'linear'},
            {'ktype': 'RBF', 'width': hp.lognormal('svm_rbf_width', 0, 1)},
            ]),
    },
    {
        'type': 'dtree',
        'criterion': hp.choice('dtree_criterion', ['gini', 'entropy']),
        'max_depth': hp.choice('dtree_max_depth',
                     [None, hp.qlognormal('dtree_max_depth_int', 3, 1, 1)]),
        'min_samples_split': hp.qlognormal('dtree_min_samples_split', 2, 1, 1),
    },
    ])
```

### 使用 pyll 添加非随机表达式
可以将这些节点用作 pyll 函数的参数（请参阅 pyll）。简而言之，只需装饰一个顶层（即可通过 pickle 序列化的）函数，以便它可以通过 scope 对象使用。

```python
import hyperopt.pyll
from hyperopt.pyll import scope


@scope.define
def foo(a, b=0):
     print('runing foo', a, b)
     return a + b / 2

# -- this will print 0, foo is called as usual.
print(foo(0))

# In describing search spaces you can use `foo` as you
# would in normal Python. These two calls will not actually call foo,
# they just record that foo should be called to evaluate the graph.

space1 = scope.foo(hp.uniform('a', 0, 10))
space2 = scope.foo(hp.uniform('a', 0, 10), hp.normal('b', 0, 1))

# -- this will print an pyll.Apply node
print(space1)

# -- this will draw a sample by running foo()
print(hyperopt.pyll.stochastic.sample(space1))
```



## 参考

* [https://github.com/hyperopt/hyperopt-sklearn](https://github.com/hyperopt/hyperopt-sklearn)

