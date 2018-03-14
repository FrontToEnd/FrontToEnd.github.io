---
layout:     post                   
title:      JavaScript高级程序设计读书笔记(六)         
subtitle:   重温红皮书精髓--面向对象的程序设计
date:       2018-03-14
author:     chuck
header-img: img/home-bg-basic.jpg
catalog: true                      
tags:                               
    - JavaScript
    - DOM
---

## 第十章 DOM

`DOM`是针对HTML和XML文档的一个API。描绘了一个层次化的节点数。允许开发人员添加、移除和修改页面的某一部分。

### 节点层次

DOM可以将任何HTML或XML文档描绘成一个由多层节点构成的结构。文档节点是每个文档的根节点。一般来说，文档节点只有一个子节点，即`<html>`元素。我们称之为**文档元素**。文档元素是文档的最外层元素。每个文档只能有一个文档元素。在HTML页面中，文档元素始终是`<html>`元素。

#### Node类型

DOM1级定义了一个Node接口，接口将由DOM中的所有节点类型实现。JS中的所有节点类型都是继承自`Node`类型。每个节点都有一个`nodeType`属性，表明节点的类型。节点类型由在Node类型中定义的下列12个数值常量来表示。

* Node.ELEMENT_NODE(1);
* Node.ATTRIBUTE_NODE(2);
* Node.TEXT_NODE(3);
* Node.CDATA_SECTION_NODE(4);
* Node.ENTITY_REFERENCE_NODE(5);
* Node.ENTITY_NODE(6);
* Node.PROCESSING_INSTRUCTION_NODE(7);
* Node.COMMENT_NODE(8);
* Node.DOCUMENT_NODE(9);
* Node.DOCUMENT_TYPE_NODE(10);
* Node.DOCUMENT_FRAGMENT_NODE(11);
* Node.NOTATION_NODE(12)。

**1.nodeName和nodeValue属性**

在使用这两个值以前，最好先检测一下节点的类型。可以通过`node.nodeType == 1`来检测该节点是否为一个元素。对于元素节点来说，`nodeName`保存的始终都是元素的标签名(大写)，而`nodeValue`的值始终是`null`。

**2.节点关系**

每个节点都有一个`childNodes`属性，其中保存着一个`NodeList`对象。其中，`NodeList`是一种类数组对象，用于保存一组有序的节点。它**实际上是基于DOM结构动态执行查询的结果，因此DOM结构的变化能够自动反映在`NodeList`对象中。**

每个节点都有一个`parentNode`属性，该属性指向文档树中的父节点。包含在`childNodes`列表中的所有节点都具有相同的父节点，因此它们的`parentNode`属性都指向同一个节点。包含在`childNodes`列表中的每个节点相互之间都是同胞节点。同胞节点之间可以使用`previousSibling`和`nextSibling`属性，来访问前一个或者下一个兄弟节点。当然，第一个兄弟节点的`previousSibling`和最后一个兄弟节点的`nextSibling`都为`null`。如果某节点没有同胞节点，则两者属性都为`null`。

父节点的`firstChild`和`lastChild`属性分别指向`childNodes`列表中的第一个和最后一个节点。如果只有一个子节点，则指向同一个；如果没有子节点，则两者的值均为`null`。

另外，`hasChildNodes()`方法在节点包含一或多个子节点时，返回`true`。这样就不需要通过检测`childNodes.length`来判断了。

**3.操作节点**

最常用的操作节点的方法就是`appendChild()`。用于向`childNodes`列表的末尾添加一个节点。 如果需要把节点放在`childNodes`列表中某个特定的位置，可以使用`insertBefore()`方法。接受两个参数：要插入的节点和作为参照的节点。插入节点后，被插入的节点会变成参照节点的前一个同胞节点，同时被方法返回。如果参照节点是`null`，则`insertBefore()`与`appendChild()`操作相同。这两个方法均不会移除节点。

