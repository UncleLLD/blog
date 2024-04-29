---
layout: '../../layouts/MarkdownPost.astro'
title: 'hyperopt基础教程'
pubDate: 2024-04-29
description: 'hyperopt基础教程'
author: 'UncleLLD'
cover:
    url: 'https://img2.imgtp.com/2024/04/29/O0MzTCRd.png'
    alt: 'cover'
tags: ["机器学习"]
theme: 'dark'
featured: true

---


# hyperopt基础教程

Hyperopt的任务是在一组可能的参数上找到标量值的最佳值，该标量值可能是随机的。

与许多优化包假定这些输入来自向量空间不同，Hyperopt是不同的，因为它鼓励使用者更详细地描述搜索空间。通过提供关于函数定义在哪里以及认为最佳值在哪里的更多信息，可以使Hyperopt中的算法更有效地搜索。

使用Hyperopt的方式是描述：

* 要最小化的目标函数
* 要搜索的空间
* 用于存储搜索的所有点评估的数据库
* 要使用的搜索算法

----

## 基础教程

在这个教程中，可以学习如何：

1. 定义搜索空间
2. 优化目标函数

本教程描述了如何使用HyperOpt优化超参数，而无需对HyperOpt中实现的任何算法具有数学理解。

```python
# Import HyperOpt Library
from hyperopt import fmin, tpe, space_eval
```

声明一个要进行优化的目标函数。在本教程中，我们将优化一个名为"objective"的简单函数，这是一个简单的二次函数。

$$ y = (x-3)^2 + 2 $$

```python
objective = lambda x: (x-3)**2 + 2
```

现在，让我们可视化这个目标函数。

```python
import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(-10, 10, 100)
y = objective(x)

fig = plt.figure()
plt.plot(x, y)
plt.show()
```

