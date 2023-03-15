---
layout: '../../layouts/MarkdownPost.astro'
title: 'Google三大论文之BigTable'
pubDate: 2023-03-15
description: 'Bigtable 是一个分布式的结构化数据存储系统，它被设计用来处理海量数据：通常是分布在数千台普通服务器上的 PB 级的数据。'
author: 'UncleLLD'
cover:
    url: 'https://imgloc.com/i/JEQDA'
    alt: 'cover'
tags: ["Google", "BigTable", "大数据", "论文总结"]
theme: 'dark'
featured: true
---

### Google三大论文之Bigtable




### 1 摘要

Bigtable 是一个分布式的结构化数据存储系统，它被设计用来处理海量数据：通常是分布在数千台普通服务器上的 PB 级的数据。

Google 的很多项目使用 Bigtable 存储数据，包括 Web 索引、Google Earth、Google Finance。这些应用的要求差异非常大，无论是在数据量上（从 URL 到网页到卫星图像）还是在响应速度上。尽管应用需求差异很大，但Bigtable 还是成功的提供了一个灵活的、高性能的解决方案。

### 2 介绍

在过去两年半时间里，设计、实现并部署了一个**分布式的结构化数据存储系统**——Bigtable。**Bigtable 的设计目的是可靠的处理 PB 级别的数据，并且能够部署到上千台机器上**。Bigtable已经实现了下面的几个目标：

* 适用性广泛
* 可扩展
* 高性能和高可用性

超过60个Google的产品对Bigtable 提出了迥异的需求，有的需要**高吞吐量的批处理，有的需要及时响应，快速返回数据给最终用户**。使用的 Bigtable 集群的配置也有很大的差异，有的集群只有几台服务器，而有的则需要上千台服务器、存储几百 TB 的数据。Bigtable 不支持完整的关系数据模型；与之相反，**Bigtable 为客户提供了简单的数据模型，利用这个模型，客户可以动态控制数据的分布和格式，用户也可以自己推测底层存储数据的位置相关性。**且可以通过 BigTable 的模式参数来控制数据是存放在内存中、还是硬盘上。

### 3 数据模型

**Bigtable** **是一个稀疏的、分布式的、持久化存储的多维度排序** **Map**。**Map 的索引是行关键字、列关键字以及时间戳**；Map 中的每个 value 都是一个未经解析的 byte 数组。

```
(row:string, column:string, time:int64)->string
```

**例子：**