而`replaceChild()`则可以移除并替换节点。该方法接受两个参数：要插入的节点和要替换的节点。要替换的节点将由这个方法返回并从文档树中被移除，同时由要插入的节点占据其位置。

```

//替换第一个子节点
var returnedNode = someNode.replaceChild(newNode,someNode,firstChild);
```
使用`removeChild()`方法可以移除节点。被移除的节点是该方法的返回值。

如果在不支持子节点的节点上调用上面这些方法，将会导致错误发生。

**4.其他方法**

`cloneNode()`,所有类型的节点都拥有这个方法。用于创建调用这个方法的节点的一个完全相同的副本。接受一个布尔值参数，表示是否执行深复制。但是复制后返回的节点并没有父节点，所以需要通过`appendChild()`等方法添加到文档中。

`cloneNode()`方法不会复制添加到DOM节点中的JS属性。

#### Document类型

`JavaScript`通过`Document`类型表示文档。在浏览器中，`document`对象是`HTMLDocument`(继承自Document类型)的一个实例，表示整个HTML页面。而且`document`对象是`window`对象是一个属性，因此可以将其作为全局对象访问。Document节点具有下列特征：

* nodeType的值为9;
* nodeName的值为"#document";
* nodeValue的值为null;
* parentNode的值为null;
* ownerDocument的值为null;
* 其子节点可能是一个DocumentType(最多一个)、Element(最多一个)、ProcessingInstruction或Comment。

**1.文档的子节点**

有两个内置的快捷方式可以访问Document子节点。一个是`documentElement`属性，该属性始终指向HTML页面中的`<html>`元素。另一个是通过`childNodes`列表访问文档元素。

```
<html>
    <body>
    </body>
</html>

let html = document.documentElement; // 取得对<html>的引用
html === document.childNodes[0]; // true
html === document.firstChild; // true
```
上述例子说明，三者的值相同，都指向了`<html>`元素。

document对象还有一个`body`属性，直接指向`<body>`元素。**所有浏览器都支持document.documentElement(指向`<html>`元素)和document.body属性(指向`<body>`元素)。**

`Document`另一个可能的子节点时`DocumentType`。通常将`<!DOCTYPE>`标签看成一个与文档其他部分不同的实体。可以通过`doctype`属性来访问它的信息。

```
document.doctype; // 取得对<!DOCTYPE>的引用
```

**2.文档信息**

作为`HTMLDocument`的实例，`document`对象还有一些标准的`Document`对象所没有的属性。首先来看看`title`属性。包含着`<title>`元素中的文本。通过`document.title`可以取得当前页面的标题，也可以修改它。

```
document.title; // get old title
document.title = "new title"; // set new title
```
接下来是和网页请求有关的三个属性。包括URL、domain和referrer。其中，URL属性包含页面完整的URL，domain属性只包含页面的域名，referrer属性保存着链接到当前页面的那个页面的URL。如果没有来源页面，referrer属性可能会包含空字符串。所有这些信息偶读存在于请求的HTTP头部，只不过可以用JS访问罢了。

上述三个属性只有`domain`是可以设置的。但也并非可以设置任何值。不能将这个属性设置为URL中不包含的域。可以通过将每个页面的`document.domain`设置为相同的值，来实现跨域访问。

**3.查找元素**

`Document`类型为此提供了两个方法：`getElementById()`和`getElementsByTagName()`。

其中，`getElementById()`接受参数是需要获取元素的ID。并且是严格匹配，包括大小写。如果页面存在多个相同ID的元素，则返回第一次出现的。如果没有，则返回`null`。

`getElementsByTagName()`接受的参数是要获取的元素的标签名。返回值是包含零或多个元素的`NodeList`。在HTML文档中，这个方法会返回`HTMLCollection`对象。与NodeList十分相似，都是类数组。都可以使用方括号语法或`item()`来访问其中的项。

`HTMLCollection`对象还有一个方法，叫做`namedItem()`，可以通过元素的name特性取得项。

