---
layout: '../../layouts/MarkdownPost.astro'
title: 'Google三大论文之MapReduce'
pubDate: 2023-03-17
description: 'MapReduce 是一个编程模型，也是一个处理和生成超大数据集的算法模型的相关实现。用户首先创建一个 Map 函数处理一个基于 key/value pair 的数据集合，输出中间的基于 key/value pair 的数据集合；然后再创建一个Reduce 函数用来合并所有的具有相同中间 key 值的中间 value 值。'
author: 'UncleLLD'
cover:
    url: 'https://i.328888.xyz/2023/03/17/LIwFZ.md.jpeg'
    alt: 'cover'
tags: ["Google", "MapReduce", "论文总结"]
theme: 'dark'
featured: true
---




### Google三大论文之MapReduce

MapReduce 是一个编程模型，也是一个处理和生成超大数据集的算法模型的相关实现。用户首先创建一个 **Map 函数处理一个基于 key/value pair 的数据集合，输出中间的基于 key/value pair 的数据集合**；然后再创建一个 **Reduce 函数用来合并所有的具有相同中间 key 值的中间 value 值**。

MapReduce 架构的程序能够在大量的普通配置的计算机上实现并行化处理。这个系统在运行时**只关心：如何分割输入数据，在大量计算机组成的集群上的调度，集群中计算机的错误处理，管理集群中计算机之间必要的通信**。采用 MapReduce 架构可以使那些没有并行计算和分布式处理系统开发经验的程序员有效利用分布式系统的丰富资源。目前已在各大公司得到广泛的应用。

### 1 介绍

为了处理海量的原始数据，已经实现了数以百计的、专用的计算方法。这些计算方法用来处理大量的**原始数据**，比如文档抓取（类似网络爬虫的程序）、Web 请求日志等；也为了计算处理各种类型的**衍生数据**，比如倒排索引、Web 文档的图结构的各种表示形势、每台主机上网络爬虫抓取的页面数量的汇总、每天被请求的最多的查询的集合等。虽然数据处理运算在概念上很容易理解，但由于输入的数据量巨大，因此要想在可接受的时间内完成运算，只有将这些计算分布在成百上千的主机上。如何处理并行计算、如何分发数据、如何处理错误？所有这些问题综合在一起，需要大量的代码处理，因此也使得原本简单的运算变得难以处理。

为了解决上述复杂的问题，设计了一个新的抽象模式，使用该模式只需要表述想要执行的简单运算即可，而不必关心并行计算，容错，数据分布，负载均衡等复杂的细节。思想源于很多函数式语言的Map和Reduce。 **大多数的运算都包含这样的操作：在输入数据的“逻辑”记录上应用 Map 操作得出一个中间 key/value pair 集合，然后在所有具有相同 key 值的 value 值上应用 Reduce 操作，从而达到合并中间的数据，得到一个想要的结果的目的**。使用 MapReduce 模型，再结合用户实现的 Map 和 Reduce 函数，就可以非常容易的实现大规模并行化计算；通过 MapReduce 模型自带的“再次执行”（re-execution）功能，也提供了初级的容灾实现方案。MapReduce的主要贡献是**通过简单的接口来实现自动的并行化和大规模的分布式计算，通过使用 MapReduce 模型接口实现在大量普通的 PC 机上高性能计算。**

### 2 编程模型

MapReduce 编程模型的原理是：**利用一个输入 key/value pair 集合来产生一个输出的key/value pair 集合**。MapReduce 库的用户用两个函数表达这个计算：Map 和 Reduce。用户自定义的 Map 函数接受一个输入的 key/value pair 值，然后产生一个中间 key/value pair 值的集合。MapReduce 库把所有具有相同中间 key 值 I 的中间 value 值集合在一起后传递给 reduce 函数。用户自定义的 Reduce 函数接受一个中间 key 的值 I 和相关的一个 value 值的集合。Reduce 函数合并这些value 值，形成一个较小的 value 值的集合。一般的，每次 Reduce 函数调用只产生 0 或 1 个输出 value 值。通常通过一个**迭代器把中间 value 值提供给 Reduce 函数**，这样就**可以处理无法全部放入内存中的大量的 value 值的集合**。

#### 2.1 例子

例如计算一个大的文档集合中每个单词出现的次数，下面是伪代码

```cpp
map(String key, String value):
 // key: document name
 // value: document contents
 for each word w in value:
 	EmitIntermediate(w, “1″);

reduce(String key, Iterator values):
 // key: a word
 // values: a list of counts
 int result = 0;
 for each v in values:
 	result += ParseInt(v);
 Emit(AsString(result));
```

**Map 函数输出文档中的每个词、以及这个词的出现次数**(在这个简单的例子里就是 1)。**Reduce 函数把 Map函数产生的每一个特定的词的计数累加起来。**

