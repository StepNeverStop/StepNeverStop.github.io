---
title: "Git命令学习记录"
date: 2018-12-29 15:48:51
updated: 2019-01-02 14:38:24
categories: 小知识
tags: Git
---

# Git命令学习记录

<!--more-->

- 删除远程仓库的文件,保留本地的文件

  ```
  git rm -r /path/to/filename
  git commit -m "msg"
  git push
  ```

- 删除远程仓库的文件,同时删除本地文件

  ```
  git rm /path/to/filename
  git commit -m "msg"
  git push
  ```


- 查看本地所有分支

  ```
  git branch -a
  ```
  
- 查看本地分支

  ```
  git branch
  ```

- 切换分支

  ```
  git checkout [branchname]
  ```
  
- 查看各个分支当前所指的对象

  ```
  git log --oneline --decorate
  ```
  
- 如果我同一项目有两个不同的版本，怎么切换某一版本到master分支呢？比如说我有两个分支名字为A和B，目前默认master分支指向A，现在我想把master切换至B，该怎么做呢？
  
  ```
  git branch -m master A
  把当前的master分支内容放置分支A
  git branch -m B master
  将分支B重命名为master
  git push -f origin master
  更新master分支
  ```
  
- 如果在本地git仓库下有另外一个clone过来的git仓库，那么当使用`git add .`，然后再`git commit ...`时会报错。并且上传到仓库的文件夹是空的，解决方案如下：
  1. `cd`到`clone`的仓库目录下，执行`rd /s/q .git`命令，删除`clone`的仓库目录下的`.git`文件夹
  2. 回到仓库根目录删除仓库中的空文件夹

     2.1 `git rm -r --cached "themes/[branchname]"`
     
     2.2 `git commit -m "remove empty folder"`
     
     2.3 `git push origin master`
     
  3. 在仓库根目录重新提交代码

     3.1 `git add .`
     
     3.2 `git commit -m "repush"`
     
     3.3 `git push origin master`