---
layout: post
title: "移动端手势的实现原理"
date: 2017-04-15 20:15
comments: true
toc: true
tags: 
	- javascript
	- mobile
categories: 
	- javascript
---

探究下移动端上一些手势实现的思路

<!-- ![](/assets/blogImg/mobile-gesture.jpg) -->

<!-- more -->

### 前言

之前写过一篇介绍[移动端触摸事件](/2017/03/25/mobile-events/)的文章，我们得知移动上几个原生的触摸事件：``touchstart``、``touchmove``、``touchend``、``touchcancel``，那怎么利用这些原生的事件来实现一些手势呢，本文主要就是介绍这些手势交互实现的一些思路

### 单指手势

前提，整个事件定义一个中间状态status表示当前的触摸状态

##### 轻触tap

``touchstart``触发时，状态改为tap，若``touchend``时还是tap，则认为是tap

##### 双击轻触doubletap

触发tap事件时记录当前时间，下次触发tap事件时用当前时间与记录的时间进行对比，如果小于300ms则认为触发了doubletap

##### 长按press

``touchstart``触发时设置一个500ms的setTimeout，500ms后仍然没有``touchend``触发则把状态改为press，等到``touchend``时检测到状态press则认为触发press

##### 平移pan

``touchmove``过程中检测状态是否是tap或者press，并且移动距离大于10px（自己规定）则认为是pan

关于移动距离的计算：

```
var distanceX = touchEnd.clientX - touchStart.clientX,	// touchmove的x坐标减去touchstart的x坐标
var distanceY = touchEnd.clientY - touchStart.clientY,	// touchmove的y坐标减去touchstart的y坐标
var distance = Math.sqrt(Math.pow(distanceX, 2) + Math.pow(distanceY, 2));	// 利用勾股定理求值
```

##### 轻拂flick

``touchend``时通过pan的移动距离和移动事件算出速度（X轴和Y轴的合速度），如果速度大于0.5且触摸过程时间小于100ms则认为是flick

关于合速度：
将物体的速度分为x方向速度（水平速度）,和y方向速度（竖直速度）
那么合速度就是&radic;(x<sup>2</sup>+y<sup>2</sup>)，即根号下x平方加y平方

### 多指手势

这里只研究两个手指的触控，假设：

``touchstart``时，两个手指的坐标点 A(x1, y1)、B(x2, y2)
``touchmove``时，两个手指的坐标点  C(x3, y3)、D(x4, y4)

##### 旋转rotate

计算AB，CD线段与坐标轴的夹角，对角度相减即得到旋转角度

##### 缩放scale

计算AB线段长度和CD线段长度(勾股定理)，两条线段做比值就好

##### 平移ranslate

平移的话我们只计算A点到C点的x坐标变化量

### 扩展

##### contains / compareDocumentPosition

回想下两个手指同时触控的场景，有个问题不可避免，就是当两个手指作用在不同的DOM上面时该触发哪个节点的事件呢？

我们规定取两个手指节点公有的最近父节点作为触发的节点，那么如何取这个公用的最近父节点呢？没错，我们可以通过``contains()``和``compareDocumentPosition()``来获取

```
function contains(ele1, ele2) {
  return ele1.contains ? ele1 != ele2 && ele1.contains(ele2) : !!(ele1.compareDocumentPosition(ele2) & 16);
}
//获得共有最近的父节点
function getCommonRootNode(ele1, ele2) {
  while (ele1) {
    if (contains(ele1, ele2) || ele1 === ele2) {
      return ele1;
    }
    ele1 = ele1.parentNode;
  }
  return null;
}
```

首先是``DOMElement.contains(DOMNode)``，作用于IE浏览器，用来确认DOMNode是否包含在另一个DOMElement中，是则返回true，注意，当DOMNode和DOMElement一致时，也是返回true，虽然不太合理

其次是``NodeA.compareDocumentPosition(NodeB)``，IE8及IE8以下不支持，比较两个节点，并返回描述它们在文档中位置的整数，需要注意这个方法的返回值：
![](/assets/blogImg/mobile-gesture-1.jpg)

如果满足P1在P2之前，而且P1包含P2，则返回结果就是4+16=20

##### load / DOMContentLoaded

``DOMContentLoaded``事件：当初始HTML文档被完全加载和解析完成之后便触发，无需等待样式表、图像和子框架完成加载，低版本IE不支持，可以用``onreadystatechange``事件（``判断if(document.readyState == 'complete')``）

``load``事件：在页面完全加载后触发，各浏览器都支持

[具体可查看DOMContentLoaded与load的区别](http://www.cnblogs.com/caizhenbo/p/6679478.html)

##### relatedTarget

``relatedTarget``是一个事件属性，返回与事件的目标节点相关的节点
  对于``mouseover``事件来说，该属性是鼠标指针移到目标节点上时所离开的那个节点
  对于``mouseout``事件来说，该属性是离开目标时，鼠标指针进入的节点
  对于其他类型的事件来说，这个属性没有用

### 成熟的手势库
[hammer.js](http://hammerjs.github.io/)
[zepto的touch模块](https://github.com/madrobby/zepto/blob/master/src/touch.js#files)

