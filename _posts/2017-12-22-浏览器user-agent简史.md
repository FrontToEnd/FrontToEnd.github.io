---
layout:     post                   
title:      浏览器user-agent简史      
subtitle:   浏览器贵圈真乱
date:       2017-12-22
author:     chuck
header-img: img/home-bg-basic.jpg
catalog: true                      
tags:                               
    - 浏览器
---




很多人都知道浏览器的 `user-agent` 字符串，服务器端通过这个字符串进行客户端的浏览器、操作系统、加密等级、浏览器语言、渲染引擎和版本信息的识别。从 1993 年 NCSA 发布首款浏览器 Mosaic 以来，这个字符串经历了纷繁复杂的变化，以下是 `user-agent` 字符串的演变简史。

1993 年，NCSA 公司发布了首款浏览器 Mosaic，使用“**NCSA_Mosaic/2.0(Windows 3.1)**”作为 user-agent 字符串。

后来 Mozilla 浏览器问世，“Mozilla”意即“Mosaic Killer”，意思是要成为 Mosaic 浏览器的终结者。Mosaic 自然不太高兴，于是 Mozilla 就把名字改成 Netscape，并把 user-agent 改为“**Mozilla/1.0(Win 3.1)**”。

那个时候 `frame` 标签很流行，Netscape 支持 `frame`，但 Mosaic 不支持，于是出现了“用户代理嗅探”，如果字符串中带有“Mozilla”字样，服务器就发送 frame 给浏览器，否则就不发送。Netscape 还开起了微软的玩笑，笑称 Windows 是“缺少调试的设备驱动器”，微软很生气，于是开发了自己的浏览器 Internet Explorer，希望能够成为“Netscape Killer”。

IE 也支持 `frame`，但它的 `user-agent` 字符串中没有包含“Mozilla”，所以服务器不会发送 `frame` 给 IE。微软没有太大耐心，他们就想着怎样才能尽快让服务器也能向 IE 发送 `frame`，于是他们把 IE 声明为与“Mozilla 兼容”的浏览器，并使用了“**Mozilla/1.22(compatible;MSIE 2.0;Windows 95)**”的字符串。这样，IE 就能够收到 frame，微软现在开心了，但其他人却一头雾水。微软将 IE 捆绑在 Windows 上销售，销量比 Netscape 要好得多，于是第一场浏览器大战爆发了。

Netscape 在这场大战中落败，不过经过了一场浴火重生，变成了 Mozilla。Mozilla 开发了 Gecko 渲染引擎，并把 `user-agent` 改为“**Mozilla/5.0 (Windows; U; Windows NT 5.0; en-US; rv:1.1) Gecko/20020826**”。

再后来，Mozilla 变成 Firefox，并把 `user-agent` 改为“**Mozilla/5.0 (Windows; U; Windows NT 5.1; sv-SE; rv:1.7.5) Gecko/20041108 Firefox/1.0**”。Gecko 开始多元化发展，其他浏览器也开始使用它的代码，并使用了“**Mozilla/5.0 (Macintosh; U; PPC Mac OS X Mach-O; en-US; rv:1.7.2) Gecko/20040825 Comino/0.8.1**”这样的字符串，还有一个是“**Mozilla/5.0 (Windows; U; Windows NT 5.1; de; rv:1.8.1.8) Gecko/20071008 SeaMonkey/1.0**”，它们都想把自己伪装成 Mozilla。

Gecko 越来越好，但 IE 没有，于是出现了新一轮的用户代理嗅探，声称自己使用了 Gecko 内核的浏览器总能收到更好的 HTML 代码，但其他浏览器就没这么好的待遇。

Linux 的拥护者感到很伤心，因为他们开发了 Konqueror，它使用了 KHTML 引擎，他们认为它比 Gecko 好。可惜的是，它不是 Gecko，所以收不到好的页面。于是 Konqueror 开始佯装“Gecko”，使用了“**Mozilla/5.0 (compatible; Konqueror/3.2; FreeBSD) (KHTML, like Gecko)**”作为 `user-agent` 字符串。

然后 Opera 也来凑热闹了，它宣称”我们应该让用户来决定使用哪个字符串“，于是 Opera 增加了一个菜单项，为用户提供多个选择：“**Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; en) Opera 9.51**”、“**Mozilla/5.0 (Windows NT 6.0; U; en; rv:1.81.) Gecko/20061208 Firefox/2.0.0 Opera 9.51**”、“**Opera/9.51 (Windows NT 5.1; U; en)**”。

苹果开发了 Safari 浏览器，以 KHTML 为基础，添加了很多新特性，并创建了一个分支，叫作 WebKit。为了能够获得 KHTML 页面，Safari 称自己为“**Mozilla/5.0 (Macintosh; U; PPC Mac OS X; de-de) AppleWebKit/85.7 (KHTML, like Gecko) Safari/85.5**”。

经历了惨败，IE 再次回归，这次它把字符串改为”**Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.0)**”，这样就能够接收到好的页面。

谷歌后来开发了 Chrome 浏览器，它使用了 WebKit，有点像 Safari。为了获得 Safari 那样的页面，它佯装自己是 Safari，于是使用了字符串“**Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US) AppleWebKit/525.13 (KHTML, like Gecko) Chrome/0.2.149.27 Safari/525.13**”。

`user-agent` 字符串变得越来越复杂，也越来越让人摸不着头脑，只因为各个浏览器在争相“佯装”对方。

转载自：**前端之巅**

原文链接：[浏览器user-agent简史](http://mp.weixin.qq.com/s/pMpKDXFm_tgWpU3geeUMFg)


