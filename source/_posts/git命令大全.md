---
title: git命令大全
tags: git
categories: git+github
toc: true
abbrlink: 2557304751
date: 2020-02-27 15:25:31
---
git分布式版本控制工具，在日常的开发中起到了非常重要的作用，掌握一些基本的命令也对工作效率的提高有很大的帮助，下面介绍一些基本的git操作。
<!--more-->
## 一、配置相关
- 配置全局帐户，该账户对所有git仓库都有效

```bash
git config --global user.name '你的账户名称'
git config --global user.email '你的email'
```
- 配置局部账户，该账户只对当前Git仓库有效

```bash
git config --local user.name '你的账户名称'
git config --local user.email '你的email'
```
- 查看配置情况

```bash
git config --global --list
git config --local --list
```
## 二、本地操作

### 基础操作
1.查看变更情况

```bash
git status
```
2.查看当前工作在那个分支上

```bash
git branch -v
```
3.切换到指定分支

```bash
git checkout 指定分支名称
```
4.把当前目录及子目录下所有变更都加入到`暂存区`

```bash
git add . 
```
5.把仓库内所有变更都加入到暂存区

```bash
git add -A
```
6.把指定文件添加到暂存区

```bash
git add 文件1 文件2 ... 文件n
```
7.创建正式的`commit`，也就是把当前更改提交，并附加描述

```bash
git commit -m "本次提交的描述"
```

### 比较差异
1.比较某文件工作区和暂存区的差异

```bash
git diff 某文件
```

2.比较某文件暂存区和HEAD的差异

```bash
git diff --cache 某文件
```
3.比较工作区和暂存区的所欲差异

```bash
git diff 
```
4.比较暂存区和HEAD的所有差异

```bash
git diff --cache
```
### 暂存区与工作区之间回滚
1.把工作区指定文件恢复和暂存区一样

```bash
git checkout 文件1 文件2... 文件n
```
2.把暂存区指定文件恢复和HEAD一样

```bash
git reset 文件1 文件2 ... 文件n
```
3.把暂存区和工作区所有文件恢复和HEAD一样

```bash
git reaet --head
```
4.用`difftool`比较两个`commit`的差异

```bash
git difftool commit1 commit2 
```
5.查看哪些文件没有被`git`管控

```bash
git ls-files --others
```

6.补充：从工作区回滚到暂存区用`checkout`， 否则用reset

## 三、 加塞临时任务处理
1.把未处理完的变更先保存到`stash`中

```bash
git stash
```
2.临时任务处理处理完之后继续之前的工作

```bash
git stash pop //pop相当于出栈和入栈一样，把之前的任务弹出来
或者
git stash apply //与pop不同的是，apply相当于从栈顶把任务取出来，但是不会从栈中把任务移除
```
3.查看所有`stash`的变更

```bash
git stash list
```
4.取回某次`stash`的变更

```bash
git stash pop stash @｛n｝
```

## 四、修改个人分支历史
- 仓库在每次变更执行commit的时候，会生成一个新的commit，有时候不想生成新的，只是修改一下之前的，该如何操作呢？


1.修改最后一次`commit`

```bash
在工作区中修改文件 
git add
git commit --amend
```
2.修改第（N）次的`commit`

```bash
git rebase -i N前面一个commit的id
在工作区修改文件
git add
git rebase --contiue
```

## 五、查看变更日志
1.当前分支的各个commit

```bash
git log --online
```
2.显示最近n个commit

```bash
git log -n
```
3.用图示显示所有的历史分支

```bash
git log --online --graph --all
```
4.查看涉及到某文件变更的所有commit

```bash
git log 某文件
```
5.某文件各行最后修改对应的commit以及作者

```bash
git blame 文件名
```

## 六、分支与标签

### ①创建新分支

1.基于当前分支创建新分支

```bash
git branch 新分支
```
2.基于指定分支创建新分支

```bash
git branch 新分支 已有分支
```
3.基于某个commit创建分支

```bash
git branch 新分支 某个commit 的id
```
4.创建分支并切换到该分支

```bash
git cheakout -b 新分支
```
### ②列出分支
1.列出本地分支

```bash
git branch -v
```
2.列出本地和远端分支

```bash
git branch -av
```
3.列出远端所有分支

```bash
git branch -rv
```
4.列出名称符合某样式的远端分支

```bash
git branch -rv -l '某样式'
```
### ③删除分支
1.安全删除本地分支

```bash
git branch -d 要删除的分支
```
2.强制删除本地分支

```bash
git branch -D 分支
```
3.删除已经合并到master分支的所有本地分支

```bash
git branch --merged master | grep -v '^\*\| master' | xargs -n 1 git branch -d
```
4.删除远端origin已不存在的所有本地分支

```bash
git remote prune origin
```
5.打标签：从commit上打标签

```bash
git tag 标签名 commit 的id
```

## 七、两分支之间的集成
1.把A分支合入到当前分支，且为merge创建commit

```bash
git merge A分支
```
2.把A分支和入到B分支，且为Merge创建commit

```bash
git merge A分支 B分支
```
3.把当前分支基于B分支作为rebase，以便B分支合入到当前分支

```bash
git rebase B分支
```
4.把A分支基于B分支做rebase，以便B分支合入到A分支

```bash
git rebase B分支 A分支
```
## 八、和远端交互
1.列出所有的remote

```bash
git remote -v
```
2.增加remote

```bash
git remote add URL地址
```
3.删除remote 

```bash
git remote remove remote的名称
```
4.改变remote的名称

```bash
git remote rname 旧名称 新名称
```
5.把远端所有分支和标签的变更都拉到本地

```bash
git fetch remote
```
6.把远端分支变更拉到本地，且merge到本地分支

```bash
git pull remote名称 分支名
```
7.把本地分支push到远端

```bash
git push remote名称 分支名
```
8.删除远端分支

```bash
git push remote --delete 远端分支名
或者
git push remote：远端分支名
```
9.向远端提交指定标签

```bash
git push remote 标签名
```
10.向远端提交所有标签

```bash
git push remote --tags
```



