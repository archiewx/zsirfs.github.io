---
layout: 'po:st'
title: git问题集合
date: 2018-03-08 15:16:07
tags:
categories:
---

git 是现在最火的的代码管理工具，一般我们提交代码到 GitHub、码云、gitlab 之类的代码仓库，会有很多操作
git 的方法。同时也会出现很多问题，所以需要总结一下 git 发现的问题。

<!-- more -->

## sourceTree 直接点击旧的版本号

先上图:

![](http://p4lzwh1mv.bkt.clouddn.com/dragon-qiniu/1520850313014.jpg)

这样，这里如果放弃本地修改就该导致你修改的代码直接 gg，我们直接切换到这个副本以后，修改完成以后按照
正常命令提交以后，直接没见了。

这个时候需要使用到：

```bash
$ git reflog
```

然后这里就出现了这个:

![](http://p4lzwh1mv.bkt.clouddn.com/dragon-qiniu/1520850608943.jpg)

红线区域就是 git 的后 8 位 hash 值，只需要找到这个，然后这个操作:

```bash
$ git checkout -b new-branch 6c5816c8
```

然后就重新建立一个分支，并且你修改提交内容就回来了。`very good`
