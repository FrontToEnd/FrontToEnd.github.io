---
layout:     post                   
title:      JavaScript高级程序设计读书笔记(九)         
subtitle:   重温红皮书精髓--面向对象的程序设计
date:       2018-03-28
author:     chuck
header-img: img/home-bg-basic.jpg
catalog: true                      
tags:                               
    - JavaScript
    - DOM
    - 事件
---


## 第十三章 事件

JS与HTML之间的交互就是通过事件实现的。浏览器的事件系统相对比较复杂。尽管所有主要浏览器已经实现了“DOM2级事件”，但规范本身并没有涵盖所有事件类型。虽然增强后的事件API很繁琐，但是核心概念还是需要了解的。

### 事件流

事件流描述的是从页面接收事件的顺序。有意思的是，IE和网景开发团队居然提出了相反的事件流的概念。IE的事件流是事件冒泡流，而Netscape的事件流是事件捕获流。

#### 事件冒泡

IE的事件流叫做事件冒泡，即事件开始时由最具体的元素(文档中嵌套层次最深的那个节点)接收，然后逐级向上传播到较为不具体的节点。就像泡泡一样往上冒，遂称事件冒泡。

#### 事件捕获

事件捕获的思想是不太具体的节点应该更早接收到事件，而最具体的节点应该最后接收到事件。事件捕获的用意在于在事件到达预定目标之前捕获它。

#### DOM事件流

DOM2级事件 规定的事件流包括三个阶段：事件捕获阶段、处于目标阶段和事件冒泡阶段。首先是事件捕获，为截获事件提供了机会。然后是实际的目标接收到事件。最后一个阶段是冒泡阶段。在这个阶段对事件做出响应。目前现代浏览器都支持该规范。

### 事件处理程序

响应某个事件的函数就叫做事件处理程序(或事件侦听器)。为事件指定处理程序的方式有下面几种：

#### HTML事件处理程序

某个元素支持的每种事件，都可以使用一个与相应事件处理程序同名的HTML特性来指定。这个特性的值应该是能够执行的JS代码。比如：

```
<input type="button" value="click" onclick="alert("hello")" />
```
这样做有两大缺点：这样扩展事件处理程序的作用域链在不同浏览器中导致不同结果；最严重的一个缺点是HTML与JS代码紧密耦合。而且不能进行事件监听。

#### DOM0级事件处理程序

通过JS指定事件处理程序的传统方式，就是将一个函数赋值给一个事件处理程序属性。每个元素(包括`window`和`document`)都有自己的事件处理程序属性，这些属性通常全部小写，例如onclick。将这种属性的值设置为一个函数，就可以指定事件处理程序。

```
var btn = document.getElementById("myBtn");

btn.onclick = function(){
    console.log('clicked');
};
```
使用DOM0级方法指定的事件处理程序被认为是**元素**的方法。所以，这时候的事件处理程序是在元素的作用域中运行，也就是说，`this`引用当前元素。所以可以通过`this`访问元素的任何属性和方法。

这种方法添加的事件处理程序会在事件流的**冒泡阶段被处理**。现在一般不采用这种方式进行事件处理，而是采用下面要说的。

也可以删除DOM0级方法指定的事件处理程序，直接赋值为null即可，与解决闭包问题带来的内存泄漏问题一样。

```
btn.onclick = null;
```

#### DOM2级事件处理程序

DOM2级事件定义了两个方法，用于处理指定和删除事件处理的操作：`addEventListener()`和`removeEventListener()`。所有DOM节点都包含。接受3个参数：要处理的事件名、作为事件处理程序的函数和一个布尔值。最后参数的布尔值**如果是`true`，表示在捕获阶段调用事件处理程序；如果是`false`，表示在冒泡阶段调用事件处理程序**。

```
var btn = document.getElementById("myBtn");
btn.addEventListener("click",function(){
    console.log(this.id);
},false);
```
与DOM0一样，这里的处理程序也是依附在其元素的作用域中运行。

需要注意的是，`removeEventListener()`传入的参数与添加处理程序的参数相同。第二参数与添加事件必须相同才能移除该事件。最好的办法就是将事件赋值给变量，这样就能保证是同一个事件。

#### IE事件处理程序

IE也有两个类似的方法：`attachEvent()`和`detachEvent()`。这两个方法接受相同的两个参数：事件处理程序名称与时间处理程序函数。由于IE8以及更早版本只支持事件冒泡，所以通过`attachEvent()`会被添加到冒泡阶段。

