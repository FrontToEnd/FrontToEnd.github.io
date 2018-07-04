---
layout:     post                   
title:      30 Seconds of CSS代码块解读(互动篇与其他)            
subtitle:   CSS黑科技
date:       2018-07-04
author:     chuck
header-img: img/home-bg-css.jpg
catalog: true                      
tags:                               
    - CSS
---

## 30 Seconds of CSS代码块解读(互动篇与其他)

这是最后一篇解读，主要还是集中在优化交互方面，比如禁止选中、弹出框、通过伪元素排除元素进行淡出等。

虽然该开源项目总结的代码块不多，但大部分都是很实用的，相信CSS还有很多很多优秀的代码块没有收集，如果各位有什么好的idea，不妨进行去[该项目](https://github.com/atomiks/30-seconds-of-css)pr，让全世界的前端童鞋共同欣赏。

### Disable selection(禁止选中)


```
.unselectable {
  user-select: none;
}
```
说明：

`user-select: none`表明无法选择文本。

### Mouse cursor gradient tracking(光标渐变跟踪)

跟随鼠标光标渐变的悬停效果。该代码块需要借助JS。

```
<button class="mouse-cursor-gradient-tracking">
  <span>Hover me</span>
</button>
```

```
.mouse-cursor-gradient-tracking {
  position: relative;
  background: #7983ff;
  padding: 0.5rem 1rem;
  font-size: 1.2rem;
  border: none;
  color: white;
  cursor: pointer;
  outline: none;
  overflow: hidden;
}
.mouse-cursor-gradient-tracking span {
  position: relative;
}
.mouse-cursor-gradient-tracking::before {
  --size: 0;
  content: '';
  position: absolute;
  left: var(--x);
  top: var(--y);
  width: var(--size);
  height: var(--size);
  background: radial-gradient(circle closest-side, pink, transparent);
  transform: translate(-50%, -50%);
  transition: width 0.2s ease, height 0.2s ease;
}
.mouse-cursor-gradient-tracking:hover::before {
  --size: 200px;
}
```

```
var btn = document.querySelector('.mouse-cursor-gradient-tracking')
btn.onmousemove = function(e) {
  var x = e.pageX - btn.offsetLeft
  var y = e.pageY - btn.offsetTop
  btn.style.setProperty('--x', x + 'px')
  btn.style.setProperty('--y', y + 'px')
}
```

说明：

1. 主要使用了径向渐变`radial-gradient`。渐变形状为圆形，由粉色到透明色渐变。`closest-side`指定径向渐变的半径长度为从圆心到离圆心最近的边。
2. 当未悬浮元素时，渐变的大小为0，悬浮时变为200px。
3. 通过js计算出鼠标在元素上的偏移量。

效果如下：

![](https://ws2.sinaimg.cn/large/006tKfTcly1fss482d5z5g303z02swg6.gif)

### Popout menu(弹出菜单)

展示悬停时互动式弹出菜单。


```
<div class="reference">
  <div class="popout-menu">
    Popout menu
  </div>
</div>
```

```
.reference {
  position: relative;
}
.popout-menu {
  position: absolute;
  visibility: hidden;
  left: 100%;
}
.reference:hover > .popout-menu {
  visibility: visible;
}
```

说明：

1. 弹出菜单相对于父元素绝对定位，`left: 100%`意味着弹出菜单从左侧移动父元素宽度的100%。也就是紧靠在父元素右侧。
2. `visibility: hidden`隐藏最初弹出菜单并允许转换。与`display:none`是不同的。前者还占据着空间，后者不占据。
3. 当hover时，改变`visibility: visible`来展示弹出菜单。当然加上动画的话，体验会更好。

### Sibling fade(兄弟元素淡出)

hover当前元素时，淡出其他兄弟元素，用来凸显当前元素。

```
<div class="sibling-fade">
  <span>Item 1</span>
  <span>Item 2</span>
  <span>Item 3</span>
  <span>Item 4</span>
  <span>Item 5</span>
  <span>Item 6</span>
</div>
```


```
span {
  padding: 0 1rem;
  transition: opacity 0.2s;
}
.sibling-fade:hover span:not(:hover) {
  opacity: 0.5;
}
```
说明：

1. 默认是所有span元素都显式显示。只有hover父元素并且排除当前hover的span元素时，其他兄弟元素才会淡出(`opacity: 0.5`)。
2. `:not(:hover)`用来排除当前正在hover的元素。
3. 如果不加`.sibling-fade:hover`选择器，那么就是默认全部span都淡出。

### Counter(计数器)

实际上，计数器是由CSS维护的变量，其值可以通过CSS规则递增以跟踪它们被使用的次数。这个属性目前还没用过，也是头一次见。说明也是按照原本的英文说明进行照搬，如果还不懂，可以查看[MDN的解释](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Counter_Styles)。

```
<ul>
  <li>List item</li>
  <li>List item</li>
  <li>List item</li>
</ul>
```

```
ul {
  counter-reset: counter;
}
li::before {
  counter-increment: counter;
  content: counters(counter, '.') ' ';
}
```

说明：

1. 您可以使用任何类型的HTML创建有序列表。
2. `counter-reset`初始化计数器，该值是计数器的名称。默认情况下，计数器从0开始。此属性还可用于将其值更改为任何特定数字。
3. `counter-increment`用于可计数的元素。一旦计数器重置初始化，计数器的值可以增加或减少。
4. `counter(name，style)`显示节计数器的值。通常用于内容属性。此函数可以接收两个参数，第一个作为计数器的名称，第二个可以是十进制或大写罗马数字（默认为十进制）。
5. `counters(counter，string，style)`显示节计数器的值。通常用于内容属性。此函数可以接收三个参数，第一个作为计数器的名称，第二个可以包含一个字符串，它位于计数器后面，第三个可以是十进制或上罗马（默认为十进制）。
6. CSS计数器对于制作轮廓列表特别有用，因为计数器的新实例是在子元素中自动创建的。使用`counters()`函数，可以在不同级别的嵌套计数器之间插入分隔文本。


### Custom variables(自定义变量)

CSS变量是包含在整个文档中可重用的变量。SCSS或LESS中也可以定义全局变量进行重用，但是现在原生CSS已经支持定义变量了，积极拥抱新变化吧！


```
<p class="custom-variables">CSS is awesome!</p>
```

```
:root {
  --some-color: #da7800;
  --some-keyword: italic;
  --some-size: 1.25em;
  --some-complex-value: 1px 1px 2px whitesmoke, 0 0 1em slategray, 0 0 0.2em slategray;
}
.custom-variables {
  color: var(--some-color);
  font-size: var(--some-size);
  font-style: var(--some-keyword);
  text-shadow: var(--some-complex-value);
}
```
说明：

1. 这些变量在`:root`内定义，该伪类与表示文档树的根元素相匹配。 如果在块内定义，变量也可以作用于选择器。

2. 使用`--variable-name:`声明变量。

3. 使用`var(--variable-name)`函数在整个文档中重用变量。
4. 注意变量的写法，前面**两条**中划线，中间用**一条**中划线连接。

