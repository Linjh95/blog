---
title: Git指令简单总结
tags: 版本控制
categories: Git
keywords: git,git总结
date: 2018-07-17 23:38:08
---

Git作为当下流行的版本控制工具，深受广大开发者喜爱。本文将简单总结一下git的常用指令，也给自己留下点岁月的足迹。更多的有关git的官方使用文档，请移步[git](https://git-scm.com/book/zh/v2/%E8%B5%B7%E6%AD%A5-%E5%85%B3%E4%BA%8E%E7%89%88%E6%9C%AC%E6%8E%A7%E5%88%B6)自行研读。
<!--more-->

### 环境准备

1. [Git Bash](https://gitforwindows.org/)命令行工具(Windows)
2. 自带Terminal终端工具(Linux/Mac OS)


### Git命令--基本文件操作
1. 跟踪新文件、或把已跟踪文件放到暂存区、用于合并时把有冲突的文件标记为已解决状态
``` bash
$ git add xxx
```
2. 检查当前文件状态
``` bash
$ git status
```
3. 文件状态简览
``` bash
$ git status -s
 M README
MM Rakefile
A  lib/git.rb
M  lib/simplegit.rb
?? LICENSE.txt

?? 标记：新添加的未跟踪文件
 A 标记：新添加到暂存区中的文件
右边M标记： 文件被修改但没放入暂存区
左边M标记： 文件被修改并放入了暂存区 

```
4. 比较工作目录中当前文件和暂存区域快照之间的差异，也就是修改之后还没有暂存起来的变化内容
``` bash
$ git diff
```
5. 查看已暂存的将要添加到下次提交里的内容
``` bash
$ git diff --staged
```
6. 提交更新
``` bash
$ git commit -m "init"
```
7. 跳过使用暂存区域,把未暂存的修改直接提交
``` bash
$ git commit -a -m "add"
```
8. 移除文件(从已跟踪文件清单中移除,确切地说,是从暂存区域移除,工作目录也删除掉该文件)
``` bash
$ git rm xxx
```
9. 移除文件(从已跟踪文件清单中移除,保留在工作目录,也删除掉该文件,如日志文件)
``` bash
$ git rm --cached xxx
```
10. 移动文件(相当于命名操作)
``` bash
$ git mv file_from file_to
```

### Git命令--提交历史
1. 每次提交的简略的统计信息
``` bash
$ git log --stat
```
2. 使用不同于默认格式的方式展示提交历史
``` bash
$ git log --pretty=oneline
```
3. 带时间显示
``` bash
$ git log --pretty=format:"%h - %an, %ar : %s" 
```
4. 带时间显示、展示分支、合并历史
``` bash
$ git log --pretty=format:"%h %s" --graph 
```

### Git命令--撤消操作
1. 某次提交后发现忘记暂存某些需要的修改
``` bash
$ git commit -m 'initial commit'
$ git add forgotten_file
$ git commit --amend
```
2. 取消暂存的文件(在暂存区域)
``` bash
$ git reset HEAD <file>
```
3. 撤消对文件的修改(已修改的，未在暂存区域)，还原成上次提交时的状态
``` bash
$ git checkout -- <file>
```

### Git命令--仓库操作
1. 查看远程仓库
``` bash
$ git remote
```
2. 查看远程仓库(显示需要读写远程仓库使用的 Git 保存的简写与其对应的 URL)
``` bash
$ git remote -v
origin	https://github.com/schacon/ticgit (fetch)
origin	https://github.com/schacon/ticgit (push)
```
3. 添加远程仓库
``` bash
$ git remote add <shortname> <url>
```
4. 从远程仓库中抓取与拉取（访问远程仓库，从中拉取所有你还没有的数据）
``` bash
$ git fetch [remote-name]
```
5. 自动抓取远程仓库然后合并远程分支到当前分支
``` bash
$ git pull [remote-name] [branch-name]
```
6. 推送到远程仓库
``` bash
$ git push [remote-name] [branch-name]
```
7. 查看远程仓库更多信息
``` bash
$ git remote show [remote-name]
```
8. 远程仓库的移除与重命名
``` bash
$ git remote rename [remote-name] [new-remote-name]
```
9. 远程仓库移除
``` bash
$ git remote [remote-name]
```

### Git命令--命令别名
``` bash
$ git config --global alias.co checkout
$ git config --global alias.br branch
$ git config --global alias.ci commit
$ git config --global alias.st status
```

### Git命令--分支操作
1. 创建新分支(分支本质上仅仅是指向提交对象的可变指针)
``` bash
$ git branch [branch-name]
```
2. 分支切换
``` bash
$ git checkout [branch-name]
```
3. 查看各个分支当前所指的对象
``` bash
$ git log --oneline --decorate
```
4. 查看分叉历史
``` bash
$ git log --oneline --decorate --graph --all
```
5. 新建分支并切换到该分支
``` bash
$ git checkout -b [branch-name]
```
6. 合并分支
``` bash
$ git checkout master
//切换到主分支
$ git merge hotfix
//将hotfix分支合并到主分支
```
7. 删除分支
``` bash
$ git branch -d [branch-name]
```
8. 强制删除分支，不管它是否已被合并
``` bash
$ git branch -D [branch-name]
```
9. 查看每一个分支的最后一次提交
``` bash
$ git branch -v
```
10. 查看哪些分支已经合并到当前分支
``` bash
$ git branch --merged
```
11. 查看哪些分支尚未合并到当前分支
``` bash
$ git branch --no-merged 
```
