---
layout: '../../layouts/MarkdownPost.astro'
title: 'Asciinema：一款强大的终端录屏工具'
pubDate: 2024-04-30
description: 'Asciinema：一款强大的终端录屏工具'
author: 'UncleLLD'
cover:
    url: 'https://img2.imgtp.com/2024/04/18/WRpNdGlx.png'
    alt: 'cover'
tags: ["工具"]
theme: 'dark'
featured: true
工具
---



# 终端录屏工具asciinema

![|inline](https://img2.imgtp.com/2024/04/18/WRpNdGlx.png)

最近看见一个好的终端录屏工具，现在记录一下并进行分享。

**地址：**

* [asciinema.org](https://asciinema.org/)

**功能：**

* 一个免费和开源的解决方案，用于记录终端会话并在网上分享

**特点：**

* **轻松录制终端**：直接在终端内进行录制。开始录制，请输入命令 `asciinema rec demo.cast`，结束时请按 `ctrl+d` 或输入 `exit`。
* **轻松复制粘贴**：观看录制内容时，暂停播放器，然后复制粘贴您需要的内容。毕竟，这些都只是文本。
* **轻松嵌入**：轻松将 asciinema 播放器嵌入到您的博客文章、项目文档或会议演讲的幻灯片中 。



## 安装

* **pip/pipx**

```shell
pipx install asciinema

python3 -m pip install asciinema
```

* Linux

  - Arch Linux

  ```shell
  sudo pacman -S asciinema
  ```

  * Debian

  ```shell
  sudo apt install asciinema
  ```

  * Fedora

  ```shell
  sudo dnf install asciinema
  ```

  * Gentoo

  ```shell
  sudo emerge -av asciinema
  ```

* Ubuntu

```shell
sudo apt install asciinema
```

* MacOS

```shell
brew install asciinema
```

* FreeBSD

```shell
cd /usr/ports/textproc/py-asciinema && make install
```

* 源码安装

```shell
git clone https://github.com/asciinema/asciinema.git
cd asciinema
git checkout main
python3 -m asciinema --version
```



## 使用

### 记录Record

现在开始记录终端操作：

输入以下命令开始记录：

```shell
asciinema rec demo.cast
```

这将启动一个新的录制会话。在此会话期间，终端中显示的所有内容都将被捕捉并保存为 asciicast 格式的 `demo.cast` 文件。

想结束录制会话时，退出 shell 即可。这可以通过按 `ctrl+d` 或输入 `exit` 命令来实现。

### 重放Replay

通过以下命令回放录制内容：

```shell
asciinema play demo.cast
```

播放过程中，您可以按空格键暂停或恢复，或按 `ctrl+c` 提前结束播放。暂停的时候可以直接复制视频中的内容。

### 分享Sharing

虽然在终端中回放录制内容很方便，但与在互联网上与更广泛的观众分享相比，其作用相对有限。

可以在 [asciinema.org](asciinema.org) 上托管您的录制内容，这是一个由 asciinema 服务器支持的终端录制专用托管平台。虽然将录制内容托管在 asciinema.org 上是可选的，但这会带来许多便利，如轻松分享和嵌入。

使用以下命令将录制内容上传到 asciinema.org：

```shell
asciinema upload demo.cast
```

执行该命令后，会显示一个私密链接，指向可以在其中查看录制内容的页面。可以自由地将这个链接分享给任何人。

录制页面上的播放器组件并非传统视频播放器，而是专为播放终端会话而构建的 asciinema 播放器。它允许复制其终端视图的内容，就像在普通终端中一样。**可以尝试：暂停播放，选择一些文本，复制，然后粘贴到其他地方。**

如果想在 asciinema.org 上管理录制内容（例如设置标题、更改终端颜色主题），需要将上传内容与 asciinema.org 用户账户关联。为此，请运行：

```shell
asciinema auth
```

这个命令会显示一个 URL，当在网页浏览器中打开时，该 URL 会将 CLI 与 asciinema.org 账户进行认证，确保能访问上传内容。

### 嵌入Embedding

asciinema 播放器可以通过在网页中嵌入 HTML `<script>` 标签来在任何网站上使用。这种嵌入的播放器常被用于博客文章、项目文档和会议演讲的幻灯片中。

上传到 asciinema.org 的所有录制内容都可以通过使用在录制页面上提供的脚本片段来嵌入到网站。这个脚本片段的样式如下：

<script async id="asciicast-569727" src="https://asciinema.org/a/569727.js"></script>

要获取这个脚本，请访问录制内容的页面，点击“分享”按钮，并从“嵌入播放器”部分复制脚本。

上面的 `<script>` 标签被放置在这段文字的正下方，这样就生成了下面的播放器。

[![asciicast|inline](https://asciinema.org/a/bJMOlPe5F4mFLY0Rl6fiJSOp3.svg)](https://asciinema.org/a/bJMOlPe5F4mFLY0Rl6fiJSOp3)

 如果不想依赖 asciinema.org 来嵌入演示，可以在网站上这样使用独立的播放器：

```html
<!DOCTYPE html>
<html>
<head>
  ...
  <link rel="stylesheet" type="text/css" href="/asciinema-player.css" />
  ...
</head>
<body>
  ...
  <div id="demo"></div>
  ...
  <script src="/asciinema-player.min.js"></script>
  <script>
    AsciinemaPlayer.create('/demo.cast', document.getElementById('demo'));
  </script>
</body>
</html>
```

## 自托管服务器

虽然 asciinema.org 是 CLI 用于上传录制内容的默认 asciinema 服务器，**但如果希望完全掌控录制内容，可以选择自行托管服务器实例。**

asciinema 服务器被制作为 OCI 容器镜像，可以在 [ghcr.io/asciinema/asciinema-server](https://github.com/asciinema/asciinema-server/pkgs/container/asciinema-server) 上找到它。

以下是一个基本的 docker-compose 示例： 

```yaml
services:
  asciinema:
    image: ghcr.io/asciinema/asciinema-server:latest
    ports:
      - '4000:4000'
    volumes:
      - asciinema_data:/var/opt/asciinema
    depends_on:
      postgres:
        condition: service_healthy

  postgres:
    image: docker.io/library/postgres:14
    volumes:
      - postgres_data:/var/lib/postgresql/data
    environment:
      - POSTGRES_HOST_AUTH_METHOD=trust
    healthcheck:
      test: ['CMD-SHELL', 'pg_isready -U postgres']
      interval: 2s
      timeout: 5s
      retries: 10

volumes:
  asciinema_data:
  postgres_data:
```

使用以下命令启动：

```shell
docker compose up
```

接着，通过设置 ASCIINEMA_API_URL 环境变量，将 asciinema CLI 指向您的服务器：

```shell
export ASCIINEMA_API_URL=http://localhost:4000
```

然后录制并上传演示：

```shell
asciinema rec demo.cast
asciinema upload demo.cast
```

> 注意，上述配置仅适用于在本地测试服务器。要了解如何以完整功能和安全的方式设置服务器，请参阅完整的[服务器自托管指南](https://docs.asciinema.org/manual/server/self-hosting/)。

## 生成GIF

在那些不支持`<script>` 标签但支持 `<img>` 标签的网站上，可以通过动画 GIF 文件来嵌入演示。

使用 agg 工具从录制文件创建 GIF：

```shell
agg demo.cast first.gif
```

如果您有条件使用 `<script>` 标签，使用 asciinema 播放器展示录制内容通常比使用 GIF 文件更佳。与 GIF 相比，播放器支持暂停、回放、复制文本，并且始终能够以最佳清晰度显示终端内容。

![https://docs.asciinema.org/manual/agg/demo.gif](https://docs.asciinema.org/manual/agg/demo.gif)

