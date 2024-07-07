---
layout: post
title: EF DbFirst
---

所谓的 `DbFirst` 是从已存在的数据库中生成代码中的实体结构类, 是一种自动生成代码的行为.

请仔细阅读 `先决条件` 部分内容, 选定其中一种方式即可, 全都要也可以.

## 目录

- [1. 先决条件](#1-先决条件)
  - [1.1 使用 `.NET CLI` 方式](#11-使用-net-cli-方式)
  - [1.2 使用 `PMC` 方式](#12-使用-pmc-方式)
- [2. 必需参数](#2-必需参数)
  - [2.1 数据库连接字符串](#21-数据库连接字符串)
- [3. 完整示例和说明](#3-完整示例和说明)
  - [3.1 使用 `.NET CLI` 来执行反向工程操作](#31-使用-net-cli-来执行反向工程操作)
  - [3.2 使用 `PMC` 来执行反向工程操作](#32-使用-pmc-来执行反向工程操作)
- [4. 对使用 `PMC` 和 `.NET CLI` 的一些看法](#4-对使用-pmc-和-net-cli-的一些看法)
- [5. 官方文档地址](#5-官方文档地址)

## 1. 先决条件

不管使用 `.NET CLI` 还是 `PMC` 方式都必须要安装 `数据库提供程序` NuGet 包, 例如: `Microsoft.EntityFrameworkCore.SqlServer`.

### 1.1 使用 `.NET CLI` 方式

1.你需要安装 `dotnet ef` 工具到本地或者全局, 大多数开发人员偏向于安装到全局:

```powershell
dotnet tool install dotnet-ef --global
```

使用下列命令更新工具:

```powershell
dotnet tool update dotnet-ef --global
```

运行以下命令, 验证是否已正确安装 `EF Core CLI` 工具:

```powershell
dotnet ef
# 或者下面这个命令, 都是可以的
dotnet-ef
```

2.还需要安装 `Microsoft.EntityFrameworkCore.Design` NuGet 包:

```powershell
Install-Package Microsoft.EntityFrameworkCore.Design
```

### 1.2 使用 `PMC` 方式

你需要向项目中安装 `Microsoft.EntityFrameworkCore.Tools` NuGet 包:

```powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

1. 安装 `Microsoft.EntityFrameworkCore.Tools` NuGet 包
2. 安装 `数据库提供程序` NuGet 包, 例如: `Microsoft.EntityFrameworkCore.SqlServer`
3. 若使用 `Visual Studio` 中的 `包管理器控制台` 则需要安装 `Microsoft.EntityFrameworkCore.Tools`

**#提问: 这3个 `NuGet 包` 安装到哪个项目中?**

答: 如果不想将自动生成的代码和入口项目放在一起, 则这两个NuGet包安装到新建的项目中, 反之则安装在入口项目中。

## 2. 必需参数

PMC 和 .NET CLI 命令都有两个必需的参数：数据库的连接字符串，以及要使用的 EF Core 数据库提供程序。

### 2.1 数据库连接字符串

当你准备将生成的代码放到另外一个单独的项目模块中和主项目分离时, 你必须在命令行参数中指定数据库连接字符串. 因为执行命令的目录在该模块项目根目录下, 而该模块项目不包含 .NET 应用程序使用托管模型和配置系统, 也不包含 `appsettings.json` 等配置文件.

如果 .NET 应用程序使用托管模型和配置系统, 例如: `ASP.NET Core项目`, **且生成的代码在同一个项目中**, 则可以使用 `Name=<connection-string>` 语法从配置( `appsettings.json` 或 `secrets.json` )中读取连接字符串.

例如, 在 `appsettings.json` 或 `secrets.json` 中如下配置:

```json
// appsettings.json 或 secrets.json
{
  "ConnectionStrings": {
    "SQLServer": ""
  },
}
```

则可以使用 `"Name=ConnectionStrings:SQLServer"` 替换命令中显示的明文数据库连接字符串。

```powershell
# 使用数据库连接字符串
# 推荐使用这种方式, 因为可以将生成的代码和主项目分离
dotnet ef dbcontext scaffold `
"Data Source=(LocalDb)\\MSSQLLocalDB;Database=AllTogetherNow" `
Microsoft.EntityFrameworkCore.SqlServer

# 使用项目中的配置文件来获取数据库连接字符串
# 注意: 生成的代码不能在单独的项目中必须和入口项目在一起才能使用这种方式
dotnet ef dbcontext scaffold `
"Name=ConnectionStrings:SQLServer" `
Microsoft.EntityFrameworkCore.SqlServer
```

## 3. 完整示例和说明

### 3.1 使用 `.NET CLI` 来执行反向工程操作

1. 必须设置设置一个启动项目(可在Visual Studio中设置)
2. 命令行中进入要保存自动生成代码的项目根目录中, 执行以下代码

```powershell
# .NET CLI
dotnet ef dbcontext scaffold `
"Name=ConnectionStrings:SQLServer" `
Microsoft.EntityFrameworkCore.SqlServer `
--context-dir ./ `
--output-dir Entities `
--context BlogDbContext `
--context-namespace Blog.Data `
--namespace Blog.Data.Entities `
--no-onconfiguring `
--force
```

可选参数说明:

1. `--output-dir` : 指定生成的所有代码文件保存目录, 建议配置到单独的目录中方便管理.
2. `--context-dir` : 指定生成的 `DbContext.cs` 文件保存目录, 建议配置防止该文件和大量实体类文件混杂在一起不方便查询, 让项目结构更加清晰明了.
3. `--context` : 指定 `DbContext` 的名称, 该配置将最终影响 `DbContext.cs` 文件的名称, 建议配置为 `xxxDbContext`.
4. `--context-namespace` : 指定 `DbContext` 的命名空间, 不建议配置, 生成工具自动会根据文件夹来设置合适的命名空间.
5. `--namespace` : 覆盖所有输出类的命名空间, 不建议配置,  生成工具自动会根据文件夹来设置合适的命名空间.
6. `--no-onconfiguring` : 禁止创建包含连接字符串的 OnConfiguring 方法
7. `--force` : 覆盖已经存在的文件, 当你修改了数据库需要再次生成实体模型的时候, 没有该参数将无法更新模型

### 3.2 使用 `PMC` 来执行反向工程操作

1. 必须设置设置一个启动项目(可在Visual Studio中设置)
2. 在 `包管理器控制台(PMC)` 中找到默认项目, 下拉选择一个项目, 自动生成的代码将保存到其中

```powershell
# PMC
Scaffold-DbContext # PMC 工具命令行
"Data Source=(LocalDb)\\MSSQLLocalDB;Database=AllTogetherNow" # *数据库连接字符串
"Name=ConnectionStrings:SQLServer" # 可以将上面的数据库连接字符串配置成从项目的配置文件中获取
Microsoft.EntityFrameworkCore.SqlServer # *指定数据库提供程序
-ContextDir ./ # 指定生成的 DbContext.cs 文件位置, 这里保存到项目根目录
-OutputDir Entities # 指定生成的所有代码文件位置, 这里保存到 Entities 文件夹中, 注意: 若不设置--context-dir 选项则会将 DbContext.cs 文件也输出到指定的目录
-Context BlogDbContext # 指定 DbContext 的名称, 这里将在根目录生成 BlogDbContext.cs 文件
-ContextNamespace Sharemee.Blog.Data # 覆盖设置 DbContext 的命名空间
-Namespace Sharemee.Blog.Data.Entities # 使用指定实体类的命名空间
-NoOnConfiguring # 禁止创建包含连接字符串的 OnConfiguring 方法
-Force # 覆盖已经存在的文件, 当你修改了数据库需要再次生成实体模型的时候, 没有该参数将无法更新模型
```

## 4. 对使用 `PMC` 和 `.NET CLI` 的一些看法

`PMC` : 获取命令帮助的方式是 `Powershell` 式的, 即使用 `Get-Helpe <command name>` 命令来获取目标命令的帮助信息, 但输出的帮助信息不便于阅读(相对于 `.NET CLI` 方式来说)

```powershell
# 似乎只能在 "包管理器控制台" 中执行才有效
Get-Helpe Scaffold-DbContext
```

`.NET CLI` : 获取命令的帮助信息则是标准的, 使用 `--help` 选项即可, 且输出的命令帮助信息全面且直观易于阅读

```powershell
dotnet ef dbcontext scaffold --help
```

## 5. 官方文档地址

Entity Framework Core: <https://learn.microsoft.com/zh-cn/ef/core>

```powershell
Scaffold-DbContext 'Name=ConnectionStrings:SqlServer' Microsoft.EntityFrameworkCore.SqlServer -Context BlogDbContext -ContextDir ./ -OutputDir Entities -Force
```

```powershell
dotnet ef dbcontext scaffold `
"Data Source=localhost;User ID=sa;Password=Aa123456;Initial Catalog=Blog;Connect Timeout=10;Encrypt=False;Trust Server Certificate=True;Application Intent=ReadWrite;Multi Subnet Failover=False" `
Microsoft.EntityFrameworkCore.SqlServer `
--context-dir ./ `
--output-dir Entities `
--context BlogDbContext `
--no-onconfiguring `
--force
```
