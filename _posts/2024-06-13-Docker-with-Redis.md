---
layout: post
title: Docker with Redis
---

## 安装

```powershell
# 拉取镜像
docker pull redis:latest

# 创建存储卷
docker volume create redis

# 创建并运行容器
docker run `
--detach `
--name redis `
--hostname redis `
--publish 6379:6379 `
--volume redis:/data `
redis:latest
```

注意看镜像信息, 若不指定存储卷则其将自动创建一个存储卷

自动创建的存储卷的名称是一个哈希字符串, 不便于查看其所属于哪个容器, 因此还是自己指定一个命名的存储卷好些
