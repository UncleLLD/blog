---
layout: '../../layouts/MarkdownPost.astro'
title: '修改Linux时间'
pubDate: 2023-12-04
description: '定时任务不准确，调整Linux时区及时间'
author: 'UncleLLD'
cover:
    url: 'https://i.imgs.ovh/2023/12/04/3Cgad.th.png'
    alt: 'cover'
tags: ["工具"]
theme: 'dark'
featured: true

---

## 修改linux时间

## 问题

在检测海外服务器日志的时候，发现脚本启动时间与定时任务设定的时间不一致，现进行问题排查。

## 基本信息

在Linux服务器上，有三个不同的时间概念：本地时间（local time），协调世界时（universal time）和实时时钟时间（RTC time）。

* 本地时间是指服务器当前所在地的当地时间，它受到时区设置的影响。本地时间是我们通常使用的时间表示方式，用于显示在服务器上的日志记录和用户界面中。

* 协调世界时（UTC）是一种标准的全球时间标准，与时区无关。它是基于原子钟的精确时间，被广泛用于网络通信、时间同步和跨时区的数据处理。

* 实时时钟（RTC）是服务器硬件上的一个时钟芯片，它负责在服务器断电后继续保持时间的记录。RTC时间通常是一个独立的硬件时钟，它可以在服务器重新启动时提供持久性的时间记录。

时区设置是为了将本地时间与全球标准时间（通常是协调世界时，UTC）进行对应，并考虑到地理位置的时差和夏令时调整等因素。

在Linux中，时区信息存储在`/usr/share/zoneinfo`目录下的文件中，每个文件代表一个特定的时区。时区文件的命名方式通常遵循"大洲/城市"的格式，例如`Asia/Shanghai`表示亚洲上海时区。

## 定时任务

在Linux系统中，`crontab`设置的定时任务是以服务器的本地时间为准的。`crontab`是一种用于在指定时间间隔内自动执行任务的工具，它使用的时间基准是服务器的本地时间。

**当通过`crontab -e`编辑并设置定时任务时，需要指定任务执行的时间和日期。这些时间和日期应该与服务器的本地时间一致。** 可以使用`date`命令来查看服务器的本地时间。



## 解决方法

* 1. 检查时区是否正确，先将时区调整到当地对应的时区，调整方法：

  * 查看系统时区，并查看所有支持时区

  ```shell
  timedatectl
  timedatectl list-timezones
  ```

  ![timezones|inline](https://i.imgs.ovh/2023/12/04/3CRLT.png)

  * 使用`timedatectl`命令设置系统时区。例如，将系统时区设置为美洲时区：

  ```shell
  sudo timedatectl set-timezone America/Indiana/Indianapolis
  ```

  * 查看时区是否已更改，再次输入

  ```shell
  timedatectl
  ```

![before|inline](https://i.imgs.ovh/2023/12/04/3Cgad.png)

* 2. 由于conrtab定时任务是以本地时间为准，并将RTC时间与本地时间保持一致，你可以通过以下步骤来修改：

  * 首先，使用`timedatectl`命令来查看当前的本地时间和UTC时间。执行以下命令：

  ```shell
  timedatectl  # 这将显示当前的本地时间和UTC时间
  ```

  * 接下来，可以使用以下命令将RTC时间设置为当前的本地时间

  ```shell
  sudo hwclock --systohc --localtime  # 这将RTC时间设置为当前的本地时间
  ```

  * 最后，再次运行`timedatectl`命令来确认时间更改是否生效。

  ```shell
  timedatectl
  ```

![now|inline](https://i.imgs.ovh/2023/12/04/3Ce1l.png)

## 注意

**修改服务器时间需要谨慎操作，特别是在生产环境中。确保对操作有足够的了解，并在必要的情况下备份重要的数据**。如果不确定如何进行操作，建议咨询系统管理员或专业人士的帮助，谨慎操作！

 

## 参考

* [https://support.huawei.com/enterprise/zh/doc/EDOC1000099509/8f0c899b](https://support.huawei.com/enterprise/zh/doc/EDOC1000099509/8f0c899b)

* [https://www.cnblogs.com/ljy2013/p/4615149.html](https://www.cnblogs.com/ljy2013/p/4615149.html)

  

