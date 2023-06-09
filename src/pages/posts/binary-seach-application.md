---
layout: '../../layouts/MarkdownPost.astro'
title: 'Binary Search Application'
pubDate: 2023-06-09
description: '二分法是算法中一种加速算法，实际场景中有很多应该该算法思想的，这里介绍一些二分法的相关应用：解方程近似解，离散化操作等'
author: 'UncleLLD'
cover:
    url: 'https://staticcdn.boyuai.com/user-assets/5085/TqwNCYg3WWexMnHTJLPG6M/a1.png!png'
    alt: 'cover'
tags: ["二分法", "数据结构与算法", "应用"]
theme: 'dark'
featured: true
---



### 二分法的应用

统计x出现的次数

首先，我们来看一个问题：

> 给出一个正整数n，和一个长度为n的整数数组a，再给出一个正整数q，接下来给出q个询问，每个询问包含一个整数x，你需要输出x在数组a中出现了几次。

右侧给出了该问题的解决方案，请运行或提交代码，并在代码区下方的输入区依次输入3个你想要查询的数。

> 比如，1 2 3
>
> 注意中间要空格，或换行输入。观察一下结果～

```cpp
#include <bits/stdc++.h>
using namespace std;

int n = 10;
int a[10] = {1, 2, 2, 3, 3, 3, 4, 4, 4, 4};

int main() {
    sort(a, a+n);   // 对a数组进行排序
    int q = 3;      // 询问3次
    while( q-- ) {  // 回答q次询问
        int x;
        cin >> x;   // 请在代码区下方的输入区依次输入3个你想要查询的数。
                    // 比如，1，2，3，注意中间要空格，或换行输入
        int *lp = lower_bound(a, a + n, x); // 第一个大于等于x的数字的指针
        int *rp = upper_bound(a, a + n, x); // 第一个大于x的数字的指针
        cout << int(rp-lp) << endl;       // 指针相减得到区间长度
    }
    return 0;
}
```

### lower_bound和upper_bound

我们先回顾一下上一步要解决的问题：

> 给出一个正整数n，和一个长度为n的整数数组a，再给出一个正整数q，接下来给出q个询问，每个询问包含一个整数x，你需要输出x在数组a中出现了几次。

在上一步给出的代码示例中用到了C++标准库中两个使用二分查找的函数：`lower_bound`和`upper_bound`。

> `lower_bound`的用途是：在指定的升序排序的数组中，找到第一个大于等于x的数字。
>
> `upper_bound`的用途是：在指定的升序排序的数组中，找到第一个大于x的数字。

