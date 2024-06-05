---
layout: post
title: Docker with mongodb
---

## 安装

```powershell
# 拉取镜像
docker pull docker.io/library/mongo:latest

# 创建存储卷
docker volume create mongodb

# 创建并运行容器
docker run `
--detach `
--name mongo `
--hostname mongodb `
--publish 27017:27017 `
--volume mongodb:/data `
mongo:latest
```