用户编写代码，使用输入和输出文件的名字、可选的调节参数来完成一个符合 MapReduce 模型规范的对象，然后调用 MapReduce 函数，并把这个规范对象传递给它。

#### 2.2 类型

尽管在前面例子的伪代码中使用了以字符串表示的输入输出值，但是在概念上，用户定义的Map和Reduce函数都有相关联的类型：

```cpp
map(k1,v1) ->list(k2,v2)
reduce(k2,list(v2)) ->list(v2)
```

输入的 key 和 value 值与输出的 key 和 value 值在类型上推导的域不同。此外，中间 key 和 value值与输出 key 和 value 值在类型上推导的域相同。

#### 2.3 更多例子

* **分布式的 Grep**：Map 函数输出匹配某个模式的一行，Reduce 函数是一个恒等函数，即把中间数据复制到输出
* **计算 URL 访问频率**：Map 函数处理日志中 web 页面请求的记录，然后输出(URL,1)。Reduce 函数把相同URL 的 value 值都累加起来，产生(URL,记录总数)结果
* **倒转网络链接图**：Map 函数在源页面（source）中搜索所有的链接目标（target）并输出为(target,source)。Reduce 函数把给定链接目标（target）的链接组合成一个列表，输出(target,list(source))
* **每个主机的检索词向量**：检索词向量用一个(词, 频率)列表来概述出现在文档或文档集中的最重要的一些词。Map 函数为每一个输入文档输出(主机名, 检索词向量)，其中主机名来自文档的 URL。Reduce 函数接收给定主机的所有文档的检索词向量，并把这些检索词向量加在一起，丢弃掉低频的检索词，输出一个最终的(主机名, 检索词向量)
* **倒排索引**：Map 函数分析每个文档输出一个(词, 文档号)的列表，Reduce 函数的输入是一个给定词的所有（词，文档号），排序所有的文档号，输出(词, list（文档号）)。所有的输出集合形成一个简单的倒排索引，它以一种简单的算法跟踪词在文档中的位置
* **分布式排序**：Map 函数从每个记录提取 key，输出(key, record)。Reduce 函数不改变任何的值

### 3 实现

MapReduce 模型可以有多种不同的实现方式。如何正确选择取决于具体的环境。例如，**一种实现方式适用于小型的共享内存方式的机器，另外一种实现方式则适用于大型 NUMA 架构的多处理器的主机，而有的实现方式更适合大型的网络连接集群。**

#### 3.1 执行概括

通过将 Map 调用的输入数据自动分割为 M 个数据片段的集合，Map 调用被分布到多台机器上执行。输入的数据片段能够在不同的机器上并行处理。使用分区函数将 Map 调用产生的中间 key 值分成 R 个不同分区Reduce 调用也被分布到多台机器上执行。分区数量（R）和分区函数由用户来指定。

<a href="https://imgloc.com/i/LIbgQ"><img src="https://i.328888.xyz/2023/03/17/LIbgQ.md.jpeg" alt="LIbgQ.md.jpeg" border="0"></a>


上图展示了MapReduce 实现中操作的全部流程。当用户调用 MapReduce 函数时，将发生下面的一系列动作（下面的序号和图 1 中的序号一一对应）：

* 1 用户程序首先调用的 **MapReduce 库将输入文件分成 M 个数据片度**，每个数据片段的大小一般从16MB 到 64MB(可以通过可选的参数来控制每个数据片段的大小)。然后用户程序在机群中创建大量的程序副本
* 2 这些程序副本中的有一个**特殊的程序–master。副本中其它的程序都是 worker 程序**，由 master 分配任务。有 M 个 Map 任务和 R 个 Reduce 任务将被分配，master 将一个 Map 任务或 Reduce 任务分配给一个空闲的 worker
* 3 **被分配了 map 任务的 worker 程序读取相关的输入数据片段，从输入的数据片段中解析出** key/value pair，然后把 key/value pair 传递给用户自定义的 Map 函数，由 Map 函数生成并输出的中间 key/value pair，并缓存在内存中
* 4 **缓存中的 key/value pair 通过分区函数分成 R 个区域，之后周期性的写入到本地磁盘上。**缓存的key/value pair 在本地磁盘上的存储位置将被回传给 master，由 master 负责把这些存储位置再传送给Reduce worker
* 5 **当 Reduce worker 程序接收到 master 程序发来的数据存储位置信息后，使用 RPC 从 Map worker 所在主机的磁盘上读取这些缓存数据**。当 Reduce worker 读取了所有的中间数据后，通过**对 key 进行排序后使得具有相同 key 值的数据聚合在一起**。由于许多不同的 key 值会映射到相同的 Reduce 任务上，因此必须进行排序。如果中间数据太大无法在内存中完成排序，那么就要在外部进行排序
* 6 Reduce worker 程序遍历排序后的中间数据，对于每一个唯一的中间 key 值，Reduce worker 程序将这个 key 值和它相关的中间 value 值的集合传递给用户自定义的 Reduce 函数。**Reduce 函数的输出被追加到所属分区的输出文件**
* 当所有的 Map 和 Reduce 任务都完成之后，master 唤醒用户程序。在这个时候，在用户程序里的对MapReduce 调用才返回

