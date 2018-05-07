---
layout: post
title: "JavaScript 模块规范"
date: 2017-02-23 20:30
comments: true
toc: true
tags: 
	- javascript
categories: 
	- javascript
---

关于JavaScript模块化编程规范一些总结

包括：CommonJS、AMD、CMD、UMD、ES6

<!-- ![](/assets/blogImg/javascript-module-Specification.jpg) -->

<!-- more -->

### 前言

随着web page进化到web application，浏览器端需要处理的逻辑越来越复杂，需要展现的样式和动画等效果越来越多，对工程的要求也越来越高，模块化的需求也应运而生

使用模块化有以下好处：

* 组件的复用，降低开发成本和维护成本
* 组件单独开发，方便分工合作
* 模块化遵循标准，方便自动化依赖管理，代码优化，部署

而模块与模块之间要相互依赖是需要编写代码时遵循一定的模块规范，现存有以下几种规范

### CommonJS

偏向于服务器端的规范，例如[Node.js](https://nodejs.org/en/)就是遵循这个规范

加载模块采取的**同步方式**（区别于AMD与CMD），是因为服务端模块放在本地硬盘上，加载很快

CommonJS的一个模块就是一个脚本文件，每个模块都是单独的作用域，即在一个文件定义的变量都是私有的，其他文件不可见

使用require方法来加载模块，第一次加载该脚本时就会执行整个脚本，然后在内存中生成一个对象，然后下次再调用require方法时，不会再执行该模块，而是从缓存中取值

```
{
  id: '...',	// 模块名字
  exports: { ... },	// exports对象，模块导出的接口，别人从这里取值
  loaded: true,	// 表示模块是否加载完成
  ...
}
```

简单的例子：

```
// math.js
exports.add = function(a, b) {
  return a + b;
}

// other.js
var math = require('math');
math.add(1, 2);	// 3
```

因为CommonJS规范采取的是同步的方式来加载模块，所以在浏览器端不适合（浏览器端从服务器端加载模块容易受带宽等影响），所以在浏览器端出现了一个**AMD**规范

### AMD

AMD(Asynchronous Module Definition)，即异步模块定义，采用异步方式加载模块，典型代表：[RequireJS](http://requirejs.org/)

AMD也采用require方法加载模块，与CommonJS不同的是，它需要两个参数

```
require([module], callback);
```

第一个参数**[module]**指想要加载的模块，是一个数组，即可以加载多个模块
第二个参数为加载模块完成后立即执行的回调函数，该回调函数的参数与前面的模块数组一一对应

简单的例子：

```
require([math], function(math) {
  math.add(1, 2);
}
```

每个模块的编写，都要遵循AMD模块规范，即每个模块都要用define()函数来定义，具体如下

```
define(id?, dependencies?, factory);
```

第一个参数id，可选，表示模块的名字，不填则默认该脚本的名字
第二个参数dependencies，可选，表示依赖的模块，不填则默认['require', 'exports', 'module']，具体依第三个参数（工厂方法的参数来定）
第三个参数是模块的工厂函数，模块初始化要执行的函数或对象，如果为函数，它应该只被执行一次，如果是对象，此对象应该为模块的输出值

简单的例子：

```
// math.js，不依赖其他模块，返回一个对象
define(function(){
  var add = function(x, y) {
	return x + y;
  }
  return {
	add: add
  }
})
```

```
// 调用math.js模块
require([math], function(math) {
  math.add(1, 2);	//3
})
```

可见，AMD模块规范是在一开始require的时候就加载了所有所需的模块，属于**依赖前置**，这就区别于接下来要说的CMD模块规范，CMD规范是**依赖就近**

### CMD

CMD规范是SeaJS在推广过程中对模块定义的规范化产出的，它推崇**依赖就近，延迟执行**

简单的例子：

```
define(function (requie, exports, module) {
  // 依赖可以就近书写
  var a = require('./a');
  a.test();
  // 软依赖，所谓软依赖就是满足一定条件才加载某模块
  if (status) {
    var b = require('./b');
    b.test();
  }
});
```

跟AMD最大的区别就是：**AMD是依赖前置，CMD是依赖就近**

跟AMD的相同点：都是用**difine**和**require**，**都会预加载依赖的js文件**（但是调用和声明依赖的地方不同）

需要注意的是：
1. AMD也可以像CMD一样在代码中使用require来加载模块，但是这样的话这个模块就不会预先加载，而是在用到的时候同步加载（所以也不推荐这样写）
2. CMD的define也可以带三个参数，但是带id和dependencies参数的define用法不属于CMD规范，而属于[Modules/Transport规范](https://github.com/seajs/seajs/issues/242)

### UMD

通用模块规范，例如：你写了个小工具，希望他支持AMD又支持CommonJS，就可以使用UMD

人们希望一种规范来支持AMD和CommonJS规范，UMD应运而生，同时支持AMD和CommonJS规范，也支持古老的全局模块规范

```
(function (root, factory) {
  if (typeof define === 'function' && define.amd) {
    // AMD
    define(['jquery', 'underscore'], factory);
  } else if (typeof exports === 'object') {
    // Node, CommonJS-like
    module.exports = factory(require('jquery'), require('underscore'));
  } else {
    // Browser globals (root is window)
    root.returnExports = factory(root.jQuery, root._);
  }
}(this, function ($, _) {
  //方法
  function a(){};    //私有方法，因为下面没有在return中暴露
  function b(){};    //在return中暴露的方法
  function c(){};    //在return中暴露的方法
  //暴露多个方法
  return {
    b: b,
    c: c
  }
}));
```

UMD规范就像是一个语法糖，应用UMD规范的JS就是一个IIFE（立即执行函数），参数一是执行的环境，参数二是模块的定义体

从代码也可以看出，优先判断是否支持AMD，再判断CommonJS，两者都不支持则认为是浏览器环境（window）

### ES6的模块化

ES6发布的module并没有直接采用CommonJS，甚至连require都没有采用

它采用import、export实现模块的输入输出，import来导入其他模块提供的功能，export来规定模块的对外接口

有关**export**(导出)的几个简单例子

```
/*
 * 导出变量 - a.js
 */ 
export var a = 1;
export var b = 2;

// 或者

var a = 1;
var b = 2;
export {a, b}

/*
 * 导出函数
 */ 
export function foo(){}
export function bar(){}
// 或者

function foo(){}
function bar(){}
export {foo, bar as bar2}	// as是来给导出的变量名重命名

/*
 * 导出类
 */ 
export default class {}		// 关于export default，下面会讲到

```

有关**import**(导入)的几个简单例子

```
/*
 * 导入变量a和b
 */

import {a, b} from 'a'	// 上面export例子中的a.js，后缀可省略
console.log(a + b);	// 3

/*
 * 导入变量a和b，并将a重命名为c
 */
import {a as c, b} from 'a'	// 利用as来重命名，在import一样有效

```

利用** * **关键字来整体加载

```
// b.js
var a = 1;
var b = 2;
export {a, b}

// main.js
import * as num from 'b'	// 使用*号来导入整体，并用as来重命名
console.log(num.a);
console.log(num.b);
```

关于**export default**

本质上，export default输出的是一个叫做default的变量或方法，输入这个default变量时不需要花括号

```
// 导出函数 - c.js
export default function() {}

// 等效于：
function a() {};
export {a as default};

// 导入时可以这样
import a from 'c';

// 等效于，或者说就是下面这种写法的简写，是同一个意思
import {default as a} from 'c';
```

所以，当我们看到import哪个变量时没有花括号（没有*号），我们应该在脑海里能还原它带花括号时应该是怎么样的

```
import $,{each,map} from 'jquery';	// 这里的$，其实就是{default as $}的简写

```

### 扩展

[关于node.js的module.exports和exports](http://blog.csdn.net/pwiling/article/details/51958693)
[Node中没搞明白require和import，你会被坑的很惨](http://imweb.io/topic/582293894067ce9726778be9?utm_source=tuicool&utm_medium=referral)