---
layout: post
title: "探索移动端事件"
date: 2017-03-25 14:56
comments: true
toc: true
tags: 
	- javascript
	- mobile
categories: 
	- javascript
---

探索移动端的几个触摸事件

<!-- ![](/assets/blogImg/mobile-events.jpg) -->

<!-- more -->

### 前言

本文主要记录移动端几个touch类事件：``touchstart``、``touchmove``、``touchend``、``touchcancel``

以及事件对象event的几个主要属性：``changedTouches``、``targetTouches``、``touches``

记录的主要目的，首先是为了增加自己对移动端事件的了解，主要也是为了防止自己以后再做出将``touchmove``讲成``touchover``的蠢事，切记木有``touchover``这样的玩意！

### touch类事件

``touchstart``：当手指触摸到屏幕时会触发
``touchmove``：当手指在屏幕上移动时触发
``touchend``：当手指离开屏幕时触发
``touchcancel``：可由系统自行触发，比如手指触摸屏幕的时候，突然alert了一下，或者系统中其他打断了touch行为，则可以触发该事件

事件触发顺序：**touchstart** > **touchmove** > **touchend** > **click**

### 事件对象的主要属性

这里重点了解下事件对象``TouchEvent``的三个重要属性：

``changedTouches``：记录着触发该次事件的信息，一般length为1
``targetTouches``：保存了当前所触碰屏幕的手指信息,记录的是当前DOM节点上全部的触摸对象的信息
``touches``：保存了当前所有触碰屏幕的手指信息,记录的是屏幕上全部的触摸对象的信息

借助下面的场景帮忙理解：
有两个div，分别为A和B，B绑定了``touchstart``事件，然后依次执行以下操作：
1. 先放一根手指到B上面，触发``touchstart``事件，这时候三个属性都是一样的，包含这次触摸的Touch对象信息
2. 在上步操作的前提下，再分别放一根手指到A和B上，又会触发B的``touchstart``事件，这时候``changedTouches``只会包含后来两次触摸的Touch对象信息，因为上步操作的手指不变，``targetTouches``记录的则是B上面两根手指触摸的Touch对象信息， ``touches``则是屏幕上所有的触摸对象的信息，这里是指这三根手指触摸的Touch对象信息

如果使用``jQuery``，需要这样取这三个原生的触摸对象：``e.originalEvent.changedTouches``、``e.originalEvent.targetTouches``、``e.originalEvent.touches``

三个属性都是``TouchList``类型，每个属性都存储相关的``Touch``对象（一些触控位置、目标元素的信息）

### Touch对象的主要属性

主要了解下``Touch``对象中有什么主要属性

``clientX`` / ``clientY``：触摸点相对浏览器窗口的位置
``pageX`` / ``pageY``：触摸点相对于页面的位置
``screenX`` / ``screenY``：触摸点相对于屏幕的位置
``identifier``：Touch对象的ID
``target``：记录当前的DOM元素

### 注意点

第一个需要注意的地方，``TouchEvent``事件对象中的``touches``、``targetTouches``只存储接触屏幕的触点信息，所以在触发``touchend``事件时（即此刻手指已离开屏幕），这两个属性是空的，所以要获取触点最后离开的状态要使用``changedTouches``

第二个需要注意的地方，``touchstart``、``touchmove``、``touchend``、``touchcancel``、``click``的事件对象的target属性永远是触控事件最先发生的那个元素，借助下面的场景帮忙理解：
![](/assets/blogImg/mobile-events-1.jpg)
大的元素A，小的元素B，我们从元素A滑动到元素B上然后松开手指，这时候是触发A元素的``touchend``事件，因为它是最先发生的元素

那如果我们想正确触发元素B的``touchend``事件的话，可借助``document.elementFromPoint``得到顶层的元素再来触发

```
document.elementFromPoint(clientX, clientY)  // 传入坐标值，可以得到包含该坐标点的最顶层的元素
```

### 扩展

既然讲到了移动端的触摸事件，顺带提下它的``click``事件，大家都知道在移动端上``click``事件会有300ms的延迟，原因是因为在移动端浏览器上双击可以放大网页，浏览器为了区分是双击缩放还是点击事件，就设置了300ms的间隔时间，如果在300ms内有两次点击则认为是双击缩放

不过现在移动端网页基本会设置viewport来禁止用户缩放，但是部分低版本安卓、微信和QQ内置的webview还是会存在300ms延迟的问题
```
<meta name="viewport" content="width=device-width,user-scalable=no,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0>
```
那怎么解决呢？有人就会想，用``touchstart``事件来替代``click``，这样会带来一些问题，首先例如长按也会被认为是一次点击，而且会带来另一个经典的问题：**点透**

那什么是**点透**呢，看以下场景：
场景：元素上有个遮罩层，点击遮罩层时让遮罩层消失
问题：点击遮罩层，根据上文我们用``touchstart``事件来绑定遮罩层，点击之后遮罩层消失，但是遮罩层下的元素300ms后触发``click``事件
原因：``touchstart``事件发生后遮罩层消失后，浏览器会在300ms后找到当前最上层的元素触发``click``事件

为了解决这些问题，[fastclick.js](https://github.com/ftlabs/fastclick)就出现了，大致原理：
在检测到``touchend``事件时通过**DOM自定义事件**立即模拟一个``click``事件，再把300ms后的原生``click``事件阻止掉

关于**DOM自定义事件**再开篇详解
> [已更新，点击查看](/2017/04/02/custom-event/)