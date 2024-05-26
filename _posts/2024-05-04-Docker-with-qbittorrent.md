---
layout: post
title: Docker with qbittorrent
---

## 安装

```powershell
# 拉取镜像
docker pull lscr.io/linuxserver/qbittorrent:latest

# 创建并启动容器
docker run -d `
--name=qbittorrent `
--env PUID=1000 `
--env PGID=1000 `
--env TZ=Asia/Shanghai `
--env UMASK_SET=022 `
--env WEBUI_PORT=50010 `
--publish 50010:50010 `
--publish 50011:6881 `
--publish 50011:6881/udp `
--volume /D/Docker/qbittorrent/config:/config `
--volume /D/Docker/qbittorrent/downloads:/downloads `
linuxserver/qbittorrent
```

## 配置说明

配置文件地址(容器内):

/config/qBittorrent/qBittorrent.conf

### 关于 WEBUI_PORT

1. 这个是访问管理界面的端口
2. 默认值是8080
3. 不要去修改配置文件中的值，因为修改了，容器重启后，配置文件会恢复到默认值
4. 若要修改该端口, 需要在启动容器时, 添加 `--env WEBUI_PORT=50010`, 以环境变量的方式指定
5. 这个端口必须内外一致, 即 `--publish 50010:50010` 否则在访问登录界面的时候会显示未授权字符串, HTTP 状态码为 401
