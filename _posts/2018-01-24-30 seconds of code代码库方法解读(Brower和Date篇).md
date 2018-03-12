---
layout:     post                   
title:      30 seconds of code代码库方法解读(Brower和Date篇)
subtitle:   学习使用ES6实现的代码片段
date:       2018-01-24
author:     chuck
header-img: img/home-bg-code.jpg
catalog: true                      
tags:                               
    - JavaScript
    - ES6
    - 源代码
---


### Browser

#### arrayToHtmlList


```
const arrayToHtmlList = (arr, listID) =>
  arr.map(item => (document.querySelector('#' + listID).innerHTML += `<li>${item}</li>`));
```
目的：将给定的数组元素转换为<li>标签并将其附加到给定id的列表中。

解析：用到了字符串模板(**\`\`**)与`document.querySelector`，通过遍历进行`<li>`标签的累加。个人感觉使用`document.createDocumentFragment()`会更好，使用片段累加后最后再使用`innerHTML`，这样就只操作了一遍DOM。


```
arrayToHtmlList(['item 1', 'item 2'], 'myListID');
```

#### bottomVisible


```
const bottomVisible = () =>
  document.documentElement.clientHeight + window.scrollY >=
  (document.documentElement.scrollHeight || document.documentElement.clientHeight);
```
目的：如果页面底部可见，则返回`true`，否则返回`false`。

解析：`document.documentElement.clientHeight`显示浏览器窗口的高度（不包括工具栏/滚动条）。`window.scrollY`返回文档在垂直方向已滚动的像素值。在chrome 63中运行该方法返回`0`。


```
bottomVisible(); // true
```

#### copyToClipboard


```
const copyToClipboard = str => {
  const el = document.createElement('textarea');
  el.value = str;
  el.setAttribute('readonly', '');
  el.style.position = 'absolute';
  el.style.left = '-9999px';
  document.body.appendChild(el);
  const selected =
    document.getSelection().rangeCount > 0 ? document.getSelection().getRangeAt(0) : false;
  el.select();
  document.execCommand('copy');
  document.body.removeChild(el);
  if (selected) {
    document.getSelection().removeAllRanges();
    document.getSelection().addRange(selected);
  }
};
```
目的：将一个字符串复制到剪贴板。 仅作为用户操作的结果（即在单击事件中使用）。

解析：该方法为高级方法，而且官网的复制代码段功能就是采用该方法。前面的很好理解，就是在页面的很左边创建一个标签，需要复制的值就是`textarea`的`value`。然后需要重点了解一下`document.getSelection()`。

`Selection`对象表示用户选择的文本范围或插入符号的当前位置。它代表页面中的文本选区，可能横跨多个元素。文本选区由用户拖拽鼠标经过文字而产生。[Selection](https://developer.mozilla.org/zh-CN/docs/Web/API/Selection)


```
copyToClipboard('Lorem ipsum'); // 'Lorem ipsum' copied to clipboard.
```

#### currentURL


```
const currentURL = () => window.location.href;
```
目的：获取当前的URL。


```
currentURL();  // https://30secondsofcode.org
```

#### detectDeviceType


```
const detectDeviceType = () =>
  /Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent)
    ? 'Mobile'
    : 'Desktop';