### 事件对象

在触发DOM上的某个事件时，会产生一个事件对象event，这个对象包含所有与事件有关的信息。包括导致事件的元素、事件的类型与和事件相关的信息。

#### DOM中的事件对象

兼容DOM的浏览器会将一个event对象传入到事件处理程序中。

```
var btn = document.getElementById("myBtn");
btn.addEventListener("click",function(event){
    console.log(event.type); //"click"
},false);
```

event对象包含与创建它的特定事件有关的属性和方法。触发的事件不同，可用的属性和方法也不一样。


| 属性/方法 | 类型 | 读/写 | 说明 |
| --- | --- | --- | --- |
| bubbles | Boolean | 只读 | 表明事件是否冒泡 |
| cancelable | Boolean | 只读 | 表明是否可以取消事件的默认行为 |
| currentTarget | Element | 只读 | 其事件处理程序当前正在处理事件的那个元素 |
| defaultPrevented | Boolean | 只读 | 为true表示已经调用了preventDefault() |
| detail | Integer | 只读 | 与事件相关的细节信息 |
| eventPhase | Integer | 只读 | 调用事件处理程序的阶段：1表示捕获阶段，2表示“处于目标”，3表示冒泡阶段 |
| preventDefault() | Function | 只读 | 取消事件的默认行为。如果cancelable是true，则可以使用这个方法 |
| stopImmediatePropagation() | Function | 只读 | 取消事件的进一步捕获或冒泡，同时阻止任何事件处理程序被调用 |
| stopPropagation() | Function | 只读 | 取消事件的进一步捕获或冒泡。如果bubbles为true，则可以使用这个方法 |
| target | Element | 只读 | 事件的目标 |
| trusted | Boolean | 只读 | 为true表示事件是浏览器生成的。为false表示事件是由开发人员通过JS创建的 |
| type | String | 只读 | 被触发的事件的类型 |
| view | AbstractView | 只读 | 与事件关联的抽象视图。等同于发生事件的window对象 |

事件处理程序内部，对象this始终等于`currentTarget`的值，而target则只包含事件的实际目标。如果直接将事件处理程序指定给了目标元素，则this、currentTarget和target包含相同的值。如果事件处理程序存在于按钮的父节点中，那么这些值是不同的。

> 只有在事件处理程序执行期间，event对象才会存在；一旦事件处理程序执行完成，event对象就会被销毁。

### 事件类型

DOM3级事件规定了以下几类事件。

* UI事件，当用户与页面上的元素交互时触发；
* 焦点事件，当元素获得或失去焦点时触发；
* 鼠标事件，当用户通过鼠标在页面上执行操作时触发；
* 滚轮事件，当使用鼠标滚轮时触发；
* 文本事件，当在文档中输入文本时触发；
* 键盘事件，当用户通过键盘在页面上执行操作时触发；
* 合成事件，当为输入法编辑器输入字符时触发；
* 变动事件，当底层DOM结构发生变化时触发。

#### UI事件

UI事件指的是那些不一定与用户操作有关的事件。在DOM规范中保留是为了向后兼容。现有的UI事件如下。

* load：当页面完全加载后在window上面触发，当所有框架都加载完毕时在框架集上面触发，当图像加载完毕时在`<img>`元素上触发。
* unload：当页面完全卸载后在window上触发，当所有框架都卸载后在框架集上触发。
* abort：在用户停止下载过程时，如果嵌入的内容没有加载完，则在`<object>`元素上触发。
* error：当发生JS错误时在window上触发，当无法加载图像时在`<img>`元素上面触发，当无法加载嵌入内容时在`<object>`元素上触发，或者当有一或多个框架无法加载时在框架集上触发。
* select：当用户选择文本框(`<input>`或`<textarea>`)中的一或多个字符串触发。
* resize：当窗口或框架的大小变化时在window或框架上触发。
* scroll：当用户滚动带滚动条的元素中的内容时，在该元素上触发。`<body>`元素中包含所加载页面的滚动条。

上述事件在DOM2级事件中归为HTML事件。要确定浏览器是否支持DOM2或DOM3级事件，可以这样判断：

```

//判断DOM2
var isSupported = document.implementation.hasFeature("HTMLEvents","2.0");

//判断DOM3
var isSupported = document.implementation.hasFeature("UIEvent","3,0");
```

