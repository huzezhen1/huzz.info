---
title: HTML5新特性与技巧
date: 2016-10-10 21:04:08
tags: 
- HTML5
- 新特性
categories: 
- HTML5
---

># 新的文档类型声明

```
<!DOCTYPE html>
```

># 字符集

HTML5使用UTF-8编码

># 去除link和script标签里的type属性

script和link里不需要写``type``

># figure和figcaption标签的组合

语义化地将图片与注释联系起来

```
<figure>
	<img src="xxx.jpg" />
	<figcaption>
		<p>这是一幅图片的描述</p>
	</figcaption>
</figure>
```

># 语义化了header和footer标签

```
<header>
...
</header>
```

```
<footer>
...
</footer>
```

># 新的small标签

HTML5里``small``标签将旁注呈现为小型文本，负责声明、注意事项、法律限制或版本声明的特征通常都是小型文本。
在HTML4或XHTML里已经定义过``small``，不过对它的使用却没有一个完整的说明。在HTML里，它主要用于网页下方的版本声明，邮箱等小型文本。

># hgroup标签(<mark>HTML5.1中已废除</mark>)

主要表明标题的集合，用处不大，如果想表明主标题与副标题，可如下：

使用标点符号

```
<h1>前端博文:HTML5新特性与技巧</h1>
```

使用span标签

```
<h1>前端博文
	<span>HTML5新特性与技巧</span>
</h1>
```

使用header标签

```
<header>
	<h1>前端博文</h1>
	<p>HTML5新特性与技巧</p>
</header>
```

># mark标签

标签内的字符会高亮显示

```
<mark>文字高亮</mark>
```

># contenteditable属性

让一个div元素变得可编辑，可在div中加上``contenteditable``属性，如：

```
<div contenteditable="true"></div>
```

但是需要注意，这样的话富文本也可以输入进去，如果想限制只能输入纯文本的话，可以这样：

```
<div contenteditable="plaintext-only"></div>
```

># placeholder属性

这个属性是占位符的意思，不用像以前用JS来实现占位符，可直接使用该属性（支持HTML5的浏览器）
placeholder属性适用于以下的``input``类型：text，search，url，telephone，email以及password

```
<input type="text" placeholder="请输入数字" >
```

但是不支持HTML5的浏览器还是得用JS的方法去实现占位符，因此大多数网站都是用``label``标签来假装占位符，通过隐藏和显示来实现效果以便兼容大部分浏览器

># required属性

指明某一输入是否必需，有两种声明方式：

```
<input type="text" name="input1" required>
或
<input type="text" name="input2" required="required">
```

主要在表单中用，声明了required的元素为空，提交时输入框会高亮提示

># autofocus属性

让元素（按钮、文本框等）自动获得焦点，而不用通过JS，如：

```
<input type="text" autofocus>
或
<input type="text" autofocus="autofocus">
```

># pattern属性

可在标签中通过这个属性来插入正则表达式，如：

```
<input type="text" pattern="[A-Za-z]{4,10}">
```

如果浏览器支持pattern属性，表单提交时会验证，不通过文本框会高亮提示

># data属性

自定义属性，以``data``前缀定义我们的自定义属性`` data-*``

HTML代码

```
<div id="myDiv" data-custom-val="my value"></div>
```

获取属性值的方法

```
var divEl = document.getElementById("myDiv");
alert(divEl.dateset.customVal); /* 通过dateset对象读取，注意需要使用驼峰写法 */
alert(divEl.getAttribute("data-custom-val")); /* 通过getAttribute方法读取 */
alert($("#myDiv").data("customVal")); /*通过jQuery的data方法读取，同样需要注意驼峰写法*/
```

设置属性值的方法

```
var divEl = document.getElementById("myDiv");
divEl.dataset.customVal = "the new value"; /* 通过dateset对象设置，驼峰写法 */
divEl.setAttribute("data-custom-val","the new value"); /* 通过setAttribute方法设置 */
$("#myDiv").data("customVal","the new value"); /* jQuery的data方法 */
```

CSS中也可以这样使用
```
<style>
	h1:hover:after{
		content:attr(data-custom-val);		/*注意这里*/
		color:black;
		position:absolute;
		left:0;
	}
</style>
```

># input文本框中type为email

表单中的input设置type为email后，提交表单会判断是否邮件格式，不是则文本框高亮提示

># input文本框的type为range

创建滑块，如：

```
<input type="range" min="0" max="10" step="1" value="">
```

可以使用css中的:before和:after来显示min和max的值，如：

```
input[type="range"]:before{
	content:attr(min);
	padding-right:5px;
}
input[type="range"]:after{
	content:attr(max);
	padding-left:5px;
}
```

># 两种本地存储方案：localStorage和sessionStorage

localStorage： 持久化的本地存储，除非自己清除，否则不会过期
sessionStorage: 会话级别的存储，同一个会话中的页面才能访问，且会话结束后数据也会销毁

后续准备写一篇文章详细探讨两者以及与cookies的区别对比

># HTML5表单特性

新增了一些新的表单元素


``datalist``、``datetime``、``output``、``keygen``、``date``、``month``、``week``、``time``、``color``、``number``、``range``、``email``、``url``


># 音视频标签

HTML5支持mp3、wav、ogg格式的音频，使用``audio``标签

```
<audio autoplay controls>
	<source src="file.ogg">
	<source src="file.mp3">
	<a href="file.mp3">Download</a>
<audio>
```

HTML5支持mp4、webm、ogg格式的视频，使用``video``标签

注意：不设置type类型是可以的，但是设置了的话浏览器就不用自己去寻找类型

```
<video controls preload>
	<source src="file.ogg" type="video/ogg; codecs='vorbis,theora'" />
	<source src="file.mp4" type="video/mp4; codecs='avc1.42E01E, mp4a.40.2'" />
	<p> Your browser is old. <a href="file.mp4"> Download this video.</a></p>
</video>
```

``preload``属性决定是否在浏览器加载页面时预先加载视频资源
``controls``属性决定是否视频上显示进度条（不同浏览器样式是不一样的）

># 检测属性支持

例如检测浏览器是否支持pattern属性，可以这样：

```
alert("pattern" in document.createElement("input")); /* boolean */							
```

># 标签的闭合与属性的引号

HTML5不是XHTML，没有要求一定要引号和闭合元素，例如：

```
<p class=myClass id=myId>这是一个没有闭合的p元素
```

不过为了可读性还是建议加上闭合

># 哪些不是HTML5，HTML5废弃哪些HTML4标签

不是HTML5：

```
SVG
CSS3
Geolocation
Client Stroage
Web Sockets
```

HTML5废弃的HTML4标签

``frame``、``frameset``、``noframe``、``applet``、``big``、``center``、``basefont``

># output标签

定义不同类型的输出，显示计算的结果

```
<form oninput="x.value=parseInt(a.value)+parseInt(b.value)">0
   <input type="range" id="a" value="50">100
   +<input type="number" id="b" value="50">
   =<output name="x" for="a b"></output>
</form> 
```
显示如下：
![结果](http://oerbra2zb.bkt.clouddn.com/QQ%E6%88%AA%E5%9B%BE20161011145123.jpg)