在成功完成任务之后，MapReduce 的输出存放在 R 个输出文件中（对应每个 Reduce 任务产生一个输出文件，文件名由用户指定）。一般情况下，用户不需要将这 R 个输出文件合并成一个文件–他们经常把这些文件作为另外一个 MapReduce 的输入，或者在另外一个可以处理多个分割文件的分布式应用中使用。

#### 3.2 Master数据结构

Master 持有一些数据结构，它存储每一个 Map 和 Reduce 任务的状态（空闲、工作中或完成)，以及 Worker机器(非空闲任务的机器)的标识。Master 就像一个数据管道，中间文件存储区域的位置信息通过这个管道从 Map 传递到 Reduce。因此，对于每个已经完成的 Map 任务，master 存储了 Map 任务产生的 R 个中间文件存储区域的大小和位置。**当 Map任务完成时，Master 接收到位置和大小的更新信息，这些信息被逐步递增的推送给那些正在工作的 Reduce 任务。**

#### 3.3 容错

由于MapReduce 库的设计初衷是使用由成百上千的机器组成的集群来处理超大规模的数据，所以该库必须要能很好地处理机器故障。

##### 3.3.1 worker故障

**master 周期性的 ping 每个 worker。如果在一个约定的时间范围内没有收到 worker 返回的信息，master 将把这个 worker 标记为失效。**所有由这个失效的 worker 完成的 Map 任务被重设为初始的空闲状态，之后这些任务就可以被安排给其他的 worker。同样的，worker 失效时正在运行的 Map 或 Reduce 任务也将被重新置为空闲状态，等待重新调度。已经完成Reduce任务的输出存储在全局文件系统重，不需要再次执行。

当一个 Map 任务首先被 worker A 执行，之后由于 worker A 失效了又被调度到 worker B 执行，这个“重新执行”的动作会被通知给所有执行 Reduce 任务的 worker。**任何还没有从 worker A 读取数据的 Reduce 任务将从 worker B 读取数据。**

**MapReduce 可以处理大规模 worker 失效的情况**。比如，在一个 MapReduce 操作执行期间，在正在运行的集群上进行网络维护引起 80 台机器在几分钟内不可访问了，MapReduce master 只需要简单的再次执行那些不可访问的 worker 完成的工作，之后继续执行未完成的任务，直到最终完成这个 MapReduce 操作。

##### 3.3.2 master失效

**一个简单的解决办法是让 master 周期性的将上面描述的数据结构的写入磁盘，即检查点（checkpoint）**。**如果该 master 任务失效了，可以从最后一个检查点（checkpoint）开始启动另一个master 进程**。然而，由于只有一个 master 进程，master 失效后再恢复是比较麻烦的，因此**现在的实现是如果 master 失效，就中止 MapReduce 运算**。客户可以检查到这个状态，并且可以**根据需要重新执行 MapReduce操作**。

##### 3.3.3 在失效方面的处理机制

**当用户提供的 Map 和 Reduce 操作是输入确定性函数（即相同的输入产生相同的输出）时，分布式实现在任何情况下的输出都和所有程序没有出现任何错误、顺序的执行产生的输出是一样的。**

依赖对Map和Reduce任务的输出原子提交的来完成这个特性。每个work重的任务把它输出写到私有的临时文件中。每个Reduce任务生成一个这样的文件，而每个Map任务则生成R个这样的文件。当一个 Map 任务完成的时，worker 发送一个包含 R 个临时文件名的完成消息给 master。如果 master 从一个已经完成的 Map 任务再次接收到到一个完成消息，master 将忽略这个消息；否则，master 将这 R 个文件的名字记录在数据结构里。

当 Reduce 任务完成时，Reduce worker 进程以原子的方式把临时文件重命名为最终的输出文件。如果同一个 Reduce 任务在多台机器上执行，针对同一个最终的输出文件将有多个重命名操作执行， 依赖底层文件系统提供的重命名操作的原子性来保证最终的文件系统状态仅仅包含一个 Reduce 任务产生的数据。

假设Map 任务 M 和 Reduce 任务 R1、R2 的情况。我们设定 e(Ri)是 Ri 已经提交的执行过程（有且仅有一个这样的执行过程）。当 e(R1)读取了由 M 一次执行产生的输出，而 e(R2)读取了由 M 的另一次执行产生的输出，导致了较弱的失效处理。

#### 3.4 存储位置

