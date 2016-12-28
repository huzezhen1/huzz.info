---
title: 关于Object.create、Object.defineProperty、Object.assign
date: 2016-12-28 10:40:08
tags: 
- javascript
categories: 
- javascript
---

## Object.create()

> ### 定义

创建一个具有指定原型且可选择性地包含指定属性的对象

> ### 语法

```
Object.create(prototype, descriptors)
```

> ### 参数

`prototype`：必需，用作原型的对象，可以为`null`
`descriptors`：可选，包含一个或多个属性描述符的js对象

<span style="color:red;">Tips</span>：属性描述符包括`value`、 `writable`、`enumerable`、`configurable`，除了`value`，其他都默认为`false`

> ### 异常

引发TypeError异常的情况：
`prototype`参数不是对象且不为`null`
`descriptors`参数中的描述符具有`value`或`writable`特性，并具有`get`或`set`特性
`descriptors`参数中的描述符具有不为函数的get或set特性

> ### 备注

函数返回一个具有指定的内部原型且包含指定的属性（如果有）的新对象

> ### 例子

例子一

创建一个没有原型且有两个可枚举属性的对象
`Object.getPrototypeOf()` 获取原始对象的原型
`Object.getOwnPropertyDescriptor()` 获取对象的属性描述符

```
var newObj = Object.create(null, {
            size: {
                value: "large",
                enumerable: true
            },
            shape: {
                value: "round",
                enumerable: true
            }
        });
document.write(newObj.size + "<br/>"); 			/* large */
document.write(newObj.shape + "<br/>");			/* round */
document.write(Object.getPrototypeOf(newObj));		/* null */

```

例子二

创建一个具有与Object对象相同的内部原型的对象
该对象具有与使用对象文本创建的对象相同的原型

```
var firstLine = { x: undefined, y: undefined };
var secondLine = Object.create(Object.prototype, {
        x: {
                value: undefined, 
                writable: true, 
                configurable: true, 
                enumerable: true
            },
            y: {
                value: undefined, 
                writable: true, 
                configurable: true, 
                enumerable: true
            }
});
document.write("first line prototype = " + Object.getPrototypeOf(firstLine)); /* first line prototype = [object Object] */
document.write("<br/>");
document.write("second line prototype = " + Object.getPrototypeOf(secondLine)); /* second line prototype = [object Object] */
```

例子三

创建一个具有与 Shape 对象相同的内部原型的对象

```
var Shape = { twoDimensional: true, color: undefined, hasLineSegments: undefined };
var Square = Object.create(Object.getPrototypeOf(Shape));
```

> ### 参考

https://msdn.microsoft.com/zh-cn/library/ff925952(v=vs.94).aspx


## Object.defineProperty()

> ### 定义

添加属性到对象上，或修改对象的现有属性

> ### 语法

```
Object.defineProperty(object,propertyname,descriptor)
```

> ### 参数

`object`：必需，要被修改或新增属性的对象
`propertyname`：必需，包含属性名称的字符串
`descriptor`：必需，一个属性描述符对象，包含数据属性或访问器属性

> ### 备注

函数返回值:修改后的对象
当新增的属性在原对象中没有的时候，该属性新增，如果有，则进行修改
特别注明，如果要新增/修改多个属性的话，使用`Object.defineProperties()`

> ### 异常

引发TypeError异常的情况
第一个参数不是对象
属性不存在原对象（即增加属性操作）且对象不可扩展
`descriptor`具有`value或writable`特性，并且具有`get`或`set`特性 
`descriptor`具有`get`或`set`特性，上述特性不是函数且已定义
属性存在原对象（即修改操作)，原对象的这个属性的`configurable`特性为`false`且`descriptor`包含一个或多个与原对象属性中特性不同的特性。不过如果原对象属性的`configurable`为`false`和`writable`为`true`时，允许`value`或`writable`特性不同

> ### 例子

例子一

添加数据属性

```
var obj = {};
Object.defineProperty(obj, "newDataProperty", {
    value: 101,
    writable: true,
    enumerable: true,
    configurable: true
});
obj.newDataProperty = 102;
document.write("Property value: " + obj.newDataProperty); //Property value:102
```

