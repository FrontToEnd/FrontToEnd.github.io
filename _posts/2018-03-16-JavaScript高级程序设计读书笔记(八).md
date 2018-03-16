---
layout:     post
title:      JavaScript高级程序设计读书笔记(八)
subtitle:   重温红皮书精髓--面向对象的程序设计
date:       2018-03-16
author:     chuck
header-img: img/home-bg-basic.jpg
catalog: true                      
tags:                               
    - JavaScript
    - DOM
    - DOM2/DOM3
---

## 第十二章 DOM2和DOM3

DOM1级主要定义的是HTML和XML文档的底层结构。DOM2和DOM3级则在这个结构的基础上引入更多的交互能力。为此，DOM2和DOM3级分为许多模块，这些模块包括：

* DOM2级核心(DOM Level 2 Core):在1级核心基础上构建，为节点添加了更多方法和属性。
* DOM2级视图(DOM Level 2 Views): 为文档定义了基于样式信息的不同视图。
* DOM2级事件(DOM Level 2 Events):说明了如何使用事件与DOM文档交互。
* DOM2级样式(DOM Level 2 Style):定义了如何以编程方式来访问和改变CSS样式信息。
* DOM2级遍历和范围(DOM Level 2 Traversal and Range):引入了遍历DOM文档和选择其特定部分的新接口。
* DOM2级HTML(DOM Level 2 HTML):在1级HTML基础上构建，添加了更多属性、方法和新接口。

### DOM变化

多数的扩展是增强了XML的需求，这里不做记录。只记录下增强HTML的部分。

**1.Document类型的变化**

Document类型变化的方法有一个是`importNode()`。这个方法的用途是从一个文档中取得一个节点，然后将其导入到另一个文档，使其成为文档结构的一部分。

`importNode()`方法与Element的`cloneNode()`方法很相似。接受两个参数：要复制的节点和一个表示是否复制子节点的布尔值。返回结果是原来节点的副本。

```
var newNode = document.importNode(oldNode,true); // 导入节点及其所有子节点
document.body.appendChild(newNode);
```

DOM2级视图模块添加了一个名为defaultView的属性，其中保存着一个指针，指向拥有给定文档的窗口。

DOM2级HTML模块为document.implementation新增一个方法，叫做`createHTMLDocument()`。这个方法的用途是创建一个**完整的HTML文档**，包括`<html>、<head>、<title>和<body>元素`。只接受一个参数，即新创建文档的标题。

**2.Node类型的变化**

`isSupported()`方法用于确定当前节点具有什么能力。接收两个参数：特性名和特性版本号。如果浏览器实现相应特性，就返回true。

DOM3级引入了两个辅助比较节点的方法：`isSameNode()`和`isEqualNode()`。两个方法都接受一个节点参数，并在传入节点与引用的节点相同或相等时返回true。相同的含义是两个节点引用同一个对象；相等指的是两个节点是相同的类型，具有相等的属性，而且`attributes`和`childNodes`属性也相等。

```
var div1 = document.createElement("div");
div1.setAttribute("class","box");

var div2 = document.createElement("div");
div2.setAttribute("class","box");

// div1与div2相等但不相同，因为不是同一个对象
div1.isSameNode(div1); // true
div1.isEqualNode(div2); // true
div1.isSameNode(div2); // false
```

DOM3级还针对为DOM节点添加额外数据引入了新方法。其中，`setUserData()`方法会将数据指定给节点，接受3个参数：要设置的键、实际的数据和处理函数。以下代码可以将数据指定给一个节点。

**3.框架的变化**

框架和内嵌框架分别用`HTMLFrameElement`和`HTMLIFrameElement`表示，它们在DOM2级中都有了一个新属性，名叫`contentDocument`。这个属性包含了一个指针，指向表示框架内容的文档对象。在此之前，无法直接通过元素取得这个文档对象。现在可以这样:

```
var iframe = document.getElementById("myIframe").contentDocument;
```
`contentDocument`属性是Document类型的实例，可以像使用其他HTML文档一样使用，包括所有属性和方法。

### 样式

在HTML中定义样式有三种方式：通过`<link>`元素包含外部样式表文件、使用`<style>`元素定义嵌入式样式和行内样式。"DOM2级样式"模块围绕这3种应用样式的机制提供了一套API。

#### 访问元素的样式

