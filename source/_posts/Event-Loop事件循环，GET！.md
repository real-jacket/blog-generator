---
title: Event Loop事件循环，GET！
date: 2018-10-29 21:14:06
tags: JS
---
JS中比较让人头疼的问题之一要算异步事件了，比如我们经常要等后台返回数据后进行dom操作，又比如我们要设置一个定时器完成特定的要求。在这些同步与异步事件里，异步事件肯定是在同步事件之后的，但是异步事件之间又是怎么样的一个顺序呢，比如多个setTimeout事件又是怎么样一个执行顺序？这就涉及到事件循环：Event Loop。

## JS的单线程

虽然现在的JS可以用来做多方面的开发，但是最初的JS是浏览器的专用语言，用来操作DOM。所以从诞生之初，JS就被设计成单线程语言，原因是不想让浏览器变得太复杂，因为多线程需要共享资源、且有可能修改彼此的运行结果，对于一种网页脚本语言来说，这就太复杂了。如果 JavaScript 同时有两个线程，一个线程在网页 DOM 节点上添加内容，另一个线程删除了这个节点，这时浏览器应该以哪个线程为准？是不是还要有锁机制？所以，为了避免复杂性，JavaScript 一开始就是单线程，这已经成了这门语言的核心特征，将来也不会改变。

但是这种单线程机制却制造了另一个麻烦，假如一个操作需花费很长时间，那么此时浏览器就会一直等待这个操作完成，就会造成不好的体验。因此，JS的另一个事件就是异步事件。异步事件是专门将一些事件以队列的形式储存到浏览器的任务队列中，等同步事件执行完后再去执行，这样就避免了页面堵塞。

JavaScript 引擎怎么知道异步任务有没有结果，能不能进入主线程呢？答案就是引擎在不停地检查，一遍又一遍，只要同步任务执行完了，引擎就会去检查那些挂起来的异步任务，是不是可以进入主线程了。这种循环检查的机制，就叫做事件循环（Event Loop）。

## 浏览器的事件循环

