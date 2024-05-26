---
layout: post
title: VSCode Extension Development Getting Start
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

## 具体扩展开发

待续...

## Build & Publish

`vsce` 是 `Visual Studio Code Extensions` 的缩写，是一个用于打包、发布和管理VS Code扩展的命令行工具:

```powershell
npm install -g @vscode/vsce
```

打包到本地:

```powershell
vsce package [-o ./dist]
```

## 官方文档

<https://code.visualstudio.com/api/get-started/your-first-extension>
