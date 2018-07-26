---
layout:     post                   
title:      30 Seconds of CSS代码块解读(布局篇)            
subtitle:   CSS黑科技
date:       2018-06-27
author:     chuck
header-img: img/home-bg-css.jpg
catalog: true                      
tags:                               
    - CSS
    - 布局
---

## 30 Seconds of CSS代码块解读(布局篇)

这是一个号称在30秒内可以了解有用的CSS代码片段。GitHub地址在[这里](https://github.com/atomiks/30-seconds-of-css)。由于没有中文版，下面就记录下所有的代码片段，并加上一丢丢个人见解。

这是第一篇布局篇，主要涉及到flex和gird布局、清除浮动、盒模型的设置等等。

### Box-sizing reset

重置盒模型，防止边框(border)和内填充(padding)影响元素宽高。


```
html {
  box-sizing: border-box;
}
*,
*::before,
*::after {
  box-sizing: inherit;
}
```

说明：

1. `box-sizing: border-box`使得添加填充或边框不会影响元素的宽度或高度。
2. `box-sizing: inherit`继承父元素的盒模型设置规则。(默认就是继承，是否略微多余？)

### Clearfix

确保元素清除子元素浮动带来的影响。该代码仅适用于使用了浮动的代码。如果可以，请使用更现代的`flex`或`grid`布局。


```

//HTML
<div class="clearfix">
  <div class="floated">float a</div>
  <div class="floated">float b</div>
  <div class="floated">float c</div>
</div>

//CSS
.clearfix::after {
  content: '';
  display: block;
  clear: both;
}
.floated {
  float: left;
}
```

说明：

1. `.clearfix::after`定义了一个伪元素。
2. `content: ''`允许伪元素影响布局。
3. `clear: both`指示元素的左侧，右侧或两侧不能与相同块格式化上下文中较早的浮动元素相邻。

### Constant width to height ratio(宽高比恒定)

给定可变宽度的元素，让其宽高比保持不变。


```

//HTML
<div class="constant-width-to-height-ratio"></div>

//CSS
.constant-width-to-height-ratio {
  background: #333;
  width: 50%;
}
.constant-width-to-height-ratio::before {
  content: '';
  padding-top: 100%;
  float: left;
}
.constant-width-to-height-ratio::after {
  content: '';
  display: block;
  clear: both;
}
```
说明：

1. 在`::before`伪元素上使用`padding-top`导致元素的高度等于其宽度的百分比。 因此100％意味着元素的高度始终为宽度的100％。
2. `::after`伪元素来清除浮动。

### Evenly distributed children(均匀分布子元素)


```

//HTML
<div class="evenly-distributed-children">
  <p>Item1</p>
  <p>Item2</p>
  <p>Item3</p>
</div>

//CSS
.evenly-distributed-children {
  display: flex;
  justify-content: space-between;
}
```

说明：

1. 这里使用了flex布局。而且子元素在主轴上的分布方式是`justify-content: space-between`。`space-between`的意思是第一个元素紧靠左侧，最后一个元素紧靠右侧，其余元素平分剩余空间。
2. 主轴的另一个分布方式还有`justify-content: space-around`，意味着左右两侧的空间是相邻元素距离的一半，也就是围绕(around)在父元素。

### Flexbox centering

使用flex布局让子元素水平垂直居中。也是现在比较流行的居中方式。


```

//HTML
<div class="flexbox-centering">
  <div class="child">Centered content.</div>
</div>

//CSS
.flexbox-centering {
  display: flex;
  justify-content: center;
  align-items: center;
}
```

### Grid centering

使用grid布局让子元素水平垂直居中。同样是比较流行的居中方式。


```

//HTML
<div class="grid-centering">
  <div class="child">Centered content.</div>
</div>

//CSS
.grid-centering {
  display: grid;
  justify-content: center;
  align-items: center;
}
```
说明：

1. 可以发现，flex和grid的居中十分相似，除了`display`属性值不同外，其余都相同。但是两者的原理是截然不同的。

### Grid layout

使用gird进行布局。(这个可不止30秒才能看完:))


```

//HTML
<div class="grid-layout">
  <div class="header">Header</div>
  <div class="sidebar">Sidebar</div>
  <div class="content">
    Content
    <br>
    Lorem ipsum dolor sit amet, consectetur adipisicing elit.
  </div>
  <div class="footer">Footer</div>
</div>

//CSS
.grid-layout {
  display: grid;
  grid-gap: 10px;
  grid-template-columns: repeat(3, 1fr);
  grid-template-areas:
    'sidebar header header'
    'sidebar content content'
    'sidebar footer  footer';
  color: white;
}
.grid-layout > div {
  background: #333;
  padding: 10px;
}
.sidebar {
  grid-area: sidebar;
}
.content {
  grid-area: content;
}
.header {
  grid-area: header;
}
.footer {
  grid-area: footer;
}
```

说明：

1. grid布局的概念还是挺多的，建议直接google搜索教程学习。这里重点是通过`grid-template-columns`和`grid-template-areas`进行gird布局。

### Truncate text(截断文本)

如果文本长于一行，则它将被截断并以省略号结束(...)。需要注意的是，只适用于单行文本。


```

//HTML
<p class="truncate-text">If I exceed one line's width, I will be truncated.</p>

//CSS
.truncate-text {
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
  width: 200px;
}
```
说明：

1. `overflow: hidden`将超出元素宽度的文本进行隐藏。
2. `white-space: nowrap`阻止元素内的文本进行换行。
3. `text-overflow: ellipsis`超出的文本用省略号替代。