在计算机运行环境中，网络带宽是一个相当匮乏的资源。**通过尽量把输入数据(由 GFS 管理)存储在集群中机器的本地磁盘上来节省网络带宽。**MapReduce 的 master 在调度 Map 任务时会考虑输入文件的位置信息，**尽量将一个 Map 任务调度在包含相关输入数据拷贝的机器上执行**。上述失败后，将尝试在保存有输入数据拷贝的机器附近的机器上执行Map任务。大不跟输入数据都能从本地机器读取，因此消耗非常少的网络带宽。

#### 3.5 任务粒度

如前所述，把 Map 拆分成了 M 个片段、把 Reduce 拆分成 R 个片段执行。**理想情况下，M 和 R 应当比集群中 worker 的机器数量要多得多**。在每台 worker 机器都执行大量的不同任务能够提高集群的动态的负载均衡能力，并且能够加快故障恢复的速度：**失效机器上执行的大量 Map 任务都可以分布到所有其他的 worker机器上去执行。**

**实际上，在具体实现中对 M 和 R 的取值都有一定的客观限制，因为 master 必须执行 O(M+R)次调度，并且在内存中保存 O(M*R)个状态。**更进一步，**R 值通常是由用户指定的，因为每个 Reduce 任务最终都会生成一个独立的输出文件**。**实际使用时也倾向于选择合适的 M 值，以使得每一个独立任务都是处理大约 16M 到 64M 的输入数据**（这样，上面描写的输入数据本地存储优化策略才最有效），另外，把 R 值设置为想使用的 worker 机器数量的小的倍数。

#### 3.6 备用任务

**影响一个 MapReduce 的总执行时间最通常的因素是“落伍者”**：在运算过程中，如果有一台机器花了很长的时间才完成最后几个 Map 或 Reduce 任务，导致 MapReduce 操作总的执行时间超过预期。出现这种情况的原因非常多，**比如硬盘出现问题，导致在读取的时候要经常的进行读取纠错操作，导致读取数据的速度从 30M/s 降低到 1M/s。**如果 cluster 的调度系统在这台机器上又调度了其他的任务，由于 CPU、内存、本地硬盘和网络带宽等竞争因素的存在，导致执行 MapReduce 代码的执行效率更加缓慢。**有一个通用的机制来减少“落伍者”出现的情况。当一个 MapReduce 操作接近完成的时候，master调度备用（backup）任务进程来执行剩下的、处于处理中状态（in-progress）的任务。无论是最初的执行进程、还是备用（backup）任务进程完成了任务，我们都把这个任务标记成为已经完成。**这种操作通常只会占用比正常操作多几个百分点的计算资源。我们发现采用这样的机制对于减少超大 MapReduce 操作的总处理时间效果显著。

### 4 技巧

在设计上述运算机制时候，发掘出了一些有价值的扩增功能。

#### 4.1 分区函数

MapReduce 的使用者通常会指定 Reduce 任务和 Reduce 任务输出文件的数量（R），**在中间 key 上使用分区函数来对数据进行分区，之后再输入到后续任务执行进程**。**一个缺省的分区函数是使用 hash 方法(比如，hash(key) mod R)进行分区。** hash 方法能产生非常平衡的分区。由于希望每个主机的所有条目保持在同一个输出文件中，可以通过专门的分区函数可以把所有来自同一个主机保存在同一个输出文件中，比如（hash(Hostname(urlkey)) mod R）将同一主机的URLS保存在同一个输出文件中。

#### 4.2 顺序保证

确保在给定的分区中，**中间 key/value pair 数据的处理顺序是按照 key 值增量顺序处理的**。这样的顺序保证对每个分成生成一个有序的输出文件。

#### 4.3 Combiner函数

在某些情况下，Map 函数产生的中间 key 值的重复数据会占很大的比重，并且用户自定义的 Reduce 函数满足结合律和交换律。**允许用户指定一个可选的 combiner 函数，combiner 函数首先在本地将这些记录进行一次合并，然后将合并的结果再通过网络发送出去。**

Combiner 函数在每台执行 Map 任务的机器上都会被执行一次，Combiner 函数和 Reduce 函数之间唯一的区别是 MapReduce 库怎样控制函数的输出。Reduce 函数的输出被保存在最终的输出文件里，而 Combiner 函数的输出被写到中间文件里，然后被发送给 Reduce 任务。部分的合并中间结果可以显著的提高一些 MapReduce 操作的速度。

#### 4.4 输入和输出的类型

**MapReduce 库支持几种不同的格式的输入数据**。比如，文本模式的输入数据的每一行被视为是一个key/value pair。**key 是文件的偏移量，value 是那一行的内容**。另外一种**常见的格式是以 key 进行排序来存储的 key/value pair 的序列**。每种输入类型的实现都必须能够把输入数据分割成数据片段，该数据片段能够由单独的 Map 任务来进行后续处理。每种输入类型的实现都必须能够把输入数据分割成数据片段，该数据片段能够由单独的 Map 任务来进行后续处理。

