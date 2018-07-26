---
layout:     post                   
title:      30 Seconds of CSS代码块解读(视觉篇)            
subtitle:   CSS黑科技
date:       2018-06-29
author:     chuck
header-img: img/home-bg-css.jpg
catalog: true                      
tags:                               
    - CSS
    - 伪元素
    - 渐变
---

## 30 Seconds of CSS代码块解读(视觉篇)

这是第二篇视觉篇，主要包括用纯CSS画圆和三角形、利用伪元素和背景实现各种美妙的装饰、利用渐变生成一系列赏心悦目的效果。通过美化视觉让页面上的元素更加饱满与生动。

### Circle

用纯CSS画一个实心圆。


```

//HTML
<div class="circle"></div>

//CSS
.circle {
  border-radius: 50%;
  width: 2rem;
  height: 2rem;
  background: #333;
}
```

说明：

1.  `border-radius: 50%`让圆角的半径值为元素的宽度或高度的一半。
2. 宽高相等就是圆形，如果宽高不相等，那么画出来的就是椭圆。
3. 同理，也可以画出半圆和四分之一圆。

### Custom scrollbar(自定义滚动条)

在WebKit平台上为文档和可滚动溢出的元素定制滚动条样式。


```
/*定义滚动条高宽及背景 高宽分别对应横竖滚动条的尺寸*/
::-webkit-scrollbar {
  width: 8px;
}
/*定义滚动条轨道 内阴影+圆角*/
::-webkit-scrollbar-track {
  box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.3);
  border-radius: 10px;
}
/*定义滑块 内阴影+圆角*/
::-webkit-scrollbar-thumb {
  border-radius: 10px;
  box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.5);
}
```

### Custom text selection(自定义文本选择)

通过伪元素更改文本选择的样式。主要可以更改两个样式，分别是背景颜色(background)和文本颜色(color)。


```
::selection {
  background: deeppink; //背景为粉色
  color: white; //文本为白色
}
```
说明：

如果没有合并任何其他选择器，则样式将在文档根级应用到任何可选元素。也就是说，其他元素会继承选中样式，除非对伪元素指定特定选择器。

### Dynamic shadow(动态阴影)

创建类似于`box-shadow`的阴影，但是是基于元素本身的颜色进行创建。


```

//HTML
<div class="dynamic-shadow"></div>

//CSS
.dynamic-shadow {
  position: relative;
  width: 10rem;
  height: 10rem;
  background: linear-gradient(75deg, #6d78ff, #00ffb8);
  z-index: 1;
}
.dynamic-shadow::after {
  content: '';
  width: 100%;
  height: 100%;
  position: absolute;
  background: inherit;
  top: 0.5rem;
  filter: blur(0.4rem);
  opacity: 0.7;
  z-index: -1;
}
```

说明：

通过创建一个与元素同样大小的伪元素，然后对伪元素做模糊和透明度处理，来达到看似是元素阴影的效果。

效果如下图所示：

