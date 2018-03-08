---
layout:     post                   
title:      JavaScript高级程序设计读书笔记(四)         
subtitle:   重温红皮书精髓--面向对象的程序设计
date:       2017-03-08
author:     chuck
header-img: img/home-bg-basic.jpg
catalog: true                      
tags:                               
    - JavaScript
    - BOM
    - 浏览器
---

## 第八章  BOM

所谓BOM，就是**浏览器对象模型**。BOM提供了很多对象，用于访问浏览器的功能。

### window对象

BOM的核心对象是`window`，它表示浏览器的一个实例。`window`对象既是通过`JavaScript`访问浏览器窗口的一个接口，又是ES规定的`Global`对象。

#### 全局作用域

由于`window`对象又是Global对象，因此在全局作用域中声明的变量、函数都会变成`window`对象的属性和方法。

但是定义全局变量与在`window`对象上直接定义属性还是有差别：全局变量不能通过`delete`操作符删除。而直接在`window`对象上定义的属性可以被`delete`删除。


```
var age = 24;
window.color = "red";

delete window.age; // false

delete window.color;  // true

console.log(age);  // 24
console.log(window.color); // undefined
```

造成此情况的原因是使用var语句添加的`window`属性有一个名为`[[Configurable]]`的特性。这个特性的值被设置为`false`，因此不可以通过`delete`操作符删除。 

除此之外，尝试访问未声明的变量会抛出错误，但是通过查询`window`对象，可以知道某个可能未声明的变量是否存在。不存在的话就会返回`undefined`。

####  窗口位置

目前浏览器基本都采用`screenLeft`和`screenTop`属性。分别表示窗口相对于屏幕左边和上边的位置。如果浏览器不支持该属性。那么`screenX`和`screenY`提供相同的窗口位置信息。

#### 窗口大小

浏览器提供了4个属性：`innerHeight、innerWidth、outerWidth和outerHeight。`在IE9+、Safari和Firefox中，`outerWidth`和`outerHeight`返回浏览器窗口本身的尺寸。而`innerWidth`和`innerHeight`则表示该容器中页面视图区的大小。在Chrome中，`outerWidth`、`outerHeight`和`innerWidth`、`innerHeight`返回相应的值，表示视口(viewport)大小而非浏览器窗口大小。(书中原文如此表达，但是在chrome 64中并非如此)

在chrome 64中，`outerWidth`和`outerHeight`是整个浏览器的宽与高(包括头部工具栏)。而`innerWidth`和`innerHeight`是视口大小，也就是说不包括工具栏和控制台部分(如果有的话)。

`document.documentElement.clientWidth`和`document.documentElement.clientHeight`保存了页面视口的信息(不包括滚动条、工具栏、控制台)。

使用`resizeTo()`和`resizeBy()`可以调整浏览器窗口的大小。两个方法都接收两个参数。不同的是，`resizeTo()`接收浏览器窗口的新宽度和新高度，而`resizeBy()`接收新窗口与原窗口的宽度和高度之差。**有可能被浏览器禁用**。

#### 导航和打开窗口

`window.open()`方法既可以导航到一个特定的URL，也可以打开一个新的浏览器窗口。该方法接收4个参数：**要加载的URL、窗口目标、一个特定字符串和一个表示新页面是否取代浏览器历史记录中当前加载页面的布尔值。**一般情况下只需要传递第一个参数。

如果传递了第二个参数，而且该参数是已有窗口或框架的名称，那么就会在具有该名称的窗口或框架中加载第一个参数指定的URL。


```
window.open("http://www.wrox.com",topFrame);

// 等价于点击<a href="http://www.wrox.com" target="topFrame"></a>
```
如果有一个名字叫做"topFrame"的窗口或者框架，就会在该窗口或框架加载这个URL；否则，新建名为"topFrame"的窗口。

##### 弹出窗口

第三个参数是一个逗号分隔的字符串，表示在新窗口都显示哪些特性。如果没有传入第三个参数，并且是打开新窗口的话，就会打开一个带有全部默认设置(工具栏、地址栏和状态栏等)的新浏览器窗口。不打开新窗口的话，会忽略第三参数。

下表是第三参数字符串的设置选项。


| 设置 | 值 | 说明 |
| --- | --- | --- |
| fullscreen | yes/no | 表示浏览器窗口是否最大化。仅限IE |
| height | 数值 | 表示新窗口的高度。不小于100 |
| left | 数值 | 表示新窗口的左坐标。不能是负值 |
| location | yes/no | 表示是否在浏览器窗口中显示地址栏。 |
| menubar | yes/no | 表示是否在浏览器窗口中显示菜单栏。 |
| resizable | yes/no | 表示是否可以通过拖动浏览器窗口的边框改变大小。默认为no |
| scrollbars | yes/no | 表示如果内容在视口中显示不下，是否允许滚动。默认为no |
| status | yes/no | 表示是否在浏览器窗口中显示状态栏。默认为no |
| toolbar | yes/no | 表示是否在浏览器窗口中显示工具栏。默认为no |
| top | 数值 | 表示新窗口的上坐标。不能是负值 |
| width | 数值 | 表示新窗口的宽度。不能小于100 |

