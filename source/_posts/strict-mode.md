---
layout: post
title: "关于Javascript的严格模式"
date: 2017-01-23 14:26
comments: true
toc: true
tags: 
	- javascript
categories: 
	- javascript
---

ECMAScript 5新增了一种新的运行模式：**严格模式**

<!-- ![](/assets/blogImg/strict-mode.jpg) -->

<!-- more -->

### 1. 前言

本文主要为了解决三个问题：为什么要用严格模式、如何进入严格模式、严格模式有哪些规则？

### 2. 使用严格模式有什么作用

消除JavaScript语法的一些不合理、不严谨的地方，减少怪异行为（当确定的问题发生时抛出相应错误）
消除代码运行中的一些不安全的地方，确保代码运行的安全
提高编译器效率，增加运行速度

### 3. 如何进入严格模式

首先，进入的严格模式的标志是：``'use strict';``

##### 3.1 针对整个脚本

在顶部写上: ``'use strict';``，这段脚本就在严格模式中执行

```
<script>
'use strict';
// 这样就进入严格模式 
console.log('严格模式');
</script>

<script>
// 这样是正常模式
console.log('正常模式')
</script>
```

##### 3.2 针对单个函数

在函数体内第一行写上：``'use strict';``，这个函数就启用严格模式执行

```
function fn1() {
  'use strict';
  console.log('严格模式');
}

function fn2() {
  console.log('正常模式');
}
```

##### 3.3 其他注意点

支持严格模式的浏览器有：IE10+、Firefox4+、safari12+、opera12+、chrome
不支持严格模式的浏览器会将``'use strict';``当作普通字符串语句执行


### 4. 严格模式的规则

##### 4.1 变量方面的规则

* 全局变量需显式声明，不能意外创建
```
'use strict';
a = 1;	// 报错，在正常模式下会当成全局变量
console.log(a);
```

* 不能对变量调用delete操作符
根本原因是``var``、``let``、``const``声明的变量默认是不可配置的，即``configurable``的值是``false``
```
'use strict';
var a = 1;
delete a;	// 报错，在正常模式下会返回false而不会报错，也是不会删除成功
```

* 严格模式下新增一些保留字不可作为变量名
保留字：``implements``, ``interface``, ``let``, ``package``, ``private``, ``protected``, ``public``, ``static``, ``yield``

##### 4.2 对象方面的规则

* 不能对只读属性赋值

```
  var person = {
    name: 'lily'
  }
  Object.defineProperty(person, 'name', {
    writable: false // 设置为只读属性
  })
  person.name = 'lucy';	// 会报错，正常模式下不会报错，但也赋值不成功
  console.log(person.name);	// 正常模式下输出'lily'
```

* 不能对不可配置的属性进行delete操作

```
  var store = {
    book: '哈利波特'
  }
  Object.defineProperty(store, 'book', {
    configurable: false	// 设置为不可配置属性
  })
  delete store.book; // 会报错，正常模式不会报错，但也删除不了
  console.log(store.book); // 正常模式会输出'哈利波特'
```

* 不能对一个使用getter方法读取的属性进行赋值

```
  var obj = {
	get p() {return 1;} // getter方法
  }
  obj.p = 2; // 报错，正常模式不会报错，但也赋值不了
  console.log(obj.p); // 正常模式会输出2
```

* 不能对禁止扩展的对象添加新属性
使用``Object.preventExtensions(obj)``方法来让``obj``无法拓展

```
var obj = {};
Object.preventExtensions(obj); // 这样obj就无法拓展
obj.a = 1; // 报错，正常模式则不会报错，但是也拓展属性不成功，obj仍是空对象
```

##### 4.3 函数方面的规则

* 函数如果有多个参数，参数名不能重名

```
  function test(num, num){ // 会报错
	console.log(num); // 正常模式会输出2
  }
  test(1, 2);
```

* 修改形参不会反映到arguments中，即arguments不追踪参数的变化

```
  function fn(value) {
    value = '形参的数据被修改';
    console.log(arguments[0]); // arguments的值不会因为形参被修改而修改，所以arguments[0]还是'形参'，正常模式下会输出'形参的数据被修改'
    console.log(value);	// '形参的数据被修改'
  }
  fn('形参');
```

* 不允许使用arguments.callee和函数的caller
函数的caller：在一个函数调用另一个函数时，被调用函数会自动生成一个caller属性，指向调用它的函数对象
arguments.callee：当函数被调用时，它的arguments.callee对象就会指向自身，也就是一个对自己的引用

```
  function fn() {  
    var caller = testCaller.caller; // 会报错
    var callee = arguments.callee; // 会报错
    alert(caller); // 正常模式会弹出 function aCaller() { fn(); }
    alert(callee); // 正常模式会弹出自身函数的实现代码
  }
  function aCaller() {  
      fn();  
  }  
  aCaller()
```

##### 4.4 其他规则

* 创设eval作用域

```
  var x = 2;
  console.log(eval('var x = 3;x')); // 3，因为在严格模式下，eval语句本身就是一个作用域
  console.log(x); // 2，正常模式会输出3，因为值经过eval语句的执行已经被改变为3
```

* 禁止this关键字指向全局对象

```
  function other() {
    console.log(this.value); // 报错，this不会指向全局对象，报错：Cannot read property 'value' of undefined
  } 
  other();
```

* 不允许使用八进制字面量

```
  var other = 010; // 报错：Octal literals are not allowed in strict mode
  console.log(other); // 正常模式下会输出8
```

* 不允许使用with语句
* 不允许使用eval和arguments作为标识符，不能对他们进行赋值