```
<img src="myimage.gif" name="myImage">

// 通过namedItem方法获取这个<img>元素
var myImage = images.namedItem("myImage");
```
要取得文档中所有元素，向`getElementsByTagName()`传入"*"即可。需要注意的是，为了与HTML页面兼容，传入该方法的标签名不需要区分大小写。

第三个方法是只有`HTMLDocument`类型才有的方法，即`getElementsByName()`。这个方法会返回带有给定name特性的所有元素。

**4.特殊集合**

下列集合都是`HTMLCollection`对象，为访问文档常用部分提供了快捷方式，包括：

* document.anchors,包含文档中所有带name特性的`<a>`元素；
* document.applets,包含文档中所有的`<applet>`元素，该元素已不推荐使用，所以可以忽略；
* document.forms，包含文档中所有的`<form>`元素，与`document.getElementsByTagName("form")`结果相同；
* document.images，包含文档中所有的`<img>`元素；
* document.links，包含文档中所有带href特性的`<a>`元素。

**5.DOM一致性检测**

`document.implementation`用来检测浏览器实现了DOM的哪些部分。

**6.文档写入**

将输出流写入到网页中的能力可以从以下4个方法体现：`write()、writeIn()、open()和close()`。其中，write()和writeIn()都接受一个字符串参数，即要写入到输出流中的文本。不同的是，`writeIn()`会在字符串的末尾添加一个换行符(\n)。open()和close()分别用于打开和关闭网页的输出流。

#### Element类型

`Element`类型用于表现XML或HTML元素，提供了对元素标签名、子节点及特性的访问。Element节点具有以下特征：

* nodeType的值为1；
* nodeName的值为元素的标签名；
* nodeValue的值为null；
* parentNode可能是Document或Element。

访问元素的标签名，可以使用`nodeName`或`tagName`属性。注意，返回的值都是全部大写的字符串(HTML中)。

**1.HTML元素**

所有HTML元素都由`HTMLElement`类型表示，`HTMLElement`类型直接继承自Element并添加了一些属性。

* id，元素在文档中的唯一标识符。
* title，有关元素的附加说明信息。
* lang，元素内容的语言代码，很少使用。
* dir，语言的方向，值为"ltr"或"rtl"，很少使用。
* className，元素的class。

元素中指定的所有信息，可以这样获取：

```
<div id="myDiv" class="bd" title="Body text" lang="en" dir="ltr"></div>

var div = document.getElementById("myDiv");
div.id; // "myDiv"
div.className; // "bd"
div.title; // "Body text"
div.lang; // "en"
div.dir; // "ltr"
```
当然，也可以为每个属性赋予新的值。

**2.取得特性**

操作特性的DOM方法有三个，分别是`getAttribute()、setAttribute()和removeAttribute()`。这三个方法可以针对任何特性使用。注意，传递给`getAttribute()`的特性名与实际的特性名相同。比如class，而不是className。该方法也可以获取自定义特性，比如data开头的。而且，特性的名称是不区分大小写的。

**3.设置特性**

`setAttribute()`接受两个参数：要设置的特性名和值。如果特性存在，则替换；如果不存在，则创建。当然，也可以操作自定义特性。

**4.创建元素**

使用`document.createElement()`可以创建新元素。接受参数为，要创建元素的**标签名**。在HTML文档中不区分大小写。可以使用`appendChild()`等方法将元素添加到文档树中，浏览器会立即呈现该元素。

#### Text类型

文本节点由Text类型表示，包含的是可以照字面解释的纯文本内容。纯文本内容可以包含转义后的HTML字符，但不能包含HTML代码。Text节点具有以下特征：

* nodeType的值为3；
* nodeName的值为"#text"；
* nodeValue的值为节点包含的文本；
* parentNode是一个Element；
* 没有子节点。

可以通过nodeValue属性或data属性访问Text节点中包含的文本。

**1.创建文本节点**