```

目的：检测网站是否在移动设备或台式机/笔记本电脑上打开。

解析：通过正则验证获取到的`navigator.userAgent`是否包含指定正则。如果包括，则表明是在移动设备上打开。


```
detectDeviceType(); // "Desktop"
```

#### elementIsVisibleInViewport


```
const elementIsVisibleInViewport = (el, partiallyVisible = false) => {
  const { top, left, bottom, right } = el.getBoundingClientRect();
  const { innerHeight, innerWidth } = window;
  return partiallyVisible
    ? ((top > 0 && top < innerHeight) || (bottom > 0 && bottom < innerHeight)) &&
        ((left > 0 && left < innerWidth) || (right > 0 && right < innerWidth))
    : top >= 0 && left >= 0 && bottom <= innerHeight && right <= innerWidth;
};
```
目的：如果指定的元素在视口中可见，则返回`true`，否则返回`false`。

解析：使用`Element.getBoundingClientRect()`和`window.inner（Width | Height）`值来确定给定元素是否在视口中可见。 省略第二个参数来确定元素是否完全可见，或者指定true以确定它是否部分可见。

`Element.getBoundingClientRect()`方法返回元素的大小及其相对于视口(左上角)的位置。使用解构来分别获取`top, left, bottom, right`。

表示部分可见的参数`partiallyVisible`默认为`false`，也就是说只有完全可见时，才会返回`true`。

`window.innerHeight`表示浏览器窗口的视口（viewport）高度（以像素为单位），如果存在水平滚动条，则包括它。参考下图：
![](https://ws1.sinaimg.cn/large/006tKfTcgy1fnjqbm07gej31400p075q.jpg)

所以如果需要完全可见，就必须满足`top >= 0 && left >= 0 && bottom <= innerHeight && right <= innerWidth`，这样才会完全展示在视口内。

如果只需要部分可见，就(相对于左上角原点)必须满足：上距离或下距离大于0，并且都小于`window.innerHeight`;同时左距离或右距离大于0，并且都小于`window.innerWidth`。

#### getScrollPosition


```
const getScrollPosition = (el = window) => ({
  x: el.pageXOffset !== undefined ? el.pageXOffset : el.scrollLeft,
  y: el.pageYOffset !== undefined ? el.pageYOffset : el.scrollTop
});
```
目的：返回当前页面的滚动位置。

解析：需要注意的是，如果在箭头函数返回一个对象时，必须在对象外包裹一个括号`()`，否则会认为是代码块。


```
getScrollPosition(); // {x: 0, y: 0}
```

#### getStyle


```
const getStyle = (el, ruleName) => getComputedStyle(el)[ruleName];
```

目的：返回指定元素的CSS规则的值。

解析：通过`getComputedStyle(el)`可以获取指定元素所有的CSS规则。然后通过`getComputedStyle(el)[ruleName]`获取指定规则的值。

需要注意的是，`getComputedStyle(el)`返回的所有CSS规则都是**驼峰命名**方式，但是`ruleName`依然可以采用中划线的方式进行调用。


```
getStyle(document.querySelector('body'), 'font-size'); // '16px'
```

#### hasClass


```
const hasClass = (el, className) => el.classList.contains(className);
```
目的：如果元素包含指定类则返回`true`，否则返回`false`。

解析：该方法主要采用了`classList`。有必要学习一下`classList`的用法。[Element.classList](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/classList)

`Element.classList` 是一个只读属性，返回一个元素的类属性的实时 [DOMTokenList](https://developer.mozilla.org/en-US/docs/Web/API/DOMTokenList)集合。可以理解为返回一个由当前元素包含的类组成的一个类数组。同时具有几个方法如下：


```
add( String [, String] )

添加指定的类值。如果这些类已经存在于元素的属性中，那么它们将被忽略。

remove( String [,String] )

删除指定的类值。

item ( Number )

按集合中的索引返回类值。

toggle ( String [, force] )

当只有一个参数时：切换 class value; 即如果类存在，则删除它并返回false，如果不存在，则添加它并返回true。
当存在第二个参数时：如果第二个参数的计算结果为true，则添加指定的类值，如果计算结果为false，则删除它。

contains( String )

检查元素的类属性中是否存在指定的类值。
```
我们这里要用到`contains(className)`方法，如果包含就返回`true`。


```
document.querySelector('.row').classList.contains('row');  // true
```

#### hide

```
const hide = (...el) => [...el].forEach(e => (e.style.display = 'none'));
```
目的：隐藏所有指定元素。

解析：使用展开运算符并且遍历数组将每个元素的`display`属性设置为`none`。


```
hide(...document.querySelectorAll('img'));
```

#### httpsRedirect

```
const httpsRedirect = () => {
  if (location.protocol !== 'https:') location.replace('https://' + location.href.split('//')[1]);
};
```
目的：如果当前页面是`http`协议，重定向到`https`协议。并且是替换当前`http`协议的页面，意味着浏览器回退无法回到`http`协议的页面。

解析：使用`location.replace()`将当前url替换为修改为`https`协议的url。

#### off

```
const off = (el, evt, fn, opts = false) => el.removeEventListener(evt, fn, opts);
```
目的：从元素上移除监听事件。

解析：使用`removeEventListener()`来移除事件监听器。

```
document.body.addEventListener('click', fn);
off(document.body, 'click', fn); // no longer logs '!' upon clicking on the page
```

#### on

```
const on = (el, evt, fn, opts = {}) => {
  const delegatorFn = e => e.target.matches(opts.target) && fn.call(e.target, e);
  el.addEventListener(evt, opts.target ? delegatorFn : fn, opts.options || false);
  if (opts.target) return delegatorFn;
};
```

目的：将事件侦听器添加到可以使用事件委派的元素中。

解析：使用`addEventListener()`进行绑定事件。

```
on(document.body, 'click', fn, { target: 'p' });
```

#### redirect


```
const redirect = (url, asLink = true) =>
  asLink ? (window.location.href = url) : window.location.replace(url);