**1.load事件**

当页面完全加载后（包括所有图像、JavaScript文件、CSS文件等外部资源），就会触发window上面的`load`事件。

有两种定义onload事件处理程序的方式。第一种是通过JS来绑定事件(addEventListener)；第二种是为`<body>`元素添加一个onload特性。但是应该尽可能的使用JS方式。

**2.unload事件**

这个事件在文档被完全卸载后触发。只要用户从一个页面切换到另一个页面，就会触发。利用这个事件最多的情况是清除引用，以避免内存泄漏。指定方式与load事件类似。

需要注意的是，要很小心的编写onunload事件。因为该事件是在一切都被卸载后触发的，此时操作DOM节点或元素样式会导致错误。

根据DOM2级事件，应该在`<body>`元素而非window对象上触发unload事件。但浏览器都在window实现了unload事件，确保兼容。

**3.resize事件**

当浏览器窗口被调整到一个新的高度或宽度时，就会触发`resize`事件。这个事件在window上触发，因此可以通过JS或者`<body>`元素中的`onresize`特性来指定事件处理程序。

需要注意的是，由于浏览器的差异性，不要在该事件的处理程序中加入大量计算的代码，因为resize事件可能频繁触发，导致浏览器反应变慢。同时，浏览器窗口最小化或最大化时也会触发resize事件。

**4.scroll事件**

虽然scroll事件是在window对象上发生的，但实际表示的则是页面中相应元素的变化。混杂模式下，可以通过`<body>`元素的`scrollLeft`和`scrollTop`来监控这变化；标准模式下，除Safari之外所有浏览器都会通过`<html>`元素反映。

scroll事件也会在文档被滚动期间重复被触发。所以要考虑到函数防抖的问题。


#### 焦点事件

焦点事件会在页面获得或失去焦点时触发。利用这些事件并与`document.hasFocus()`方法及`document.activeElement`属性配合，可以知晓用户在页面上的行踪。有一下焦点事件：(原书中以废弃或者只有个别浏览器支持的事件不列举)

* blur：在元素失去焦点时触发。这个事件不会冒泡。所有浏览器都支持。
* focus：在元素获得焦点时触发。这个事件不会冒泡。所有浏览器都支持。
* focusin：在元素获得焦点时触发。这个事件与HTML事件focus等价，但它冒泡。
* focusout：在元素失去焦点时触发。这个事件是HTML事件blur的通用版本。

当焦点从页面中的一个元素移动到另一个元素，会依次触发下列事件：

1. focusout在失去焦点的元素上触发；
2. focusin在获得焦点的元素上触发；
3. blur在失去焦点的元素上触发；
4. focus在获得焦点的元素上触发。


#### 鼠标与滚轮事件

DOM3级事件定义了9个鼠标事件，简介如下。

* click：在用户单击主鼠标按钮或者按下回车键时触发。意味着onclick事件也可以通过键盘回车键触发。
* dblclick：在用户双击主鼠标按钮触发。
* mousedown：在用户按下了任意鼠标按钮时触发。**不能通过键盘触发**。
* mouseenter：在鼠标光标从元素外部**首次**移动到元素范围内时触发。这个事件不冒泡，而且在光标移动到后代元素上不会触发。
* mouseleave：在位于元素上方的鼠标光标移动到元素范围之外时触发。这个事件不冒泡，而且在光标移动到后代元素上不会触发。
* mousemove：当鼠标指针在元素内部移动时重复地触发。**不能通过键盘触发**。
* mouseout：在鼠标指针位于一个元素上方，然后用户将其移入另一个元素时触发。另一个元素可能是这个元素的子元素。**不能通过键盘触发**。
* mouseover：在鼠标指针位于一个元素外部，然后用户将其首次移入另一个元素边界之内时触发。**不能通过键盘触发**。
* mouseup：在用户释放鼠标按钮时触发。**不能通过键盘触发**。

页面上的所有元素都支持鼠标事件。除了`mouseenter`和`mouseleave`,所有鼠标事件都会冒泡，也可以被取消，取消鼠标事件会影响浏览器的默认行为。

只有在同一个元素上相继触发`mousedown`和`mouseup`，才会触发`click`事件；如果其中一个被取消，就不会触发`click`事件。类似的，触发两次`click`事件，才会触发`dblclick`事件。这四个事件触发顺序如下：

