---
layout:     post                   
title:      30 Seconds of CSS代码块解读(动画篇)            
subtitle:   CSS黑科技
date:       2018-07-02
author:     chuck
header-img: img/home-bg-css.jpg
catalog: true                      
tags:                               
    - CSS
    - 动画
---

## 30 Seconds of CSS代码块解读(动画篇)

这是第三篇解读，主要包括加载效果，自定义过渡动画函数和下划线动画。

### Bouncing loader(弹跳加载)

创建一个弹跳加载动画。

```
<div class="bouncing-loader">
  <div></div>
  <div></div>
  <div></div>
</div>
```

```
@keyframes bouncing-loader {
  from {
    opacity: 1;
    transform: translateY(0);
  }
  to {
    opacity: 0.1;
    transform: translateY(-1rem);
  }
}
.bouncing-loader {
  display: flex;
  justify-content: center;
}
.bouncing-loader > div {
  width: 1rem;
  height: 1rem;
  margin: 3rem 0.2rem;
  background: #8385aa;
  border-radius: 50%;
  animation: bouncing-loader 0.6s infinite alternate;
}
.bouncing-loader > div:nth-child(2) {
  animation-delay: 0.2s;
}
.bouncing-loader > div:nth-child(3) {
  animation-delay: 0.4s;
}
```

说明：

1. `@keyframes`定义了一个具有两种状态的动画，`from`等价于0%，`to`等价于100%。其中动画是将元素的透明度变为0.1，同时在2D平面上将元素向上移动1rem。
2. 父容器使用`flex`布局将包裹元素进行居中。
3. `animation`是各种动画属性的缩写属性:animation-name, animation-duration, animation-iteration-count, animation-direction。在本例中，动画名称为`bouncing-loader`，动画持续0.6s，无限播放(infinite)并且结束时反方向播放(alternate)。
4. `animation-delay`用来规定指定元素的动画延迟相应的时间再播放。该属性接受负值，表示提前到相应时间所对应的状态，从当前状态播放。

效果如下：

