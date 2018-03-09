---
layout:     post                   
title:      JavaScript高级程序设计读书笔记(五)         
subtitle:   重温红皮书精髓--面向对象的程序设计
date:       2018-03-09
author:     chuck
header-img: img/home-bg-basic.jpg
catalog: true                      
tags:                               
    - JavaScript
    - 用户代理
    - 浏览器
---

## 第九章 客户端检测


-------

### 能力检测

在可能的情况下，尽量使用`typeof`进行能力检测。

```

//这样检测
function isSortable(object) {
    return typeof object.sort == "function";
}

//不要这样
function isSortable(object) {
    return !!object.sort;
}
```

### 怪癖检测

怪癖检测的目标是识别浏览器的特殊行为。不过书中的例子都是用来检测很古老的浏览器，对于快速更迭的前端已经没有什么参考意义了。下面的才是重点，我们来看一下。

### 用户代理检测

用户代理检测通过检测用户代理字符串来确定实际使用的浏览器。该字符串可以通过JS的`navigator.userAgent`属性访问。而用户代理字符串的历史可以说是非常乱了。简单概括就是大家都想伪装成`Mozilla`浏览器，含义是初代浏览器的杀手(Mosaic Killer)。到最后演变成很长很长的一串。下面是最新的Chrome、FireFox和Safari浏览器的用户代理字符串：

```
navigator.userAgent;

// Chrome 64
"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.186 Safari/537.36"

// Safari
"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_3) AppleWebKit/604.5.6 (KHTML, like Gecko) Version/11.0.3 Safari/604.5.6"

//FireFox
"Mozilla/5.0 (Macintosh; Intel Mac OS X 10.13; rv:58.0) Gecko/20100101 Firefox/58.0"

//Opera 跟Chrome实在是太相似了！
"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/53.0.2785.21 Safari/537.36 MMS/1.0.2531.0"
```

#### 识别呈现引擎

首先我们需要识别Opera，因为它的用户代理字符串可能完全模仿其他浏览器。可以通过检测`window.opera`对象来识别`Opera`。这个对象用以保存与浏览器相关的标识信息以及与浏览器直接交互。

然后应该检测的呈现引擎是WebKit。因为WebKit的用户代理字符串中包含"`Gecko`"和"`KHTML`"子字符串，刚开始就检测可能会出错。这里有个字符串是WebKit独一无二的，就是"`AppleWebKit`"。因为可以通过正则检测来判断引擎是否为`WebKit`。

接下来要检测的呈现引擎是`KHTML`。同样的，`KHTML`的用户代理字符串中也包含"Gecko"，所以要先检测`KHTML`。需要注意的是，KHTML的版本号与后继的标记之间有一个空格，所以可以根据这个正则来匹配。

最后就来检测`Gecko`。在该引擎下的用户代理字符串中，`Gecko`的版本号不会出现在字符串"Gecko"的后面，而是会出现在字符串"rv:"的后面。所以可以根据这个特性来匹配。

