---
layout:     post                       
title:      快速部署使用Watchtower
subtitle:   
description: 自动更新 Docker 容器基础镜像
date:       2023-11-26                
author:     369                        
header-img: img/IMG_0000.JPG     
catalog: true                       
tags:                               
    - Docker
    - 工具
---

## 1 前言

> 项目的GitHub地址： [watchtower](https://github.com/containrrr/watchtower) 
> 
> 官网地址： [Watchtower官网](https://containrrr.dev/watchtower/) 

在软件开发中，特别是在使用容器化技术（如Docker）的环境中，Watchtower可以自动检测基础镜像和应用程序的更新，并在发现新版本时触发更新过程。

对于Docker用户来说，Watchtower是一个流行的开源工具，可以定期检查Docker Hub或私有仓库中的镜像，并自动更新正在运行的容器。这有助于确保应用程序使用的是最新的软件版本，提高安全性和性能。

## 2 步骤

> 由于部署是基于Docker，所以要确保你已安装好了`Docker`。
> 
> 如果已安装`Docker`，可以忽略步骤2.1。

### 2.1 安装Docker

使用 Docker 官方安装脚本快速安装：

```bash
curl -sSL https://get.docker.com/ | sh
```

命令具体解释：

 `curl`: 是一个用于在命令行下传输数据的工具，支持多种协议，包括 HTTP 和 HTTPS。在这里，它被用于下载文件。 

其中`-sSL`: 

- `-s` 表示 "silent"，即在执行过程中不输出任何信息。

- `-S` 表示 "show error"，即在发生错误时显示错误信息。

- `-L` 表示 "follow redirects"，即在遇到重定向时，`curl` 会继续跟随重定向。
  
  综合起来，`-sSL` 的作用是以静默模式下载文件并在发生重定向时继续跟随。 
1. `https://get.docker.com/`: 这是要下载的文件的 URL，指向 Docker 官方安装脚本。
2. `|`: 管道操作符，将 `curl` 命令的输出传递给下一个命令。
3. `sh`: 是 shell 的命令，用于执行脚本。在这里，它执行通过 `curl` 下载的脚本。

### 2.2  **运行 Watchtower 容器**

#### 1. 运行容器

使用下面命令在后台运行  Watchtower 容器： 

```bash
docker run -d \
    --name watchtower \
    -v /var/run/docker.sock:/var/run/docker.sock \
    containrrr/watchtower
```

这个命令会创建一个名为 `watchtower` 的容器，并将 Docker 守护进程的 socket 映射到 Watchtower 容器，以便 Watchtower 可以与 Docker 通信。

查看该容器日志：

```bash
docker logs watchtower
```

#### 2. 定期检查更新

 Watchtower 默认每 24 小时检查一次更新。如果你想自定义检查更新的频率，可以使用 `--interval` 参数。例如，要每小时检查一次更新，可以使用以下命令： 

```bash
docker run -d \
    --name watchtower \
    -v /var/run/docker.sock:/var/run/docker.sock \
    containrrr/watchtower \
    --interval 3600
```

#### 3. 指定容器监控

默认情况下，watchtower 将监控在它所指向的 Docker 守护进程中运行的所有容器 。

例如，监控nginx，redis：

```bash
docker run -d \
    --name watchtower \
    -v /var/run/docker.sock:/var/run/docker.sock \
    containrrr/watchtower \
    nginx redis
```

#### 4. 其他参数

比如，`--cleanup`， 更新后删除旧镜像 ，使用此选项可防止在更新容器时在系统上累积孤立镜像。 更多参数请查看：[Arguments - Watchtower](https://containrrr.dev/watchtower/arguments/)


