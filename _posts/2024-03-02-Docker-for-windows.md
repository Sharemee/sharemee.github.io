---
layout: post
title: Docker for windows
---

声明: 所有命令都使用全称以免出现命令混淆或者含义不清晰等问题

在 Windows 下使用 Docker 的一些记录:

- [docker-desktop 和 docker-desktop-data](#docker-desktop-和-docker-desktop-data)
- [目录映射格式](#目录映射格式)
- [wsl](#wsl)
- [Docker 创建的 Volume 存储在哪](#docker-创建的-volume-存储在哪)

## docker-desktop 和 docker-desktop-data

在 Windows 下安装 Docker 的时候, 会同时安装 docker-desktop 和 docker-desktop-data 两个应用, 两个应用的作用是: docker-desktop 是 docker 的客户端, docker-desktop-data 是 docker 的数据存储.

## 目录映射格式

```powershell
# linux 下执行目录映射
docker run -rm --volume /var/data:/var/data jekyll/jekyll jekyll build .

# Windows 下目录映射
docker run -rm --volume /D/data:/var/data jekyll/jekyll jekyll build .

# 数据卷挂载到容器内部
docker volume create test-volume # 创建数据卷
docker run -rm --volume test-volume:/var/data jekyll/jekyll jekyll build .
```

## wsl

wsl 的全称是: Windows Subsystem for Linux

查看微软官方文档: <https://docs.microsoft.com/zh-cn/windows/wsl>

首次安装需要执行如下命令:

```powershell
> wsl --update
正在安装: 适用于 Linux 的 Windows 子系统
已安装 适用于 Linux 的 Windows 子系统。
```

## Docker 创建的 Volume 存储在哪

```powershell
# 查看 Volume 列表
docker volume list

# 查看 test-volume 详细信息
docker inspect test-volume
#返回的是一个 JSON 格式
#[
#  {
#    "CreatedAt": "2024-03-02T12:14:14Z",
#    "Driver": "local",
#    "Labels": null,
#    "Mountpoint": "/var/lib/docker/volumes/test-volume/_data",
#    "Name": "test-volume",
#    "Options": null,
#    "Scope": "local"
#  }
#]

# Windows 下并不存在 "/var", 所以在哪呢?

#\\wsl.localhost\docker-desktop-data\data\docker
```

在资源管理器的地址栏中输入 `\\wsl.localhost\docker-desktop-data\data\docker`

在这个文件夹里有2个比较特殊的文件夹: `containers`, `volumes`

从名字里就可以看出来了 `Docker 创建的 Volume` 保存在 `\\wsl.localhost\docker-desktop-data\data\docker\volumes` 中
