---
layout: post
title:  "Git命令学习记录"
date:   2018-12-29 15:24:35
categories: 小知识
tag: Git
---

* content
{:toc}


# Git命令学习记录

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