#### 4.5 副作用

在某些情况下，如果在 Map 和/或 Reduce 操作过程中增加辅助的输出文件会比较省事。通常应用程序首先把输出结果写到一个临时文件中，在输出全部数据之后，在使用系统级的原子操作 rename 重新命名这个临时文件。

#### 4.6 跳过损坏的记录

有时候，用户程序中的 bug 导致 Map 或者 Reduce 函数在处理某些记录的时候 crash 掉，MapReduce 操作无法顺利完成。惯常的做法是修复 bug 后再次执行 MapReduce 操作，但是，有时候找出这些 bug 并修复它们不是一件容易的事情；这些 bug 也许是在第三方库里边，而我们手头没有这些库的源代码。而且在很多时候，忽略一些有问题的记录也是可以接受的。每个 worker 进程都设置了信号处理函数捕获内存段异常（segmentation violation）和总线错误（bus error）。在执行 Map 或者 Reduce 操作之前，MapReduce 库通过全局变量保存记录序号。如果用户程序触发了一个系统信号，消息处理函数将用“最后一口气”通过 UDP 包向 master 发送处理的最后一条记录的序号。当 master看到在处理某条特定记录不止失败一次时，master 就标志着条记录需要被跳过，并且在下次重新执行相关的Map 或者 Reduce 任务的时候跳过这条记录。

#### 4.7 本地执行

开发了一套 MapReduce 库的本地实现版本，通过使用本地版本的MapReduce 库，MapReduce 操作在本地计算机上顺序的执行。用户可以控制 MapReduce 操作的执行，可以把操作限制到特定的 Map 任务上。用户通过设定特别的标志来在本地执行他们的程序，之后就可以很容易的使用本地调试和测试工具（比如 gdb）。

#### 4.8  状态信息

master 使用嵌入式的 HTTP 服务器（如 Jetty）显示一组状态信息页面，用户可以监控各种执行状态。状态信息页面显示了包括计算执行的进度，比如已经完成了多少任务、有多少任务正在处理、输入的字节数、中间数据的字节数、输出的字节数、处理百分比等等，页面还包含了指向每个任务的 stderr 和 stdout 文件的链接。处于最顶层的状态页面显示了哪些 worker 失效了，以及他们失效的时候正在运行的 Map 和 Reduce任务。

#### 4.9 计数器

MapReduce 库使用计数器统计不同事件发生次数。比如，用户可能想统计已经处理了多少个单词、已经索引的多少篇 German 文档等等。为了使用这个特性，用户在程序中创建一个命名的计数器对象，在 Map 和 Reduce 函数中相应的增加计数器的值。例如：

```
Counter* uppercase;
uppercase = GetCounter(“uppercase”);
map(String name, String contents):
for each word w in contents:
 if (IsCapitalized(w)):
   uppercase->Increment();
 EmitIntermediate(w, “1″);
```

这些计数器的值周期性的从各个单独的worker机器上传递给master（附加在ping的应答包中传递）。master把执行成功的 Map 和 Reduce 任务的计数器值进行累计，当 MapReduce 操作完成之后，返回给用户代码。计数器当前的值也会显示在 master 的状态页面上，这样用户就可以看到当前计算的进度。当累加计数器的值的时候，master 要检查重复运行的 Map 或者 Reduce 任务，避免重复累加（之前提到的备用任务和失效后重新执行任务这两种情况会导致相同的任务被多次执行）。

### 5 性能

使用两个计算来衡量MapReduce的性能，**一个计算在大约 1TB 的数据中进行特定的模式匹配，另一个计算对大约 1TB 的数据进行排序。** 一类是对数据格式进行转换，从一种表现形式转换为另外一种表现形式；另一类是从海量数据中抽取少部分的用户感兴趣的数据。

#### 5.1 集群配置

程序都运行在一个大约由 1800 台机器构成的集群上。每台机器配置 2 个 2G 主频、支持超线程的 Intel Xeon 处理器，4GB 的物理内存，两个 160GB 的 IDE 硬盘和一个千兆以太网卡。

#### 5.2 GREP

分布式的 grep 程序需要扫描大概 10 的 10 次方个由 100 个字节组成的记录，查找出现概率较小的 3个字符的模式。输入数据被拆分成大约 64M 的 Block（M=15000），整个输出数据存放在一个文件中（R=1）。

<img src="https://i.328888.xyz/2023/03/17/LIlj8.jpeg" alt="LIlj8.jpeg" border="0" />

**上图显示了这个运算随时间的处理过程。其中 Y 轴表示输入数据的处理速度**。处理速度随着参与MapReduce 计算的机器数量的增加而增加，**当 1764 台 worker 参与计算的时，处理速度达到了 30GB/s**。当Map 任务结束的时候，即在计算开始后 80 秒，输入的处理速度降到 0。整个计算过程从开始到结束一共花了大概 150 秒。

