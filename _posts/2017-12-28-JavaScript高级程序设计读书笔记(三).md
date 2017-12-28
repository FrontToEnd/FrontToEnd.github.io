---
layout:     post                   
title:      JavaScript高级程序设计读书笔记(三)         
subtitle:   重温红皮书精髓--面向对象的程序设计
date:       2017-12-28
author:     chuck
header-img: img/home-bg-basic.jpg
catalog: true                      
tags:                               
    - JavaScript
    - 原型
    - 继承
---

## 第六章 面向对象的程序设计

### 1.理解对象

我们可以给对象添加属性和方法，有些属性在创建的时候带有一些特征值，来定义他们的行为。但是这些定义都是内部值，无法在JS中直接访问，并且是放在`[[]]`中，比如是否可枚举的属性定义`[[Enumerable]]`。

ES中有两种属性：数据属性和访问器属性。

**1.数据属性**

数据属性包含一个数据值的位置。这个位置可以读取和写入值。数据属性有4个特性。

* [[Configurable]]:表示能否通过`delete`删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为访问器属性。默认值为`true`。
* [[Enumerable]]:表示能否通过`for-in`循环返回属性。默认值为`true`。
* [[Writable]]:表示能否修改属性的值。默认值为`true`。
* [[Value]]:包含这么属性的数据值。读取属性值时，从这个位置读；写入属性值时，把新值保存在这个位置。默认值为`undefined`。

如果要修改属性默认的特性，就必须使用`Object.defineProperty()`方法。接收三个参数：属性所在的对象、属性的名字和一个描述符对象。其中，描述符对象的属性必须是：`configurable、enumerable、writable和value`。


```
var person = {};
Object.defineProperty(person,'name',{
    value:'chuck'
})
person.name; // "chuck"
person.name = "kerwin"; // "kerwin" 
person.name; // "chuck",依旧是以前的值，表示只读
delete person.name; // false 表示无法删除
for(let item in person) {
	console.log(item); // 返回undefined，表明无法枚举
}
```
上面的例子表明：**如果不显式指定，`configurable、enumerable、writable`特性的默认值都是`false`**。上面例子的操作在非严格模式下没什么问题，但是在严格模式下回报错。而且，一旦把`configurable`定义为`false`(不管是手动定义还是默认值)，就不会再变回可配置了。

**2.访问器属性**

访问器属性不包含数据值，包含一对`getter`和`setter`函数，但不是必需的。读取访问器属性时，会调用`getter`函数，这个函数负责返回有效的值；写入访问器属性时，调用`setter`函数并传入新值，这个函数负责决定如何处理数据。有以下4个特性：

* [[Configurable]]:表示能否通过`delete`删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为访问器属性。默认值为`true`。
* [[Enumerable]]:表示能否通过`for-in`循环返回属性。默认值为`true`。
* [[Get]]:在读取属性时调用的函数，默认值是`undefined`。
* [[Set]]:在写入属性时调用的函数，默认值是`undefined`。

访问器属性不能直接定义，必须使用`Object.defineProperty()`方法定义。

需要注意的是，不一定非要同时指定`getter`和`setter`。只指定`getter`意味着只读不能写，**尝试写入属性在严格模式会报错**。同样的，没有指定`setter`的只能写不能读，否则非严格模式返回`undefined`，严格模式报错。

可以使用`Object.defineProperties()`来定义多个属性。举个[例子](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperties)：

```
var obj = {};
Object.defineProperties(obj, {
  'property1': {
    value: true,
    writable: true
  },
  'property2': {
    value: 'Hello',
    writable: false
  }
  // etc. etc.
});
```

`Object.getOwnPropertyDescriptor()`方法返回指定对象上一个自有属性对应的属性描述符。（自有属性指的是直接赋予该对象的属性，不需要从原型链上进行查找的属性）。


```
var o,d;
o = { bar: 42 };
d = Object.getOwnPropertyDescriptor(o, "bar");
// d {
//   configurable: true,
//   enumerable: true,
//   value: 42,
//   writable: true
// }
```

### 2.创建对象

#### 工厂模式

这种模式抽象了创建具体对象的过程，是用的较多的一种设计模式。

```
function create (name,age) {
    var o = {
        name,
        age
    };
    return o;
}
var person = create('chuck',22);
```

可以无限制的调用该函数，每次都返回一个全新的对象。

#### 构造函数模式

使用构造函数模式重写上述例子：

```
function Person (name,age) {
    this.name = name;
    this.age = age;
    this.say = function () {
        alert(this.name);
    };
}
var person = new Person('chuck',22);
```

以这种方式调用构造函数会经历以下步骤：

* 创建一个新对象；
* 将构造函数的作用域赋给新对象(this指向了这个新对象)；
* 执行构造函数中的代码(为这个新对象添加属性)；
* 返回新对象。

这个例子创建的所有对象即是`Object`的实例，也是`Person`的实例。**创建自定义的构造函数意味着将来可以将它的实例标识为一种特定的类型**。

```
person instanceof Person; // true,这就是特定的类型
person instanceof Object; // true
```

#### 原型模式

创建的每个函数都有一个`prototype`属性，这个属性是一个指针，指向一个对象，这个对象包含可以由特定类型的所有实例共享的属性和方法。所以，**`prototype`就是通过调用构造函数而创建的那个对象实例的原型对象**。

原型和继承这块理解起来比较抽象，目前我还没有融会贯通，等真正理解后再来总结。





