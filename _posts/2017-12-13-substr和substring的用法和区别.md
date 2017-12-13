---
layout:     post                   
title:      substr和substring的用法和区别            
subtitle:   容易混淆的两个方法
date:       2017-12-13
author:     chuck
header-img: img/home-bg-blog.jpg
catalog: true                      
tags:                               
    - JavaScript
---


`substr`和`substring`都是JS截取字符串函数，但是总让人傻傻分不清楚，而且两者的用法很相近，下面就分析一下两者的用法：

### 一、substr 方法

返回一个从指定位置开始的指定长度的子字符串。 

```
string.substr(start [, length ])
```

注意: length可选项。如 length 为 0 或负数，将返回一个空字符串。如果没有指定该参数，则子字符串到 string 的最后。

### 二、substring 方法

返回位于`String` 对象中指定位置的子字符串。

```
string.substring(start, end)
```

注意:
`substring`方法将返回一个包含从 start 到最后（不包含 end ）的子字符串的字符串。

我是这么记的：[start,end)——左闭右开，数学中的区间记法

### 三、示例代码


```
var str = "I love JS!";// 有一个str字符串，如想获取JS子字符串，用两种方法如何实现。 str.substr(7, 2); // 获取子字符串。
str.substring(7, 9); // 获取子字符串。
```
**结果**:  JS

**区别**:第二参数，substr第二个参数是获取子字符串的长度，substring第二个参数是获取子字符串的结束位置。

### 四、注意事项

substr和substring两个函数截取带有空格的字符串后的长度是每个空格算一个字符长度。例如：

```
let a = 'I am male!';
```

`a.substring(0, 5).length`的值是5，而不是4，但`a.substring(0, 5);`的值却是 'I am '，这样在做alert("I am" == a.substring(0, 5));的时候就是false了，alert("I am" == a.substring(0, 4));才是true。



