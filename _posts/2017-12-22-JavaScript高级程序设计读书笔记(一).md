---
layout:     post                   
title:      JavaScript高级程序设计读书笔记(一)         
subtitle:   重温红皮书精髓
date:       2017-12-22
author:     chuck
header-img: img/home-bg-basic.jpg
catalog: true                      
tags:                               
    - JavaScript
    - 基本概念
---

## 第三章  基本概念

### 1、语法

`ECMAScript`中的一切(变量、函数名和操作符)都区分大小写。

所谓`标识符`，就是指变量、函数、属性的名字，或者函数的参数。可按照下列格式规则命名：
1) 第一个字符必须是一个字母、下划线`_`或一个美元符号`$`。
2) 其他字符可以是字母、下划线、美元符号或者数字。
也可以包含扩展的ASCII或Unicode字母字符，但不推荐。

需要注意的是，**不能把关键字、保留字、true、false和null用作标识符**。

### 2、数据类型

ECMAscript中有5中简单数据类型(基本数据类型)：`Undefined、Null、Boolean、Number、String`。还有一种复杂数据类型---Object。

#### typeof操作符

对一个值使用typeof操作符可能返回下列某个字符串：
1)"undefined"---如果这个值未定义；
2)"boolean"---如果这个值是布尔值；
3)"string"---如果是字符串；
4)"number"---如果是数值；
5)"object"---如果是对象或者null；
6)"function"---如果是函数。

需要注意的是，`typeof`是操作符而非函数。

从技术角度讲，函数在ES中是对象，不是一种数据类型。但是，函数确实有一些特殊的属性，因此区分函数和对象是有必要的。

#### Undefined类型

在使用var声明变量但未对其加以初始化时，这个变量的值就是undefined。不需要显式地把一个变量设置为`undefined`，但存在显式设置为`null`，因为这样可以解决闭包问题。

对于尚未声明过的变量，只能使用typeof操作符检测数据类型(对未经声明的变量调用`delete`不会导致错误，但毫无意义，而且严格模式会报错)。

需要注意的是，对**未初始化**的变量执行typeof操作符会返回`undefined`，对**未声明**的变量同样返回`undefined`。
 
```
var message;
alert(typeof message); // "undefined"
alert(typeof age); // 变量age未声明，同样返回"undefined"
```
这个结果有其逻辑上的合理性。因为虽然这两种变量从技术角度看有本质区别，但实际无论对哪种变量也不可能执行真正的操作。

#### Null类型

从逻辑角度来看，`null`值表示一个空对象指针，这也是使用typeof操作符检测null值会返回"object"的原因。

如果定义的变量打算用来保存对象，那么最好将该变量初始化为null。

#### Boolean类型

要将一个值转换为对应的`Boolean`值，可以调用转型函数`Boolean()`。下表给出了各种数据类型的转换规则。

