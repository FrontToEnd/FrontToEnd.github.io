---
layout:     post                   
title:      JavaScript高级程序设计读书笔记(十)         
subtitle:   重温红皮书精髓--面向对象的程序设计
date:       2018-03-30
author:     chuck
header-img: img/home-bg-basic.jpg
catalog: true                      
tags:                               
    - JavaScript
    - DOM
    - 表单脚本
---


## 第十四章 表单脚本

### 表单的基础知识

在HTML中，表单是由`<form>`元素来表示，而在JS中，表单对应的则是`HTMLFormElement`类型。`HTMLFormElement`继承了`HTMLElement`。下面是`HTMLFormElement`独有的属性和方法。

* acceptCharset:服务器能够处理的字符集；等价于HTML中的accept-charset特性。
* action:接受请求的URL；等价于HTML中的action特性。
* elements:表单中所有控件的集合(HTMLCollection)。
* enctype:请求的编码类型；等价于HTML中的enctype特性。
* length:表单中控件的数量。
* method:要发送的HTTP请求类型，通常是"get"或"post";等价于HTML的method特性。
* name:表单的名称；等价于HTML的name特性。
* reset():将所有表单域重置为默认值。
* submit():提交表单。
* target:用于发送请求和接收响应的窗口名称；等价于HTML的target特性。

可以通过`document.forms`取得页面中所有的表单。在这个集合中，可以通过数值索引或name值来取得特定表单。


```

//第一个表单
document.forms[0];

//名字为myForm的表单
document.forms["myForm"];
```

#### 提交表单

可以使用`<input>`或`<button>`进行表单提交，🌰如下：

```

//通用提交按钮
<input type="submit" value="submit form">

//自定义提交按钮
<button type="submit">提交</button>

//图像按钮
<input type="image" src="">
```
采用上述方式提交表单时，浏览器会在将请求发送给服务器之前触发submit事件。这样就可以验证表单数据，决定是否允许表单提交。可以调用`preventDefault()`来阻止表单提交。

在js中，调用`submit()`也可以提交表单。但是不会触发submit事件，所以需要先主动验证一下表单数据。

```
var form = document.getElementById("myForm");

//提交
form.submit();
```
解决重复提交表单有以下两种方法：当点击提交按钮后禁用按钮并展示加载中的友好动画；或者利用onsubmit事件处理程序取消后续提交操作。

#### 重置表单

使用type特性值为"reset"的`<input>`或`<button>`可以创建重置按钮。在重置表单时，所有表单字段都会恢复到页面刚加载完毕时的**初始值**。

同样的，可以使用`reset()`进行重置。

#### 表单字段

每个表单都有`elements`属性，该属性是表单中所有元素的集合。该集合是一个有序列表，其中包含着表单中的所有字段。可以根据索引和name特性来访问表单元素。

```
var form = document.getElementById("form");

//获取第一个字段
form.elements[0];

//获取名称为"text"的字段
form.elements["text"];

//如果多个表单元素使用同一个name，那么就会返回一个NodeList。
```

**1.共有的表单字段属性**

表单字段共有的属性和方法如下：

* disabled:布尔值，表示当前字段是否被禁用。
* form:指向当前字段所属表单的指针，只读。
* name:当前字段的名称。
* readOnly:布尔值，表示当前字段是否只读。
* tabIndex:表示当前字段的切换序号。
* type:当前字段的类型，如"radio"。
* value:当前字段提交给服务器的值。

可以通过js来动态修改某字段的属性值。可以用来禁用某按钮后者设置为只读。

除了`<fieldset>`之外，所有表单字段都有type属性。对于`<input>`来说，这个值等同于HTML特性type的值。

`<input>`和`<button>`元素的type属性是可以动态修改的。而`<select>`元素的type属性是只读的。

**2.共有的表单字段方法**

每个表单字段都有两个方法：`focus()`和`blur()`。`focus()`用于将浏览器的焦点设置到表单字段，使其可以响应键盘事件。比如页面加载完后，将第一个表单元素聚焦。

```
document.forms[0].elements[0].focus();
```
如果第一个表单字段是一个hidden的输入框，那么上述代码会报错。另外，使用css的display和visibility隐藏该字段，同样会报错。

HTML5为表单字段新增了`autofocus`属性。只要设置了这个属性，不需要js就能自动把焦点移动到相应字段。