任何支持style特性的HTML元素在JS中都有一个对应的style属性。这个style对象是`CSSStyleDeclaration`实例,**包含着通过HTML的style特性指定的所有样式信息，但不包括与外部样式表或嵌入样式表经层叠而来的样式。**在style特性中指定的任何CSS属性都将表现为这个style对象的相应属性。对于使用中划线的CSS属性名，必须使用驼峰形式才能用JS访问。由于css属性中的`float`是JS的保留字，因此通过js获取的时候应该是`cssFloat`。

```
var myDiv = document.getElementById("myDiv");

// 设置背景颜色
myDiv.style.backgroundColor = "red";

// 标准模式下，所有度量值必须指定单位。建议如此做
myDiv.style.width = "100px";
```

**1.DOM样式属性和方法**

这些属性和方法在提供元素的style特性值的同时，也可以修改样式。(**切记该特性不包含外部样式的值**)

首先是`cssText`。通过`cssText`属性可以访问style特性中的CSS代码。在读模式下，`cssText`返回浏览器对style特性中CSS代码的内部表示。在写模式下，赋给`cssText`的值会重写整个style特性的值。

> 结合上篇提到的内容，我们可以通过JS原生方法这样修改类名/样式：使用`classList`来添加或者删除类名；使用`cssText`一次性重写当前元素上所有的属性。当然，也可以通过`className`来修改类名，只不过太麻烦了。总的来说，JS操作类名不是首选，能少用就少用。

接下来是`item()`方法和length属性。没错，就是NodeList也有的方法，用来方便迭代元素中定义的CSS属性。style对象就是一个类数组，可以通过方括号语法访问。使用方括号或者`item()`访问css属性时，采用中划线而不是驼峰。

还有就是`setProperty()`和`removeProperty()`，用来设置或删除给定属性。其中`setProperty()`包含三个参数：属性名称、值和优先权标志("important"或空字符串)。

**2.计算的样式**

虽然style对象能够提供样式信息，但**不包含从其他样式表层叠而来并影响到当前元素的样式信息**。DOM2级样式增强了document.defaultView,提供了`getComputedStyle()`方法。该方法接受两个参数：要取得计算样式的元素和一个伪元素字符串。如果不需要伪元素信息，第二个参数可以是`null`。该方法返回一个`CSSStyleDeclaration`对象，其中包含当前元素的所有计算的样式。

即使有些浏览器支持这种功能，但表示值的方式可能会有所区别。最关键的是，所有计算的样式都是只读的；不能修改计算后样式对象中的CSS属性。而且，计算后的样式也包含属于浏览器内部样式表的样式信息。

#### 操作样式表

`CSSStyleSheet`类型表示的是样式表，包括通过`<link>`元素包含的样式表和在`<style>`元素中定义的样式表。这两个元素分别是由`HTMLLinkElement`和`HTMLStyleElement`类型表示。但是`CSSStyleSheet`更通用，只表示样式表。并且是只读的接口。

应用于文档的所有样式表通过`document.styleSheets`集合来表示。同样的，可以通过item()或方括号语法来访问每个样式表。访问到的样式表还可以获取当前表的href属性。`document.styleSheets[i].href`。(`<style>元素没有href属性`)

> 总结一下。获取样式表有两种方式：其一，就是通过`document.styleSheets`对象获取页面上所有的样式表；其二，通过`<link>`或`<style>`元素上的`sheet`属性来获取。比如：`document.getElementsByTagName("link")[0].sheet`。

`CSSStyleSheet`从`StyleSheet`接口继承而来的属性有很多，下面逐一来整理。

**1.CSS规则**

`CSSRule`对象表示样式表中的每一个规则。实际上，`CSSRule`是一个供其他多种类型继承的基类型，最常见的就是`CSSStyleRule`类型。`CSSStyleRule`下最常用的属性是cssText(返回整条规则对应的文本)、selectorText(返回当前规则的选择符文本)和style(设置和取得规则中特定的样式值)。这里的`cssText`与`style.cssText`不同，前者包含选择符文本和花括号，并只读。

**2.创建规则**

向现有样式表中添加新规则，需要使用`insertRule()`方法。接受两个参数：规则文本和表示在哪里插入规则的索引。

**3.删除规则**

从样式表中删除规则的方法是`deleteRule()`,接受一个参数：要删除的规则的位置。

创建和删除规则建议不要使用，了解即可。

#### 元素大小

方便确定页面中元素的大小。

**1.偏移量**

**偏移量**包括元素在屏幕上占用的所有可见的空间。元素的可见大小由其高度、宽度决定，包括内边距、滚动条和边框(不包括外边距)。下列属性可以取得偏移量。

