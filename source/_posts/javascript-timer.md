---
layout: post
title: "JavaScript定时器"
date: 2017-04-21 21:56
comments: true
toc: true
tags: 
	- javascript
categories: 
	- javascript
---

主要探究``setTimeout()``、``setInterval()``的运行机制

如果你知道``setTimeout(fn, 0)``的作用，那可以不用点开本文

![](/assets/blogImg/javascript-timer.jpg)

<!-- more -->

### 前言

JavaScript的定时器主要是由``setTimeout()``和``setInterval()``组成

### setTimeout()

```
setTimeout(fn|code, delay);
```

表示``delay``秒之后执行``fn``或者``code``，只执行一次

第一个参数可以是一个函数，也可以是一段代码字符串，因为内部是用``eval``来执行的，所以需要是字符串

第二个参数是指延迟多少毫秒后执行第一个参数，省略的话则表示0

其实后面还可以带其他参数，作为第一个参数（函数）的实参，但是IE9以下不支持，例如：

```
// 2秒后输出1
// 但是一般没这么写，直接把实参写到函数体内不就好了
setTimeout(function(a){
  console.log(a);
}, 2000, 1)
```

注意：
1. 返回值是一个整数值，代表这个计时器，供给``clearTimeout()``来清除这个定时器
2. HTML5标准规定，``setTimeout()``的最短时间间隔是**4毫秒**
3. ``setTimeout()``是挂在**window**对象下的，延迟之后的执行函数中的``this``，永远指向**window**

### setInterval()

大概用法与``setTimeout()``一致，不过``setInterval()``表示的是**每隔多久就会执行一次**

需要注意的是，这次间隔时间是从**上次执行开始**就开始计算的，比如说，每100ms执行一次，但是执行过程需要5ms，也就是95ms之后就会执行第二次了

注意：HTML5标准规定，``setInterval()``的最短间隔时间是**10毫秒**

### clearTimeout()、clearInterval()

``setTimeout()``、``setInterval()``返回的是一个整数值，代表这个定时器，传入对应的``clearTimeout()``或``clearInterval()``则可清除对应的定时器

### 运行机制

想要了解运行机制，首先得了解下JavaScript的``Event Loop``：

> [JavaScript：彻底理解同步、异步和事件循环(Event Loop)](https://segmentfault.com/a/1190000004322358)
> [并发模型与事件循环](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/EventLoop)
> [关于Event Loop](http://www.ruanyifeng.com/blog/2014/10/event-loop.html) 

JavaScript是**单线程**的，我们称这条单线程为**主线程**，所有同步任务（包括调用异步函数，但不包括执行异步任务、触发回调等操作）都在主线程上执行，形成一个**执行栈**

主线程之外，还存在一个**任务队列**（task queue，也有人叫做**消息队列**），只要异步任务有了运行结果，就在任务队列之中放置一个事件(消息，对应着回调函数)

一旦执行栈中的所有同步任务执行完毕，系统就会读取任务队列（由于定时器的存在，所以会先判断是否到了执行时间），于是那些对应的异步任务就结束等待状态，进入执行栈，开始执行，主线程会不断重复这一操作，这过程就叫**事件循环**(Event Loop)

![](/assets/blogImg/javascript-timer-1.png)

那么有上面的知识基础的话，我们应该清楚``setTimeout()``、``setInterval()``会将执行代码先放到异步任务（即移出本次的执行栈），然后在下一次事件循环时判断是否到了执行时间，是则执行，还没到则下次事件循环再判断，换句话说，也就是要等到本轮Event Loop的同步任务都执行完，才会判断是否开始执行

因此也无法保证一定会按照规定的时间执行，比如说本轮的某个同步任务执行完需要很久很久的时间

特别是对于``setInterval()``事件，它具有积累性，比如说：

```
setInterval(function () {
  console.log(2);
}, 1000);

//下面执行完需要3秒
(function () {
  sleeping(3000);	
})();
```

结果会是:下面的语句完成(需3秒)后连续输出三个2，然后再开始每隔1秒输出一个2，就是因为``setInterval()``具有累积效应

### setTimeout(fn, 0)

经常看到``setTimeout(fn, 0)``，可能很多人看到0会认为是立即执行，其实不然，通过上面运行机制的了解也应该知道不会立即执行

其实他最主要的作用是用来改变事件的执行顺序，比如：

```
var input = document.getElementsByTagName('input[type=button]')[0];

input.onclick = function A() {
  setTimeout(function B() {
    input.value +=' input';
  }, 0)
};

document.body.onclick = function C() {
  input.value += ' body'
};
```

当我们点击input时，触发的几个函数的执行顺序为：A -> C -> B

### 扩展

关于**正常任务**与**微任务**

**正常任务**：下一轮Event Loop才会执行的任务，大概有这样的正常任务：setTimeout、setInterval、setImmediate、I/O、各种事件（比如鼠标单击事件）的回调函数
**微任务**：本轮Event Loop的所有任务结束后执行，目前主要是：process.nextTick和Promise

简单例子加以理解：

```
console.log(1);

setTimeout(function() {
  console.log(2);
}, 0);

Promise.resolve().then(function() {
  console.log(3);
}).then(function() {
  console.log(4);
});

console.log(5);
```

执行结果为：先输出1 然后5 然后3 然后4 然后2
解释：首先输出1，然后setTimeout(fn,0)是在本轮队列全部执行完后才执行，Promise的resolve是放在本轮队列的最后执行，但还是属于本轮，因此接下来是输出5，然后才是Promise的resolve，输出3，输出4，最后才是输出2

另一个经典例子：

```
setTimeout(function() {
  console.log(1)
}, 0);
new Promise(function executor(resolve) {
  console.log(2);
  for( var i=0 ; i<10000 ; i++ ) {
    i == 9999 && resolve();
  }
  console.log(3);
}).then(function() {
  console.log(4);
});
console.log(5);
```

执行结果为：输出2，3，5，4，1
解释：setTimeout(fn,0)是在本轮队列执行完后才执行，Promise里面的函数直接执行，输出2，输出3，Promise的resolve则是放到本轮队列的最后，因此接下来执行5，然后才是执行resolve，即then里面的输出4，最后才是setTimeout的输出1