#### 5.3 排序

排序程序处理 10 的 10 次方个 100 个字节组成的记录（大概 1TB 的数据）。这个程序模仿 TeraSort benchmark。

排序程序由不到 50 行代码组成。只有三行的 Map 函数从文本行中解析出 10 个字节的 key 值作为排序的key，并且把这个 key 和原始文本行作为中间的 key/value pair 值输出。

<img src="https://i.328888.xyz/2023/03/17/LI2QF.jpeg" alt="LI2QF.jpeg" border="0" />

上图显示了这个排序程序的正常执行过程。左上的图显示了输入数据读取的速度，左边中间的图显示了中间数据从 Map 任务发送到 Reduce 任务的网络速度，左下图表示 Reduce 任务把排序后的数据写到最终的输出文件的速度，在第一个排序阶段结束和数据开始写入磁盘之间有一个小的延时，这是因为 worker 机器正在忙于排序中间数据。

输入数据的读取速度比排序速度和输出数据写入磁盘速度要高不少，这是因为我们的输入数据本地化优化策略起了作用，节省了网络宽带；排序速度比输出数据写入到磁盘的速度快，这是因为输出数据写了两份，写入复制节点的原因是为了保证数据可靠性和可用性。

#### 5.4 高效的backup任务

图三（b）显示了关闭了**备用任务**后排序程序执行情况。执行的过程和图 3（a）很相似，除了输出数据写磁盘的动作在时间上拖了一个很长的尾巴，而且在这段时间里，几乎没有什么写入动作。在 960 秒后，只有5 个 Reduce 任务没有完成。这些拖后腿的任务又执行了 300 秒才完成。**整个计算消耗了 1283 秒，多了 44%的执行时间。**

#### 5.5 失效的机器

图三（c）中演示的排序程序执行的过程中，在程序开始后几分钟有意的 kill 了 1746 个 worker 中的 200 个。**集群底层的调度立刻在这些机器上重新开始新的 worker 处理进程**（因为只是 worker 机器上的处理进程被 kill 了，机器本身还在工作）。

图三（c）**显示出了一个“负”的输入数据读取速度，这是因为一些已经完成的 Map 任务丢失了**（由于相应的执行 Map 任务的 worker 进程被 kill 了），需要重新执行这些任务。相关 Map 任务很快就被重新执行了。整个运算在 933 秒内完成，包括了初始启动时间（**只比正常执行多消耗了 5%的时间**）。

### 6 经验

MapReduce 库能广泛应用于我们日常工作中遇到的各类问题。它现在在 Google 内部各个领域得到广泛应用，包括：

* 大规模机器学习问题
* Google News 和 Froogle 产品的集群问题
* 从公众查询产品（比如 Google 的 Zeitgeist）的报告中抽取数据
* 从大量的新应用和新产品的网页中提取有用信息（比如，从大量的位置搜索网页中抽取地理位置信息）
* 大规模的图形计算

<img src="https://i.328888.xyz/2023/03/17/LIOlH.jpeg" alt="LIOlH.jpeg" border="0" />

上图显示了在源代码管理系统中，随着时间推移，独立的 MapReduce 程序数量的显著增加。MapReduce的成功取决于采用MapReduce库能够在不到半个小时时间内写出一个简单的程序，这个简单的程序能够在上千台机器的组成的集群上做大规模并发处理，这极大的加快了开发和原形设计的周期。另外，采用 MapReduce 库，可以让完全没有分布式和/或并行系统开发经验的程序员很容易的利用大量的资源，开发出分布式和/或并行处理的应用。

<img src="https://i.328888.xyz/2023/03/17/LIbgQ.jpeg" alt="LIbgQ.jpeg" border="0" />

在每个任务结束的时候，MapReduce 库统计计算资源的使用状况。

#### 6.1 大规模索引

到目前为止，**MapReduce 最成功的应用就是重写了 Google 网络搜索服务所使用到的 index 系统**。索引系统的输入数据是网络爬虫抓取回来的海量的文档，这些文档数据都保存在 GFS 文件系统里。

实现索引部分的代码简单、小巧、容易理解，因为对于容错、分布式以及并行计算的处理都是 MapReduce库提供的。比如，使用 MapReduce 库，计算的代码行数从原来的 3800 行 C++代码减少到大概 700 行代码。

索引系统的操作管理更容易了。因为由机器失效、机器处理速度缓慢、以及网络的瞬间阻塞等引起的绝大部分问题都已经由 MapReduce 库解决了，不再需要操作人员的介入了。另外，**可以通过在索引系统集群中增加机器的简单方法提高整体处理性能。**

### 7 相关工作

很多系统都提供了严格的编程模式，并且通过对编程的严格限制来实现并行计算。大部分并发处理系统都只在小规模的集群上实现，并且把容错处理交给了程序员。比如

