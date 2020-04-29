---
title: github下载项目加速
tags: git
categories: git+github
toc: true
abbrlink: 3770390475
date: 2020-02-25 09:36:26
---
在github上面找好的开源项目，想要clone到本地，自己调试运行一下，但是由于github的服务器在国外，所以下载速度真的是惊人的慢，有的时候2k/s，如果是一个小项目的话。那还好，等个两三分钟，但是项目大一点的，那就很鸡肋了，这里就介绍一个投机取巧的方法，来加速git clone
<!--more-->

- **核心思想：** 利用`gitee`（码云）做中间工具


1. 注册好码云，并配置好本地仓库，保证可以从`gitee`上`git clone`
2. 在`github`上复制你要克隆的项目的`url`。在码云中选择新建仓库的`从github/github导入仓库`，选择`从url导入`。粘贴刚才`github`上的`URL`到其中，点击导入即可。
3. 然后在从码云将项目克隆到本地。
4. 但是现在的项目，关联的是码云，而不是github。所以就要修改配置文件。
5. 打开项目的`.git`，找到`config`文件，找到里面的`url`关键字，修改`gitee`为`github`，即可。

- **补充：** 修改，提交四步走


1. `git status`
2. `git add " 文件"`
3. `git commit -m "本次提交操作的相关描述"`
4. `git push`