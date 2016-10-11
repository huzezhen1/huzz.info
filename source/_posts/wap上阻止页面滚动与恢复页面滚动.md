---
title: 移动端上阻止页面滚动与恢复页面滚动
date: 2016-10-09 17:53:07
tags: 
- javascript
- wap
categories:
- javascript
---
># 场景

在一个HTML5页面上，弹出框弹出之后，显示遮罩层，背景其他按钮确实不能点，但在手机上测试，背景可以上下滑动

># 目标

在弹出框出现时阻止页面滚动并在弹出框消失后恢复滚动

># 方案

### 方案一
上锁：
```
$("body").css("position","fixed"); //这样的话，背景就固定了，但是页面会滚动到顶部
```
解锁：
```
$("body").css("position","relative"); //这样就恢复滚动
```

### 方案二
上锁：思路就是阻止``touchmove``事件的默认行为，在弹出框的时候阻止body的``touchmove``事件的默认行为
解锁：在弹出框消失的时候恢复这个默认行为
具体方案如下：
```
function bodyScroll(e){
	e.preventDefault();
}
```

原生写法，注意IE8以及IE8以下使用``attachEvent()``和``detachEvent()``
```
document.addEventListener("touchmove",bodyScroll,false);  /*上锁*/
docume.removeEventListener("touchmove",bodyScroll,false); /*解锁*/
```
jQuery写法
```
$("body").on("touchmove",bodyScroll);  /*上锁*/
$("body").off("touchmove",bodyScroll); /*解锁*/
```
