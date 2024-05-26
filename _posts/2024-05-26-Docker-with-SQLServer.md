---
layout: post
title: Docker with SQL Server 2022
---

通过 Docker 安装 SqlServer 2022, 用于学习开发使用

## 安装

```powershell
# 拉取镜像
docker pull mcr.microsoft.com/mssql/server:2022-latest

# 创建数据卷用以保留容器内的数据(--label 可不加)
docker volume create sqlserver --label version="2022" --lablel tag="2022-latest" --label env="dev"

# 创建并运行容器
docker run `
--detach `
--name sqlserver-dev `
--hostname sqlserver `
--env "ACCEPT_EULA=Y" `
--env "MSSQL_SA_PASSWORD=Aa123456" `
--volume sqlserver:/var/opt/mssql `
--publish 1433:1433 `
mcr.microsoft.com/mssql/server:2022-latest
```

官方文档: <https://learn.microsoft.com/zh-cn/sql/linux/quickstart-install-connect-docker?view=sql-server-ver16&tabs=cli&pivots=cs1-bash>

## 通过 `Visual Studio` 连接并管理 `SQL Server 2022`

1. 在 `Visual Studio` 菜单栏中找到 `视图(V)` 选项卡
2. 在 `视图(V)` 选项卡中找到 `SQL Server 资源管理器`
3. 在 `SQL Server 资源管理器` 中找到 `添加 SQL Server` 入口
4. `添加 SQL Server` 会弹出一个连接到 SQL Server 的配置窗口
5. 填入 SQL Server 服务器地址: localhost
6. 将身份验证改成 `SQL Server 身份验证`
7. 用户名: sa
8. 密码: 你懂的
9. 将 `信任服务器证书` 改成 `True`

这样基本上就可以连接上了, 如果你没改默认映射的 1433 端口的话