| 数据类型 | 转换为true的值 | 转换为false的值 |
| --- | --- | --- |
| Boolean | true | false |
| String | 任何非空字符串 | '' |
| Number | 任何非零数字值(包括无穷大) | 0和NaN |
| Object | 任何对象(包括’{}’和’[]') | null |
| Undefined | N/A | undefined |

#### Number类型

这种类型使用IEEE754格式来表示整数和浮点数值。在进行算术计算时，所有以八进制和十六进制表示的数值最终都将被转换为十进制数值。

JS可以保存正零(+0)和负零(-0)。但是它们被认为相等。

```
console.log(+0 === -0) // true
// 虽然两者相等，但是也有例外
console.log(1 / +0 === 1 / -0) // false 因为1 / +0 的结果为Infinity，1 / -0 的结果为-Infinity。
```

##### 1.浮点数值

所谓浮点数值，就是该数值中必须包含一个小数点，并且小数点后面必须至少有一位数字。

```
var floatNum1 = 1.1;
var floatNum2 = 0.1;
var floatNum3 = .1; // 有效，但不推荐
```

由于保存浮点数值需要的内存空间是保存整数值的两倍，因为ES会不失时机地将浮点数值转换为整数值。

```
var floatNum1 = 1.; // 小数点后没有数字---解析为1
var floatNum2 = 10.0; // 整数---解析为10
```

对于极大或极小的数值，可以用e表示法表示的浮点数值表示。用法就是科学计数法。默认情况下，ES会将那些**小数点后面带有6个零及以上**的浮点数值转换为e表示法。

```
var floatNum = 1.14e7; // 等价于11400000
var floatNum2 = 9e-7; // 等价于0.0000009
```

浮点数值的最高精度是17位小数，但在进行算术计算时其精确度远不如整数。比如：

```
console.log(0.1 + 0.2); // 0.30000000000000004
```
至于浮点数值会产生误差的问题，这是使用基于IEEE754计算的通病。所以我们不应该直接比较非整数，而是取其上限，把误差计算进去
这样一个上限称为 `machine epsilon`，双精度的标准epsilon值是`2^-53`

```
const EPSILON = Math.pow(2, -53); //1.1102230246251565e-16

function epsEqu(x,y) {
  return Math.abs(x - y) < EPSILON;
} // 如果两数相减绝对值的误差小于2^-53，那么就认为两数是相等的

epsEqu(0.1+0.2, 0.3) //true
```

##### 2.数值范围

最小数值保存在`Number.MIN_VALUE`，大多数浏览器中，这个数值是`5e-324`；最大数值保存在`Number.MAX_VALUE`，大多数浏览器中，这个数值是`1.7976931348623157e+308`。如果某次计算超出范围，则会自动转换成 ±`Infinity`。

```
console.log(Infinity * -Infinity); // -Infinity
console.log(Infinity + Infinity); // Infinity
console.log(Infinity + -Infinity); // NaN
console.log(Infinity / 0); // Infinity
console.log(Infinity * 0); // NaN
```

要想确定一个数值是否是有穷，可以使用`window.isFinite()`函数。如果有穷，则返回true。

```
console.log(isFinite('1')) // true
console.log(isFinite('1blue')) // false
console.log(isFinite(0x12)) // true
console.log(isFinite(Infinity)) // false
```

还有几个Number对象下的几个常量值得注意：

```
Number.MAX_SAFE_INTEGER //值是Math.pow(2,53) - 1，9007199254740991
Number.MIN_SAFE_INTEGER // 值是 -9007199254740991
Number.NEGATIVE_INFINITY // 值是-Infinity
Number.POSITIVE_INFINITY // 值是Infinity
// 需要注意的是，数组的长度最长是Array(Math.pow(2,32)-1),否则报错‘Invalid array length’
```

##### 3.NaN

这个数值用于表示一个本来要返回数值的操作数未返回数值的情况，防止抛出错误。

NaN有两个很独特的特点。首先，任何涉及NaN的操作都会返回NaN，在多步运算中如果有一步产生NaN，那么结果一般都会是NaN。其次，NaN与任何值不相等，包括NaN本身。

可以通过`isNaN()`函数来判断是否“不是数值”。函数在接收一个参数后，会尝试将这个值转换为数值。

```
console.log(isNaN(NaN)); // true
console.log(isNaN(10)); // false
console.log(isNaN("blue")); // true
```
在Underscore的源码中的equal()函数是这么判断NaN是否相等的：

```
if (a !== a) return b !== b;
// 不得不说这个思路真的很巧妙
```

##### 4.数值转换

有3个函数可以把非数值转换为数值:`Number()、parseInt()和parseFloat()`。`Number()`可以用于任何数据类型，而后两个专门用来把字符串转换成数值。
先来看看`Number()`函数的转换规则:
1) 如果是`Boolean`值，`true`和`false`将分别被转换为1和0。

2) 如果是数字，则直接返回。

3) 如果是`null`，则返回0。

4) 如果是`undefined`，返回NaN。

5) 如果是字符串，则：

1. 如果字符串中只包含数字（包括前面带正号或负号的情况），则转换为**十进制数值**。"1" --> 1,"011" --> 11
2. 如果字符串中包含有效的浮点格式，则转换为对应的浮点数值。
3. 如果字符串中包含有效的十六进制格式，则转换为十进制整数值。
4. 如果为空字符串，转换为0.
5. 如果字符串包含除上述格式外的字符，则转换为NaN。

6) 如果是对象，则调用对象的`valueOf()`方法，然后依据前面规则返回值。如果转换的结果是NaN，则调用对象的`toString()`方法，然后再依据前面规则转换返回的字符串值。

```
console.log(Number("Hi")); // NaN
console.log(Number("")); // 0
console.log(Number("011")); // 11
console.log(Number(true)); // 1
```
需要注意的是，**一元加操作符`+`的操作与`Number()`函数相同。**

接下来看看`parseInt()`函数的转换规则。千万不要忘记参数是针对字符串的。它会忽略字符串前面的空格，直到找到第一个非空格字符。

如果第一个字符不是数字字符或者负号，那么就会返回NaN;也就是说，用`parseInt()`转换空字符串会返回NaN。它会直到解析遇到一个非数字字符为止。包括小数点。

如果字符串中的第一个字符是数字字符，`parseInt()`也能够识别各种整数格式。如果字符串以"0x"开头且后跟数字字符，就会当作一个十六进制整数；如果字符串以"0"开头且后跟数字字符，就会当作八进制数解析。

```
console.log(parseInt("1234blue")); // 1234
console.log(parseInt("")); // NaN
console.log(parseInt("0xA")); // 10（十六进制）
console.log(parseInt(22.5)); // 22
console.log(parseInt("070")); // 56(八进制)
console.log(parseInt("70")); // 70(十进制)
```
为了消除在使用`parseInt()`函数的困惑，可以提供第二个参数，转换时使用的基数。

```
var num1 = parseInt("10",2); //2 (二进制)
var num2 = parseInt("10",8); //8 (八进制)
var num3 = parseInt("10",10); //10 (十进制)
var num4 = parseInt("10",16); //16 (十六进制)
```
因此在使用该函数时，显示声明第二参数是一个很好的编码习惯。

然后就是`parseFloat()`函数。与`parseInt()`第一个不同点是，`parseFloat()`会解析第一个小数点。"22.33.44"会解析为22.33。