![浏览器事件循环](https://upload-images.jianshu.io/upload_images/12812641-300cc2c2bc72ad43.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如上图所示，js中的基本数据与对象都会储存在栈内存中，其中复杂类型数据对象会在堆内存储存其数据结构，栈内存储存的是对这个数据结构的引用。

### 执行栈
javaScript是单线程，也就是说只有一个主线程，主线程有一个栈。当JS代码执行时，代码会被推入执行栈中进行运行，运行代码的过程中，同步事件会立即执行，其中Dom、Ajax以及SetTimeout等异步事件会注册回调函数，放入事件回调队列中，等同步代码执行完之后执行。这样一个循环便是浏览器的Event Loop。

![执行过程中栈的变化](https://upload-images.jianshu.io/upload_images/12812641-ea9bfa93fad8e30e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 异步过程

但是在回调队列中这些事件又是怎么样一个执行顺序呢？实际上异步队列存在两个队列，一个宏任务队列，一个微任务队列，其这就涉及到两个概念:
* 宏任务(MacroTask):
包括整体代码script，setTimeout、setInterval、setImmediate、I/O、UI渲染
* 微任务(MicroTask)：
Promise、process.nextTick、Object.observe、MutationObserver

在栈内存中代码执行完后，浏览器空闲，立即处理回调队列，将回调队列中的宏任务队列中的事件推入执行栈中执行。
* 首先会执行宏任务，如果宏任务中存在宏任务，则会把该任务放到宏任务队列中。如果该任务里存在微任务，则把微任务放在微任务队列。
* 在这个宏任务执行完后，首先去看微任务队列中是否有任务，然后把微任务推到执行栈中执行。

执行完微任务队列，这一次循环就结束了，然后再进行在宏任务队列中进行下一个宏任务，微任务，直至回调队列清空。

上述事件归纳后，以下例说明：
![代码示例](https://upload-images.jianshu.io/upload_images/12812641-a70610755236a536.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

分析：

循环1：
* 【task队列：script ；microtask队列：】
1.首先整个代码被推到执行栈中执行，这是一个宏任务（整个script代码）
2.运行中，同步代码立即执行，new Promise中的fn是立即执行的。setTimeout被放在宏任务队列中，promise1、promise2被放在微任务队列中。
* 【task队列：setTimeout ；microtask队列：promise1、promise2】
3.宏任务script执行完后，执行微任务队列，取出microtask队列，推入执行栈执行，第一次循环到此结束。

循环2：
* 【task队列：setTimeout ；microtask队列：】
4.取出宏任务中的setTimeout推入执行栈执行，如果有微任务则，则被放在微任务队列（这里没有）。
5.宏任务执行完，去微任务队列执行（微任务队列为空）。
* 【task队列：；microtask队列：】
6.宏任务队列为空，循环至此结束。



## Nodejs 事件循环

nodejs中的事件循环跟浏览器不一样，浏览器的循环是遵循ES标准里的，nodejs里的循环是通过LIBUV库实现的。

当 Node.js 启动时，会做这几件事

* 初始化 event loop
* 开始执行脚本（或者进入 REPL，本文不涉及 REPL）。这些脚本有可能会调用一些异步 API、设定计时器或者调用 process.nextTick()
* 开始处理 event loop

nodejs的Event Loop 一共有6个阶段：
```
   ┌───────────────────────┐
┌─>│        timers         │
│  └──────────┬────────────┘
│  ┌──────────┴────────────┐
│  │     I/O callbacks     │
│  └──────────┬────────────┘
│  ┌──────────┴────────────┐
│  │     idle, prepare     │
│  └──────────┬────────────┘      ┌───────────────┐
│  ┌──────────┴────────────┐      │   incoming:   │
│  │         poll          │<─────┤  connections, │
│  └──────────┬────────────┘      │   data, etc.  │
│  ┌──────────┴────────────┐      └───────────────┘
│  │        check          │
│  └──────────┬────────────┘
│  ┌──────────┴────────────┐
└──┤    close callbacks    │
   └───────────────────────┘
```
其中我们主要需要关注的是timers、poll、check阶段：
* timers 阶段：这个阶段执行 setTimeout 和 setInterval 的回调函数。
* I/O callbacks 阶段：不在 timers 阶段、close callbacks 阶段和 check 阶段这三个阶段执行的回调，都由此阶段负责，这几乎包含了所有回调函数。
* idle, prepare 阶段（译注：看起来是两个阶段，不过这不重要）：event loop 内部使用的阶段（译注：我们不用关心这个阶段）
* poll 阶段：获取新的 I/O 事件。在某些场景下 Node.js 会阻塞在这个阶段。
* check 阶段：执行 setImmediate() 的回调函数。
* close callbacks 阶段：执行关闭事件的回调函数，如 socket.on('close', fn) 里的 fn。

### timers阶段

计时器实际上是在指定多久以后可以执行某个回调函数，而不是指定某个函数的确切执行时间。当指定的时间达到后，计时器的回调函数会尽早被执行。如果操作系统很忙，或者 Node.js 正在执行一个耗时的函数，那么计时器的回调函数就会被推迟执行。

### poll 阶段（轮询阶段）

poll 阶段有两个功能：

1. 如果发现计时器的时间到了，就绕回到 timers 阶段执行计时器的回调。
2. 然后再，执行 poll 队列里的回调。

当 event loop 进入 poll 阶段，如果发现没有计时器，就会：

* 如果 poll 队列不是空的，event loop 就会依次执行队列里的回调函数，直到队列被清空或者到达 poll 阶段的时间上限。
* 如果 poll 队列是空的，就会：
    1. 如果有 setImmediate() 任务，event loop 就结束 poll 阶段去往 check 阶段。
    2. 如果没有 setImmediate() 任务，event loop 就会等待新的回调函数进入 poll 队列，并立即执行它。

一旦 poll 队列为空，event loop 就会检查计时器有没有到期，如果有计时器到期了，event loop 就会回到 timers 阶段执行计时器的回调。

### check 阶段

这个阶段允许开发者在 poll 阶段结束后立即执行一些函数。如果 poll 阶段空闲了，同时存在 setImmediate() 任务，event loop 就会进入 check 阶段，执行setImmediate() 回调。

### Event Loop 大体流程
每一个阶段都有一个队列，我们只关注 timers、poll、check阶段来分析一下，我们在用命令行运行 node server.js 命令时，发生了什么：
1、Node.js启动，初始化 Event Loop
2、运行server.js脚本内容
3、开始运行Event Loop
4、timers阶段看脚本里是否设置定时器setTimeout，比如一个4ms延迟与一个100ms延迟的定时器，把它放到timers队列中，进入下一步，I/O callbacks 阶段，idle, prepare 阶段，这两个阶段都不会停留。
5、进入poll（轮询）阶段，首先它会查看定时器时间是否到了，比如4ms到了，他就进入下一阶段check阶段、close callbacks 阶段，然后回到timers阶段执行设置的4ms回调函数，接着继续第4步到第5步。4ms没到，则停留在这一阶段，处理poll队列里的任务，直到4ms到、100ms到，然后循环回到timers阶段执行回调。

这里有一个问题：当poll阶段在处理任务1时，比如这个任务1要花费100ms，在这100ms期间，setTimeout定时器到了，则它的回调会等poll处理玩任务1后立即循环进入timers阶段执行

6、从poll阶段进入check阶段时，主要是看是否有setImmediate() 任务，如果有则立即执行，然后再进入close callbacks 阶段，进行循环，进入timers阶段。

### setImmediate() vs setTimeout()

setImmediate 和 setTimeout 很相似，但是其回调函数的调用时机却不一样。

setImmediate() 的作用是在当前 poll 阶段结束后调用一个函数。 setTimeout() 的作用是在一段时间后调用一个函数。一般来说 setImmediate 会先于 setTimeout 执行，但是第一次启动的时候不一样，这两者的回调的执行顺序取决于 setTimeout 和 setImmediate 被调用时的环境。
例如:
```js
setTimeout(()=>{
    console.log('setTiomeout')
},0)

setInmediate(()=>{
    console.log('setInmediate')
})
```
为什么会发生这种情况呢？因为我们启动node.js时, node会做三件事, 初始化event loop，运行脚本,开始event loop。运行脚本与开始event loop这两件事不是同时执行的，它两中间间隔多少并不清楚，这跟环境性能有关。然后要注意的一点，setTimeout的延迟时间最小为4ms，所以这里的0相当于4。
* 可能两者间隔5ms，当进入timers阶段的时候，node发现，4ms已经过了，立即执行setTimeout定时器回调，然后执行setImmediate。
* 也可能两者间隔3ms，当进入timers阶段的时候，node发现，4ms还没过，就进入下一阶段，一直到checked，执行setImmediate，然后等到4ms时再执行setTimeout。

### process.nextTick()

从技术上来讲 process.nextTick() 并不是 event loop 的一部分。实际上，event loop 再次进入循环前，会去先执行process.nextTick()。

```js
setTimeout(()=>{
    console.log('setTiomeout')
},0)

setInmediate(()=>{
    console.log('setInmediate')
})

proces.nextTick(()=>{
    console.log('nextTick')
})
```
上述代码中nextTick先于其它两个执行，Vue中有`Vue.nextTick()`方法就是类似的思想。
 
注：
本篇文章参考：
[Event Loop、计时器、nextTick](https://juejin.im/post/5ab7677f6fb9a028d56711d0#heading-9)
[这一次，彻底弄懂 JavaScript 执行机制](https://juejin.im/post/59e85eebf265da430d571f89#heading-2)
[从event loop规范探究javaScript异步及浏览器更新渲染时机](https://github.com/aooy/blog/issues/5)