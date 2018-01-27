---
layout: 'po:st'
title: 'npm安装sass,phantomjs,electron等失败'
date: 2018-01-27 14:13:47
tags:
  - nodejs
  - npm
  - sass
  - phantomjs
  - electron
categories:
  - javascript
  - npm
  - electron
---

安装 npm 总会失败，比如安装`node-sass`, `phantomjs`, `electron`,还有 `nvm`等等，这些都要请求国外的，很显然很多时候，我们是安装失败的，经常会链接失败，遇到 npm 安装时候连接超时的问题，需要来解决这个问题就需要配置国内的开源的镜像，比如[taobao npm](https://npm.taobao.org/mirrors)

<!-- more -->

## macos 或者 unix 用户

通过 npm 文档查询得到，可以在用户目录通过配置`.npmrc`文件来配置用户配置，因此，只需要在用户目录下修改配置文件(不存在就自己创建)

然后写入下面脚本:

```bash
# nvm
nvm_nodejs_org_mirror=http://npm.taobao.org/mirrors/node
# phantomjs
phantomjs_cdnurl=http://npm.taobao.org/mirrors/phantomjs
# chromedriver
chromedriver_cdnurl=http://npm.taobao.org/mirrors/chromedriver
# operadriver
operadriver_cdnurl=http://npm.taobao.org/mirrors/operadriver
# electron
electron_mirror=http://npm.taobao.org/mirrors/electron/
# node-sass
sass_binary_site=http://npm.taobao.org/mirrors/node-sass
# sqlite3
sqlite3_binary_site=http://npm.taobao.org/mirrors/sqlite3
```

### 安装 electron-rebuild

```bash
$ electron-rebuild -d=http://npm.taobao.org/mirrors/atom-shell
$ electron-rebuild -d=http://npm.taobao.org/mirrors/atom-shell -f -w $module_with_c++_code
```

### 安装 nvmw

```bash
$ set "NVMW_NODEJS_ORG_MIRROR=http://npm.taobao.org/mirrors/node"
$ set "NVMW_IOJS_ORG_MIRROR=http://npm.taobao.org/mirrors/iojs"
$ set "NVMW_NPM_MIRROR=http://npm.taobao.org/mirrors/npm"
$ nvmw install node-v0.12.2
$ nvmw install iojs-v1.6.3
```