![|inline](https://img2.imgtp.com/2024/04/29/HfeJxYEb.png)

我们试图通过改变超参数 $x$ 来优化这个目标函数。因此，将为 $x$ 声明一个搜索空间。与搜索空间相关的函数是在 `hyperopt.hp` 中实现的，列表如下：

- `hp.randint(label, upper)` 或 `hp.randint(label, low, high)`
- `hp.uniform(label, low, high)`
- `hp.loguniform(label, low, high)`
- `hp.normal(label, mu, sigma)`
- `hp.lognormal(label, mu, sigma)`
- `hp.quniform(label, low, high, q)`
- `hp.qloguniform(label, low, high, q)`
- `hp.qnormal(label, mu, sigma, q)`
- `hp.qlognormal(label, mu, sigma, q)`
- `hp.choice(label, list)`
- `hp.pchoice(label, p_list)`，其中 p_list 是概率和选项对的列表
- `hp.uniformint(label, low, high, q)` 或 `hp.uniformint(label, low, high)`，因为当 q = 1.0 时，等同于 `uniformint(label, low, high)`。

在本教程中，将使用最基本的 `hp.uniform`。

```python
# Define the search space of x between -10 and 10.
space = hp.uniform('x', -10, 10)
```

现在只剩下最后一步了。到目前为止，已经定义了一个目标函数，也为 $x$ 定义了一个搜索空间。现在，可以在搜索空间 $x$ 中搜索，并找到能够优化目标函数的 $x$ 的值。HyperOpt 使用 `fmin` 来执行这个过程。

```python
best = fmin(
    fn=objective,  # Objective Function to optimize
    space=space,  # Hyperparameter's Search Space
    algo=tpe.suggest,  # Optimization algorithm
    max_evals=1000  # Number of optimization attempts
)
print(best)
print(space_eval(space, best))
```

```
100%|██████████| 1000/1000 [00:04<00:00, 228.56trial/s, best loss: 2.000001036046408]
{'x': 3.0010178636491283}
3.0010178636491283
```

HyperOpt找到的最优 $x$ 值约为3.0。这非常接近min $y=(x-3)^2+2$ 的一个解。

总的代码如下

```python
# Import HyperOpt Library
from hyperopt import fmin, tpe, space_eval

objective = lambda x: (x-3)**2 + 2

import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(-10, 10, 100)
y = objective(x)

fig = plt.figure()
plt.plot(x, y)
plt.show()

# Define the search space of x between -10 and 10.
space = hp.uniform('x', -10, 10)

best = fmin(
    fn=objective,  # Objective Function to optimize
    space=space,  # Hyperparameter's Search Space
    algo=tpe.suggest,  # Optimization algorithm
    max_evals=1000  # Number of optimization attempts
)
print(best)
print(space_eval(space, best))
```

----

## 多参数教程

在这个教程中，将学习如何：

1. 使用多个超参数优化目标函数
2. 定义不同类型的搜索空间

```python
# Import HyperOpt Library
import numpy as np
from hyperopt import tpe, hp, fmin, space_eval
```

声明一个要进行优化的目标函数。与上次不同，这次将使用两个超参数 $x$ 和 $y$ 来优化函数。

$$ z = sin\sqrt{x^2 + y^2} $$

```python
def objective(params):
    x, y = params['x'], params['y']
    return np.sin(np.sqrt(x**2 + y**2))
```

现在尝试将其可视化。但与上次不同的是，这次有两个超参数，因此需要在三维空间中将它们可视化。

```python
import matplotlib.pyplot as plt
from matplotlib import cm
from mpl_toolkits.mplot3d import Axes3D

x = np.linspace(-6, 6, 30)
y = np.linspace(-6, 6, 30)
x, y = np.meshgrid(x, y)

z = objective({'x': x, 'y': y})

fig = plt.figure()
ax = plt.axes(projection='3d')
ax.plot_surface(x, y, z, cmap=cm.coolwarm)

ax.set_xlabel('x')
ax.set_ylabel('y')
ax.set_zlabel('z')

plt.show()
```

![|inline](https://img2.imgtp.com/2024/04/29/O0MzTCRd.png)

同样，定义搜索空间。但这次，需要定义两个搜索空间（$x, y$），所以您将它们分别放在 `dict()` 中。

```python
space = {
    'x': hp.uniform('x', -6, 6),
    'y': hp.uniform('y', -6, 6)
}
```

```python
best = fmin(
    fn=objective, # Objective Function to optimize
    space=space, # Hyperparameter's Search Space
    algo=tpe.suggest, # Optimization algorithm (representative TPE)
    max_evals=1000 # Number of optimization attempts
)
print(best)
print(objective(best))
```

```
100%|██████████| 1000/1000 [00:07<00:00, 140.14trial/s, best loss: -0.9999998138498082]
{'x': 4.660020883316342, 'y': -0.7030201784219683}
-0.99999993408562725
```

----

## 定义不同类型的搜索空间

* `hp.randint(label, upper)` 在区间 [0, upper) 中搜索整数。 
*  `hp.choice(label, list)` 在列表中搜索元素。

```python
def f(params):
    x1, x2 = params['x1'], params['x2']
    if x1 == 'james':
        return -1 * x2
    if x1 == 'max':
        return 2 * x2
    if x1 == 'wansoo':
        return -3 * x2

search_space = {
    'x1': hp.choice('x1', ['james', 'max', 'wansoo']),
    'x2': hp.randint('x2', -5, 5)
}

best = fmin(
    fn=f,
    space=search_space,
    algo=tpe.suggest,
    max_evals=100
)

print(best)
print(f(best))
```

```
100%|██████████| 100/100 [00:00<00:00, 396.61trial/s, best loss: -12.0]
{'x1': 2, 'x2': 4}
```

## 参考

* [https://github.com/hyperopt/hyperopt](https://github.com/hyperopt/hyperopt)
* [https://hyperopt.github.io/hyperopt/](https://hyperopt.github.io/hyperopt/)
* [https://github.com/hyperopt/hyperopt-sklearn](https://github.com/hyperopt/hyperopt-sklearn)
* [做模型搜索的科学:视觉架构的数百维超参数优化](https://dl.acm.org/doi/10.5555/3042817.3042832)。
* [Hyperopt-Sklearn:sci kit-Learn 的自动超参数配置](https://conference.scipy.org/proceedings/scipy2014/pdfs/komer.pdf)