```
        if(window.opera){  
            browsers.ver= engines.ver=window.opera.version();  
            browsers.opera=engines.opera=parseFloat(engines.ver);  
        }else if(/AppleWebKit\/(\S+)/.test(UA)){//说明是webkit引擎,使用webkit引擎的有chrome，safari，和opera  
  
            engines.ver=RegExp["$1"];  
            engines.webkit=parseFloat(engines.ver);  
            if(/OPR\/(\S+)/.test(UA)){                    
                browsers.ver=RegExp["$1"];                    
                browsers.opera=parseFloat(browsers.ver);  
            }  
            else if(/Version\/(\S+)/.test(UA)){  
                browsers.ver=RegExp["$1"];  
                browsers.safari=parseFloat(browsers.ver);  
            }else if(/Chrome\/(\S+)/.test(UA)){  
                browsers.ver=RegExp["$1"];  
                browsers.chrome=parseFloat(browsers.ver);  
            }  
  
        }else if(/KHTML\/(\S+)/.test(UA)||/Konqueror\/([^;]+)/.test(UA)){  
          browsers.ver=engines.ver=RegExp["$1"];  
          browsers.konqueror=engines.khtml=parseFloat(engines.ver);  
  
        }else if(/rv\:([^\)]+)\) Gecko\/\d{8}/.test(UA)){  
            engines.ver=RegExp["$1"];  
            engines.gecko=parseFloat(engines.ver);  
            if(/Firefox\/(\S+)/.test(UA)){  
                browsers.ver=RegExp["$1"];  
                browsers.firefox=parseFloat(browsers.ver);  
            }  
        }  
        else if(/Trident\/([^;]+)/.test(UA)){  
            browsers.ver=engines.ver=parseFloat(RegExp["$1"])+4.0;  
            engines.ie=browsers.ie=engines.ver;   
        }else if(/MSIE ([^;]+)/.test(UA)){  
                browsers.ver=engines.ver=RegExp["$1"];  
                engines.ie=browsers.ie=parseFloat(engines.ver);  
        }  
```

#### 识别浏览器

用正则来匹配每种浏览器特有的字符串。

```
   var userAgent = navigator.userAgent; //取得浏览器的userAgent字符串 
   var isOpera = userAgent.indexOf("Opera") > -1; //判断是否Opera浏览器 
   // var isIE = userAgent.indexOf("compatible") > -1 && userAgent.indexOf("MSIE") > -1 && !isOpera; //判断是否IE浏览器 
   var isIE=window.ActiveXObject || "ActiveXObject" in window
   // var isEdge = userAgent.indexOf("Windows NT 6.1; Trident/7.0;") > -1 && !isIE; //判断是否IE的Edge浏览器 
   var isEdge = userAgent.indexOf("Edge") > -1; //判断是否IE的Edge浏览器
   var isFF = userAgent.indexOf("Firefox") > -1; //判断是否Firefox浏览器 
   var isSafari = userAgent.indexOf("Safari") > -1 && userAgent.indexOf("Chrome") == -1; //判断是否Safari浏览器 
   var isChrome = userAgent.indexOf("Chrome") > -1 && userAgent.indexOf("Safari") > -1&&!isEdge; //判断Chrome浏览器 
  
   if (isIE)  
   { 
      var reIE = new RegExp("MSIE (\\d+\\.\\d+);"); 
      reIE.test(userAgent); 
      var fIEVersion = parseFloat(RegExp["$1"]); 
      if(userAgent.indexOf('MSIE 6.0')!=-1){
        return "IE6";
      }else if(fIEVersion == 7) 
        { return "IE7";} 
      else if(fIEVersion == 8) 
        { return "IE8";} 
      else if(fIEVersion == 9) 
        { return "IE9";} 
      else if(fIEVersion == 10) 
        { return "IE10";} 
      else if(userAgent.toLowerCase().match(/rv:([\d.]+)\) like gecko/)){ 
            return "IE11";
        } 
      else
        { return "0"}//IE版本过低
    }//isIE end
```

#### 识别平台

目前三大主流平台是Windows、Mac和Unix。可以根据`navigator.platform`来检测平台。可能的值包括`Wind32、Win64、MacPPC、MacIntel、X11、Linux i686`。
在我的MBA上的信息就是`MacIntel`。

```
navigator.platform;  // "MacIntel"
```

所以可以通过`indexOf()`方法来具体判断是哪个平台。

#### 识别移动设备

通过检测字符串"iPhone"、"iPod"、"iPad"就可以区分出IOS设备。

检查系统是否为IOS，可以通过检查系统是不是Mac OS、并且字符串中是否存在"Mobile"。

检测`Android`操作系统也简单，直接匹配字符串"Android"并取得紧随其后的版本号。

由于诺基亚和WP手机已经几乎没有份额，所以这里也不做记录了。