* Bulk Synchronous Programming和MPI 原语实现并行处理抽象，但**MapReduce 利用限制性编程模式**实现了用户程序的自动并发处理，并且提供了透明的容错处理

* 数据本地优化策略的灵感来源于 active disks， 在挂载几个硬盘的普通机器上执行运算，而不是在磁盘处理器上执行工作

* 备用任务机制和 Charlotte System提出的 eager 调度机制比较类似，通过忽略引起故障的记录的方式在某种程度上解决任务反复失效后计算不能完成的问题

* 如Condor类似，MapReduce 的实现依赖于一个内部的集群管理系统，这个集群管理系统负责在一个超大的、共享机器的集群上分布和运行用户任务

* 排序机制和 NOW-Sort的操作上很类似，读取输入源的机器（map workers）把待排序的数据进行分区后，发送到 R 个 Reduce worker 中的一个进行处理。每个 Reduce worker 在本地对数据进行排序（尽可能在内存中排序）

* 与River类型，提供了一个编程模型：处理进程通过分布式队列传送数据的方式进行互相通讯。但MapReduce 库采用了其它的方法。通过对编程模型进行限制，MapReduce 框架把问题分解成为大量的“小”任务。通过对编程模型进行限制，**可用在工作接近完成的时候调度备用任务，缩短在硬件配置不均衡的情况下缩小整个操作完成的时间**

* BAD-FS和MapReduce有着相似的基础功能：

  * 两个系统采用重新执行的方式来防止由于失效导致的数据丢失
  * 两个都使用数据本地化调度策略，减少网络通讯的数据量

* 和 TACC一样，也是一个用于简化构造高可用性网络服务的系统，依靠重新执行机制来

  实现的容错处理

### 8 结束语

MapReduce 编程模型在 Google 内部成功应用于多个领域， 把这种成功归结为几个方面：

* 由于MapReduce 封装了并行处理、容错处理、数据本地化优化、负载均衡等等技术难点的细节，这使得 MapReduce库易于使用
* 大量不同类型的问题都可以通过 MapReduce 简单的解决。比如，MapReduce 用于生成 Google 的网络搜索服务所需要的数据、用来排序、用来数据挖掘、用于机器学习，以及很多其它的系统
* 实现了一个在数千台计算机组成的大型集群上灵活部署运行的 MapReduce，使得有效利用这些丰富的计算资源变得非常简单

经验：

* **约束编程模式**使得并行和分布式计算非常容易，也易于构造容错的计算环境
* **网络带宽是稀有资源**。大量的系统优化是针对减少网络传输量为目的的：本地优化策略使大量的数据从本地磁盘读取，中间文件写入本地磁盘、并且只写一份中间文件也节约了网络带宽
* 多次执行相同的任务可以减少性能缓慢的机器带来的负面影响，同时解决了由于机器失效导致的数据丢失问题



### 9 感谢

* Josh Levenberg
* Mohit Aron
* Howard Gobioff
* Markus Gutschke
* David Kramer
* Shun-Tak Leung
*  Josh Redstone 
* Percy Liang
* Olcan Sercinoglu
* Mike Burrows
* Wilson Hsieh
* Sharon Perl
* Rob Pike
* Debby Wallach
* Eric Brewer

### 10 参考资料