1. mousedown
2. mouseup
3. click
4. mousedown
5. mouseup
6. click
7. dblclick

显然，click和dblclick事件都会依赖于其他先行事件的触发。

**1.客户区坐标位置**

鼠标事件都是在浏览器视口中的特定位置上发生的。这个位置信息保存在事件对象的clientX和clientY属性中。表示事件发生时鼠标指针在视口中的水平和垂直坐标。需要注意的是，该值不包括页面滚动的距离，因此这个位置**并不表示鼠标在页面的位置**，而是视口的位置。

**2.页面坐标位置**

页面坐标通过事件对象的pageX和pageY属性，表明事件在**页面**的什么位置发生的。所以该坐标是从页面的左上角计算的。

如果页面没有滚动，`pageX`和`pageY`的值与`clientX`和`clientY`的值相等。

**3.屏幕坐标位置**

通过`screenX`和`screenY`属性可以确定鼠标事件发生时鼠标指针相对于整个屏幕的坐标信息。

该坐标是相对于设备屏幕左上角的坐标。

**4.修改键**

修改键包括`shift、ctrl、alt和meta`(Windows中是键盘中的windows键或者是一个微软的logo，Mac是Cmd键)。它们被用来修改鼠标事件的行为。DOM为此规定了4个属性，表示这些修改键的状态：`shiftKey、ctrlKey、altKey和metaKay`。这些属性中包含的都是布尔值，如果对应键被按下，值为`true`，否则为`false`。

**5.相关元素**

发生`mouseover`和`mouseout`事件时，会涉及到更多的元素。对于mouseover而言，事件的主目标是获得光标的元素，而**相关元素**就是那个失去光标的元素。

DOM通过event对象的`relatedTarget`属性提供了相关元素的信息。这个属性只对于mouseover和mouseout事件才包含之，对于其他事件，属性值为`null`。

**6.鼠标按钮**

对于`mousedown`和`mouseup`事件来说，其event对象存在一个`button`属性，表示按下或释放的按钮。DOM的button属性可能有如下3个值：0表示主鼠标按钮(左键)，1表示中间的鼠标按钮，2表示次鼠标按钮(右键)。

**7.更多的事件信息**

DOM2级事件规范在event对象中还提供了`detail`属性，用于给出有关事件的更多信息。对于鼠标事件来说，`detail`包含一个数值，表示在给定位置上发生了多少次单击。**在同一像素上相继发生一次`mousedown`和`mouseup`算一次单击。**`detail`属性从1开始计数，每次单击后递增。

**8.鼠标滚轮事件**

当用户通过鼠标滚轮与页面交互上下滚动页面时，就会触发`mousewheel`事件。这个事件可以在任何元素上触发。与`mousewheel`事件对应的event对象除包含鼠标事件的所有标准信息外，还包含一个特殊的`wheelDelta`属性。当用户向前滚动鼠标滚动时，`wheelDelta`是120的倍数；当用户向后滚动鼠标滚轮时，`wheelDelta`是-120的倍数。

**9.触摸设备**

在面向iPhone中的Safari开发时，记住一下几点：

* 不支持dblclick事件。双击浏览器窗口会放大窗口(如果可以的话)。
* 轻击可单击元素会触发mousemove事件。
* mousemove事件也会触发mouseover和mouseout事件。
* 两个手指放在屏幕上且页面随手指移动而滚动时会触发mousewheel和scroll事件。(Mac也是,双指滑动页面会触发mousewheel事件)

#### 键盘与文本事件

键盘事件有3个，分别是：

* keydown：当用户按下键盘上的任意键时触发，而且如果按住不放的话，会重复触发此事件。
* keypress：当用户按下键盘上的字符键时触发，如果按住不放的话，会重复触发此事件。按下Esc键也会触发(实测Chrome 65**并未触发**此事件)
* keyup：当用户释放键盘上的键时触发。

文本事件只有一个：`textInput`。在文本插入文本框之前会触发`textInput`事件。(然后会触发input事件)。所以在文本框插入文本的事件顺序为 **keydown-->keypress-->textInput-->input-->keyup**

如果用户按下了一个字符键不放，就会重复触发keydown和keypress事件(有的话)，直到用户松手。(经测试，**chrome 65中，4个修改键不会重复触发keydown，并且没有keypress事件**)