想要**存储海量的网页及相关信息**，这些数据可以用于很多不同的项目，姑且称这个特殊的表为 **Webtable**。在 Webtable 里，**使用 URL 作为行关键字**，使用**网页的某些属性作为列名**，网页的内容存在“contents:”列中，并用获取**该网页的时间戳作为标识**（按照获取时间不同，存储了多个版本的网页数据）。如图所示：
[![](https://i.328888.xyz/2023/03/15/JEQDA.jpeg)](https://imgloc.com/i/JEQDA)

**行名是一个反向 URL**。contents 列族存放的是网页的内容，**anchor 列族存放引用该网页的锚链接文本**。CNN 的主页被 Sports Illustrator 和 MY-look 的主页引用，因此该行包含了名为“anchor:cnnsi.com”和“anchhor:my.look.ca”的列。**每个锚链接只有一个版本；而 contents 列则有三个版本，分别由时间戳 t3，t5，和 t6 标识。**

#### 3.1 行

表中的行关键字可以是任意的字符串（最大64KB），**对同一个行关键字的读或者写操作都是原子的（不管读或者写这一行里多少个不同列）**，这个设计决策能够使用户很容易的理解程序在对**同一个行进行并发更新**操作时的行为。

**Bigtable 通过行关键字的字典顺序来组织数据**。表中的**每个行都可以动态分区**。每个分区叫做一个”Tablet”，**Tablet 是数据分布和负载均衡调整的最小单位**。当操作只读取行中很少几列的数据时效率很高，通常只需要很少几次机器间的通信即可完成。**在 Webtable 里，通过反转 URL 中主机名的方式，可以把同一个域名下的网页聚集起来组织成连续的行**。 

maps.google.com/index.html的数据存放在关键字 com.google.maps/index.html 下，可以让基于主机和域名的分析更加有效。

#### 3.2 列族

列关键字组成的集合叫做“列族“，列族是访问控制的基本单位。列族在使用之前必须先创建，然后才能在列族中任何的列关键字下存放数据；列族创建后，其中的任何一个列关键字下都可以存放数据。

列关键字的命名语法如下：**列族：限定词**。列族的名字必须是**可打印的字符串**，而限定词的名字可以是任意的字符串。

**访问控制、磁盘和内存的使用统计都是在列族层面进行的**。在 Webtable 的例子中，上述的控制权限能帮助我们管理不同类型的应用：允许一些应用可以添加新的基本数据、一些应用可以读取基本数据并创建继承的列族、一些应用则只允许浏览数据（甚至可能因为隐私的原因不能浏览所有数据）。

#### 3.3 时间戳

在 Bigtable 中，**表的每一个数据项都可以包含同一份数据的不同版本**；**不同版本的数据通过时间戳来索引**。Bigtable 时间戳的类型是 64 位整型。Bigtable 可以给时间戳赋值，用来表示精确到毫秒的“实时”时间；数据项中，不同版本的数据按照时间戳倒序排序，即最新的数据排在最前面。

为了减轻多个版本数据的管理负担，可以对每一个列族配有两个设置参数，**Bigtable 通过这两个参数可以**

**对废弃版本的数据自动进行垃圾收集**。

* 用户可以指定**只保存最后 n 个版本的数据**
* **只保存“足够新”的版本的数据**（比如，只保存最近 7 天的内容写入的数据）

### 4 API

Bigtable 提供了**建立和删除表以及列族的 API 函数**。Bigtable 还提供了**修改集群、表和列族的元数据的API**，比如修改访问权限

```
// Open the table
Table *T = OpenOrDie(“/bigtable/web/webtable”);
// Write a new anchor and delete an old anchor
RowMutation r1(T, “com.cnn.www”);
r1.Set(“anchor:www.c-span.org”, “CNN”);
r1.Delete(“anchor:www.abc.com”);
Operation op;
Apply(&op, &r1);
```

客户程序可以对 Bigtable 进行如下的操作：写**入或者删除 Bigtable 中的值、从每个行中查找值、或者遍历表中的一个数据子集**

```
Scanner scanner(T);
ScanStream *stream;
stream = scanner.FetchColumnFamily(“anchor”);
stream->SetReturnAllVersions();
scanner.Lookup(“com.cnn.www”);
for (; !stream->Done(); stream->Next()) {
 printf(“%s %s %lld %s\n”,
 scanner.RowName(),
 stream->ColumnName(),
 stream->MicroTimestamp(),
 stream->Value());
}
```

**C++代码使用 Scanner 抽象对象遍历一个行内的所有锚点。客户程序可以遍历多个列族，有几种方法可以对扫描输出的行、列和时间戳进行限制**，可以限制上面的扫描，让它只输出那些匹配正则表达式*.cnn.com 的锚点，或者那些时间戳在当前时间前 10 天的锚点。

### 5 BigTable构件

Bigtable 是建立在其它的几个 Google 基础构件上的。BigTable 使用 Google 的分布式文件系统(GFS)存储日志文件和数据文件。BigTable 的进程经常要和其它应用的进程共享机器。BigTable 依赖集群管理系统来调度任务、管理共享的机器上的资源、处理机器的故障、以及监视机器的状态。

BigTable 内部存储数据的文件是 Google SSTable 格式的。**SSTable 是一个持久化的、排序的、不可更改的Map 结构**，而 Map 是一个 key-value 映射的数据结构，key 和 value 的值都是任意的 Byte 串。可以对 SSTable进行如下的操作：**查询与一个 key 值相关的 value，或者遍历某个 key 值范围内的所有的 key-value 对。**SSTable 使用块索引来定位数据块；**在打开 SSTable 的时候，索引被加载到内存。每次查找都可以通过一次磁盘搜索完成：首先使用二分查找法在内存中的索引里找到数据块的位置，然后再从硬盘读取相应的数据块**。也可以选择把整个 SSTable 都放在内存中，这样就不必访问硬盘了

**BigTable 还依赖一个高可用的、序列化的分布式锁服务组件**，叫做 Chubby。一个 Chubby 服务包括了 5 个活动的副本，其中的一个副本被选为 Master，并且处理请求。只有在大多数副本都是正常运行的，并且彼此之间能够互相通信的情况下，Chubby 服务才是可用的。当有副本失效的时候，Chubby 使用 Paxos 算法来保证副本的一致性。

Bigtable 使用 Chubby 完成以下的几个任务：

* 确保在任何给定的时间内最多只有一个活动的 Master 副本
* 存储 BigTable 数据的自引导指令的位置 
* 查找 Tablet 服务器，以及在 Tablet 服务器失效时进行善后 
* 存储 BigTable 的模式信息 
* 以及存储访问控制列表

### 6 介绍

Bigtable 包括了三个主要的组件：**链接到客户程序中的库、一个 Master 服务器和多个 Tablet 服务器**。针对系统工作负载的变化情况，BigTable 可以动态的向集群中添加（或者删除）Tablet 服务器。

Master 服务器主要负责以下工作：

* 为 Tablet 服务器分配 Tablets
* 检测新加入的或者过期失效的 Table 服务器
* 对 Tablet 服务器进行负载均衡
* 以及对保存在 GFS 上的文件进行垃圾收集
* 处理对模式的相关修改操作，例如建立表和列族

一个 BigTable 集群存储了很多表，每个表包含了一个 Tablet 的集合，而每个 Tablet 包含了某个范围内的行的所有相关数据。初始状态下，一个表只有一个 Tablet。随着表中数据的增长，它被自动分割成多个 Tablet，缺省情况下，每个 Tablet 的尺寸大约是 100MB 到 200MB。

#### 6.1 Tablet的位置

使用一个三层的、类似Ｂ+树的结构存储 Tablet 的位置信息(如图 4)。

![](https://i.328888.xyz/2023/03/15/JEaJc.jpeg)](https://imgloc.com/i/JEaJc)

第一层是一个存储在 Chubby 中的文件，它包含了 Root Tablet 的位置信息。表的每个 Tablet 包含了一个用户 Tablet 的集合。采用这种三层结构的存储模式，可以标识 $2^34$ 个 Tablet 的地址（如果每个 Tablet 存储 128MB 数据，那么一共可以存储 $2^61$ 字节数据）。

**客户程序使用的库会缓存 Tablet 的位置信息**。如果客户程序没有缓存某个 Tablet 的地址信息，或者发现它缓存的地址信息不正确，客户程序就在树状的存储结构中递归的查询 Tablet 位置信息；如果客户端缓存是空的，那么寻址算法需要通过三次网络来回通信寻址，这其中包括了一次 Chubby 读操作。

#### 6.2 Table分配

**在任何一个时刻，一个Tablet 只能分配给一个Tablet服务器**。**Master服务器记录了当前有哪些活跃的 Tablet服务器、哪些 Tablet 分配给了哪些 Tablet 服务器、哪些 Tablet 还没有被分配**。当一个 Tablet 还没有被分配、并且刚好有一个 Tablet 服务器有足够的空闲空间装载该 Tablet 时，Master 服务器会给这个 Tablet 服务器发送一个装载请求，把 Tablet 分配给这个服务器。Chubby 提供了一种高效的机制，利用这种机制，Tablet 服务器能够在不增加网络负担的情况下知道它是否还持有锁。

当集群管理系统启动了一个 Master 服务器之后，Master 服务器首先要了解当前 Tablet 的分配状态，之后才能够修改分配状态。Master 服务器在启动的时候执行以下步骤：

* Master 服务器从 Chubby 获取一个唯一的 Master 锁，用来阻止创建其它的 Master 服务器实例
* Master 服务器扫描 Chubby 的服务器文件锁存储目录，获取当前正在运行的服务器列表
* Master 服务器和所有的正在运行的 Tablet 表服务器通信，获取每个 Tablet 服务器上 Tablet 的分配信息
* Master 服务器扫描 METADATA 表获取所有的 Tablet 的集合

#### 6.3 Tablet服务

![JEYro.jpeg](https://i.328888.xyz/2023/03/15/JEYro.jpeg)](https://imgloc.com/i/JEYro)

Tablet 的持久化状态信息保存在 GFS 上。更新操作提交到 REDO 日志中。在这些更新操作中，最近提交的那些存放在一个排序的缓存中，我们称这个缓存为 memtable；较早的更新存放在一系列SSTable 中。

成功的修改操作会记录在提交日志里。可以采用批量提交方式来提高包含大量小的修改操作的应用程序的吞吐量。当一个写操作提交后，写的内容插入到 memtable 里面。

#### 6.4 空间收缩

随着写操作的执行，memtable 的大小不断增加。当 memtable 的尺寸到达一个门限值的时候，这个memtable就会被冻结，然后创建一个新的 memtable；被冻结住 memtable 会被转换成 SSTable，然后写入 GFS。

**合并所有的 SSTable 并生成一个新的 SSTable 的 Merging Compaction 过程叫作 Major Compaction。**Bigtable循环扫描它所有的 Tablet，并且定期对它们执行 Major Compaction。**Major Compaction 机制允许 Bigtable 回收已经删除的数据占有的资源，并且确保 BigTable 能及时清除已经删除的数据，这对存放敏感数据的服务是非常重要。**

### 7 优化

要很多优化工作才能使 Bigtable 到达用户要求的高性能、高可用性和高可靠性。

#### 7.1 局部性群组

客户程序可以将多个列族组合成一个局部性群族。**将通常不会一起访问的列族分割成不同的局部性群组可以提高读取操作的效率**。网页的内容可以在另外一个群组：当一个应用程序要读取网页的元数据的时候，它没有必要去读取所有的页面内容。**Tablet 服务器依照惰性加载的策略将设定为放入内存的局部性群组的 SSTable 装载进内存**。加载完成之后，访问属于该局部性群组的列族的时候就不必读取硬盘了。这个特性对于需要频繁访问的小块数据特别有用。

#### 7.2 压缩

客户程序可以控制一个局部性群组的 SSTable 是否需要压缩；如果需要压缩，那么以什么格式来压缩。**很多客户程序使用了“两遍”的、可定制的压缩方式**。**第一遍采用 Bentley and McIlroy’s 方式，这种方式在一个很大的扫描窗口里对常见的长字符串进行压缩**；第二遍是**采用快速压缩算法，即在一个 16KB 的小扫描窗口中寻找重复数据**。两个压缩的算法都很快，在现在的机器上，压缩的速率达到 100-200MB/s，解压的速率达到 400-1000MB/s。**“两遍”的压缩模式如此高效的原因是由于 Webtable 的行的存放方式：从同一个主机获取的页面都存在临近的地方。利用这个特性，Bentley-McIlroy 算法可以从来自同一个主机的页面里找到大量的重复内容。**同一份数据的多个版本压缩效率会更高。

#### 7.3 通过缓存提高读操作的性能

为了提高读操作的性能，Tablet 服务器使用二级缓存的策略。扫描缓存是第一级缓存，主要缓存 Tablet服务器通过 SSTable 接口获取的 Key-Value 对；Block 缓存是二级缓存，缓存的是从 GFS 读取的 SSTable 的Block。缓存对于经常要重复读相同数据及经常读刚刚读过的数据附近的数据的应用程序来说，Block缓存都非常有用。

##### 7.3.1 **Bloom** 过滤器

https://china.googleblog.com/2007/07/bloom-filter_7469.html

**允许客户程序对特定局部性群组的 SSTable 指定 Bloom 过滤器来减少硬盘访问的次数。**使用 Bloom 过滤器查询一个 SSTable 是否包含了特定行和列的数据。对于某些特定应用程序，只付出了少量的、用于存储 Bloom 过滤器的内存的代价，就换来了读操作显著减少的磁盘访问的次数。不适用于零容忍错误的场景。

##### 7.3.2 **Commit** 日志的实现

对每个Tablet 设置单独的日志文件也会给批量提交本应具有的优化效果带来很大的负面影响。为了避免这些问题，**设置每个 Tablet 服务器一个 Commit 日志文件，把修改操作的日志以追加方式写入同一个日志文件，因此一个实际的日志文件中混合了对多个 Tablet 修改的日志记录。**使用单个日志显著提高了普通操作的性能，但是将恢复的工作复杂化。一种恢复方法新的 Tablet 服务器读取完整的 Commit 日志文件，然后只重复执行它需要恢复的 Tablet 的相关修改操作。使用这种方法，假如有 100 台 Tablet 服务器，每台都加载了失效的 Tablet 服务器上的一个 Tablet，那么，这个日志文件就要被读取 100 次（每个服务器读取一次）。为了避免多次读取日志文件，首先把**日志按照关键字（table，row name，log sequence number）排序。排序之后，对同一个 Tablet 的修改操作的日志记录就连续存放在了一起**。如果并行的话可以将日志进行分割成64MB的段，之后在不同的Table服务器对段进行并行排序。

##### 7.3.3 Tablet恢复提速

当 Master 服务器将一个 Tablet 从一个 Tablet 服务器移到另外一个 Tablet 服务器时，源 Tablet 服务器会对这个 Tablet 做一次 Minor Compaction。Compaction 完成之后，该服务器就停止为该 Tablet 提供服务。在卸载 Tablet 之前，源 Tablet 服务器还会再做一次Minor Compaction，以消除前面在一次压缩过程中又产生的未归并的记录。

##### 7.3.4 利用不变性

在使用 Bigtable 时，**除了 SSTable 缓存之外的其它部分产生的 SSTable 都是不变的，可以利用这一点对系统进行简化。**可以非常高效的实现对行的并行操作。memtable 是唯一一个能被读和写操作同时访问的可变数据结构。**为了减少在读操作时的竞争，对内存表采用 COW(Copy-on-write)机制，这样就允许读写操作并行执行。**

### 8 性能评估

Tablet 服务器、Master 服务器、测试机、以及 GFS 服务器都运行在同一组机器上。每台机器都运行一个GFS 的服务器。其它的机器要么运行 Tablet 服务器、要么运行客户程序、要么运行在测试过程中，使用这组机器的其它的任务启动的进程。

![](https://i.328888.xyz/2023/03/15/JEq5N.jpeg)](https://imgloc.com/i/JEq5N)

图中的数据和曲线是读/写 1000-byte value 值时取得的。图中的表格显示了每个 Tablet 服务器每秒钟进行的操作的次数；图中的曲线显示了每秒种所有的 Tablet服务器上操作次数的总和。

#### 8.1 单个 Tablet 服务器的性能

随机读的性能比其它操作慢一个数量级或以上， 内存中的随机读操作速度快很多。随机和序列写操作的性能比随机读要好些，原因是每个 Tablet 服务器直接把写入操作的内容追加到一个Commit 日志文件的尾部，并且采用批量提交的方式，通过把数据以流的方式写入到 GFS 来提高性能。序列读的性能好于随机读，因为每取出 64KB 的 SSTable 的 Block 后，这些数据会缓存到 Block 缓存中，后续的 64 次读操作直接从缓存读取数据。扫描的性能更高。

#### 8.2 性能提升

随着将系统中的 Tablet 服务器从 1 台增加到 500 台，系统的整体吞吐量有了梦幻般的增长，增长的倍率超过了 100。比如，**随着 Tablet 服务器的数量增加了 500 倍，内存中的随机读操作的性能增加了 300 倍**。之所以会有这样的性能提升，主要是因为这个基准测试的瓶颈是单台 Tablet 服务器的 CPU。性能的提升还不是线性的，这是由于负载不均衡造成的，可以采用负载均衡的算法会尽量避免这种不均衡，但是基于两个主要原因，这个算法并不能完美的工作：一个是尽量减少 Tablet 的移动导致重新负载均衡能力受限，另一个是基准测试程序产生的负载会有波动。随机读基准测试的测试结果显示，随机读的性能随 Tablet 服务器数量增加的提升幅度最小。

### 9 实际应用

![](https://i.328888.xyz/2023/03/15/JeZiV.jpeg)](https://imgloc.com/i/JeZiV)

截止到2006年8月，Google内部一共有388个非测试用的Bigtable集群运行在各种各样的服务器集群上，合计大约有 24500 个 Tablet 服务器。通过观察一个由 14 个集群、8069 个 Tablet 服务器组成的集群组，我们看到整体的吞吐量超过了每秒 1200000 次请求，发送到系统的 RPC 请求导致的网络负载达到了741MB/s，系统发出的 RPC 请求网络负载大约是 16GB/s。

![](https://i.328888.xyz/2023/03/15/JeiOd.jpeg)](https://imgloc.com/i/JeiOd)

#### 9.1 Google Analytics

Google Analytics 是用来帮助 Web 站点的管理员分析他们网站的流量模式的服务。它提供了整体状况的统计数据，比如每天的独立访问的用户数量、每天每个 URL 的浏览次数；它还提供了用户使用网站的行为报告，比如根据用户之前访问的某些页面，统计出几成的用户购买了商品。

* **Row Click 表**（大约有 200TB 数据）的每一行存放了一个最终用户的会话。**行的名字是一个包含 Web 站点名字以及用户会话创建时间的元组**。这种模式保证了对同一个 Web 站点的访问会话是顺序的，会话按时间顺序存储。这个表可以压缩到原来尺寸的 14%
* **Summary 表**（大约有 20TB 的数据）包含了关于每个 Web 站点的、各种类型的预定义汇总信息。一个周期性运行的 MapReduce 任务根据 Raw Click 表的数据生成 Summary 表的数据，能够压缩到原有尺寸的 29%

#### 9.2 Google Earth

Google 通过一组服务为用户提供了高分辨率的地球表面卫星图像，访问的方式可以使通过基于 Web 的Google Maps 访问接口（maps.google.com），也可以通过 Google Earth 定制的客户端软件访问。使用一个表存储预处理数据，使用另外一组表存储用户数据。

数据预处理流水线使用一个表存储原始图像。在预处理过程中，图像被清除，图像数据合并到最终的服务数据中。Imagery 表的每一行都代表了一个单独的地理区域。行都有名称，以确保毗邻的区域存储在了一起。Imagery 表中有一个列族用来记录每个区域的数据源，列族包含了大量的列：基本上市每个列对应一个原始图片的数据。由于每个地理区域都是由很少的几张图片构成的，因此这个列族是非常稀疏的。数据预处理流水线高度依赖运行在 Bigtable 上的 MapReduce 任务传输数据。

#### 9.3 个性化查询

个性化查询使用 Bigtable 存储每个用户的数据。**每个用户都有一个唯一的用户 id，每个用户 id 和一个列名绑定**。一个单独的列族被用来存储各种类型的行为（比如，有个列族可能是用来存储所有的 Web 查询的）。**每个数据项都被用作 Bigtable 的时间戳，记录了相应的用户行为发生的时间**。个性化查询使用以 Bigtable 为存储的 MapReduce 任务生成用户的数据图表。这些用户数据图表用来个性化当前的查询结果。**个性化查询的数据会复制到几个 Bigtable 的集群上， 增强了数据可用性，同时减少了由客户端和Bigtable 集群间的“距离”造成的延时。**

个性化查询存储系统的设计允许其它的团队在它们自己的列中加入新的用户数据，在多个团队之间分享数据的结果是产生了大量的列族。**为了更好的支持数据共享，加入了一个简单的配额机制限制用户在共享表中使用的空间；配额也为使用个性化查询系统存储用户级信息的产品团体提供了隔离机制。**

### 10 经验教训

在设计、实现、维护和支持 Bigtable 的过程中，得到了很多有用的经验和一些有趣的教训：

*  **很多类型的错误都会导致大型分布式系统受损**，这些错误不仅仅是通常的网络中断、或者很多分布式协议中设想的 fail-stop 类型的错误。内存数据损坏、网络中断、时钟偏差、机器挂起、扩展的和非对称的网络分区、使用的其它系统的Bug（比如 Chubby）、GFS 配额溢出、计划内和计划外的硬件维护。通过修改协议来解决这些问题， 比如RPC 机制中加入了 Checksum
*  **在彻底了解一个新特性会被如何使用之后，再决定是否添加这个新特性是非常重要的。**
*  **系统级的监控对 Bigtable 非常重要**。这个特性允许我们检测和修正很多的问题，比如 Tablet 数据结构上的锁的内容、在修改操作提交时对 GFS 的写入非常慢的问题、以及在 METADATA 表的 Tablet 不可用时，对 METADATA 表的访问挂起的问题。还可以帮助我们跟踪所有的集群状态、监控它们的大小、检查集群运行的我们软件的版本、监控集群流入数据的流量，以及检查是否有引发集群高延时的潜在因素。
*  **简洁的设计和编码给维护和调试带来的巨大好处**。

### 11 相关工作

* Boxwood：提供了诸如分布式协议、锁、分布式 Chunk 存储以及分布式 B-tree 存储，目的是提供创建类似文件系统、数据库等高级服务的基础构件。
* 分布式的hash表： CAN、Chord、Tapestry和 Pastry，应对各种不同的传输带宽、不可信的协作者、频繁的更改配置等。
* 并行的数据库系统，能够存储海量的数据：RAC， DB2
* 基于列的存储方案在压缩和磁盘读取方面具有的性能：C-Store、 Sybase IQ、SenSage、KDB+， MonetDB/X100，Daytona
* C-Store： 操作更像关系型数据库



### 12 结论

Bigtable 的集群从 2005 年 4月开始已经投入使用了，**在此之前，花了大约 7 人年设计和实现这个系统**。截止到 2006 年 4 月，已经有超过 60 个项目使用 Bigtable 了。用户对 Bigtable 提供的高性能和高可用性很满意，随着时间的推移，可以根据自己的系统对资源的需求增加情况，通过简单的增加机器，扩展系统的承载能力。后续对 Bigtable 加入一些新的特性，比如支持二级索引，以及支持多 Master 节点的、跨数据中心复制的 Bigtable 的基础构件。

建设 Google 自己的存储解决方案带来了很多优势。通过为 Bigtable 设计自己的数据模型，使得系统极具灵活性。另外，由于全面控制着 Bigtable 的实现过程，以及 Bigtable 使用到的其它的 Google 的基础构件，这就意味着在系统出现瓶颈或效率低下的情况时，能够快速的解决这些问题。

### 13 感谢

* David Nagle
* Brad Calder
* Dan Aguayo
* Sameer Ajmani
* Zhifeng Chen
* Bill Coughran
* Mike Epstein
* Healfdene Goguen
* Robert Griesemer
* Jeremy Hylton
* Josh Hyman
* Alex Khesin
* Joanna Kulik
* Alberto Lerner
* Sherry Listgarten
* Mike Maloney
* Eduardo Pinheiro
* Kathy Polizzi
* Frank Yellin
* Arthur Zwiegincew.

### 参考文献

* [1] ABADI, D. J., MADDEN, S. R., AND FERREIRA, M. C. Integrating compression and execution in  columnoriented database systems. Proc. of SIGMOD (2006).

* [2] AILAMAKI, A., DEWITT, D. J., HILL, M. D., AND SKOUNAKIS, M. Weaving relations for cache performance.In The VLDB Journal (2001), pp. 169-180.

* [3] BANGA, G., DRUSCHEL, P., AND MOGUL, J. C. Resource containers: A new facility for resource management in server systems. In Proc. of the 3rd OSDI (Feb. 1999), pp. 45-58.

* [4] BARU, C. K., FECTEAU, G., GOYAL, A., HSIAO, H., JHINGRAN, A., PADMANABHAN, S., COPELAND,G. P., AND WILSON, W. G. DB2 parallel edition. IBM Systems Journal 34, 2 (1995), 292-322.
* [5] BAVIER, A., BOWMAN, M., CHUN, B., CULLER, D., KARLIN, S., PETERSON, L., ROSCOE, T., SPALINK, T., AND WAWRZONIAK, M. Operating system support for planetary-scale network services. In Proc. of the 1st NSDI(Mar. 2004), pp. 253-266.
* [6] BENTLEY, J. L., AND MCILROY, M. D. Data compression using long common strings. In Data Compression Conference (1999), pp. 287-295.
* [7] BLOOM, B. H. Space/time trade-offs in hash coding with allowable errors. CACM 13, 7 (1970), 422-426.
* [8] BURROWS, M. The Chubby lock service for looselycoupled distributed systems. In Proc. of the 7th OSDI (Nov. 2006).
* [9] CHANDRA, T., GRIESEMER, R., AND REDSTONE, J.Paxos made live ? An engineering perspective. In Proc. of PODC (2007).
* [10] COMER, D. Ubiquitous B-tree. Computing Surveys 11, 2 (June 1979), 121-137.
* [11] COPELAND, G. P., ALEXANDER, W., BOUGHTER, E. E., AND KELLER, T. W. Data placement in Bubba. In Proc. of SIGMOD (1988), pp. 99-108.
* [12] DEAN, J., AND GHEMAWAT, S. MapReduce: Simplified data processing on large clusters. In Proc. of the 6th OSDI (Dec. 2004), pp. 137-150.
* [13] DEWITT, D., KATZ, R., OLKEN, F., SHAPIRO, L., STONEBRAKER, M., AND WOOD, D. Implementation techniques for main memory database systems. In Proc. of SIGMOD (June 1984), pp. 1-8.
* [14] DEWITT, D. J., AND GRAY, J. Parallel database systems: The future of high performance database systems. CACM 35, 6 (June 1992), 85-98.
* [15] FRENCH, C. D. One size ts all database architectures do not work for DSS. In Proc. of SIGMOD (May 1995), pp. 449-450.
* [16] GAWLICK, D., AND KINKADE, D. Varieties of concurrency control in IMS/VS fast path. Database Engineering Bulletin 8, 2 (1985), 3-10.
* [17] GHEMAWAT, S., GOBIOFF, H., AND LEUNG, S.-T. The Google file system. In Proc. of the 19th ACM SOSP (Dec.2003), pp. 29-43.
* [18] GRAY, J. Notes on database operating systems. In Operating Systems ? An Advanced Course, vol. 60 of Lecture Notes in Computer Science. Springer-Verlag, 1978.
* [19] GREER, R. Daytona and the fourth-generation language Cymbal. In Proc. of SIGMOD (1999), pp. 525-526.
* [20] HAGMANN, R. Reimplementing the Cedar file system using logging and group commit. In Proc. of the 11th SOSP (Dec. 1987), pp. 155-162.
* [21] HARTMAN, J. H., AND OUSTERHOUT, J. K. The Zebra striped network file system. In Proc. of the 14th SOSP(Asheville, NC, 1993), pp. 29-43.
* [22] KX.COM. kx.com/products/database.php. Product page.
* [23] LAMPORT, L. The part-time parliament. ACM TOCS 16,2 (1998), 133-169.
* [24] MACCORMICK, J., MURPHY, N., NAJORK, M., THEKKATH, C. A., AND ZHOU, L. Boxwood: Abstractions as the foundation for storage infrastructure. In Proc. of the 6th OSDI (Dec. 2004), pp. 105-120.
* [25] MCCARTHY, J. Recursive functions of symbolic expressions and their computation by machine. CACM 3, 4 (Apr. 1960), 184-195.
* [26] O’NEIL, P., CHENG, E., GAWLICK, D., AND O’NEIL, E. The log-structured merge-tree (LSM-tree). Acta Inf. 33, 4 (1996), 351-385.
* [27] ORACLE.COM. www.oracle.com/technology/products/database/clustering/index.html. Product page.
* [28] PIKE, R., DORWARD, S., GRIESEMER, R., AND QUINLAN, S. Interpreting the data: Parallel analysis with Sawzall. Scientific Programming Journal 13, 4 (2005), 227-298.
* [29] RATNASAMY, S., FRANCIS, P., HANDLEY, M., KARP, R., AND SHENKER, S. A scalable content-addressable network. In Proc. of SIGCOMM (Aug. 2001), pp. 161-172.
* [30] ROWSTRON, A., AND DRUSCHEL, P. Pastry: Scalable, distributed object location and routing for largescale peer-to-peer systems. In Proc. of Middleware 2001(Nov. 2001), pp. 329-350.
* [31] SENSAGE.COM. sensage.com/products-sensage.htm. Product page.
* [32] STOICA, I., MORRIS, R., KARGER, D., KAASHOEK, M. F., AND BALAKRISHNAN, H. Chord: A scalable peer-to-peer lookup service for Internet applications. In Proc. of SIGCOMM (Aug. 2001), pp. 149-160.
* [33] STONEBRAKER, M. The case for shared nothing. Database Engineering Bulletin 9, 1 (Mar. 1986), 4-9.
* [34] STONEBRAKER,M., ABADI, D. J., BATKIN, A., CHEN, X., CHERNIACK, M., FERREIRA, M., LAU, E., LIN, A., MADDEN, S., O’NEIL, E., O’NEIL, P., RASIN, A., TRAN, N., AND ZDONIK, S. C-Store: A columnoriented DBMS. In Proc. of VLDB (Aug. 2005), pp. 553-564.
* [35] STONEBRAKER, M., AOKI, P. M., DEVINE, R., LITWIN, W., AND OLSON, M. A. Mariposa: A new architecture for distributed data. In Proc. of the Tenth ICDE(1994), IEEE Computer Society, pp. 54-65.
* [36] SYBASE.COM. www.sybase.com/products/databaseservers/sybaseiq. Product page.
* [37] ZHAO, B. Y., KUBIATOWICZ, J., AND JOSEPH, A. D. Tapestry: An infrastructure for fault-tolerant wide-area location and routing. Tech. Rep. UCB/CSD-01-1141, CS Division, UC Berkeley, Apr. 2001.
* [38] ZUKOWSKI, M., BONCZ, P. A., NES, N., AND HEMAN, S. MonetDB/X100 ?A DBMS in the CPU cache. IEEE Data Eng. Bull. 28, 2 (2005), 17-22.