* offsetHeight:元素在垂直方向上占用的空间大小，以像素计。包括元素的高度、可见的水平滚动条的高度、上下边框的高度。
* offsetWidth:元素在水平方向上占用的空间大小，以像素计。包括元素的宽度、可见的垂直滚动条的宽度、左右边框的宽度。
* offsetLeft:元素的左外边框至包含元素的左内边框之间的像素距离。
* offsetTop:元素的上外边框至包含元素的上内边框之间的像素距离。

其中，offsetLeft和offsetTop属性与包含元素有关。包含元素的引用保存在offsetParent属性中。要想知道某个元素在页面上的偏移量，将这个元素的offsetLeft和offsetTop与其offsetParent的相同属性相加，循环直至根元素，可以得到一个基本准确的值。

> 所有偏移量属性都是只读的，每次访问它们都需要重新计算。如果需要重复使用，可以保存在局部变量中。

**2.客户区大小**

元素的客户区大小指元素内容及其内边距所占据的空间大小。有关客户区大小的属性有两个：`clientWidth`和`clientHeight`。`clientWidth`是元素内容区宽度加上左右内边距宽度；`clientHeight`是元素内容区高度加上上下内边距高度。

> 要确定浏览器的视口大小，可以使用`document.documentElement`的`clientWidth`和`clientHeight`。同样的，客户区大小也是只读，每次访问需要重新计算。

**3.滚动大小**

滚动大小指的是包含滚动内容的元素的大小。以下是4个与滚动大小相关的属性。

* scrollHeight：没有滚动条的情况下，元素内容的总高度。
* scrollWidth：没有滚动条的情况下，元素内容的总宽度。
* scrollLeft：被隐藏在内容区域左侧的像素数。通过设置这个属性可以改变元素的滚动位置。
* scrollTop：被隐藏在内容区域上方的像素数。通过设置这个属性可以改变元素的滚动位置。

scrollWidth和scrollHeight主要用于确定元素内容的实际大小。在确定文档的总高度时，必须取得scrollWidth/clientWidth和scrollHeight/clientHeight的最大值。

通过scrollLeft和scrollTop属性既可以确定元素当前滚动的状态，也可以设置元素的滚动位置。一般可以将scrollLeft和scrollTop设置为0，重置元素的滚动位置。

**4.确定元素大小**

`getBoundingClientRect()`方法会返回一个矩形对象，包含4个属性：left、top、right、bottom。表示元素在页面中相对于视口的位置。

### 遍历

DOM2级遍历和范围模块定义了两个用于辅助完成顺序遍历DOM结构的类型：NodeIterator和TreeWalker。这两个类型能够基于给定的起点对DOM结构进行深度优先的遍历操作。

#### NodeIterator

`NodeIterator`类型可以使用`document.createNodeIterator()`方法创建新实例。接受4个参数。

* root：想要作为搜索起点的树中的节点。
* whatToShow：表示要访问哪些节点的数字代码。
* filter：是一个NodeFilter对象，或者是一个表示应该接受还是拒绝某种特定节点的函数。
* entityReferenceExpansion：布尔值，表示是否要扩展实体引用。这个参数在HTML页面没用，因为其实体引用不能扩展。

`NodeIterator`类型两个主要方法是`nextNode()`和`previousNode()`。在深度优先的DOM子树遍历中，`nextNode()`方法用于向前前进一步，而`previousNode()`用于向后后退一步。

#### TreeWalker

`TreeWalker`是`NodeIterator`更高级的版本。除了包括`nextNode()`和`previousNode()`相同的功能外，还提供了以下方法：

* parentNode():遍历到当前节点的父节点；
* firstChild():遍历到当前节点的第一个子节点；
* lastChild():遍历到当前节点的最后一个子节点；
* nextSibling():遍历到当前节点的下一个同辈节点；
* previousSibling():遍历到当前节点的上一个同辈节点。

创建TreeWalker对象要使用`document.createTreeWalker()`方法，这个方法也接受4个参数：作为遍历起点的根节点、要显示的节点类型、过滤器和一个表示是否扩展实体引用的布尔值。

`TreeWalker`类型还有一个属性，名叫`currentNode`，表示任何遍历方法在上一次遍历中返回的节点。通过设置这个属性也可以修改遍历继续进行的起点。

实际中基本没见过有使用遍历方法的，我觉得了解一下即可。

### 范围

通过范围可以选择文档中的一个区域，而不必考虑节点的界限。范围是选择DOM结构中特定部分，然后再执行相应操作的一种手段。使用范围选区可以在删除文档中某些部分的同时，保持文档结构的格式良好，或者复制文档中的相应部分。