![](https://ws3.sinaimg.cn/large/006tKfTcly1fsrwoccakpg309q02saac.gif)

### Donut spinner(加载圈)

创建一个表示内容加载的加载圈。


```
<div class="donut"></div>
```

```
@keyframes donut-spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
.donut {
  display: inline-block;
  border: 4px solid rgba(0, 0, 0, 0.1);
  border-left-color: #7983ff;
  border-radius: 50%;
  width: 30px;
  height: 30px;
  animation: donut-spin 1.2s linear infinite;
}
```
说明：

创建一个没有背景的圆，然后声明透明度为0.1的黑色边框(看起来是灰色)，修改左侧边框颜色。此时会有一个静态的看起来只有左边框有颜色的空心圆。然后声明一个该元素逆时针旋转360度的动画，并让该动画无限播放(infinite)即可。

### Easing variables(简化变量)

可以在`transition-timing-function`(过渡动画函数)使用变量，比`ease, ease-in, ease-out` 和 `ease-in-out`更加强大。


```
<div class="easing-variables"></div>
```

```
:root {
  --ease-in-quad: cubic-bezier(0.55, 0.085, 0.68, 0.53);
  --ease-in-cubic: cubic-bezier(0.55, 0.055, 0.675, 0.19);
  --ease-in-quart: cubic-bezier(0.895, 0.03, 0.685, 0.22);
  --ease-in-quint: cubic-bezier(0.755, 0.05, 0.855, 0.06);
  --ease-in-expo: cubic-bezier(0.95, 0.05, 0.795, 0.035);
  --ease-in-circ: cubic-bezier(0.6, 0.04, 0.98, 0.335);
  --ease-out-quad: cubic-bezier(0.25, 0.46, 0.45, 0.94);
  --ease-out-cubic: cubic-bezier(0.215, 0.61, 0.355, 1);
  --ease-out-quart: cubic-bezier(0.165, 0.84, 0.44, 1);
  --ease-out-quint: cubic-bezier(0.23, 1, 0.32, 1);
  --ease-out-expo: cubic-bezier(0.19, 1, 0.22, 1);
  --ease-out-circ: cubic-bezier(0.075, 0.82, 0.165, 1);
  --ease-in-out-quad: cubic-bezier(0.455, 0.03, 0.515, 0.955);
  --ease-in-out-cubic: cubic-bezier(0.645, 0.045, 0.355, 1);
  --ease-in-out-quart: cubic-bezier(0.77, 0, 0.175, 1);
  --ease-in-out-quint: cubic-bezier(0.86, 0, 0.07, 1);
  --ease-in-out-expo: cubic-bezier(1, 0, 0, 1);
  --ease-in-out-circ: cubic-bezier(0.785, 0.135, 0.15, 0.86);
}
.easing-variables {
  width: 50px;
  height: 50px;
  background: #333;
  transition: transform 1s var(--ease-out-quart);
}
.easing-variables:hover {
  transform: rotate(45deg);
}
```

说明：

1. `:root`CSS伪类与表示文档的树的根元素相匹配。 在HTML中，`:root`代表`<html>`元素，并且与选择器html相同，只不过它的特异性更高。

2. 当鼠标移入当前元素时，元素按照指定的三次贝塞尔曲线变量进行过渡。

    在CSS3中，定义三次贝塞尔曲线语法如下：
    
    ```
    //每个点的取值范围是0~1
    cubic-bezier(P0,P1,P2,P3);
    ```

### Height transition(高度过渡)

当元素高度未知的时候，将该元素的高度由`0`过渡到`auto`。该代码段需要借助JavaScript。因为CSS无法获取元素的实际高度。


```
<div class="trigger">
  Hover me to see a height transition.
  <div class="el">content</div>
</div>
```

```
.el {
  transition: max-height 0.5s;
  overflow: hidden;
  max-height: 0;
}
.trigger:hover > .el {
  max-height: var(--max-height);
}
```

```
var el = document.querySelector('.el')
var height = el.scrollHeight
el.style.setProperty('--max-height', height + 'px')
```

说明：

1. 先将元素`overflow: hidden`，防止隐藏元素的内容溢出其容器。然后设置最大高度为`0`。
2. 当触发动画时，将元素`max-height: var(--max-height)`。这里的变量是由JS定义。
3. 使用JS获取元素的滚动高度`el.scrollHeight`。然后设置`--max-height` CSS变量，用于指定目标所在元素的最大高度，允许它从`0`平滑过渡到`auto`。

### Hover underline animation(悬停下划线动画)

悬停文字上时创建动画下划线效果。


```
<p class="hover-underline-animation">Hover this text to see the effect!</p>
```

```
.hover-underline-animation {
  display: inline-block;
  position: relative;
  color: #0087ca;
}
.hover-underline-animation::after {
  content: '';
  position: absolute;
  width: 100%;
  transform: scaleX(0);
  height: 2px;
  bottom: 0;
  left: 0;
  background-color: #0087ca;
  transform-origin: bottom right;
  transition: transform 0.25s ease-out;
}
.hover-underline-animation:hover::after {
  transform: scaleX(1);
  transform-origin: bottom left;
}
```
说明：

1. 首先通过伪元素给文字创建一个下划线，此时没有任何动画效果。
2. 然后使用`transform：scaleX(0)`将伪元素缩放为0，因此它没有宽度并且不可见。
3. 当鼠标悬浮时，使用`transform: scaleX(1)`将下划线缩放为1，此时宽度为文字宽度并可见。
4. 如果不设置`transform-origin`会发现动画默认是从中心开始向两边扩散，因为这是默认值。所以当悬浮时，设置`transform-origin: bottom left`，动画会从底层左侧开始，直至缩放为1；当鼠标离开文本时，设置`transform-origin: bottom right`，动画会从底层右侧开始，直至缩放为0。

效果如下：

![](https://ws1.sinaimg.cn/large/006tKfTcly1fss429nr0yg309702sdgi.gif)

