---
layout: '../../layouts/MarkdownPost.astro'
title: 'BATSIGN 世界上最简单的个人电子邮件通知 API'
pubDate: 2024-02-06
description: 'BATSIGN 世界上最简单的个人电子邮件通知 API'
author: 'UncleLLD'
cover:
    url: 'https://i0.imgs.ovh/2024/02/05/bJmR9.th.png'
    alt: 'cover'
tags: ["工具"]
theme: 'dark'
featured: true

---


## 引言

今天看邮箱，发现有封邮件，在垃圾箱，看了一眼挺真诚的不是骗子，应该是应用者进行宣传什么的，也挺不容易的。

![bJmR9.png|inline](https://i0.imgs.ovh/2024/02/05/bJmR9.png)

注意：基于安全反欺诈宣传这类链接一般不要随便点，以免造成财产物品损失。

## 试用

该网站地址[https://batsign.me](https://batsign.me/)， 打开界面很简洁，页面如下：

![bJ6qO.png|inline](https://i0.imgs.ovh/2024/02/05/bJ6qO.png)

注册非常简单，只需要在上述框中填入个人的邮件地址，然后会收到验证信息并进行验证即可。

![bJ0oD.png|inline](https://i0.imgs.ovh/2024/02/05/bJ0oD.png)

验证完毕后，就会收到个人单独的地址：

![bJBYo.png|inline](https://i0.imgs.ovh/2024/02/05/bJBYo.png)

假如这里个人的url地址是：https://batsign.me/at/your@e.mail/12345



下面是一些使用方法，涵盖很多方式：

### Command line

**任务完成**

```shell
dd if=linux-amd64.iso of=/dev/sdd bs=1M  ; curl https://batsign.me/at/your@e.mail/12345

python epic_modelling_job.py ; curl https://batsign.me/at/your@e.mail/12345
```

### Cron

**通知服务失败**

```shell
# m h  dom mon dow   command
  0 *  *   *   *     systemctl is-active --quiet apache2 || curl --data "panic.." https://batsign.me/at/your@e.mail/12345
```

服务器重启通知

```bash
# m h  dom mon dow   command
@reboot curl --data "$(hostname) was rebooted at: `date +%m.%d.%Y.%H.%M`" https://batsign.me/at/your@e.mail/12345
```

### Bash

**从 sqlite 发送报告**

```bash
#!/bin/bash
curl --data "$(
    sqlite3 db.sqlite3 -list -header  "
        select count(*) 'Total Records'
        from users
    " | column -t -s'|')
" https://batsign.me/at/your@e.mail/12345
```

### Python

**可以放到Jupyter中提示任务运行结束**

```python
import urllib
urllib.request.urlopen("https://batsign.me/at/your@e.mail/12345")
```

**发送内容**

```python
import requests
requests.post("https://batsign.me/at/your@e.mail/12345", "story boss")
```

### Jenkins

**通知部署新代码**

```bash
stage('build') {
    steps {
        sh 'make'
    }
}
stage('alert') {
    steps {
        sh 'curl --data "new version deployed." https://batsign.me/at/your@e.mail/12345'
    }
}
```

**通知构建管道失败**

```bash
try {
    sh 'mvn test'
} catch (e) {
    currentBuild.result = 'FAILURE'
    throw e
} finally {
    sh 'curl --data "Tests failed!." https://batsign.me/at/your@e.mail/12345'
}
```



运行实验：

`curl https://batsign.me/at/your@e.mail/12345`

结果：

![bJxzs.png|inline](https://i0.imgs.ovh/2024/02/05/bJxzs.png)

## 其他

* 可以看到上面写着“无需信用卡”——这是否意味着这是一项免费服务，或者您将向我收取升级费用，如果是的话，价格是多少？
  *  Batsign Personal 是一项免费服务，没有专门收费的计划。
* Batsign.me 缓解了什么痛点？ 
  * 设置电子邮件服务器可能既复杂又痛苦。安装电子邮件客户端是一个额外的系统依赖项，也需要进行配置。 Batsign 的构建是为了让设置电子邮件通知变得非常容易。
* 限制？ 
  * 只能给自己发送一封电子邮件。电子邮件只能是纯文本。您无法更改主题行。电子邮件正文只能是一定长度。
* 使用Batsign与仅使用sendmail相比有何不同？
  * 启用个人电子邮件通知在Batsign上是一个一步过程。而sendmail的设置需要多个步骤。如果已经能够访问一个sendmail服务器，那么两者之间可能没有太大差别，尽管仍然需要配置一个电子邮件客户端。
* 为什么POST请求中需要包含"/your@e.mail/"，使用随机的UUID来防止伪造的Batsign不是更好吗？
  * URL中的电子邮件地址使得容易确定消息将发送到的地址。对此持开放态度，愿意进行更改。每个URL都添加了一个随机的安全令牌，以使伪造变得不可能。

## 参考

* [https://batsign.me/](https://batsign.me/)