第二个不同点是，`parseFloat()`始终都会忽略前导的零。也就是说`parseFloat()`只解析十进制值，所以也没有第二参数。还有一个需要注意的是，如果字符串包含的是一个可解析为整数的数(没有小数点，或小数点后是0)，`parseFloat()`会返回整数。

#### String类型

String类型用于表示由零或多个16位Unicode字符组成的字符序列。用双引号或单引号表示的字符串完全等价。

任何字符串的长度都可以访问其`length`属性取得。如果字符串包含双字节字符，那么length可能不那么准确。

```
console.log("chuck".length); // 5
```

把一个值转换为一个字符串有两种方式。第一种是几乎每个值都有的`toString()`方法。数值、布尔值、对象和字符串值都有`toString()`方法。但是**null和undefined没有**。

大多数情况下，调用`toString()`不需要传递参数。但是，在调用数值的`toString()`方法时，可以传递参数用来指明输出基数。默认情况是以十进制格式返回。

```
var num = 10;
num.toString(); // "10"
num.toString(2); // "1010"
num.toString(8); // "12"
num.toString(10); // "10"
num.toString(16); // "a"
```
在不知道要转换的值是不是null或undefined的情况下，还可以使用转型函数String()，这个函数能将任何类型的值转换为字符串。规则如下：

1. 如果值有 `toString()`方法，则调用该方法并返回结果。
2. 如果值是null，则返回"null"。
3. 如果值是undefined，则返回"undefined"。

要想把某个值转换为字符串，可以使用加号操作符`+`把它与一个空字符串加在一起。

#### Object类型

对象可以通过执行new操作符后跟要创建的对象类型的名称来创建。

```
var o = new Object();
```
在ES中，Object类型是所有它的实例的基础。Object的每个实例都具有下列属性和方法。

1. Constructor:保存着用于创建当前对象的函数。对于上述例子，构造函数就是`Object()`。
2. hasOwnProperty():用于检查给定的属性在当前对象实例中，而不是原型中。
3. isPrototypeOf():用于检查传入的对象是否是另一个对象的原型。
4. propertyIsEnumerable():用于检查给定的属性是否能够使用for-in语句来枚举。
5. toLocaleString():返回对象的字符串表示，该字符串与执行环境的地区对应。
6. toString():返回对象的字符串表示。
7. valueOf():返回对象的字符串、数值或布尔值表示。通常与toString()方法的返回值相同。

### 3.操作符

#### 一元操作符

只能操作一个值的操作符叫做一元操作符。
1. 递减和递增操作符

递增和递减操作符直接借鉴自C，包括前置型和后置型。

```
var age = 26;
++age; // 27 
--age; // 26;
```
执行前置递增和递减操作时，变量的值都是在语句被求值前改变的。

```
var num1 = 2;
var num2 = 20;
var num3 = --num1 + num2; //求值的时候，num1变成19，所以返回21
var num4 = num1 + num2; // num1已经变成19,所以还是21
```

后置型语法不变，区别是后置型是在包含它们的语句被求值之后才执行。

所有这4个操作符对任何值都适合，应用于不同值时，遵循下列规则。

1. 在应用于一个包含有效数字字符的字符串时，先转换为数字值，再执行加减1的操作。 字符串变量变成数值变量。
2. 在应用于一个不包含有效数字字符的字符串时，将变量的值设置为NaN。字符串变量变成数值变量。
3. 在应用于布尔值false时，先将其转换为0再执行加减1的操作。
4. 在应用于浮点数时，执行加减1的操作。
5. 在应用于对象时，先调用对象的valueOf()方法，如果结果是NaN,再调用toString()方法。

2.一元加和减操作符

在对非数值应用一元加操作符时，该操作符会像`Number()`转型函数一样对这个值执行转换。一元减操作符主要用于表示负数，在将一元操作符应用于数值时，该值会变成负数。而当应用于非数值时，一元减操作符遵循与一元加操作符相同的规则。

#### 位操作符

位操作符用于在最基本的层次上，即**按内存中表示数值的位来操作数值**。ES中的所有数值都以IEEE-754 64位格式存储，但位操作符并不直接操作64位的值。而是先将64位的值转换为32位的整数，执行操作后，再转回64位。

对于有符号的整数，32位中的前31位用于表示整数的值。第32位用于表示数值的符号：0为正数，1为负数。

正数以纯二进制格式存储，每一位都是2的幂。没有用到的位以0填充，即忽略不计。负数同样以二进制码存储，但使用的格式是二进制补码。计算二进制补码，需要以下步骤:
1) 求出这个数值绝对值的二进制码；
2) 求二进制反码，1换0，0换1；
3) 得到的二进制反码加1。

ES会尽力隐藏所有这些信息。在以二进制字符形式输出一个负数时，我们看到的只是这个负数绝对值的二进制码前加上负号。

```
var num = -18;
num.toString(2); // "-10010"
```

如果对非数值应用操作符，会先使用Number()函数将该值转换为一个数值，然后操作得到一个数值。当遇到特殊的**NaN和Infinity值应用位操作时，会被当做0处理**。

1.按位非(NOT)