使用`document.createTextNode()`创建新文本节点。接收参数为，要插入节点中的文本。除非把新节点添加到文档树中已经存在的节点中，否则我们不会在浏览器窗口看到新节点。

```
var element = document.createElement("div");
element.className = "message";

var textNode = document.createTextNode("Hello world!");
element.appendChild(textNode);

document.body.appendChild(element);
```
如果element再添加一个同胞的文本节点，那么两个节点会连起来显示，没有空格。

#### Comment类型

注释在DOM中是通过Comment类型来表示的。Comment节点具有下列特征:

* nodeType的值为8；
* nodeName的值为"#comment"；
* nodeValue的值是注释的内容；
* parentNode可能是Document或Element；
* 没有子节点。

Comment类型与Text类型继承自相同的基类，与Text类型相似，可以通过nodeValue或data属性取得注释的内容。

```
var div = document.getElementById("myDiv");
var comment = div.firstChild;
comment.data; // "A comment"
```
另外，使用`document.createComment()`并为其传递注释文本也可以创建注释节点。

#### DocumentType类型

DocumentType包含着与文档的doctype有关的所有信息。DOM1会把DocumentType对象保存在document.doctype中。包括三个属性：name、entities和notations。

#### DocumentFragment类型

所有节点类型中，只有`DocumentFragment`在文档中没有对应的标记。DOM规定文档片段(document fragment)是一种"轻量级"的文档，可以包含和控制节点，但不会像完整的文档那样占用额外的资源。使用片段可以有效的减少操作DOM的次数，可以提高性能。用法如下：

```
var fragment = document.createDocumentFragment();

//将循环内的元素动态添加到fragment中，然后再将fragment一次性添加到元素节点中，这样就只操作了一次DOM
```

#### Attr类型

元素的特性在DOM中以Attr类型表示。Attr对象有三个属性：name、value和specified。其中，name是特性名称(与nodeName的值相同)，value是特性的值(与nodeValue的值相同)，而specified是一个布尔值，用以区别特性是在代码中指定的。平时也不直接使用，了解一下。

### DOM操作技术

#### 动态脚本

创建动态脚本有两种方式：插入外部文件和直接插入JS代码。这里看下动态插入外部文件怎么实现：

```
var script = document.createElement("script");

// 这步也可以省略，因为现在script标签默认类型就是"text/javascript"，link标签同理
script.type = "text/javascript";
script.src = "client.js";
document.body.appendChild(script);
```

#### 动态样式

将CSS样式包含到HTML页面有两种方式。其中，`<link>`元素用于包含来自外部的文件，`<style>`元素用于指定嵌入的样式。来看看`<link>`如何动态添加到页面中：

```
var link = document.createElement("link");
link.rel = "stylesheet";

//可省略
link.type = "text/css";
link.href = "style.css";
var head = document.getElementsByTagName("head")[0];
head.appendChild(link);
```
加载外部样式文件的过程是异步的，也就是加载样式与执行JS代码的过程没有固定的次序。

#### 使用NodeList

`NodeList`以及近亲`NamedNodeMap`和`HTMLCollection`，三个集合都是动态的。也就是说，每当文档结构发生变化时，都会得到更新。本质上讲，所有`NodeList`对象都是在访问DOM文档时实时运行的查询。

一般来说，应该尽量减少访问NodeList的次数。因为每次访问NodeList，都会运行一次基于文档的查询。所以应该尽可能将值缓存起来。

### 总结

DOM的难点就在于性能问题，雅虎军规也建议尽可能少操作DOM，如果把JS引擎和DOM比作两个岛屿，操作一次DOM就要过一次桥，频繁的操作就会在两地之间来回奔走，这肯定是十分消耗资源的。目前Vue的做法就是采用Virtual DOM来解决频繁操作DOM的问题。

还有一个办法就是使用片段。也就是document.createDocumentFragment()，可以将处理过后的DOM放入片段，然后将片段一次性插入到页面节点中，这样就只涉及一次DOM操作。

所以，最好的办法就是尽量减少DOM操作。

