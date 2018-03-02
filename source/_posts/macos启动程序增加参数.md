---
layout: 'po:st'
title: macos启动程序增加参数
date: 2018-03-02 14:22:50
tags:
  - os
  - macos
categories:
  - utils
---

因为最近使用 vscode 中的插件`debugger for chrome` 这个牛逼的调试 js 的插件，然后呢，每次需要在控制台
启动 chrome 然后增加参数`--remote-debugging-port=9222`这段代码，每次启动，很烦人呢，然后去 google 一
下。谁知道网上都是一毛一样的，然后一毛一样的不能启动，直接报错，后来，然后我就发现了，都特么有问题，
使用`ls -al`已查看全都是不靠谱，用户根本没有执行命令嘛，下面查看正确打开方式

<!-- more -->

## 打开应用目录

```bash
$ cd /Applications/Google Chrome.app/Contents/MacOS
```

## 使用sudo 命令，新建脚本

```bash
$ sudo mv Google\ Chrome Google.real
$ sudo vim Goolge\ Chrome
```

复制这段命令进去:

```text
"/Applications/Google Chrome.app/Contents/MacOS/Google.real" --user-data-dir --remote-debugging-port=9222
```

## 给 Google Chrome 脚本增加运行命令

```bash
$ sudo chmod a+x Google\ Chrome
```

然后就可以快乐的启动Google 浏览器，然后带上调试命令了。