```
目的：重定向到指定URL中。

解析：默认重新链接到指定URL，如果指定第二参数为`false`，则是替换当前页面URL。(区别就是是否有浏览器历史记录)

```
redirect('https://google.com');
```

#### scrollToTop


```
const scrollToTop = () => {
  const c = document.documentElement.scrollTop || document.body.scrollTop;
  if (c > 0) {
    window.requestAnimationFrame(scrollToTop);
    window.scrollTo(0, c - c / 8);
  }
};
```

目的：平滑滚动到页面顶部。

解析：首先获取到距离顶部的实际距离。使用`window.requestAnimationFrame()`来动画滚动，回调函数的次数常是每秒60次。每次滚动7/8初始距离，直至滚动到最顶部。

#### setStyle

```
const setStyle = (el, ruleName, val) => (el.style[ruleName] = val);
```

目的：给指定元素设置CSS属性。


```
setStyle(document.querySelector('p'),'font-size','20px');
```

#### show

```
const show = (...el) => [...el].forEach(e => (e.style.display = ''));
```
目的：显示所有指定元素。

解析：遍历所有元素，并设置`e.style.display = ''`，目的是将指定元素的`display`属性设置为**默认值**，比如`div`元素的默认值是`block`。


```
show(...document.querySelectorAll('img'));
```

#### toggleClass

```
const toggleClass = (el, className) => el.classList.toggle(className);
```

目的：切换一个元素的类。可以理解为jquery的`toggle()`。

解析：使用到了在`hasClass`方法中提到过的`Element.classList`只读属性。其中包括`toggle()`方法。下面再重温一下：

当只有一个参数时：切换 class; 即如果类存在，则删除它并返回`false`，如果不存在，则添加它并返回`true`。

当存在第二个参数时：如果第二个参数的计算结果为true，则添加指定的类值，如果计算结果为false，则删除它。


```
toggleClass(document.querySelector('p.special'), 'special');

// 该例子会删除p元素的class---special
```

#### UUIDGeneratorBrowser

```
const UUIDGeneratorBrowser = () =>
  ([1e7] + -1e3 + -4e3 + -8e3 + -1e11).replace(/[018]/g, c =>
    (c ^ (crypto.getRandomValues(new Uint8Array(1))[0] & (15 >> (c / 4)))).toString(16)
  );
```
目的：在浏览器随机生成一个UUID。(表示实在看不懂原理)。

```
UUIDGeneratorBrowser();
// "35de0d7c-2ca0-40d9-82e1-fb3cbf67e64a"
```

### Date

#### formatDuration

```
const formatDuration = ms => {
  if (ms < 0) ms = -ms;
  const time = {
    day: Math.floor(ms / 86400000),
    hour: Math.floor(ms / 3600000) % 24,
    minute: Math.floor(ms / 60000) % 60,
    second: Math.floor(ms / 1000) % 60,
    millisecond: Math.floor(ms) % 1000
  };
  return Object.entries(time)
    .filter(val => val[1] !== 0)
    .map(val => val[1] + ' ' + (val[1] !== 1 ? val[0] + 's' : val[0]))
    .join(', ');
};
```
目的：返回给定毫秒的可读格式。

解析：首先将传入的毫秒转化成由`day、hour、minute、second和millisecond`组成的对象。然后通过`Object.entries()`和`filter()`过滤掉`time`对象值为空的键值对。其中`Object.entries()`会返回一个二维数组，每一项对应的数组由键值对组成，比如`[["day",1],["hour",3]]`。最后通过`join()`将数组转换为字符串。

```
formatDuration(+new Date());

// "17555 days, 2 hours, 18 minutes, 18 seconds, 150 milliseconds"
```

#### tomorrow

```
const tomorrow = () => {
  let t = new Date();
  t.setDate(t.getDate() + 1);
  return `${t.getFullYear()}-${String(t.getMonth() + 1).padStart(2, '0')}-${String(
    t.getDate()
  ).padStart(2, '0')}`;
};
```
目的：返回一个表示明天日期的字符串。格式为yyyy-mm-dd.


```
tomorrow(); // "2018-01-25" 当前日期是2018-01-24

```