同样的，键盘事件的事件对象中也有shiftKey、ctrlKey、altKey和metaKey属性。如果按下某修改键，相应属性为true。只有keydown为true，keyup一直为false。

**1.键码**

发生键盘事件时，event对象的`keyCode`属性包含一个代码，与键盘上一个特定的键对应。对数字字母字符键，keyCode属性的值与ASCII码中对应**小写字母**或数字的编码相关。比如，字母A键的keyCode值为65。

**2.字符编码**

发生keypress事件意味着按下的键会影响到屏幕中文本的显示。所有浏览器中，按下能够插入或删除字符的键都会触发keypress事件。

charCode属性只有在发生keypress事件时才包含值，而且这个值是按下的那个键所代表字符的ASCII编码。此时的keyCode通常为0，或者等于所按键的键码。

**3.DOM3级变化**

DOM3级事件中的键盘事件，不再包含charCode属性，而是包含两个新属性：key和char。

其中，key属性是为了取代keyCode而新增的，它的值是一个字符串。在按下某个字符键时，key的值就是相应的文本字符(如"s")；按下非字符键时，key的值是相应键的名(如"Shift")。

而char属性在按下字符键时的行为与key相同，但在按下非字符键值为`null`。chrome 65中并没有char属性，但是有code属性。

由于有跨浏览器问题，因为不推荐使用上述属性。

**4.textInput事件**

DOM3级事件引入一个新事件，名叫`textInput`。当用户在可编辑区域中输入字符时，就会触发该事件。

`textInput`与`keypress`有两个区别：之一，任何可以获得焦点的元素都可以触发keypress事件，但只有可编辑区域才能触发textInput事件。之二，`textInput`事件只会在用户按下能够输入实际字符的键时才会被触发，而`keypress`事件则在按下那些能够影响文本显示的键时也会触发，比如退格键。

`textInput`事件的event对象还包含一个**data属性**，这个属性的**值就是用户输入的字符**。

另外，event对象还有一个属性，叫inputMethod，表示把文本输入到文本框中的方式。使用这个属性可以确定文本是如何输入到控件中的。

#### 复合事件

**复合事件**是DOM3级事件新添加的事件，用于**处理IME的输入序列**。IME(Input Method Editor,输入法编辑器)可以让用户输入在物理键盘上找不到的字符。IME通常需要同时按住多个键，但最终只输入一个字符。共有三种复合事件：

* compositionstart:在IME的文本复合系统打开时触发，表示要开始输入了。
* compositionupdate:在向输入字段中插入新字符时触发。
* 在IME的文本复合系统关闭时触发，表示返回正常键盘输入状态。

但是由于缺少支持，它的用处不大。

#### 变动事件

DOM2级的变动事件能在DOM中的某一部分发生变化时给出提示。DOM2级定义了如下变动事件。

* DOMSubtreeModified:
* DOMNodeInserted:
* DOMNodeRemoved:
* DOMNodeInsertedIntoDocument:
* DOMNodeRemovedFromDocument:
* DOMAttrModified:
* DOMCharacterDataModified:

由于DOM3级事件作废了很多变动事件，所以只记录支持的事件。

**1.删除节点**

在使用`removeChild()`或`replaceChild()`从DOM中删除节点时，首先会触发`DOMNodeRemoved`事件。这个事件的目标(event.target)是被删除的节点，这个事件触发时，节点尚未被删除，因此其parentNode属性仍然指向父节点。该事件会冒泡。

然后会触发`DOMSubtreeModified`事件。这个事件的目标是被移除节点的父节点；此时的event对象也不会提供与事件相关的其他信息。

**2. 插入节点**

在使用appendChild()、replaceChild()或insertBefore()向DOM中插入节点时，首先会触发`DOMNodeInserted`事件。这个事件的目标是被插入的节点。这个事件触发时，节点已经被插入到新的父节点中。该事件会冒泡。

#### 触摸与手势事件

以下介绍的事件只针对触摸设备。

**1.触摸事件**

触摸事件会在用户手指放在屏幕上面时，在屏幕上滑动时或从屏幕上移开时触发。具体来说，有以下几个触摸事件。

* touchstart:当手指触摸屏幕时触发；即使已经有一个手指放在了屏幕上也会触发。
* touchmove:当手指在屏幕上滑动时连续地触发。在这个事件发生期间，调用`preventDefault()`可以阻止滚动。
* touchend:当手指从屏幕上移开时触发。
* touchcancel:当系统停止跟踪触摸时触发。