* [1] Andrea C. Arpaci-Dusseau, Remzi H. Arpaci-Dusseau,David E. Culler, Joseph M. Hellerstein, and David A.  Patterson.High-performance sorting on networks of workstations.In Proceedings of the 1997 ACM SIGMOD  InternationalConference on Management of Data, Tucson,Arizona, May 1997.
* [2] Remzi H. Arpaci-Dusseau, Eric Anderson, NoahTreuhaft, David E. Culler, Joseph M. Hellerstein, David  Patterson, and Kathy Yelick. Cluster I/O with River:Making the fast case common. In Proceedings of the Sixth  Workshop on Input/Output in Parallel and Distributed Systems (IOPADS ’99), pages 10.22, Atlanta, Georgia, May  1999.
* [3] Arash Baratloo, Mehmet Karaul, Zvi Kedem, and Peter Wyckoff. Charlotte: Metacomputing on the web. In Proceedings of the 9th International Conference on Parallel and Distributed Computing Systems, 1996. [4] Luiz A.  Barroso, Jeffrey Dean, and Urs H¨olzle. Web search for a planet: The Google cluster architecture. IEEE Micro,  23(2):22.28, April 2003.
* [5] John Bent, Douglas Thain, Andrea C.Arpaci-Dusseau, Remzi H. Arpaci-Dusseau, and Miron Livny. Explicit  control in a batch-aware distributed file system. In Proceedings of the 1st USENIX Symposium on Networked  Systems Design and Implementation NSDI, March 2004.
* [6] Guy E. Blelloch. Scans as primitive parallel operations.IEEE Transactions on Computers, C-38(11),  November 1989.
* [7] Armando Fox, Steven D. Gribble, Yatin Chawathe, Eric A. Brewer, and Paul Gauthier. Cluster-based scalable network services. In Proceedings of the 16th ACM Symposium on Operating System Principles, pages 78. 91, Saint-Malo, France, 1997.
* [8] Sanjay Ghemawat, Howard Gobioff, and Shun-Tak Leung. The Google file system. In 19th Symposium on Operating Systems Principles, pages 29.43, Lake George, New York, 2003. To appear in OSDI 2004 12
* [9] S. Gorlatch. Systematic efficient parallelization of scan and other list homomorphisms. In L. Bouge, P. Fraigniaud, A. Mignotte, and Y. Robert, editors, Euro-Par’96. Parallel Processing, Lecture Notes in Computer Science 1124, pages 401.408. Springer-Verlag, 1996.
* [10] Jim Gray. Sort benchmark home page. http://research.microsoft.com/barc/SortBenchmark/.
* [11] William Gropp, Ewing Lusk, and Anthony Skjellum. Using MPI: Portable Parallel Programming with the Message-Passing Interface. MIT Press, Cambridge, MA, 1999.
* [12] L. Huston, R. Sukthankar, R.Wickremesinghe, M. Satyanarayanan, G. R. Ganger, E. Riedel, and A. 
* Ailamaki. Diamond: A storage architecture for early discard in interactive search. In Proceedings of the 2004 USENIX File and Storage Technologies FAST Conference, April 2004.
* [13] Richard E. Ladner and Michael J. Fischer. Parallel prefix computation. Journal of the ACM, 27(4):831.838, 1980.
* [14] Michael O. Rabin. Efficient dispersal of information for security, load balancing and fault tolerance. Journal of the ACM, 36(2):335.348, 1989.
* [15] Erik Riedel, Christos Faloutsos, Garth A. Gibson, and David Nagle. Active disks for large-scale data processing. IEEE Computer, pages 68.74, June 2001.
* [16] Douglas Thain, Todd Tannenbaum, and Miron Livny. Distributed computing in practice: The Condor experience. Concurrency and Computation: Practice and Experience, 2004.
* [17] L. G. Valiant. A bridging model for parallel computation. Communications of the ACM, 33(8):103.111, 1997.
* [18] Jim Wyllie. Spsort: How to sort a terabyte quickly. http://alme1.almaden.ibm.com/cs/spsort.pdf.



### 11 附录-单词频率统计

用于统计在一组命令行指定的输入文件中，每一个不同的单词出现频率

```cpp
#include “mapreduce/mapreduce.h”
// User’s map function
class WordCounter : public Mapper {
public:
 virtual void Map(const MapInput& input) {
 const string& text = input.value();
 const int n = text.size();
 for (int i = 0; i < n; ) {
 // Skip past leading whitespace
 while ((i < n) && isspace(text[i]))
 	i++;
 
 // Find word end
 int start = i;
 while ((i < n) && !isspace(text[i]))
 	i++;
 if (start < i)
 	Emit(text.substr(start,i-start),”1″);
 	}
  }
};

REGISTER_MAPPER(WordCounter);

// User’s reduce function
class Adder : public Reducer {
	virtual void Reduce(ReduceInput* input) {
 	// Iterate over all entries with the
 	// same key and add the values
	int64 value = 0;
 	while (!input->done()) {
 		value += StringToInt(input->value());
 		input->NextValue();
 	}
        
  	 // Emit sum for input->key()
 	Emit(IntToString(value));
	}
};

REGISTER_REDUCER(Adder);

int main(int argc, char** argv) {
	ParseCommandLineFlags(argc, argv);
    MapReduceSpecification spec;
    
    // Store list of input files into “spec”
	for (int i = 1; i < argc; i++) {
 		MapReduceInput* input = spec.add_input();
 		input->set_format(“text”);
 		input->set_filepattern(argv[i]);
 		input->set_mapper_class(“WordCounter”);
	}
    
    // Specify the output files:
	// /gfs/test/freq-00000-of-00100
	// /gfs/test/freq-00001-of-00100
	MapReduceOutput* out = spec.output();
	out->set_filebase(“/gfs/test/freq”);
	out->set_num_tasks(100);
	out->set_format(“text”);
	out->set_reducer_class(“Adder”);
    
    // Optional: do partial sums within map
	// tasks to save network bandwidth
	out->set_combiner_class(“Adder”);
	// Tuning parameters: use at most 2000
	// machines and 100 MB of memory per task
	spec.set_machines(2000);
	spec.set_map_megabytes(100);
	spec.set_reduce_megabytes(100);
    // Now run it
	MapReduceResult result;
	if (!MapReduce(spec, &result)) abort();
	// Done: ‘result’ structure contains info
	// about counters, time taken, number of
	// machines used, etc.
	return 0;
}
```





