例子二

修改数据属性

```
// 接着例子一的obj
Object.defineProperty(obj, "newDataProperty", { writable: false });
```

例子三

添加访问器属性（`get`和`set`函数）

```
var obj = {};
Object.defineProperty(obj, "newAccessorProperty", {
    set: function (x) {
        document.write("in property set accessor" + newLine);
        this.newaccpropvalue = x;
    },
    get: function () {
        document.write("in property get accessor" + newLine);
        return this.newaccpropvalue;
    },
    enumerable: true,
    configurable: true
});
```

例子四

修改DOM元素上的属性

```
var descriptor = Object.getOwnPropertyDescriptor(Element.prototype, "querySelector");
descriptor.value = "query";
descriptor.writable = false;
Object.defineProperty(Element.prototype, "querySelector", descriptor);
var elem = document.getElementById("div");
elem.querySelector = "anotherQuery";
document.write(elem.querySelector);
```

> ### 拓展

`Object.getOwnPropertyNames` 获取所有属性名

`Object.getOwnPropertyDescriptor` 获取某个属性的描述符


> ### 参考

https://msdn.microsoft.com/library/dd548687(v=vs.94).aspx



## Object.assign()

> ### 定义

将来自一个或多个源对象中的可枚举自有属性值复制到一个目标对象
	assign [ə'saɪn] 分配、指派


这里需要注意三点：
可枚举属性（js对象属性的`enumerable`值决定是否可枚举，可枚举性决定了这个属性能否被for…in查找遍历到，如果对象不可枚举，则方法`for…in`、`JSON.stringify()`、`Object.keys()`都会失效）
自有属性（js对象除了自有属性，还有从原型对象继承而来的属性）
`String`或`Symbol`类型可以被直接分配（`Symbol`类型:ES6引出的第七种数据类型）

> ### 语法

```
Obejct.assign(target, ...sources);
```

> ### 参数

`target`：必需，目标对象，其他对象的可枚举属性会复制这个目标对象
`...sources`：必需，源对象，被复制可枚举属性的对象，这些对象的可枚举属性会被复制到`target`对象中去

> ### 异常

如果`target`(目标对象)属性不可写，会引发TypeError

> ### 备注

函数返回`target`对象，需要注意的是，源对象中只有可枚举自有属性会被复制到`target`对象，`null`和`undefined`会被视为空对象

可用来：合并对象、克隆对象

> ### 例子

例子一

```
var o1 = { a: 1 };
var o2 = { b: 2 };
var o3 = { c: 3 };
var obj = Object.assign(o1, o2, o3);
console.log(obj); /* { a: 1, b: 2, c: 3 } */
console.log(o1);  /* { a: 1, b: 2, c: 3 }, target对象也改变了 */
```

例子二

不可枚举的属性，target对象会自动忽略
注意：使用`Object.defineProperty`初始化的对象默认是不可枚举的属性

```
var obj = Object.create({ foo: 1 }, { /* foo从原型继承，非自有属性. */
  bar: {
    value: 2  /* bar 不可枚举. */
  },
  baz: {
    value: 3,
    enumerable: true  /* baz 可枚举. */
  }
});
var copy = Object.assign({}, obj);
console.log(copy); /* { baz: 3 } */ 
```

例子三

`target`对象中的只读属性，当分配新的覆盖他时，将抛出异常

```
var target = Object.defineProperty({}, 'foo', {
  value: 1,
  writable: false
}); 
Object.assign(target, { bar: 2 })
/* {bar: 2, foo: 1} */
Object.assign(target, { foo: 2 })
/* Uncaught TypeError: Cannot assign to read only property 'foo' of object  '#<Object>'(…) */
```

> ### 扩展

`Object.assign`是覆盖之前的内容，不能完全融合对象，如：

```
Object.assign({a: {b: 0}}, {a: {b: 1, c: 2}}, {a: {c: 3}}); //{a:{c:3}}
```

> ### 参考

http://cnodejs.org/topic/56c49662db16d3343df34b13
https://msdn.microsoft.com/zh-cn/library/dn858229(v=vs.94).aspx 