`blur()`的作用是从元素中移走焦点。可以使用该方法来创建只读字段。现在一般使用readonly来实现。

**3.共有的表单字段事件**

所有表单字段都支持下列3个事件。

* blur:当前字段失去焦点时触发。
* change:对于`<input>`和`<textarea>`元素，在它们失去焦点且value值改变时触发；对于`<select>`元素，在其选项改变时触发。
* focus:当前字段获得焦点时触发。

### 文本框脚本

有两种方式表现文本框：一种是使用`<input>`的单行文本框，另一种是使用`<textarea>`的多行文本框。

`<textarea>`始终会呈现一个多行文本框。可以使用rows和cols来指定大小。其中，rows指定文本框的字符行数，cols指定列数。`<textarea>`的初始值需要放在标签之间。而`<input>`可以通过value设置文本框的初始值。

两者都会将用户输入的内容保存在value属性中。可以通过诸如`document.forms[0].elements["textarea"].value`进行读写操作。

#### 选择文本

两种文本框都支持`select()`方法，用于选择文本框中的所有文本。该方法不接受参数。常用场景是文本框获得焦点时选择其所有文本。

在选择了文本框中的文本或调用`select()`时，就会触发select事件。

`selectionStart`和`selectionEnd`两个属性可以用来取得选择的文本。这两个属性中保存的是基于0的数值，表示所选择文本的范围。

也可以选择部分文本，所有文本框都有`setSelectionRange()`方法。该方法接收两个参数：起始和结束字符的索引。类似于`substring()`的参数。

#### 过滤输入

**1.操作剪贴板**

一共有6个剪贴板事件，包括：

* beforecopy:在发生复制操作前触发。
* copy:在发生复制操作时触发。
* beforecut:在发生剪切操作前触发。
* cut:在发生剪切操作时触发。
* beforepaste:在发生粘贴操作前触发。
* paste:在发生粘贴操作时触发。

在实际事件发生之前，通过beforecopy、beforecut和beforepaste事件可以向剪贴板发送数据，或者修改数据。取消这三个事件并不会取消对剪贴板的操作。

要访问剪贴板中的数据，可以使用`clipboardData`对象。chrome中，这个对象是相应event对象的属性。`clipboardData`对象有三个方法：getData()、setData()和clearData()。

`getData()`用于从剪贴板中取得数据，接受一个参数，即要取得的数据的格式。类似的，`setData()`第一个参数也是数据类型，第二个参数是要放在剪贴板中的文字。

在paste事件中，可以确定剪贴板中的值是否有效，如果无效可以使用`preventDefault()`进行阻止事件触发。


#### HTML5约束验证API

HTML5新增了一些将表单提交到服务器之前就验证数据的功能。浏览器自己会根据标记中的规则执行验证，不需要js干涉。只有在某些情况下，表单字段才能进行自动验证。

**1.必填字段**

任何标注有`required`的字段，在提交表单时不能空着。这个属性适用于`<input>、<textarea>和<select>`字段。js也可以检测出某个字段是否为必填字段。

```
var isRequired = document.forms[0].elements["input"].required;
```

**2.其他输入类型**

HTML5为`<input>`的type属性又增加了几个值。不仅能反映数据类型的信息，还能提供一些默认的验证功能。包括"email","url","phone"等等。用得最多的应该是"number"类型了。但是目前浏览器允许数字类型的输入框输入字母"e"，应该是因为e表示自然底数。总的来说，设置特定的输入类型并不能阻止用户输入无效的值，只是应用某些默认的验证而已。

**3.数值范围**

对所有这些数值类型的输入元素，可以指定min、max和step属性(最小到最大值的刻度)。

```

// 允许输入0到100且是5的倍数的数组
<input type="number" min="0" max="100" step="5" name="count">
```
数值类型可能会有数值调节按钮（向上和向下按钮）。

**4.输入模式**

HTML5为文本字段新增了pattern属性。属性值是一个正则表达式，用于匹配文本框中的值。

```

//只允许文本字段输入数值
<input type="text" pattern="\d+" name="count">
```

注意，模式的开头和末尾不用加^和$符号。指定pattern不能阻止用户输入无效的文本。

**5.检测有效性**

使用`checkValidity()`方法可以检测表单的某个字段是否有效。如果字段的值有效，这个方法返回true，否则返回false。字段的值是否有效的判断依据就是上述提到过的约束。比如说，必填字段是空的，就是无效的。