上述事件都会冒泡，也都可以取消。虽然没有在DOM规范中定义，但它们却是以兼容DOM的方式实现。因此，每个触摸事件的event对象都提供了在鼠标事件中常见的属性：`bubbles、cancelable、view、clientX、clientY、screenX、screenY、detail、altKey、shiftKey、ctrlKey和metaKey`。

触摸事件还包含下列三个用于跟踪触摸的属性。

* touches:表示当前跟踪的触摸操作的Touch对象的数组。
* targetTouchs:特定于事件目标的Touch对象的数组。
* changeTouches:表示自上次触摸以来发生了什么改变的Touch对象的数组。

每个Touch对象包含下列属性。

* clientX:触摸目标在`视口`中的x坐标。
* clientY:触摸目标在`视口`中的y坐标。
* identifier:标识触摸的唯一ID。
* pageX:触摸目标在`页面`中的x坐标。
* pageY:触摸目标在`页面`中的y坐标。
* screenX:触摸目标在`屏幕`中的x坐标。
* screenY:触摸目标在`屏幕`中的y坐标。
* target:触摸的DOM节点目标。


这些事件会在文档的所有元素上面触发，因而可以分别操作页面的不同部分，在触摸屏幕上的元素时，这些事件发生的顺序如下：

1. touchstart
2. mouseover
3. mousemove
4. mousedown
5. mouseup
6. click
7. touchend

**2.手势事件**

当两个手指触摸屏幕时就会产生手势，手势通常会改变显示项的大小，或者旋转显示项。有三个手势，分别是：

* gesturestart:当一个手指已经按在屏幕上而另一个手指又触摸屏幕时触发。
* gesturechange:当触摸屏幕的任何一个手指的位置发生变化时触发。
* gestureend:当任何一个手指从屏幕上面移开时触发。

只有两个手指都触摸到事件的接收容器时才会触发这些事件。意味着两个手指必须同时位于该元素的范围之内，才能触发手势事件。

与触摸事件一样，手势事件的event事件也包含标准的鼠标事件属性。除此之外，还包含两个额外的属性：`rotation`和`scale`。其中，rotation属性表示手指变化引起的旋转角度，负值表示逆时针旋转，正值表示顺时针旋转。而scale属性表示两个手指间距离的变化情况(从1开始)。

### 内存和性能

从如何利用好事件处理程序的角度，下面的方案可以提升一些性能。

#### 事件委托

事件委托利用了事件冒泡，只指定一个事件处理程序，就可以管理某一类型的所有事件。可以为整个页面指定事件，而不必给每个可单击的元素分别添加事件处理程序。

事件委托有如下优点：

* document对象很快就可以访问，而且可以在页面生命周期的任何时点为它添加事件处理程序(无需等待DOMContentLoaded或load事件)。
* 在页面中设置事件处理程序所需时间更少。只添加一个事件处理程序所需的DOM引用更少，花的时间也更少。
* 提升性能。

适合采用事件委托技术的事件包括：click、mousedown、mouseup、keydown、keyup和keypress。

#### 移除事件处理程序

在使用innerHTML替换页面之前，先移除按钮的事件处理程序，确保内存可以被再次利用。

### 模拟事件

可以使用JS在任意时刻来触发特定的事件，就如同浏览器创建的事件一样。

#### DOM中的事件模拟

可以在document对象上使用`createEvent()`方法创建event对象。这个方法接收一个参数，即表示要创建的事件类型的字符串。在DOM2级中，所有字符串都是复数形式，而在DOM3级变成了单数。

* UIEvents:一般化的UI事件。鼠标事件和键盘事件都继承自UI事件。DOM3级中是UIEvent。
* MouseEvents:一般化的鼠标事件。DOM3级中是MouseEvent。
* MutationEvents:一般化的DOM变动事件。DOM3级中是MutationEvent。
* HTMLEvents:一般化的HTML事件。没有对应的DOM3级事件。

然后需要触发事件。需要调用`dispatchEvent()`方法。


```
var btn = document.getElementById("myBtn");

// 创建鼠标事件
var event = document.createEvent("MouseEvents");

//初始化事件,该方法接收15个参数，这里忽略
event.initMouseEvent("click",...arg);

//最后需要触发事件
btn.dispatchEvent(event);
```


