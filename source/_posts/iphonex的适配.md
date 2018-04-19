---
layout: 'po:st'
title: iphonex的适配
description: <blockquote class="blockquote-center">"iphonex的适配"</blockquote>
date: 2018-04-19 11:48:04
tags:
categories:
---

iphonex 已经上线有一段时间了，作为业界刘海屏幕第一款机型，导致全屏不能正常的全屏显示了，，所以需要对
iphonx 适配，下面就详细说说如何适配

<!-- more -->

先看一张适配前后的图: [](http://ojgquc007.bkt.clouddn.com/dragon-qiniu/1524115029816.jpg)
[](http://ojgquc007.bkt.clouddn.com/dragon-qiniu/1524114909262.jpg)

## iphonex 提供的 meta 头

```html
<meta name="viewport" content="viewport-fit=contain">
<meta name="viewport" content="viewport-fit=cover">
<meta name="viewport" content="viewport-fit=auto">
```

### contain

可视化窗口完全包含网页内容

### cover

网页内容完全覆盖

### auto

默认值和 contain 一样

详细见下图(无耻盗图): [](http://ojgquc007.bkt.clouddn.com/dragon-qiniu/1524114553748.jpg)

> 注意：网页默认不添加扩展的表现是 viewport-fit=contain，需要适配 iPhoneX 必须设置
> viewport-fit=cover，这是适配的关键步骤。

## ios11 增加新特性，webkit 的 css 函数

为了应对刘海屏幕，苹果也给出了响应的策略

### css 预定义变量

四个预定义变量为设定安全区域和边界的距离，如下:

* safe-area-inset-left：安全区域距离左边边界距离
* safe-area-inset-right：安全区域距离右边边界距离
* safe-area-inset-top：安全区域距离顶部边界距离
* safe-area-inset-bottom：安全区域距离底部边界距离

#### safe-area-inset-left

一般情况下是 0

#### safe-area-inset-right

一般情况下是 0

#### safe-area-inset-top

在刘海全屏的时候 top 为 `44px`

#### safe-area-inset-bottom

刘海全屏的条件下是 `34px`

### css 函数 env() 和 constant()

这两个函数都是 webkit 中 css 函数，可以直接使用变量函数，只有在 webkit 内核下才支持

#### env 函数

必须在 ios >= 11.2 才支持

#### constant 函数

必须 ios < 11.2 支持

> The env() function shipped in iOS 11 with the name constant(). Beginning with Safari Technology
> Preview 41 and the iOS 11.2 beta, constant() has been removed and replaced with env(). You can use
> the CSS fallback mechanism to support both versions, if necessary, but should prefer env() going
> forward.

#### 兼容前后版本代码

```css
padding-top: constant(safe-area-inset-top);
padding-top: env(safe-area-inset-top);
```

看下图: [](http://ojgquc007.bkt.clouddn.com/dragon-qiniu/1524115956774.jpg)

### 兼容代码

下面写的是 scss

```scss
.phonex {
  padding-top: constant(safe-area-inset-top); //为导航栏+状态栏的高度 88px
  padding-top: env(safe-area-inset-top); //为导航栏+状态栏的高度 88px
  padding-left: constant(safe-area-inset-left); //如果未竖屏时为0
  padding-left: env(safe-area-inset-left); //如果未竖屏时为0
  padding-right: constant(safe-area-inset-right); //如果未竖屏时为0
  padding-right: env(safe-area-inset-right); //如果未竖屏时为0
  padding-bottom: constant(safe-area-inset-bottom); //为底下圆弧的高度 34px
  padding-bottom: env(safe-area-inset-bottom); //为底下圆弧的高度 34px
}
```

## 完整检测代码

### @supports 隔离兼容模式

```scss
@mixin iphonex-css {
  padding-top: constant(safe-area-inset-top); //为导航栏+状态栏的高度 88px
  padding-top: env(safe-area-inset-top); //为导航栏+状态栏的高度 88px
  padding-left: constant(safe-area-inset-left); //如果未竖屏时为0
  padding-left: env(safe-area-inset-left); //如果未竖屏时为0
  padding-right: constant(safe-area-inset-right); //如果未竖屏时为0
  padding-right: env(safe-area-inset-right); //如果未竖屏时为0
  padding-bottom: constant(safe-area-inset-bottom); //为底下圆弧的高度 34px
  padding-bottom: env(safe-area-inset-bottom); //为底下圆弧的高度 34px
}
@mixin iphonex-support {
  @supports (bottom: constant(safe-area-inset-top)) or (bottom: env(safe-area-inset-top)) {
    body.iphonex {
      @include iphonex-css;
    }
  }
}
```

### 使用@media 媒体查询

```scss
@mixin iphonex-css {
  padding-top: constant(safe-area-inset-top); //为导航栏+状态栏的高度 88px
  padding-top: env(safe-area-inset-top); //为导航栏+状态栏的高度 88px
  padding-left: constant(safe-area-inset-left); //如果未竖屏时为0
  padding-left: env(safe-area-inset-left); //如果未竖屏时为0
  padding-right: constant(safe-area-inset-right); //如果未竖屏时为0
  padding-right: env(safe-area-inset-right); //如果未竖屏时为0
  padding-bottom: constant(safe-area-inset-bottom); //为底下圆弧的高度 34px
  padding-bottom: env(safe-area-inset-bottom); //为底下圆弧的高度 34px
}
// iphonex 适配
@mixin iphonex-media {
  @media only screen and (device-width: 375px) and (device-height: 812px) and (-webkit-device-pixel-ratio: 3) {
    body.iphonex {
      @include iphonex-css;
    }
  }
}
```

## 最后

env 和 constant 只有在 viewport-fit=cover 时候才能生效, 上面使用的safari 的控制台可以检测模拟器中网页开启web inspector.

> https://webkit.org/blog/7929/designing-websites-for-iphone-x/?hmsr=funteas.com&utm_medium=funteas.com&utm_source=funteas.com >
> https://aotu.io/notes/2017/11/27/iphonex/index.html