**6.禁用验证**

通过设置`novalidate`属性，可以告诉表单不进行验证。js中使用noValidate属性可以取得或设置这个值。

```
document.forms[0].noValidate = true; //禁用验证
```

### 选择框脚本

选择框是通过`<select>`和`<option>`元素创建的。HTMLSelectElement类型还提供了下列属性和方法。

* add():向控件中插入新`<option>`元素。
* multiple:布尔值，表示是否允许多项选择；等价于HTML中的multiple特性。
* options:控件中所有`<option>`元素的HTMLCollection。
* remove(index):移除给定位置的选项。
* selectedIndex:基于0的选中项的索引。
* size:选择框中可见的行数。

选择框的change事件只要选中了选项就会触发。

#### 选择选项

对于只允许选择一项的选择框，访问选中项的最简单方式，就是使用选择框的`selectedIndex`属性。

```

//获取选择框第一项的文本和值
var selectbox = document.forms[0].elements["select"];

var text = selectbox.options[0].text; // 选项的文本
var value = selectbox.options[0].value; //选项的值

//获取选中项
var selectOption = selectbox.options[selectbox.selectedIndex];
```

另一种选择选项的方式，就是取得对某一项的引用，然后将其selected属性设置为true。例如：

```

//选中第一项
selectbox.options[0].selected = true;
```

可以通过selected属性确定选择框哪一项被选中。

#### 添加选项

首先，可以使用DOM方法：先创建一个新的`<option>`元素，然后添加一个文本节点，并设置其value特性。最后将它添加到选择框中。

第二种方式是使用`Option`构造函数来创建。
`Option`构造函数接受两个参数：文本和值。第二个参数可选。

```
var option = new Option("some text","some value");
selectbox.appendChild(option);
```

第三种是使用选择框的`add()`方法。接受两个参数：要添加的新选项和将位于新选项之后的选项。为第二个参数传入`undefined`，将新选项插入到列表最后，兼容所有浏览器。

```
var option = new Option("some text","some value");
selectbox.add(option,undefined);
```

#### 移除选项

首先，可以使用DOM的`removeChild()`方法，移除指定项。

```
selectbox.removeChild(selectbox.options[0]); //移除第一项
```

其次，可以使用选择框的`remove()`方法。接受一个参数，移除项的索引。

```
selectbox.remove(0); //移除第一项
```

### 表单序列化

在编写代码之前，有必要搞清楚在表单提交期间，浏览器是怎样将数据发送给服务器的。

* 对表单字段的名称和值进行URL编码，使用`&`分隔。
* 不发送禁用的表单字段。
* 只发送勾选的复选框和单选按钮。
* 不发送type为"reset"和"button"的按钮。
* 多选选择框中的每个选中的值单独一个条目。
* 在单击提交按钮提交表单的情况下，也会发送提交按钮；否则不发送提交按钮。

### 富文本编辑

通过设置`designMode`属性，可以让整个页面可编辑。该属性有两个可能的值:"off"(默认值)和"on"。

```

//整个文档可编辑
document.designMode = "on"
```

#### 使用contenteditable属性

`contenteditable`属性应用给页面中的任何元素，然后用户立即就可以编辑该元素。通过设置`contenteditable`属性，也能打开或关闭编辑模式。

```
var div = document.getElementById("richedit");
div.contentEditable = "true";
```
contenteditable有三个可能的值："true"表示打开、"false"表示关闭，"inherit"表示从父元素继承。

#### 操作富文本

使用`document.execCommand()`来与富文本交互。该方法可以对文档执行预定义的命令，而且可以应用大多数格式。可以为该方法传递3个参数：要执行的命令名称、表示浏览器是否应该为当前命令提供用户界面的一个布尔值和执行命令必须的一个值(如果不需要，则传递null)。为确保兼容性，**第二参数始终为false**。

#### 富文本选区

使用框架的`getSelection()`方法，可以确定实际选择的文本。这个方法是window对象和document对象的属性，调用它会返回一个表示当前选择文本的Selection对象。Selection对象对象包括下列属性：

* anchorNode:选区起点所在的节点。
* anchorOffset:在到达选区起点位置之前跳过的anchorNode中的字符数量。
* focusNode:选区终点所在的节点。

