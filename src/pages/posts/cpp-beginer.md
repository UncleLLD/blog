---
layout: '../../layouts/MarkdownPost.astro'
title: 'C++初学者指南'
pubDate: 2023-08-19
description: '曹德旺，籍贯福建福清，中华人民共和国企业家，福耀集团董事局主席、世界华人联合总会荣誉主席之一，首个安永全球企业家大奖华人获得者。曾任福建省政协委员、全国政协委员、国务院国情研究员、福建省政府特约经济研究员、中国汽车玻璃协会会长、福建省企业家协会常务副会长。'
author: 'UncleLLD'
cover:
    url: 'https://i.imgs.ovh/i/2023/08/20/64e1739ae3df5.png'
    alt: 'cover'
tags: ["编程语言"]
theme: 'dark'
featured: true
---


## 环境

*   编程工具：VSCode
*   插件：
    *   Bazel
    *   C/C++
    *   Clang-Format
    *   Vim

![1.png](https://i.imgs.ovh/i/2023/08/20/64e1739a50591.png)

## 职业选择

*   AI领域：
    *   算法框架（Tensorflow， Pytorch）
    *   图像（OpenCV）等
*   基础架构领域：
    *   网络，数据库，运维等；
    *   grpc/brpc，postgrepsql等；
*   音视频领域：
    *   ffmpeg
    *   v4l
    *   OpenCV
*   图形游戏领域：
    *   opengl
    *   unity

## 基本技能

*   C++基础知识
*   专业领域知识
*   算法
*   计算机基础知识

## 新特性与学习曲线

*   2003年，标准委员会针对98版本中的诸多问题进行了修订后发布了C++03
*   2005年，标准委员会发布了一份技术报告，详细说明了计划引入的新特性，非正式的命令C++0x
*   2011年，C++0x正式发布，成为了现代C++的基础


![2.png](https://i.imgs.ovh/i/2023/08/20/64e1739ae3df5.png)

针对每个人的情况学习路线也不一样：

*   有任何编程基础，直接写Demo
*   无任何编程基础，老老实实看书
    *   C++ 11标准
    *  《Effective modern C++》
    *  《STL源码剖析》
    *  《C++那些事》git地址：CplusPlusThings

需要掌握的有：

*   移动语义
*   右值引用
*   智能指针
*   容器：
    *   vector
    *   map
    *   unordered\_map
    *   list
    *   set
    *   traits
    *   ...
*   模版
*   Lambda

## 高阶技能

### C++模版元编程

```cpp
template <typename F, typename R, typename... A>
static std::integral_constant<int, sizeof..(A)> argument_count_impl(R(F::*)(A...));
```


![3.png](https://i.imgs.ovh/i/2023/08/20/64e17399dee41.png)

### 线程池，异步任务

单个线程工作一般而言是可以的，但复杂的工程项目可能需要异步多线程多进程，这个时候需要线程池，异步任务操作了。
参考资料：

*   [C++ 20 coroutine](https://en.cppreference.com/w/cpp/language/coroutines)
*   Mutex
*   unique\_lock
*   xxx\_lock
*   atomic
*   cv
*   promise
*   future
*   routine
*   ...

## C++ 相关工具及资源

### C++ Tools

*   Online Debugger：<https://www.onlinegdb.com/online_c++_debugger>
*   BenchMark: <https://quick-bench.com>
*   CppInsights: <https://cppinsights.io>

### C++ Resources

*   gcc: [<https://gcc.gnu.org/projects/cxx-status.html>](https://gcc.gnu.org/projects/cxx-status.html)
*   cppreference: [<https://en.cppreference.com/w/>](https://en.cppreference.com/w/)
*   isocpp：[<https://isocpp.org/get-started>](https://isocpp.org/get-started)
*   AnthonyCalandra: [<https://github.com/AnthonyCalandra/modern-cpp-features>](https://github.com/AnthonyCalandra/modern-cpp-features)
*   awesomecpp：[<https://awesomecpp.com>](https://awesomecpp.com)

## 项目

### 大项目

*   STL: [<https://github.com/gcc-mirror/gcc>](https://github.com/gcc-mirror/gcc)
*   LLVM: [<https://github.com/llvm/llvm-project>](https://github.com/llvm/llvm-project)
*   Boost：[<https://github.com/boostorg/wiki/wiki/Getting-Strated%3A-Overview>](https://github.com/boostorg/wiki/wiki/Getting-Strated%3A-Overview)

### 小项目

*   CPTL(Thread Pool)：[<https://github.com/vit-vit/ctpl>](https://github.com/vit-vit/ctpl)
*   Leveldb：[<https://github.com/google/leveldb>](https://github.com/google/leveldb)
*   SQLiteCpp：[<https://github.com/SRombauts/SQLiteCpp>](https://github.com/SRombauts/SQLiteCpp)
*   Muduo: [<https://github.com/chenshuo/muduo>](https://github.com/chenshuo/muduo)

## 如何学

*   查阅相关资料，先收集一波，看看大概有什么东西
*   选择阅读的方向，简单->困难，还是困难->简单
*   理解其设计思想，并记录笔记实践

以`Leveldb`为例子：

*   第一步：先`compile and run`
*   第二步：先看看系统的架构长什么样子，有哪些模块
*   第三步：挑一个简单的练练手，看看有多难
*   第四步：深入核心，探究其设计
*   第五步：自己仿写，改造，重构

## 未来期望

*   如何调试
*   如何管理内存
*   如何快速使用c++ 20编写一个小demo
*   如何理解大项目的脉络


