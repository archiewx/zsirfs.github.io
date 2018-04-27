---
layout: 'po:st'
title: cypress进行e2e测试之理论
date: 2018-04-24 18:29:28
tags:
categories:
---

# cypress 进行 e2e 测试之理论

cypress 是目前 e2e 很火的一个测试组件，内部绑定了 macha、chai、chai-jquery 之类的断言,为了让代码代码
更有说服力，减少提交测试错误，进行 e2e 测试显然是非常有必要的。

> [官网](https://docs.cypress.io) \
> [GitHub](https://github.com/cypress-io/cypress)

借鉴官网一句话来说:

> Cypress is a next generation front end testing tool built for the modern web. We address the key
> pain points developers and QA engineers face when testing modern applications.

<!-- more -->

## 本文环境

```
node v9.5\
npm v5.5
```

## e2e 简介

e2e 测试端对端测试的简称, e2e 即为`end to end`,
[指任意一个人的社交、交易、休闲都可以直接与另外任意一个人产生关系，去中心化、渠道化](https://baike.baidu.com/item/E2E/9812039).

## cypress

### cypress 环境搭建

做前端怎么少的多的了 npm 呢

```bash
$ npm i -D cypress
```

然后为了方便起见，咱们在`package.json`中写入下面脚本:

```json
{
  "scripts": {
    "e2e:open": "cypress open",
    "e2e:run": "cypress run"
  }
}
```

![](http://ojgquc007.bkt.clouddn.com/dragon-qiniu/1524630068200.jpg)

运行`npm run e2e:open`,启动一个 cypress 的服务器，如下:
![](http://ojgquc007.bkt.clouddn.com/dragon-qiniu/1524723249070.jpg)

如下图这就完成了一个启动一个 cypress。第一次点开时候，cypress 会帮你创建一个初始化配置目录，这是
cypress 推荐的目录的结构，当然也可以自己创建。

![](http://ojgquc007.bkt.clouddn.com/dragon-qiniu/1524723515804.jpg)

点击 example_spec.js 文件，然后可以看到如下界面,cypress 开始测试:

![](http://ojgquc007.bkt.clouddn.com/dragon-qiniu/1524724620147.jpg)

上面就看到 cypress 的运行过程了。下面看看 example_spec.js(文件的位置
:projectName/cypress/integration)文件中写了啥:

```js
describe('Kitchen Sink', function() {
  it('.should() - assert that <title> is correct', function() {
    // ...
  }
})
```

这是主要结构的，下面大部分都是在一个`it`函数内部，是测试里面的回调函数。详细可以查看 TDD 和 BDD 测试
框架，cypress 绑定了这些测试框架。

## cy.visit

这是 cypress 里面一个很重要的方法，可以访问一个链接,列入 example.js 文件如下:

```js
beforeEach(function() {
  // Visiting our app before each test removes any state build up from
  // previous tests. Visiting acts as if we closed a tab and opened a fresh one
  cy.visit('https://example.cypress.io/commands/querying')
})
```

这里就是在前置钩子函数里面访问了`https://...../querying`这个链接。如果代码需要浏览器调试，比如用户交
互点击，用户输入之类的。第一步就是访问：cy.visit

## cy.get

还是从 example_spec.js 问中说起：

```js
it('cy.get() - query DOM elements', function() {
  // https://on.cypress.io/get

  // Get DOM elements by id
  cy.get('#query-btn').should('contain', 'Button')

  // Get DOM elements by class
  cy.get('.query-btn').should('contain', 'Button')

  cy.get('#querying .well>button:first').should('contain', 'Button')
  //              ↲
  // Use CSS selectors just like jQuery
})
```

这里定义了一个测试单元，在这个里面做了啥呢？第一步获取 id 为 query-btn 这个按钮。接下来 should 操作
，奉上一张表自行查看: ![](http://ojgquc007.bkt.clouddn.com/dragon-qiniu/1524734614828.jpg)

cy.get 还有一个玩法就是 cy.get('@app')这种，意思说之前你已经`cy.get('.app').as('app')`,不需要再次获
取了，直接使用别名就好了

从官网截图的表格，详
细[jquery-chai 文档表格](https://docs.cypress.io/guides/references/assertions.html#Chai-jQuery)

这里看到`cy.get()`和`jquery.$`是不是很像，在官网这里说了这样一句话:

> The querying behavior of this command matches exactly how $(…) works in jQuery.

所以可以将 cy.get()当$一样来用即可，不过这里返回的不过 jquery 对象罢了，这里返回的事通过 cypress 包
装过的对象可以在控制台看到这样的东西,见下图:
![](http://ojgquc007.bkt.clouddn.com/dragon-qiniu/1524734935768.jpg)

是一个用于 cypress 所有方法的对象。然后可以操作他的 api 了。

第一部分，主要是查询，查询页面元素是否按照我们开发想要的存在，下面看第二部分:

```js
context('Actions', function() {
  beforeEach(function() {
    cy.visit('https://example.cypress.io/commands/actions')
  })

  // Let's perform some actions on DOM elements
  // https://on.cypress.io/interacting-with-elements

  it('.type() - type into a DOM element', function() {
    // https://on.cypress.io/type
    cy
      .get('.action-email')
      .type('fake@email.com')
      .should('have.value', 'fake@email.com')

      // .type() with special character sequences
      .type('{leftarrow}{rightarrow}{uparrow}{downarrow}')
      .type('{del}{selectall}{backspace}')

      // .type() with key modifiers
      .type('{alt}{option}') //these are equivalent
      .type('{ctrl}{control}') //these are equivalent
      .type('{meta}{command}{cmd}') //these are equivalent
      .type('{shift}')

      // Delay each keypress by 0.1 sec
      .type('slow.typing@email.com', { delay: 100 })
      .should('have.value', 'slow.typing@email.com')

    cy
      .get('.action-disabled')
      // Ignore error checking prior to type
      // like whether the input is visible or disabled
      .type('disabled error checking', { force: true })
      .should('have.value', 'disabled error checking')
  })
})
```

这一部分主要是进行获取元素交互, 下面来说交互是如何搞得。 与 cy.get 相似还有:

* cy.contains 通过文本获取元素
* cy.closet 见 jqery
* cy.next/cy.nextAll 可以和 cy.contains 联合使用获取该节点的下一个节点
* cy.prev/cy.prevAll 同上
* cy.children/cy.parents/cy.parent 获取子节点/ 所有的父节点 / 父节点
* cy.first/cy.last
* cy.url 获取当前页面 url
* cy.title 获取当前页面标题
* ... API 挺多的，同样奉
  上[api 文档](https://docs.cypress.io/api/commands/type.html#Key-Combinations)

## cypress 交互逻辑

既然要交互肯定需要点击输入滚动，可以还存在拖拽等等。咱们就暂时从输入开始说起啦

### cy.type

这不是一个可以直接使用的方法，要配合`cy.get`使用的,作用是给空间进行输入。例如:

#### 测试输入例如 text, textarea

```js
cy.get('input').type('hello world')
```

#### 测试 tabIndex

```html
  <div class="el" tabIndex="1">
    This is TabIndex div.
  </div>
```

```js
cy.get('.el').type('laldkadaljdkljasf') // 这个里面是随机字符串
```

#### 测试 input 为日期的

```js
cy.get('input[type=date]').type('2008-8-9')
```

#### 键盘绑定

下面直接是对 input 进行组合键盘操作

```js
cy.get('input').type('{shift}{alt}Q')
```

按住键盘操作

```js
cy.get('input').type('{alt}这里是按了一下alt后输入的内容')
```

还有长按键盘之类的操作，详细就看官网了这里之类奉上链
接[https://docs.cypress.io/api/commands/type.html#Key-Combinations](https://docs.cypress.io/api/commands/type.html#Key-Combinations)

这里就是关于键盘的组合操作。

#### 对于选择例如 radio, checkbox

这些就只需要利用点击事件即可，如下:

```js
cy
  .get('input[type=radio]')
  .as('radio')
  .click()
cy.get('@radio').should('be.checked')
```

### 定时

#### cy.wait

下面是等待 1s

```js
cy.wait(1000)
```

#### cy.clock 和 cy.tick

自己的代码：

```js
var seconds = 0
setInterval(() => {
  $('#seconds-elapsed').text(++seconds + ' seconds')
}, 1000)
```

测试代码

```js
cy.clock()
cy.visit('/index.html')
cy.tick(1000)
cy.get('#seconds-elapsed').should('have.text', '1 seconds')
cy.tick(1000)
cy.get('#seconds-elapsed').should('have.text', '2 seconds')
```

![](http://ojgquc007.bkt.clouddn.com/dragon-qiniu/1524795818095.jpg) 这里就会出现关于 clock 和 tick
的用法，更多用法看文档，我也有部分迷惑的。待后来再解决。老规矩文档地址:
[地址](https://docs.cypress.io/api/commands/clock.html#Now)

### 关于 cypress 的配置

先复制一段出来:

```json
{
  "baseUrl": "http://localhost:8080",
  "pageLoadTimeout": 3000,
  "viewportHeight": 667,
  "viewportWidth": 375
}
```

这是一个非常精简的配置了:

* baseUrl 基础链接，之后在是使用 cy.visit 的时候，只需要访问具体路由例如: cy.visit('/Hello')
* viewport 两个属性
  * viewportHeight 测试窗口的高度
  * viewportWidth 测试窗口的宽度
* pageLoadTimeout 页面家安在超过 3000ms 即为超时。

## 总结

上面是 cypress 的基本用法，cypress 是基于 electron 的一个测试框架，提供 web 环境进行点对点的测试，在
programer 思维下，进行自动化的交互操作，必要点检测说明，这是一个非常棒的用处。例如之后拥有数据埋点，
可以在固定的位置检测是否有埋点。测试想要的地方是否匹配的数据。模拟用户的点击操作，这都是非常棒的。在
jquery 操作年代，各种 id 和 class 奇怪命名下，这些都可以容易找到，在 vue 和 react 大行其道的年代，但
是却可以通过文本寻找节点。这也是非常棒的体验，更多秘密需要去体验，奉上官方地址
：[官网 cypress](https://docs.cypress.io/)
