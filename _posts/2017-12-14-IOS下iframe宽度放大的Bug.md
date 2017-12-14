---
layout:     post                   
title:      IOS下iframe宽度放大的Bug            
subtitle:   IOS真的好坑
date:       2017-12-14
author:     chuck
header-img: img/home-bg-iframe.jpg
catalog: true                      
tags:                               
    - JavaScript
    - HTML5
    - Iframe
    - IOS
---

最近项目中有涉及到富文本的功能，既然有了富文本，那就不可避免的涉及到添加超链接，所以就要对超链接进行校验，如果不是正确的绝对路径的超链接，我们前端就不能让用户保存，因为项目是单页面应用，如果超链接使用的是相对路径，也就是不加超文本协议，这样在app访问超链接就会跳转到项目中一个不存在的页面，那这样的体验肯定是不行的。
所以就需要在pc端保存富文本内容前就对超链接进行校验，刚开始的思路是这样的：

1、获取富文本所生成的字符串`this.content`。

2、使用获取字符串中`<a></a>`标签的正则进行匹配。

```
const reg = /<a\s(\s*\w*?=".+?")*(\s*href=".+?")(\s*\w*?=".+?")*\s*>[\s\S]*?<\/a>/g;
let num = this.content.match(reg); //返回的是匹配成功的数组，如果为空，则返回null
```
3、使用匹配获取超链接的正则进行匹配。

```
const http  = new RegExp("(http[s]{0,1})://[a-zA-Z0-9\\.\\-]+\\.([a-zA-Z]{2,4})(:\\d+)?(/[a-zA-Z0-9\\.\\-~!@#$%^&amp;*+?:_/=<>]*)?", "gi");  //第二种创建正则的写法
let httpNum = this.content.match(http);
```
4、然后对比`num`和`httpNum`数组的长度，如果相同就意味着每个超链接都是正确的。

这样写完代码后觉得很完美，但是逃不过测试的检验，果不其然提了一个bug，那就是如果`<a></a>`标签的内容中也存在超链接，那么这样判断就彻底不管用了。那好吧，只能接着改正则了。

经过前端的讨论我们决定在匹配超链接的正则前面加上匹配`href="`的正则，但还是有一个bug，就是内容里的超链接前面也有`href="`,Em....，不管了，先上一版再说。等以后有更好的方案了再来更新。

现在超链接的问题是“完美”解决了，而且app跳转超链接的做法是前端再写一个页面，页面上就是`iframe`，里面的`src`属性就是跳转的超链接，在展示富文本的页面加上`click`事件阻止默认的跳转事件。

```
openImageProxy: function (event) {
// 判断点击的是否是A标签(nodeName属性都是大写)
if (event.target.nodeName === 'A') {
    //阻止默认的事件
    event.preventDefault();
    let fullPath = this.$route.fullPath;
    //获取超链接地址
    let param = event.target.href;
    //获取A标签的内容，方便在新页面设置title
    let desc = event.target.innerText;
    //跳转到新页面
    this.$router.push({path:'linkTo',query:{param,desc,fullPath}});
    }
}
```

正当我以为这个功能改好的时候，测试又来一个bug，说这个`iframe`页面在ios系统中变得很大，我一看还真是，特别的丑，这就不能忍了赶紧改。

经过我的调试，发现只有ios有这个问题，Android的表现很完美。于是我就上网搜到了一个解决方案：

**产生原因**：iOS下的safari是按照iframe里面页面元素的全尺寸来调整iframe的大小的。


在iframe上设置以下样式：

```
iframe {
  width: 1px;
  min-width: 100%;
  *width: 100%;
}
```
同时设置`scrolling`属性为’no’。


```
<iframe :src="temp" class="iframe-temp" scrolling="no"></iframe>
```
然后拿起苹果手机打开一看，很完美。拿起安卓手机打开一看，无法滚动了，Em....
既然`scrolling`属性为’no’是特意针对ios设置的，那干脆就判断一下客户端类型来分别展示吧！如果是ios，那就设置`scrolling`属性，如果是Android，那就不需要。


```
window.navigator.userAgent.toLowerCase().indexOf('iphone');
//通过检测客户端类型中是否有'iphone'来判断操作系统。
//然后再通过Vue的v-if指令来渲染不同的iframe
```
以上就是关于遇到的iframe的bug并解决它的方案。里面用到了`match()`匹配正则的方法，那么下次就来记录一下所有的涉及到正则的方法。

参考资料：[iOS的iframe宽度bug](http://60kmlh.ink/2017/09/30/iOS%E7%9A%84iframe%E5%AE%BD%E5%BA%A6bug/)


