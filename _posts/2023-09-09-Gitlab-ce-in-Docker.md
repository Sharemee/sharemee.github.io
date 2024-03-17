---
layout: post
title: Gitlab-ce in Docker
---

容器第一次安装启动需要比较长的时间, 我的机器大概用了12分钟左右, 需要耐心等待。

正常运行时内存消耗量大概在 5 ~ 6 GB。

我的是开发环境不需要启动 Docker 就运行容器, 因此不带 "--restart always" 参数。

gitlab 共使用3个端口, 443端口看情况可以不映射:

- 22: &nbsp;&nbsp;以 ssh 方式 Clone/Push 仓库
- 80: &nbsp;&nbsp;以 http 方式Clone/Push 仓库, 访问 Web 界面
- 443: 以 https 方式 Clone/Push 仓库, 访问 Web 界面

## 安装配置命令

配置须知:

1. 启动容器后需要需要等待12分钟左右, 等容器内部安装并初始化完成
2. 我本地80端口有其它程序占用, 因此 gitlab 映射的端口一律增加 5000,
   因此Web后台的访问地址便是 [http://localhost:5080](http://localhost:5080)
3. 仓库界面显示的 Clone 地址是不带端口号的,
   映射了其它端口的需要自己改下地址把端口号加上
   - http: <http://localhost:5080/user/repo.git>
   - ssh: &nbsp;ssh://localhost:5022/user/repo.git

### 1. 拉取官方的镜像

```powershell
# gitlab-ce: ce 表示社区版
docker pull gitlab/gitlab-ce
```

### 2. 创建存储卷, 若直接映射宿主机的目录可跳过

```powershell
# 存储gitlab的配置文件, 首次安装保留24小时的root密码文件也在其中
docker volume create gitlab-config-volume
# 存储gitlab的数据, 例如: 仓库代码等
docker volume create gitlab-data-volume
# 存储gitlab的程序日志, 数据库日志, ssh日志, nginx日志等
docker volume create gitlab-log-volume
```

### 3. 创建并运行容器

```powershell
docker run `
--detach `
--hostname docker-gitlab `
--name gitlab-ce `
--publish 5022:22 `
--publish 5080:80 `
--publish 5443:443 `
--volume gitlab-config-volume:/etc/gitlab:Z `
--volume gitlab-data-volume:/var/opt/gitlab:Z `
--volume gitlab-log-volume:/var/log/gitlab:Z `
--env GITLAB_OMNIBUS_CONFIG="external_url 'http://192.168.31.20'" `
--shm-size 256m `
gitlab/gitlab-ce

# --detach:   后台运行容器
# --hostname: 指定容器主机名称
# --name:     指定容器的名称
# --publish:  本地端口映射到容器内部的端口, 末尾的 ":Z" 照抄即可
# --volume:   本地存储卷挂载到容器内部的文件系统目录中
# --shm-size: 设置共享内存大小
# --env:      设置环境变量 GITLAB_OMNIBUS_CONFIG="external_url 'http://192.168.31.20'"
#             在Web界面Clone地址栏里显示的值, 端口默认是80
#             地址后面的端口号会影响gitlab的配置
#             因此不建议加端口在地址末尾, 否则你可能会遇到无法访问的问题
```

### 4. 可以使用以下命令查看容器的健康状态

```powershell
docker ps gitlab-ce
# 若 STATUS 一栏输出的状态信息是 (healthy), 则可以尝试访问Web管理界面
```

## 登录使用指导

(...未完待续)
