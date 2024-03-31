---
layout: post
title: VSCode Extension Development(1)
description: VSCode Extension Development getting start.
---

## 搭建VSCode 扩展开发环境

首先需要安装VSCode.

然后安装脚手架:

```powershell
# 全局安装 yo(yeoman) generator-code 两个包
npm install --global yo generator-code
```

[yo](https://yeoman.io/): 是一个面向web的脚手架工具.用来创建各种web项目.
[generator-code](https://github.com/Microsoft/vscode-generator-code): 是微软官方的VSCode扩展脚手架.

## 创建扩展项目

```powershell
# 以命令行交互方式协助开发者创建扩展项目
yo code
# code 就是之前安装的generator-code
```

如此就完成了扩展项目的搭建, 接下来就是开发扩展内容了.

具体扩展开发过程待续...

扩展开发完成后发布到官方插件市场待续...

## 官方文档

<https://code.visualstudio.com/api/get-started/your-first-extension>