这两个函数会返回对应数字的指针（或者是[迭代器](http://c.biancheng.net/view/413.html)）。

```cpp
int a[100000], n;
cin >> n;
for( int i = 0; i < n; ++i )
    cin >> a[i];
sort(a, a + n);
int *p = lower_bound(a, a + n, 13);  // 第一个大于等于13的数字
int *q = upper_bound(a, a + n, 13);  // 第一个大于13的数字
```

这两个函数的具体用法和细节，可以参考[cppreference](https://en.cppreference.com/w/cpp/algorithm/lower_bound)，我们这里介绍的，是这两个函数有什么用。

假如使用`lower_bound`和`upper_bound`二分查找同一个数字13，容易发现，得到的两个指针构成了一个左闭右开区间，这个区间里全部都是数字13。

![img](https://staticcdn.boyuai.com/user-assets/5085/TqwNCYg3WWexMnHTJLPG6M/a1.png!png)

巧妙地运用这两个函数，可以完成所有常见的二分查找操作：

- 找到第一个大于等于x的数字
- 找到第一个大于x的数字
- 找到最后一个等于x的数字
- 查找数组中是否有数字x
- 查询数组中有几个数字x
- 找到最后一个小于x的数字
- ……

回到我们的问题：

> 给出一个正整数n，和一个长度为n的整数数组a，再给出一个正整数q，接下来给出q个询问，每个询问包含一个整数x，你需要输出x在数组a中出现了几次。

由于`lower_bound`和`upper_bound`构成了一个包含指定数字的左闭右开区间，因此直接将这两个指针相减，即可得到指定数字的出现次数。

 

#### 求方程的解

**问题**

> 请输出方程$x^3 + 16 = 0$的解，已知这个解在$[-1e9,1e9]$之间，并且函数$f(x) = x^3 + 16$在定义域上单调递增。输出的答案保留5位小数。

我们现在想求出某个方程`f(x) = 0`的解，并且我们知道这个解在`[L,R]`之间，且函数`f(x)`在`[L,R]`上单调递增。我们只需要这个解精确到5位小数即可。

函数示意图：

![img](https://staticcdn.boyuai.com/user-assets/5085/JSeUPB57vJoEAL6Y6oXcKf/1.png!png)

这就是一个典型的二分法求方程的解了，是高中数学课本上所讲解的知识。

右侧代码部分语句被挖空了，请根据二分查找的伪代码补全右侧的二分查找过程。

- 二分查找伪代码

```cpp
while( L < R ) {
    int M = L+(R-L)/2;
    if( 答案在[M+1,R]中 ) { // 思考一下，什么情况下能够说明“答案在[M+1,R]中”
        L = M+1;
    } else { // 答案在[L,M]中
        R = M;
    }
}
```

```CPP
#include <bits/stdc++.h>
using namespace std;

double f( double x ) {
    return x * x * x + 16; // 某个函数f(x)
}

    
double solve() {
    double L = -1e9, R = 1e9; // 方程解在[L,R]之间，且函数在[L,R]上单调增
    while( R-L >= 1e-6 ) { // 精确到6位小数，然后四舍五入
        double M = (R+L)/2;
        // cout << M <<endl;
        if(f(M) < 0)
        {
            L = M;
        }
        else
            R = M;
    }
    return L;
}  
```

在double上二分的注意事项

如果我们要求精确到10位小数，你可能会写出这样的代码：

```cpp
double L = -1e9, R = 1e9;
while( R - L >= 1e-11 ) { // 精确到11位小数，然后四舍五入
    // 此处省略二分内容
}
```

你可能会发现，最终的结果并没有精确到10位小数，或者是这个二分直接陷入了**死循环**。

**在精度要求越高的时候，就越可能出现这样匪夷所思的情况。**

这是因为double本身存在不小的精度误差，我们通过`R - L >= 1e-10`这种方式来控制二分的终止条件，**会带来非常大的精度问题。**

这种时候，可以采用**固定次数二分**的方法：

```cpp
double L = -1e9, R = 1e9;
for( int times = 0; times < 100; ++times ) { // 二分100次
    double mid = (L+R)/2;
    // 此处省略二分内容
}
```

这里我们二分100次，是因为2的100次方约为`1e30`，而我们二分的初始条件是`1e9`左右，足以在最后把精度控制在`1e-20`左右。

#### 总结

我们总结一些二分查找的常见应用：

- `lower_bound`和`upper_bound`

  > `lower_bound`的用途是：在指定的升序排序的数组中，找到第一个大于等于x的数字。
  >
  > `upper_bound`的用途是：在指定的升序排序的数组中，找到第一个大于x的数字。

  使用`lower_bound`和`upper_bound`可以帮我们解决绝大多数二分查找问题。

  这两个函数会返回对应数字的指针。示例代码如下：

```cpp
int a[100000], n;
cin >> n;
for( int i = 0; i < n; ++i )
    cin >> a[i];
sort(a, a + n);
int *p = lower_bound(a, a + n, 13); // 第一个大于等于13的数字
int *q = upper_bound(a, a + n, 13); // 第一个大于13的数字
```

> 假如我们使用`lower_bound`和`upper_bound`二分查找同一个数字13，容易发现，我们得到的两个指针构成了一个左闭右开区间，这个区间里全部都是数字13。

> 巧妙地运用这两个函数，可以完成所有常见的二分查找操作：

> - 找到第一个大于等于x的数字
> - 找到第一个大于x的数字
> - 找到最后一个等于x的数字
> - 查找数组中是否有数字x
> - 查询数组中有几个数字x
> - 找到最后一个小于x的数字

- 二分法可以求方程的近似解。
- 二分法可以用来优美地实现离散化操作。
- 在double上二分时，尽量使用固定次数二分的方法。