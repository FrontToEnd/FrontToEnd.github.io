---
layout:     post                   
title:      JavaScript高级程序设计读书笔记(七)         
subtitle:   重温红皮书精髓--面向对象的程序设计
date:       2018-03-15
author:     chuck
header-img: img/home-bg-basic.jpg
catalog: true                      
tags:                               
    - JavaScript
    - DOM
---

## 第十一章 DOM扩展

对DOM的两个主要的扩展是`Selectors API`和`HTML5`。都源自开发社区。

### 选择符API

`Selectors API`是由W3C发起制定的一个标准。致力于让浏览器原生支持CSS查询。核心的两个方法是：`querySelector()`和`querySelectorAll()`。

#### querySelector()方法

`querySelector()`方法接收一个**CSS选择符**，返回与该模式匹配的**第一个**元素，如果没有匹配的，则返回`null`。

传入的参数与使用jQuery十分相似，可以说是一模一样。通过`Document`类型调用`querySelector()`方法时，会在文档元素的范围内查找匹配的元素。而通过`Element`类型调用`querySelector()`方法时，只会在该元素后代元素的范围内查找匹配的元素。

如果传入不被支持的选择符，`querySelector()`会抛出错误。

#### querySelectorAll()方法

`querySelectorAll()`方法接收的参数与`querySelector()`方法一样，该方法返回的是一个`NodeList`实例。

具体来说，返回的值实际上是带有所有属性和方法的NodeList，底层实现则类似于一组元素的**快照**，而非不断对文档进行搜索的动态查询。通俗的说，就是不会动态更新。

只要传入方法的CSS选择符有效，该方法就会返回一个`NodeList`对象。如果没有找到匹配的元素，那么`NodeList`就是空的。

如果要获取返回的NodeList中的元素，可以使用`item()`方法，也可以使用方括号语法。

```

//取得div中的所有em元素
var ems = document.getElementById("myDiv").querySelectorAll("em");

//取得类为"selected"的所有元素
var selecteds = document.querySelectorAll(".selected");
```

#### matchesSelector()方法

Selectors API Level 2规范为Element类型新增了一个方法`matchesSelector()`。该方法接收一个参数，即CSS选择符。如果调用元素与该选择符匹配，返回true；否则返回false。

### 元素遍历

对于元素间的空格，IE9及之前版本不会返回文本节点，而其他所有浏览器都会返回文本节点。这导致了使用childNodes等属性时行为不一致。为了弥补这差异，Element Traversal规范新定义一组属性。包括：

* childElementCount:返回子元素(不包括文本节点和注释)的个数。
* firstElementChild:指向第一个子元素；firstChild的元素版。
* lastElementChild:指向最后一个子元素；lastChild的元素版。
* previousElementSibling:指向前一个同辈元素；previousSibling的元素版。
* nextElementSibling:指向后一个同辈元素；nextSibling的元素版。

利用这些属性不必担心空白文本节点，从而可以更方便的查找DOM元素。

### HTML5

HTML5规范围绕如何使用新增标记定义了大量JS API。一些API与DOM重叠，定义了浏览器应该支持的DOM扩展。

#### 与类相关的扩充

HTML5新增了很多API，致力于简化CSS类的用法。

**1.getElementsByClassName()方法**

该方法接收一个参数，即包含一或多个类名的字符串，返回带有指定类的所有元素的NodeList。传入多个类名时，无次序，以空格分隔。

在document对象上调用`getElementsByClassName()`始终会返回与类名匹配的**所有元素**；在元素上调用该方法就只会返回**后代元素中匹配**的元素。

**2.classList属性**

在HTML5规定出现以前，删除某元素的类名时，只能通过对className重新赋值来实现，并且如果有多个class时会更麻烦。

HTML5新增了一种操作类名的方式，为所有元素添加`classList`属性。这个属性是新集合类型`DOMTokenList`的实例。`DOMTokenList`有一个表示自己包含多少元素的`length`属性，取得元素可以使用`item()`方法，也可以使用方括号语法。同时还有以下方法：

* add(value):将给定的字符串值添加到列表中。如果已存在，则不添加。
* contains(value):表示列表中是否存在给定的值，如果存在返回true，否则返回false。
* remove(value):从列表中删除给定的字符串。
* toggle(value):如果列表中存在给定的值，删除；如果不存在，添加。

有了classList就可以很方便的对类进行操作了，比如删除一个类名：

```
document.getElementById("myDiv").classList.remove("user");
```

#### 焦点管理