举个例子：

```
window.open("https://www.google.com","newWindow","height=400,width=400,top=100,left=100,resizable=yes");
```
经测试，在Chrome 64中，某些设置默认为yes。比如`scrollBars、resizable`。可能因浏览器而异。

调用`close()`方法可以关闭新打开的窗口。但是该方法仅用于通过`window.open()`打开的弹出窗口。浏览器的主窗口，如果没有得到用户的允许不能关闭。

##### 弹出窗口屏蔽程序

如果浏览器内置的屏蔽程序阻止的弹出窗口，那么`window.open()`很可能会返回`null`。

#### 间歇调用和超时调用

超时调用需要使用`window`对象的`setTimeout()`方法。接收两个参数：要执行的代码和需要等待的毫秒数。其中第一个参数可以是包含JS代码的字符串，也可以是函数。建议采用函数的方法传递参数。

调用`setTimeout()`之后，该方法会返回一个数值ID,表示超时调用。这个ID是计划执行代码的唯一标识符，可以用它来取消超时调用。具体办法是调用`clearTimeout()`方法并将相应的超时调用ID作为参数。


```
let id = setTimeout(()=>{console.log('suceess!')},3000);

//取消调用
clearTimeout(id);
```

只要在指定时间未过去之前调用`clearTimeout()`,就可以完全取消超时调用。上例就会在执行前直接取消掉调用。

> 超时调用的代码是在全局作用域执行的，所以函数中`this`的值在非严格模式下指向`window`对象。严格模式是`undefined`。

相似的，间歇调用采用了`setInterval()`,取消调用采用`clearInterval()`。如果不加干涉，间歇调用会一直执行到页面卸载。

### location对象

`location`对象是很特别的一个对象，因为它既是`window`对象的属性，也是`document`对象的属性。也就是说，`window.location`和`document.location`引用同一个对象。


```
window.location === document.location  // true
```
下表是`location`对象的所有属性。

| 属性名 | 例子 | 说明 |
| --- | --- | :-- |
| hash | "#/login" | 返回URL中hash。如果URL中不包含散列，则返回空字符串。 |
| host | "10.0.52.6:8083" | 返回服务器名称和端口号 |
| href | "http://10.0.52.6:8083/#/login" | 返回当前页面的完整URL。location对象的toString()也返回这个 |
| pathname | "/" | 返回URL的目录或文件名 |
| port | "8083" | 返回URL指定的端口号。如果URL不包含端口号，返回空字符串 |
| hostname | "10.0.52.6" | 返回不包含端口号的服务器名称 |
| protocol | "http:" | 返回页面的协议。一般是"http:”或者"https:" |
| search | "?key=status" | 返回URL的查询字符串。以问号开头 |

#### 位置操作

使用`location.assign()`传入一个URL可以立即打开新URL并在浏览器的历史记录中生成一条记录。

```
location.assign("https://www.google.com");

//下面的方法也会打开新的URL，效果相同，并且内部都是调用的assign()方法
window.location = "https://www.google.com";
location.href = "https://www.google.com";
```

> 每次修改`location`的属性(hash除外)，页面都会以新URL重新加载。并在浏览器的历史记录中生成一条新纪录。使用`replace()`可以导航到指定的URL，但不会在历史记录中生成新记录。

使用`reload()`重新加载当前页面。如果不传递参数，并且页面没有改变，则从浏览器缓存中重新加载。如果传入参数`true`，则强制从服务器重新加载。

```
location.reload();  // 可能从缓存加载
location.reload(true);   // 强制从服务器重新加载
```

### navigator 对象

`navigator`对象用来识别客户端浏览器。具体的方法会在第九章提到。这里先不进行记录。

### screen 对象

`screen`对象基本上只用来表明客户端的能力，包括浏览器窗口外部的显示器的信息等。基本上很少用到，这里不做记录。

### history 对象

`history`对象保存着用户上网的历史记录。因为`history`是`window`对象的属性，因此每个浏览器窗口、每个标签页、每个框架都有自己的`history`对象与特定的`window`对象关联。

使用`history.go()`可以在用户的历史记录中任意跳转，可以向前也可以向后。接受一个参数，表示向后或向前跳转的页面数的一个数组值。负数表示向后，正数表示向前。也可以传递字符串参数。会跳转到历史记录中包含该字符串的位置：可能向前也可能后退，具体看那个更近。

`history.back()`和`history.forward()`分别表示后退和前进一步。不用传参数。`history`对象还有一个`length`属性，保存着历史记录的数量。


