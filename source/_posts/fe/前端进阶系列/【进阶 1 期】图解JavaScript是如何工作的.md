---
title: 【进阶 1 期】图解JavaScript是如何工作的
tags: 前端进阶系列
abbrlink: 65436
date: 2018-12-14 16:21:05
description: V8引擎由什么组成？内存堆是什么？调用栈是什么？EventLoop有什么用？代码具体在浏览器是如何执行的？
---

> 用简单的语言和案例告诉你『JavaScript 是如何工作的』的一个良心教程。
> [《Youtube=>菲利普·罗伯茨：到底什么是 Event Loop 呢？ | 欧洲 JSConf 2014》](https://www.youtube.com/watch?v=8aGhZQkoFbQ)

[《可视化调用栈如何运行的网站》](http://latentflip.com/loupe/)

---

一步一步带你了解 JavaScript 是如何运行的。

## Q1：JavaScript 引擎

> `JavaScript引擎`是一个专门处理`JavaScript脚本`的`虚拟机`，一般会附带在网页浏览器之中。 ----维基百科

JS 引擎的具体内容，不是这篇文章的重点，本文知道它是用来解释，执行 JS 代码的就可以了。

JS 引擎有很多个，但是用的最多，性能最好的是 Google 用 C++开发的 V8 引擎， 下文的例子实在 Chrome 上运行的。

## Q2：浏览器运行时环境组成

![](https://i.loli.net/2018/12/05/5c079cfa41bb0.png)

- 事件循环（Event Loop）: 处理主线程（V8 引擎的代码执行线程）和其他线程的通讯
- JS 引擎（V8）：解释、执行 JS 代码
- WebAPIs：浏览器提供的一些 API（DOM 操作，异步，定时器等）
- 回调队列（Callback Queue）：回调函数在这里排队，等待主线程翻牌子。

### 2.1、Event Loop

简单理解，`Event Loop` 就是负责主线程合其他进程之间的通讯的线程栗子。

看个栗子：

> 这个栗子来源[《什么是 Event Loop？》](http://www.ruanyifeng.com/blog/2013/10/event_loop.html)

如果某个任务很耗时，比如涉及很多 I/O（输入/输出）操作，那么线程的运行大概是下面的样子。

![](http://static.izhongxia.com/pictures/2013102002.png)

上图的绿色部分是程序的运行时间，红色部分是等待时间。可以看到，由于 I/O 操作很慢，所以这个线程的大部分运行时间都在空等 I/O 操作的返回结果。这种运行方式称为`"同步模式"（synchronous I/O）`或`"堵塞模式"（blocking I/O）`。

`Event Loop`就是为了解决这个问题而提出的。

> "Event Loop 是一个程序结构，用于等待、发送消息和事件。（a programming construct that waits for and dispatches events or messages in a program.）"

简单说，就是在程序中设置两个线程：一个负责程序本身的运行，称为"主线程"；另一个负责主线程与其他进程、线程（主要是各种 I/O 操作）的通信，被称为"Event Loop 线程"（可以译为"消息线程"）。

![](http://static.izhongxia.com/pictures/2013102004.png)

上图主线程的绿色部分，还是表示运行时间，而橙色部分表示空闲时间。每当遇到 I/O 的时候，主线程就让 Event Loop 线程去通知相应的 I/O 程序，然后接着往后运行，所以不存在红色的等待时间。等到 I/O 程序完成操作，Event Loop 线程再把结果返回主线程。主线程就调用事先设定的回调函数，完成整个任务。

可以看到，由于多出了橙色的空闲时间，所以主线程得以运行更多的任务，这就提高了效率。这种运行方式称为"异步模式"（asynchronous I/O）或"非堵塞模式"（non-blocking mode）。

> 上面这段是搬过来的内容

### 2.2、 JS 引擎

JS 引擎的两个重要组成部分

#### 1. 内存堆

计算机程序在运行期中动态分配使用内存（比如，`let list = [1,2,3,4,5]`, list 变量的值就存放在内存堆里面）

#### 2. 调用栈

调用栈是解析器(如浏览器中的的 javascript 解析器)的一种机制，可以在脚本调用多个函数时，跟踪每个函数在完成执行时应该返回控制的点。（如什么函数正在执行，什么函数被这个函数调用，下一个调用的函数是谁）

参考：https://developer.mozilla.org/zh-CN/docs/Glossary/Call_stack

PS：讲一堆理论看不懂什么鬼，来点实在的例子吧。

1. 举个栗子：

```js
function greeting() {
  sayHi()
}

function sayHi() {
  console.log('Hi')
}

greeting()
```

执行步骤描述如下：

1. 函数声明，变量定义先不管
2. 执行到 `greeting()`, 压入栈中
3. `greeting` 中调用了 `sayHi()`， 把 `sayHi()` 压入栈中
4. `sayHi` 中执行 `console.log`，压入栈中
5. 执行 `console.log('hi')`, 控制台看到 Hi，把 `console.log` 从栈中删除
6. `sayHi` 执行结束，把 `sayHi` 从栈中删除
7. `greeting` 执行结束，把 `greeting` 从栈中删除
8. 本次执行结束（栈为空）

调用栈可视化图如下：

![](http://static.izhongxia.com/pictures/blog_stack.gif)

2. 再来个栗子：

控制台，打印出的报错信息其实就是栈的内容。

foo 报错了，是谁调用了 foo 呢，看这个栈就可以了解了

匿名函数 => baz => bar => foo

![](https://i.loli.net/2018/12/05/5c07d541595f4.png)

3. 再来个死循环的栗子，看在调用栈是怎么表现的

```js
// 死循环
function foo() {
  return foo()
}

foo()
```

栈的大小是有限制的，死循环后，一直压入栈，栈就会被撑爆了。 然后浏览器就报`栈溢出`错误了

![](https://i.loli.net/2018/12/05/5c07d6100ea8d.png)

## Q3：单线程没有异步操作的话，多个 HTTP 请求(或 IO 操作)是什么样的？

```js
// 假装HTTP请求
var foo = $.getSync('//foo.com')
var bar = $.getSync('//bar.com')
var qux = $.getSync('//qux.com')

console.log(foo)
console.log(bar)
console.log(qux)
```

执行步骤描述：

1. 获取 `foo.com`的数据
2. 等 1 获取到数据，开始获取 `bar.com` 的数据
3. 等 2 获取到数据，开始获取 `qux.com` 的数据
4. 输出数据

总运行时间：1 + 2 + 3 + 4 的时间

每次操作一下，页面就卡住了，等执行完，才继续操作。

所以光靠单线程来执行代码，体验不好，只能做页面显示和一些简单交互。

就像这个图：
![](http://static.izhongxia.com/pictures/blog_sync.gif)

## Q4：同步获取数据在有什么问题？ ==》 阻塞问题

JavaScript 是单线程的语言，执行同步请求（或者耗时操作）的时候，页面就卡住了，等请求结束，页面才可以操作， 这种现象叫『阻塞（Blocking）』。

阻塞后，页面就卡住了，这种体验极差。

## Q5： 阻塞问题什么解决呢？==》 异步操作

1. 异步操作在调用栈什么样的呢？

```js
console.log('hi')

setTimeout(function() {
  console.log('I am a setTimeout')
}, 1000)

console.log('end')
```

执行步骤描述：

1. 执行 `console.log('Hi')` [压入栈，执行，执行完，从栈中删除（后面同样操作，统一说『执行』）]
2. 执行 `setTimeout`， 检测到是浏览器提供的 API，因此交给浏览器的线程去执行，主线程继续往下执行。
3. 执行 `console.log('end')`
4. 等待浏览器线程处理异步操作完成（注意：异步代码执行 和 `步骤3` 是`并行`的）
5. 浏览器线程 处理 `setTimeout` 结束，就会把相应的回调函数放到 `回调队列（Calback Queue）`中，等待主线程翻牌子
6. 主线程调用栈为空时，会去判断`回调队列`是否有函数需要执行， 有则拿出回调函数，压入到`调用栈`去执行，没有就等会再来看看。
7. 执行回调函数
8. 执行 `console.log('I am a setTimeout')`
9. 结束，调用栈为空

调用栈的顺序就像这个图：
![setTimeuot异步操作](http://static.izhongxia.com/pictures/blog_async.gif)

2. Ajax 异步操作，调用栈顺序如图：【和上面 setTimeout 异步操作的顺序是一样的】

![Ajax异步操作](http://static.izhongxia.com/pictures/blog_asycn_ajax.gif)

3. 如果有多个异步操作呢？

```js
console.log('hi')

setTimeout(function() {
  console.log(1000)
}, 1000)

setTimeout(function() {
  console.log(500)
}, 500)

setTimeout(function() {
  console.log(300)
}, 300)

console.log('end')
```

可以看到两点：

1. 异步操作是并发执行的
2. 异步回调执行的先后顺序跟 `Callback Queue`的排队顺序有关

![](http://static.izhongxia.com/pictures/blog_mul_async.gif)

## Q6：DOM API 操作，调用栈又是什么样的？

```js
console.log('hi')

$.on('button', 'click', function() {
  console.log('btn click')
})

setTimeout(function() {
  console.log('settimetout 5000')
}, 5000)

console.log('end')
```

从图中可以看出，

1. DOM 点击事件的监听，会一直在 浏览器线程里面运行着

- 2. 点击 DOM，则把点击的回调函数放到 `Callback Queue`

3. 多次点击，回调函数会在`Callback Queue` 排队等待主线程调用栈为空时，来拿回调函数去执行。

> 除了 DOM 事件一直在浏览器线程监听着外，其他和上面异步操作的执行方式一样

![](http://static.izhongxia.com/pictures/blog_dom_click.gif)

到这里，已经大概了解 JavaScript 代码在浏览器下如何运行的，有了一个基本的认识。

下面是一些小思考。

## Q7：JavaScript 是单线程，又是异步的，这两者有冲突吗？

- 单线程  
  因为 JavaScript 引擎只有一个主线程在执行 JS 的代码，所以同一时刻只会有一段代码在执行。
- 异步操作  
  异步操作是浏览器的多个常驻线程去执行的, 这个是浏览器的行为。

举个栗子：  
异步 Http 请求，是浏览器的`执行线程` 和 `事件触发线程`共同完成的， 执行线程去执行异步请求， 事件触发线程监测 异步请求是否完成，完成则把回调函数插入 回调队列(Callback Queue)，JS 引擎主线程在执行完调用栈的函数后（调用栈为空时），会去查看 回调队列是否有需要执行的回调，有则一个一个拿出来执行，没有就等一会儿在来看。

## 参考文章

1. [《【Youtube 视频】菲利普·罗伯茨：到底什么是 Event Loop 呢？ | 欧洲 JSConf 2014》](https://www.youtube.com/watch?v=8aGhZQkoFbQ)
2. [《什么是 Event Loop？》](http://www.ruanyifeng.com/blog/2013/10/event_loop.html)
3. [《【MDN】调用栈
   》](https://developer.mozilla.org/zh-CN/docs/Glossary/Call_stack)
4. [《并发模型与事件循环
   》](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/EventLoop)