HTML5也添加了辅助管理DOM焦点的功能。`document.activeElement`属性始终会引用DOM中当前获得了焦点的元素。获得焦点方式有页面加载、用户输入和在代码中调用focus()方法。

默认情况下，文档刚刚加载完成时，`document.activeElement`中保存的是`document.body`元素的引用。文档加载期间，`document.activeElement`值为null。

另一个方法是`document.hasFocus()`,这个方法用于确定文档是否获得焦点。

```
var button = document.getElementById("myButton");
button.focus();
document.activeElement === button; // true
document.hasFocus(); // true
```

#### HTMLDocument的变化

HTML5扩展了HTMLDocument，增加了新的功能。

**1.readyState属性**

Document的readyState属性有两个可能的值：

* loading，正在加载文档；
* complete，已经加载完文档。

基本用法如下：

```
if (document.readyState == "complete") {
    // 执行操作
}
```

**2.兼容模式**

标准模式下，`document.compatMode`的值为"CSSlCompat"，混杂模式下，`document.compatMode`的值等于"BackCompat"。

**3.head属性**

作为对`document.body`引用文档的`<body>`元素的补充，HTML5新增了`document.head`属性，引用文档的`<head>`元素。

#### 字符集属性

HTML5新增了几个与文档字符集有关的属性。charset属性表示文档中实际使用的字符集，也可以用来指定新字符集。默认情况下，这个属性的值为"UTF-16"，也可以通过`<meta>`元素、响应头部或直接设置charset属性修改。

```
document.charset = "UTF-8";
```

另一个属性是`defaultCharset`,表示根据默认浏览器及操作系统的设置，当前文档默认的字符集应该是什么。

#### 自定义数据属性

HTML5规定可以为元素添加非标准的属性，需要添加前缀`data-`，目的是提供一些有用的信息。举个🌰：

```
<div data-name="chuck"></div>
```
可以通过元素的`dataset`属性来访问自定义属性的值。`dataset`属性的值是`DOMStringMap`的一个实例，也就是一个名值对的映射。可以通过去除前缀后的名称来获取具体的值。

#### 插入标记

使用插入标记的技术，直接插入HTML字符串不仅更简单，速度也更快。

**1.innerHTML属性**

在读模式下，`innerHTML`属性返回与调用元素的所有子节点(包括元素、注释和文本节点)
对应的HTML标记。在写模式下，innerHTML属性会根据指定的值创建新的DOM树，然后用DOM树完全替换调用元素原先的所有子节点。

由于在写模式下，会将字符串转换为DOM树。所以在使用`innerHTML`插入代码前，确保文本内容尽可能正确，否则会发生一些意料之外的事情。

不是所有元素都支持innerHTML属性。不支持的元素有：`<col>、<colgroup>、<frameset>、<haed>、<html>、<style>、<table>、<tbody>、<thead>、<tfoot>和<tr>`。

**2.outerHTML属性**

在读模式下，`outerHTML`返回调用它的元素及所有子节点的HTML标签。在写模式下，`outerHTML`会根据指定的HTML字符串创建新的DOM子树，然后用这个DOM子树完全替换调用元素。

**3.insertAdjacentHTML()方法**

该方法接收两个参数：插入位置和要插入的HTML文本。第一个参数必须是下列值之一：

* "beforebegin"，在当前元素之前插入一个紧邻的同辈元素；
* "afterbegin"，在当前元素之下插入一个新的子元素或在第一个子元素之前再插入新的子元素；
* "beforeend"，在当前元素之下插入一个新的子元素或在最后一个子元素之后再插入新的子元素；
* "afterend"，在当前元素之后插入一个紧邻的同辈元素。

需要注意的是，这些值必须是小写形式。第二参数是一个HTML字符串。

**4.内存与性能问题**

在使用`innerHTML`、`outerHTML`等方法时，最好先手工删除要被替换的元素的所有事件处理程序和JS对象属性。

最好将设置`innerHTML`、`outerHTML`的次数控制在合理的范围内。每次循环都设置innerHTML的效率很低，最好就是先在循环内拼接好字符串，最后只操作一次DOM。

#### scrollIntoView()方法


`scrollIntoView()`方法方便开发人员更好地控制页面滚动。`scrollIntoView()`可以在所有HTML元素上调用。如果给这个方法传入`true`作为参数，或者不传入参数，那么窗口滚动之后会让调用元素的顶部与视口顶部尽可能平齐。如果传入`false`作为参数，调用元素会尽可能全部出现在视口中。