![](https://ws2.sinaimg.cn/large/006tKfTcly1fsqu2aly51j305k05j0sn.jpg)

### Etched text(镂空文本)

创建让文字看起来被“蚀刻”或雕刻到背景中的效果。通俗地讲，也就是文字陷进去或凹进去了。一般从视觉角度来说，元素上方有白色，看起来就是凸出的效果；如果元素下方有白色，看起来就凹进的效果。该代码块就用到了这个效果。


```

//HTML
<p class="etched-text">I appear etched into the background.</p>

//CSS
.etched-text {
  text-shadow: 0 2px white;
  font-size: 1.5rem;
  font-weight: bold;
  color: #b8bec5;
}
```

说明：

通过`text-shadow`设置y轴正向偏移并赋予白色，让文字下方有白色亮光，就会看起来像是凹进去一样。而且文本颜色要比背景色更加深沉，效果更加逼真。

效果如下：

![](https://ws1.sinaimg.cn/large/006tKfTcly1fsqucndd2kj30c7026mwz.jpg)

### Gradient text(渐变文本)

让文本产生渐变的效果。


```

//HTML
<p class="gradient-text">Gradient text</p>

//CSS
.gradient-text {
  background: -webkit-linear-gradient(pink, red);
  -webkit-text-fill-color: transparent;
  -webkit-background-clip: text;
}

```

说明：

1. 使用`linear-gradient`给文本元素设置一个渐变背景。线性渐变默认是to bottom方向，也就是从上到下。不指定百分比的话，颜色平分。
2. 使用`-webkit-text-fill-color: transparent`让文本填充透明色。
3. `-webkit-background-clip: text`用文本剪辑背景，用渐变背景填充作为文本颜色。最后的效果就是文本拥有了渐变色。

### Hairline border(细线边框)

为元素提供一个边框，宽度等于1个本地设备像素，可以显得非常清晰。该代码块使用`box-shadow`模仿边框，但不支持单边，因为`box-shadow`不能单独定义单边边框阴影样式。


```

//HTML
<div class="hairline-border">text</div>

//CSS
.hairline-border {
  box-shadow: 0 0 0 1px;
}
@media (min-resolution: 2dppx) {
  .hairline-border {
    box-shadow: 0 0 0 0.5px;
  }
}
@media (min-resolution: 3dppx) {
  .hairline-border {
    box-shadow: 0 0 0 0.33333333px;
  }
}
@media (min-resolution: 4dppx) {
  .hairline-border {
    box-shadow: 0 0 0 0.25px;
  }
}
```

说明：

1. 这里使用到了`box-shadow`的第四个属性**扩展半径**，如果前三个属性：x轴偏移量、y轴偏移量和模糊半径都为0，就可以使用第四个属性扩展半径来模仿`border`属性。
2. 使用`@media（min-resolution：...）`检查设备像素比率（1 dppx等于96 DPI），将`box-shadow`的扩展半径设置为`1 / dp` px。

### :not selector

使用`:not`伪选择器来排除指定元素拥有一些样式。


```

//HTML
<ul class="css-not-selector-shortcut">
  <li>One</li>
  <li>Two</li>
  <li>Three</li>
  <li>Four</li>
  <li>Five</li>
</ul>

//CSS
.css-not-selector-shortcut {
  display: flex;
}
li {
  list-style-type: none;
  margin: 0;
  padding: 0 0.75rem;
}
li:not(:last-child) {
  border-right: 2px solid #d2d5e4;
}
```

说明：

1. `li:not(:last-child)`指定除了最后一个li元素外，对其他所有li元素应用右边框样式。
2. 额外知识点：`li:first-child:last-child`等价于`li:only-child`。

### Overflow scroll gradient(滚动溢出渐变)

将渐变添加到溢出元素以更好地指示有更多内容需要滚动。


```
//HTML
<div class="overflow-scroll-gradient">
  <div class="overflow-scroll-gradient__scroller">
    Content to be scrolled
  </div>
</div>
```


```
//CSS
.overflow-scroll-gradient {
  position: relative;
}
.overflow-scroll-gradient::after {
  content: '';
  position: absolute;
  bottom: 0;
  width: 240px;
  height: 25px;
  background: linear-gradient(
    rgba(255, 255, 255, 0.001),
    white
  ); /* transparent keyword is broken in Safari */
  pointer-events: none;
}
.overflow-scroll-gradient__scroller {
  overflow-y: scroll;
  background: white;
  width: 240px;
  height: 200px;
  padding: 15px 0;
  line-height: 1.2;
  text-align: center;
}
```
说明：

1. 重点就是通过父元素的伪元素`::after`在底部生成一个半透明的矩形块。为了兼容Safari没有使用`transparent`关键字。
2. `pointer-events:none`指定伪元素不能作为鼠标事件的目标，允许它后面的文本仍然可以选择/交互。否则就无法选择伪元素下面的文本。

### Pretty text underline(文本下划线)

更好的替代`text-decoration: underline`。 原生实现为`text-decoration-skip-ink:auto`，但对下划线的控制较少。


```
<p class="pretty-text-underline">Pretty text underline without clipping descending letters.</p>
```


```
.pretty-text-underline {
  display: inline;
  text-shadow: 1px 1px #f5f6f9, -1px 1px #f5f6f9, -1px -1px #f5f6f9, 1px -1px #f5f6f9;
  background-image: linear-gradient(90deg, currentColor 100%, transparent 100%);
  background-position: bottom;
  background-repeat: no-repeat;
  background-size: 100% 1px;
}
.pretty-text-underline::-moz-selection {
  background-color: rgba(0, 150, 255, 0.3);
  text-shadow: none;
}
.pretty-text-underline::selection {
  background-color: rgba(0, 150, 255, 0.3);
  text-shadow: none;
}
```

说明：

1. 使用`text-shadow`声明4个方向的偏移量，覆盖4x4像素区域，以确保下划线有一个“厚”阴影。阴影效果看起来就像有一层外边框。阴影颜色要与背景色相同。对于较大的字体，请使用较大的px大小。
2. 使用线性渐变创建一个90度的背景，颜色为`currentColor`关键字，也就是文本的颜色。背景的大小为文字的宽度度，1px的高度。位置在底部。
3. `::selection`伪选择器规则确保文本阴影不会干扰文本选择。不设置的话，选择文本时，就会有阴影凸显。

效果如下：

![](https://ws1.sinaimg.cn/large/006tKfTcly1fsrufqv1kqj30dc019dfn.jpg)

### Reset all styles(重置所有样式)

使用一个属性将所有样式重设为默认值。 这不会影响`direction`和`unicode-bidi`属性。

```
.reset-all-styles {
  all: initial;
}
```

说明：`all: initial`将所有样式重置为默认值。也就是说应用了该属性的元素，标签也会恢复到默认值。比如说`li`元素会默认有小圆点，`h1~h6`会有各自应有的`font-size`。

### Shape separator(形状分隔符)

使用SVG分隔两个不同的块以创建更有趣的视觉外观。


```
<div class="shape-separator"></div>
```

```
.shape-separator {
  position: relative;
  height: 48px;
  background: #333;
}
.shape-separator::after {
  content: '';
  background-image: url(data:image/svg+xml;base64,PHN2ZyB2aWV3Qm94PSIwIDAgMjQgMjQiIHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyIgZmlsbC1ydWxlPSJldmVub2RkIiBjbGlwLXJ1bGU9ImV2ZW5vZGQiIHN0cm9rZS1saW5lam9pbj0icm91bmQiIHN0cm9rZS1taXRlcmxpbWl0PSIxLjQxNCI+PHBhdGggZD0iTTEyIDEybDEyIDEySDBsMTItMTJ6IiBmaWxsPSIjZmZmIi8+PC9zdmc+);
  position: absolute;
  width: 100%;
  height: 24px;
  bottom: 0;
}
```

说明:

作用一目了然，通过伪元素来产生svg背景，伪元素位于目标元素的下方(bottom)。从而使用更有趣的分隔符。

### System font stack(系统字体堆栈)

使用操作系统的原生字体，看起来更像原生APP。

```
<p class="system-font-stack">This text uses the system font.</p>
```

```
.system-font-stack {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen-Sans, Ubuntu,
    Cantarell, 'Helvetica Neue', Helvetica, Arial, sans-serif;
}
```
说明：

浏览器逐个查找每个字体，如果可能的话首选第一个字体，如果找不到字体（在系统上或在CSS中定义），则继续查找下一个字体。

1. `-apple-system`用于iOS和macOS（不是Chrome）。
2. `BlinkMacSystemFont`在macOS Chrome上使用。
3. `Segoe UI`在Windows 10上使用。
4. `Roboto`在Android上使用。
5. `Oxygen-Sans`在GNU + Linux上使用。
6. `Ubuntu`在Linux上使用。
7. `"Helvetica Neue"`和`Helvetica`在macOS 10.10及更低版本中使用（用引号括起来是因为它有一个空格）。
8. `Arial`是所有操作系统广泛支持的字体。
9. 如果没有其他字体被支持，`sans-serif`是后备字体。

### Triangle(三角形)

用纯CSS创建一个实心三角形。

```
<div class="triangle"></div>
```

```
.triangle {
  width: 0;
  height: 0;
  border-top: 20px solid #333;
  border-left: 20px solid transparent;
  border-right: 20px solid transparent;
}
```

说明：

实质就是让元素本身宽高为0，而放大元素的border。因为border可以单独控制四个边，如果元素的宽高为0，每个边框的形状就是个三角形，再结合透明属性隐藏其余边框，只保留其中一个边框就产生了三角形。

上述例子就是保留了`border-top`，隐藏了其余边框。注意这里没有显示的声明`border-bottom`进行隐藏，但效果是一样的。


