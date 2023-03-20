---
layout: '../../layouts/MarkdownPost.astro'
title: 'Docker删除镜像及容器'
pubDate: 2023-03-20
description: 'docker删除相关无用镜像及容器'
author: 'UncleLLD'
cover:
    url: 'https://i.328888.xyz/2023/03/20/PdXit.jpeg'
    alt: 'cover'
tags: ["Docker"]
theme: 'dark'
featured: true
---


## 问题
清理服务器相关无用docker镜像及容器。

## 删除镜像image
要删除 Docker 中的镜像，可以使用 `docker rmi` 命令。请注意，删除镜像前请确保不再需要它，并且已经停止了使用该镜像的所有容器。否则，需要首先删除使用该镜像的容器。

以下是删除镜像的步骤：

* 确定要删除的镜像的名称或 ID。可以使用 `docker images` 命令查看拥有的所有镜像。该命令将列出每个镜像的名称、版本、镜像 ID、创建时间和大小等信息。

* 运行 `docker rmi` 命令来删除指定的镜像。例如，要删除名为 `my-image` 的镜像，可以运行以下命令：

 ```
   docker rmi my-image
   ```

   如果要删除的镜像有多个标签，请指定标签名来删除特定标签的镜像。例如：

  ```
   docker rmi my-image:1.0
   ```

   如果想要删除所有没有被使用的镜像，可以运行以下命令：

   ```
   docker image prune
   ```

   此命令将删除所有未被使用的镜像。如果您想要删除所有镜像，可以添加 `--all` 参数：
   ```
   docker image prune --all
   ```

   此命令将删除所有镜像，即使它们当前正在使用。

* 运行 `docker images` 命令检查镜像是否已成功删除。

注意，删除镜像时可能需要一些时间，具体取决于镜像的大小和您的系统性能。

## 删除容器
要删除 Docker 中的容器，可以使用 `docker rm` 命令。请注意，删除容器前请确保不再需要它，并且该容器已经停止运行。否则，需要首停止运行该容器。

以下是删除容器的步骤：

* 确定要删除的容器的名称或 ID。可以使用 `docker ps` 命令查看正在运行的所有容器，包括它们的名称、ID、状态和所使用的映像。

* 运行 `docker rm` 命令来删除指定的容器。例如，要删除名为 `my-container` 的容器，可以运行以下命令：

   ```
   docker rm my-container
   ```

   如果要删除多个容器，可以在命令后面添加多个容器名称或 ID。

   ```
   docker rm container1 container2
   ```

   如果想要删除所有已停止的容器，可以运行以下命令：

   ```
   docker container prune
   ```

   此命令将删除所有已停止的容器。如果您想要删除所有容器，包括正在运行的容器，可以添加 `--all` 参数：

   ```
   docker container prune --all
   ```

   此命令将删除所有容器，即使它们当前正在运行。

* . 运行 `docker ps` 命令检查容器是否已成功删除。

请注意，删除容器时可能需要一些时间，具体取决于容器的大小和您的系统性能。


## 注意
* 如果`docker`命令无法操作时候请使用`sudo docker`
* 操作的时候一定要小心谨慎，避免删错