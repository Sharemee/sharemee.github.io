---
layout: post
title: Npm command
---

npm install

--save: 安装的依赖包信息会写到 `package.json` 下的 `dependencies` 节点中

--save-dev: 安装的依赖包信息会写到 `package.json` 下的 `devDependencies` 节点中。开发依赖项通常是你只在开发过程中需要的工具，比如测试库、构建工具等。

--save-exact：这个选项告诉 `npm` 精确地安装指定版本的包，在 `package.json` 文件中将会精确记录这个版本号，而不是写成1.18.x或者^1.18.0。
