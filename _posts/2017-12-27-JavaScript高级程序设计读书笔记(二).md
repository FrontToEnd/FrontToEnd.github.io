---
layout:     post                   
title:      JavaScript高级程序设计读书笔记(二)         
subtitle:   重温红皮书精髓--引用类型
date:       2017-12-22
author:     chuck
header-img: img/home-bg-basic.jpg
catalog: true                      
tags:                               
    - JavaScript
    - 基本概念
    - 引用类型
---

## 第四章 变量、作用域和内存问题

### 1.基本类型和引用类型的值

对于引用类型的值，我们可以为其添加属性和方法，也可以改变和删除。但是，我们不能给基本类型的值添加属性，尽管不会导致任何错误。所以有一个很容易理解的点就是，所有关于字符串的方法都只是返回一个新的字符串，而不是修改原字符串。

从一个变量向另一个变量复制基本类型和引用类型时，也有不同之处。复制基本类型时，两个变量是独立的，不会相互影响。但是复制引用类型时，实际上是一个指针。两个变量引用的是同一个对象。所以会相互影响。

这里就涉及到一个概念：深复制和浅复制。[这里](https://juejin.im/post/59ac1c4ef265da248e75892b)有一篇文章，讲得很好。

ES中所有函数的参数是按值传递的。在向参数传递基本类型的值时，被传递的值会被复制给命名参数。传递引用类型的值时，会把这个值在内存中的地址复制给一个局部变量，因此这个局部变量的变化会反映在函数的外部。

#### 类型检测

检测基本类型的时候，我们可以使用`typeof`操作符完成，但是检测引用类型的值时，`typeof`就没什么用了。使用`instanceof`操作符可以知道是什么类型的对象。如果是给定引用类型，就返回true。

> 所有引用类型的值都是`Object`的实例。因此，在检测一个引用类型值和Object构造函数时，`instanceof`操作符始终返回`true`。

### 执行环境及作用域

所有变量都存在于一个执行环境(作用域)中，这个执行环境决定了变量的生命周期。

* 执行环境有全局执行环境和函数执行环境之分。
* 每次进入一个新执行环境，都会创构建一个用于搜索变量和函数的作用域链。
* 函数的局部环境不仅有权访问函数作用域中的变量，而且有权访问父环境，甚至全局环境。
* 变量的执行环境有助于确定应该何时释放内存。
* 解除变量的引用不仅有助于消除循环引用现象，而且对垃圾收集也有好处。应该及时解除不使用的全局对象。

## 第五章 引用类型


-------

### Object类型

创建Object实例包括构造函数和对象字面量表示法。

访问对象属性时使用的一般是点表示法，但也可以使用方括号表示法来访问。方括号的优点在于**可以通过变量来访问属性**。很多的JS库都是通过方括号来访问属性的。

```
var obj = {
    name:"chuck",
    age:22,
    0:1
}
obj.name; // "chuck"
obj['name']; // "chuck"
obj.0; // Uncaught SyntaxError: Unexpected number
obj[0]; // 1
```

### Array类型

给构造函数传递数量，就会自动变成length属性的值。

```
var arr = new Array(20); // new可以省略
arr.length; // 20
```
数组的length属性可读可写。可以从数组的末尾移除或者添加新项。

```
var arr = [1,2,3];
arr.length = 2;
arr[2]; // undefined
arr.length = 4;
arr[99]; // undefined
// 移除最后一项或新增项都会是undefined
值
arr.length; // 100
// 当把一个值放在超出数组大小的位置时，就会重新计算长度
```

#### 检测数组

可以使用ES5新增的`Array.isArray()`来检测。

```
let arr = [];
Array.isArray(arr); // true
```
著名的underscore库也是根据这个方法来判断是否为数组的，当然也处理了一下兼容的情况：

```
var nativeIsArray = Array.isArray;
_.isArray = nativeIsArray || function(obj) {
    return Object.prototype.toString.call(obj) === '[object Array]';
  };
```

#### 转换方法

调用数组的`toString()`方法会返回由数组中每个值的字符串形式拼接而成的以逗号分隔的字符串。调用`valueOf()`返回的还是数组。

```
var round = ['width','height'];
rounds.toString(); // "width,height"
rounds.valueOf(); // ["width","height"]
```
数组继承的`toString()`方法默认情况下会以逗号分隔。如果使用`join()`方法，可以使用指定的分隔符。**如果不传参数，或者传入`undefined`,则使用逗号作为分隔符**。

```
var round = ['width','height'];
round.join("||"); // "width||height"
```
如果数组的某一项的值是`null`或者`undefined`,那么在`join()、toString()、valueOf()`方法返回的结果中以空字符串表示。

#### 数组的几个方法

关于数组的方法有很多，并且总是会记混掉，所以这块的东西打算单独写一篇文章，方便以后查阅。

### 3.Date类型

ES中的Date类型是在早起Java中的`java.util.Date`类基础上构建。所以，`Date`类型使用自UTC **1970年1月1日零时开始经过的毫秒数来保存日期**。

如果直接将表示日期的字符串传递给Date构造函数，会在**后台调用`Date.parse()`**。其中，`Date.parse()`方法接收一个表示日期的字符串参数，尝试根据这个字符串返回相应日期的毫秒数。但是标准并没有定义应该支持哪种日期格式，所以该方法会因为传入一些奇怪的字符串而返回一些奇怪的东西。如果传入`Date.parse()`方法的字符串不能表示日期，那么会返回`NaN`。

`Date.UTC()`方法也会返回表示日期的毫秒数，`Date.UTC()`的参数分别是年份、基于0的月份(0~11)、月中的哪一天(1~31)、小时数(0~23)、分钟、秒和毫秒数。只有前两个参数是必需的。如果没有提供天数，假设为1，其余统统为0。

使用`Date.now()`方法或者`+new Date()`都可以获取调用方法时的**毫秒数**。

```
Date.now(); // 1514340790147
+new Date(); // 1514340790147
```
#### 继承的方法

`toString()`方法通常返回带有时区信息的日期。`valueOf()`方法返回的是毫秒数。

```
new Date(); // Wed Dec 27 2017 10:14:23 GMT+0800 (CST)
new Date().toString(); // "Wed Dec 27 2017 10:14:27 GMT+0800 (CST)"
new Date().valueOf(); // 1514340876459
```
日期还有很多其他的方法，这里就不赘述了，最常用的就是通过时间戳来获取当前具体日期来格式化时间，看个例子:

```
export function formatDate(date, fmt) {
	date = new Date(date);
    if (/(y+)/.test(fmt)) {
        fmt = fmt.replace(RegExp.$1, (date.getFullYear() + '').substr(4 - RegExp.$1.length));
    }
    let o = {
        'M+': date.getMonth() + 1, // 0~11
        'd+': date.getDate(), // 1~31
        'h+': date.getHours(), // 0~23
        'm+': date.getMinutes(), // 0~59
        's+': date.getSeconds() // 0~59
    };
    for (let k in o) {
        if (new RegExp(`(${k})`).test(fmt)) {
            let str = o[k] + '';
            fmt = fmt.replace(RegExp.$1, (RegExp.$1.length === 1) ? str : padLeftZero(str));
        }
    }
    return fmt;
};

function padLeftZero(str) {
    return ('00' + str).substr(str.length);
}
```

### 4.RegExp 类型

创建一个正则表达式可以使用字面量形式或者构造函数。

```
var pat = /JavaScript/gi;
var pat1 = new RegExp("JavaScript","gi"); //两个参数都需要是字符串，第二个参数可选。对于某些特殊字符需要转义
```
正则的匹配模式支持以下3个标志：

* g:表示全局模式，模式将被应用于所有字符串。
* i:表示不区分大小写模式。
* m:表示多行模式，在到达一行文本末尾时还会继续查找下一行。

#### RegExp实例属性

每个实例都具有下列属性，但是没什么用处，因为都包含在模式声明中。

* **global**:布尔值，表示是否设置了`g`标志。
* **ignoreCase**:布尔值，表示是否设置了`i`标志。
* **lastIndex**：整数，表示开始搜索下一个匹配项的字符位置，从0算起。
* **multiline**：布尔值，表示是否设置了     `m`标志。
* **source**：正则表达式的字符串表示。保存的是规范形式的字符串，即字面量形式所用的字符串。


```
var pattern = /^JavaScript$/gi;
pattern.global; // true
pattern.source; // "^JavaScript$"
pattern.lastIndex; // 0
```

#### RegExp实例方法

包括`exec()`和`test()`,我的[这篇文章](https://qukun.com.cn/2017/12/15/%E6%9C%89%E5%85%B3%E6%AD%A3%E5%88%99%E7%9A%84%E6%96%B9%E6%B3%95/)已经做了总结，这里不再赘述。

继承的`toString()`方法会返回正则表达式的字面量形式的字符串，与创建方法无关。`valueOf()`方法返回正则表达式本身。

```
var pat = new RegExp("^JavaScript$","gi");
pat.toString(); // "/^JavaScript$/gi"
pat.valueOf(); // /^JavaScript$/gi
```

### 5.Function 类型

函数实际上是对象，因此函数名实际上也是一个指向函数对象的指针，不会与某个函数绑定。因此如果有声明的重名的函数，则后来的会覆盖以前的引用函数的变量(函数名)。

**函数声明与函数表达式的区别**：解析器会率先读取函数声明，并使其在执行任何代码之前可用；而函数表达式必须等到解析器执行到所在代码行才会被解析执行。

跟变量提升类似，函数声明也具有函数声明提升的过程，解析器会在解析阶段将函数声明添加到执行环境中，并放到顶部。但是函数表达式并不会。

#### 函数内部属性

`arguments`的主要用途是保存函数参数，这个对象还有一个`callee`属性，该属性是一个指针，指向拥有这个`arguments`对象的函数。

`caller`是函数对象的属性。这个属性白村这调用当前函数的函数的引用。如果是全局作用域调用当前函数，则`caller`的值为`null`。

需要注意的是，当函数在严格模式下运行时，访问`arguments.callee`会报错。ES5还定义了`arguments.caller`属性，在严格模式下也会报错，非严格模式下值始终是`undefined`。
严格模式还有一个限制：不能为函数的`caller`属性赋值，否则会报错。

#### 函数属性和方法

每个函数都包含两个属性：`length`和`prototype`。`length`属性表示函数希望接收的命名参数的个数。在ES5中，`prototype`属性不可枚举。

每个函数都包含两个非继承而来的方法：`apply()`和`call()`。用途是在特定作用域中调用函数，就是用来设置函数体内this对象的值。我们先来看看`apply()`。

`apply()`方法接收两个参数：一个是在其中运行函数的作用域，另一个是**参数数组**。第二个参数可以是Array的实例，也可以是`arguments`对象。

```
function sum (num1,num2) {
    return num1 + num2;
}
function call1(num1,num2) {
    return sum.apply(this,arguments);
}
function call2(num1,num2) {
    return sum.apply(this,[num1,num2]);
}

call1(10,10); // 20
call2(10,10); // 20
```

`call()`与`apply()`的区别在于接收参数的方式不同。变化就是`call()`将其余参数直接传递给函数，而不是使用数组形式。

使用`call()`与`apply()`来扩充作用域的好处就是，对象不需要与方法有任何耦合关系。

ES5还定义了一个方法`bind()`。这个方法会创建一个函数的实例，其this值会被绑定到传给`bind()`函数的值。

函数继承的`toString()`和`valueOf()`方法都返回函数的代码。

### 6.基本包装类型

为了便于操作基本类型值，ES还提供了3个特殊的引用类型：`Boolean`、`Number`和`String`。每当读取一个基本类型值的时候，后台就会创建一个对应的基本包装类型的对象，从而能够调用一些方法。

```
new Boolean(true);
new Number(26);
new String('26'); 
```

比如截取字符串的方法`substr()`和`substring()`可以直接通过字符串进行调用，按理来说，基本类型不是对象，所以不应该有方法，但确实有。那是因为后台自动完成了一系列的处理(同样适用于Boolean和Number类型)。

1. 创建String类型的一个实例；
2. 在实例上调用指定的方法；
3. 销毁这个实例。

也可以通过下列代码进行理解(虽然实际并不是这么操作):

```
var s1 = new String("chuck");
var s2 = s1.substring(2);
s1 = null;
```

> **引用类型与基本包装类型的主要区别是对象的生存期**。使用new操作符创建的引用类型的实例，在执行流离开当前作用域之前一直保存在内存中。但是，***自动创建的基本包装类型的对象，则只存在于一行代码的执行瞬间，然后立即被销毁***。这意味着我们不能在运行时为基本类型值添加属性和方法（因为自动创建的对象已销毁）。

对基本包装类型的实例调用`typeof`会返回"object"，而且所有基本包装类型的对象都会被转换为布尔值`true`。

需要注意的是，使用`new`调用基本包装类型的构造函数，与直接调用同名的**转型函数**时不一样的。

```
typeof Number('26'); // "number"
typeof new Number('26'); // "object"
(new Object('26')) instanceof String; // true,说明Object构造函数会根据传入值类型返回相应基本包装类型的实例
```

这样创建的对象会在某些地方产生歧义，最主要的区别就是`typeof`操作符返回的字符串。而且会重写`toString()和valueOf()`，所以最好不要主动使用(Number和Boolean类型)。下面主要看下String类型的一些比较有用的方法。

#### String类型

其继承的`toString()和valueOf()`都返回对象所表示的基本字符串值。String类型的每个实例都有一个`length`属性，表示字符串中包含多个字符。

1.字符方法

`charAt()`和`charCodeAt()`用于访问字符串中特定字符。两个方法都接收一个参数，基于0的位置。其中，`charAt()`方法以单字符字符串的形式返回给定位置的那么字符；`charCodeAt()`返回的是**字符编码**。

也可以使用方括号加数字索引来访问。


```
var str = "hello world";
str.charAt(1); // "e"
str.charCodeAt(1); // 101
str[1]; // "e"
```

2.字符串操作方法

首先是`concat()`。用于将一个或多个字符串拼接起来，返回拼接到的新字符串。也就是说，可以接受多个参数，来拼接任意多个字符串。没错，数组也有一个类似的方法，用于拼接数组，不传数组还可以浅复制数组，这是后话。但是并不怎么使用`concat()`，因为我们完全可以使用`+`操作符来拼接字符串。而且，ES6中，我们还可以通过字符模板来拼接字符串，更加的方便。

```
'chuck'.concat(' is',' tall'); // "chuck is tall"
'chuck' + ' is' + ' tall'; // "chuck is tall"
// ES6语法
const name = 'chuck ';
const height = ' tall';
`${name}is${height}`; // "chuck is tall"
```

然后是`slice()`。参数为一个或两个。
 
**beginSlice**

从该索引（以 0 为基数）处开始提取原字符串中的字符。如果值为负数，会被当做 sourceLength + beginSlice 看待，这里的sourceLength 是字符串的长度 (例如， 如果beginSlice 是 -3 则看作是: sourceLength - 3)

**endSlice**

可选。在该索引（以 0 为基数）处结束提取字符串。如果省略该参数，slice会一直提取到字符串末尾。如果该参数为负数，则被看作是 sourceLength + endSlice，这里的 sourceLength 就是字符串的长度(例如，如果 endSlice 是 -3，则是, sourceLength - 3)。

`slice()` 不修改原字符串，只会返回一个包含了原字符串中部分字符的新字符串。`slice()` 提取的新字符串包括`beginSlice`但不包括 `endSlice`。也就是区间上的左闭右开。举个[例子](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/slice)：


```
var str1 = 'The morning is upon us.';
var str2 = str1.slice(4, -2);

console.log(str2); // OUTPUT: morning is upon u
```

然后是`substr()`和`substring()`,这两个方法已在以前的文章分析过，文章在[这里](https://qukun.com.cn/2017/12/13/substr%E5%92%8Csubstring%E7%9A%84%E7%94%A8%E6%B3%95%E5%92%8C%E5%8C%BA%E5%88%AB/)，就不再赘述了。

有个需要注意的点是，当第二个参数是负数时，这三个方法有些不同。`substr()`和`substring()`会把第二个参数转换为0，而`slice()`会使用该负数与长度相加，得到第二参数。

3.字符串位置方法

`indexOf()`和`lastIndexOf()`可以从字符串中查找子字符串。两个方法都是从一个字符串中搜索给定的子字符串，然后**返回子字符串的位置**。如果没有，则返回-1。区别在于，一个从字符串开头搜索，一个从结尾搜索。

如果指定字符在一个字符串只出现一次，那么两个方法返回相同的位置值。两个方法都接收第二个可选参数。表示从字符串哪个位置开始搜索。

```
var str = 'chuck';
str.indexOf('c');  // 0
str.lastIndexOf('c'); // 3
```

4.trim()方法

该方法会创建一个字符串的副本，删除前置及后缀的所有空格，然后返回结果。此外，还有非标准的`trimLeft()`和`trimRight()`方法，分别用于删除字符串开头和末尾的空格。

5.字符串大小写转换方法

共有4个方法，包括:`toLowerCase()、toLocaleLowerCase()、toUpperCase()和toLocaleUpperCase()`。通过方法名可以很容易就看出来转换规则。只不过`toLocaleLowerCase()和toLocaleUpperCase()`是针对特定地区的实现。

6.字符串的模式匹配方法

一共有四个方法:`match()、search()、replace()和split()`，[这里](https://qukun.com.cn/2017/12/15/%E6%9C%89%E5%85%B3%E6%AD%A3%E5%88%99%E7%9A%84%E6%96%B9%E6%B3%95/)已经分析过，不再赘述。

7.localeCompare()方法

这个方法比较两个字符串，并返回下列值中的一个：

* 如果字符串在字母表中应该排在字符串参数之前，则返回一个负数，大多数情况是-1；
* 如果字符串等于字符串参数，则返回0；
* 如果字符串在字母表中应该排在字符串参数之后，则返回一个正数，大多数情况是1。

目前不太确定该方法是否和比较运算符的处理方法一样。

8.fromCharCode()方法

String构造函数本身还有一个静态方法:`fromCharCode()`。与实例方法`charCodeAt()`执行的相反的操作。

### 7.单体内置对象

ECMA-262对内置对象的定义是:"有ECMAScript实现提供的、不依赖与宿主环境的对象，在程序执行前就已存在。"所有代码执行以前，作用域中就已经存在两个内置对象：`Global`和`Math`。

#### Global对象

不属于任何其他对象的属性和方法，最终都是它的属性和方法。所有在全局作用域中定义的属性和函数，都是`Global`对象的属性。诸如`isNaN()、isFinite()、parseInt()和parseFloat()`都是Global对象的方法。下面也是`Global`对象的一些方法。

1.URI编码方法

`encodeURI()`和`encodeURIComponent()`可以对URI(Uniform Resource Identifiers,通用资源标识符)进行编码。与之对应的是`decodeURI()`和`decodeURIComponent()`,用于对编码的URI进行解码。

编码方法用特殊的UTF-8编码替换所有无效的字符。其中，`encodeURI()`主要用于整个URI，而`encodeURIComponent()`用于对URI中某一段进行编码。区别在于，前者不会对本身属于URI的特殊字符进行编码，例如冒号、正斜杠、问号和井字号；而后者会对任何非标准字符进行编码。

总的来说，`encodeURIComponent()`更有用些，因为主要是对URI里的查询参数进行编码，而不需要对域名进行编码。

2.eval()方法

目前主流意见是禁止使用该方法，所以不做笔记。

3.window对象

ES没有指出如何直接访问Global对象，但是浏览器都是讲这个全局对象作为window对象的一部分加以实现。

#### Math对象

主要记录下几个常用的方法。

1.min()和max()方法

两个方法用于确定一组数值中的最小值和最大值。这两个方法可以接收任意多的数值参数。可以使用该方法找到数组中的最大最小值。

```
var arr = [1,2,3,4,5];
Math.max.apply(Math,arr); // 5

//下面是使用ES6中的扩展操作符来获取数组的极值。
Math.max(...arr); // 5
```

2.舍入方法

将小数值舍入为整数的有三个方法:`Math.ceil()、Math.floor()和Math.round()`。规则如下：

* Math.ceil()向上舍入，即总是将数值向上舍入为最接近的整数。(巧记：ceil为天花板的意思，所以就是往上舍入，也可以理解为进1法)。
* Math.floor()向下舍入，即总是将数值向下舍入为最接近的整数。(巧记：floor为地板的意思，所以就是向下舍入，可以当做是舍去法)。
* Math.round()执行标准舍入，也就是四舍五入。算法为`Math.floor(x+0.5)`,所以`Math.round(-26.5)`的值为`-26`。

4.random()方法

该方法返回介于0和1之间的一个随机数，不包括0和1。可以使用该方法来返回一个指定范围内的随机数。

```
Math.floor(Math.random()*(max-min+1)+min);
// max - 范围内的最大值，min - 单位内的最小值
```







