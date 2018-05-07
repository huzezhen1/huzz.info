---
layout: post
title: "自定义事件"
date: 2017-04-02 18:26
comments: true
toc: true
tags: 
	- javascript
categories: 
	- javascript
---

了解下自定义事件，主要是JavaScript中的事件触发器

<!-- ![](/assets/blogImg/custom-event.jpg) -->

<!-- more -->

### 前言

在之前一篇关于 [移动端触摸事件](/2017/03/25/mobile-events/) 的文章中，我们用到自定义事件来模拟``click``事件并阻止300ms后的原生``click``事件来解决点透问题

现在就讲下这个自定义事件，尤其是事件触发器：``dispatchEvent(高级浏览器)``、``fireEvent(IE浏览器)``

### 绑定

绑定一个自定义事件，还是使用大家比较熟悉的``addEventListener()``和``attachEvent()``

既然提到了，就带大家回顾下这两个方法，可自行选择跳过

首先是``addEventListener()``方法，语法如下：
```
element.addEventListener(event, callback, useCapture)
```
参数``event``：绑定的事件名称
参数``callback``：触发的回调函数，事件对象会作为第一个参数传入函数
参数``useCapture``：布尔值，表示指定事件是在捕获（true）或冒泡（false）阶段执行，默认值是false（[事件冒泡和捕获的执行顺序](http://blog.csdn.net/moguzhale/article/details/53503044)）

简单例子：
```
var dom = document.getElementById('test');
dom.addEventListener('click', function(){
  console.log('click success!');
}, false)
```

需要注意的是IE8及IE8以下不支持``addEventListener``这个方法，它们绑定事件使用的是``attachEvent()``

```
elemetn.attachEvent(event, callback)
```
参数``event``：绑定的事件名称，但是注意要加``on``，例如：``onclick``
参数``callback``：触发的回调函数，事件对象会作为第一个参数传入函数

简单例子：
```
var dom = document.getElementById("test");
dom.attachEvent('click', function(){
  console.log('click success!');
})
```


### 生成与触发

我们先看一个例子，这例子就是一个自定义事件的触发器：

```
var fireEvent = function(element,event) {  
  if (document.createEventObject) {  
    // IE浏览器支持fireEvent方法  
    var evt = document.createEventObject();  
    return element.fireEvent('on'+ event, evt)  
  } else {  
    // 其他标准浏览器使用dispatchEvent方法  
    var evt = document.createEvent('HTMLEvents');   
    evt.initEvent(event, true, true);  
    return !element.dispatchEvent(evt);  
  }  
};
```

这里注意两个方面，一个是**event对象实例的创建**，一个是**事件的触发**

主要分为高级浏览器与IE浏览器两种情况：

在高级浏览器下：
1. 先调用``document``对象的``createEvent``方法得到一个event对象实例
2. 再执行这个对象实例的``initEvent``方法初始化，这方法接收三个参数：事件类型，是否冒泡，是否阻止浏览器的默认行为
3. 初始化之后可以给这个对象实例加属性并定义值，这些属性的值能在触发之后的回调函数的事件对象取到
4. 调用该DOM元素的``dispatchEvent``方法，参数是上面生成的对象实例，这样就触发了

在IE浏览器下：
1. 调用``doucment``对象的``createEventObject``方法得到一个event对象实例
2. 可以这个对象实例加属性并定义值，这些属性的值能在触发之后的回调函数的事件对象取到
3. 调用该DOM元素的``fireEvent``方法，第一个参数是事件类型(绑定时事件没有on开头的话，这里记得前缀加上on)，第二个参数是上面得到的event对象实例


### 总结

``document.creatEventObject()``是IE创建event对象实例的方法，和document.creatEvent( 'HTMLEvents' )在非IE主流浏览器下的作用相同

``fireEvent``是IE下的事件触发器，与``dispatchEvent``在非IE主流浏览器下作用相同


