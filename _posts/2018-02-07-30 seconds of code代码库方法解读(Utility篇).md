---
layout:     post                   
title:      30 seconds of code代码库方法解读(Utility篇)
subtitle:   学习使用ES6实现的代码片段
date:       2018-02-07
author:     chuck
header-img: img/home-bg-code.jpg
catalog: true                      
tags:                               
    - JavaScript
    - ES6
    - 源代码
---



### Utility

#### castArray

```
const castArray = val => (Array.isArray(val) ? val : [val]);
```
目的：如果给定值不是数组，则转换为数组。

解析：使用`Array.isArray()`来判断是否为数组。

```
castArray('foo'); // ['foo']
castArray([1]); // [1]
```


#### cloneRegExp

```
const cloneRegExp = regExp => new RegExp(regExp.source, regExp.flags);
```
目的：克隆正则表达式。

解析：使用正则的构造函数`new RegExp()`来克隆正则表达式。构造函数接受两个参数，第一个参数是正则表达式的文本；第二个参数是`flags`，包括：`g、i、m、u、y`。

```
const regExp = /lorem ipsum/gi;
const regExp2 = cloneRegExp(regExp); // /lorem ipsum/gi
```

#### coalesce

```
const coalesce = (...args) => args.find(_ => ![undefined, null].includes(_));
```
目的：返回第一个非空/未定义的参数。

解析：使用`Array.find()`来返回第一个非空/未定义的参数。

可以借鉴一种表达方式，那就是：如果要查找不包括某些值的时候，可以使用`Array.includes()`并取反，这样就表示了不包括。

```
coalesce(null, undefined, '', NaN, 'Waldo'); // ""
```

#### extendHex

```
const extendHex = shortHex =>
  '#' +
  shortHex
    .slice(shortHex.startsWith('#') ? 1 : 0)
    .split('')
    .map(x => x + x)
    .join(''); 
```
目的：将3位十六进制颜色代码转换为6位数形式。

解析：使用`String.startsWith()`来判断字符串是否以某指定字符串开头。使用`String.split('')`将去除`#`的字符串分割为单字符串数组，遍历数组将每一位重复一遍(因为是字符串，所以数字会进行拼接而不是四则运算)。最后再将数组拼接为字符串。

抛开性能不说，重复字符串除了使用`+`，其实还可以采用ES6定义的`String.repeat(2)`来实现。参数是重复的个数，返回值是重复后的值。

```
extendHex('#03f'); // '#0033ff'
extendHex('05a'); // '#0055aa'
```

#### getURLParameters

```
const getURLParameters = url =>
  (url.match(/([^?=&]+)(=([^&]*))/g) || []).reduce(
    (a, v) => ((a[v.slice(0, v.indexOf('='))] = v.slice(v.indexOf('=') + 1)), a),
    {}
  );
```
目的：返回由当前URL参数组成的对象。

解析：首先使用`String.match(/([^?=&]+)(=([^&]*))/g)`获取由键值对组成的数组(因为`match`方法会返回数组)。遍历数组将`key=value`的形式转换为`{key:value}`。

```
getURLParameters('http://url.com/page?name=Adam&surname=Smith'); // {name: 'Adam', surname: 'Smith'}
getURLParameters('google.com'); // {}
```

#### httpGet

```
const httpGet = (url, callback, err = console.error) => {
  const request = new XMLHttpRequest();
  request.open('GET', url, true);
  request.onload = () => callback(request.responseText);
  request.onerror = () => err(request);
  request.send();
};
```
目的：对传递的URL进行GET请求。

解析：使用`XMLHttpRequest` web api对给定的url进行获取请求。通过调用指定的回调函数处理`onload`事件。如果需要添加查询参数的话，直接在url后面添加。

```
httpGet(
  'https://jsonplaceholder.typicode.com/posts/1',
  console.log
);

// 下面是执行回调函数返回的结果
{
  "userId": 1,
  "id": 1,
  "title": "sunt aut facere repellat provident occaecati excepturi optio reprehenderit",
  "body": "quia et suscipit\nsuscipit recusandae consequuntur expedita et cum\nreprehenderit molestiae ut ut quas totam\nnostrum rerum est autem sunt rem eveniet architecto"
}
```

#### httpPost

```
const httpPost = (url, data, callback, err = console.error) => {
  const request = new XMLHttpRequest();
  request.open('POST', url, true);
  request.setRequestHeader('Content-type', 'application/json; charset=utf-8');
  request.onload = () => callback(request.responseText);
  request.onerror = () => err(request);
  request.send(data);
};
```
目的：对传递的URL进行POST请求。

解析：使用`XMLHttpRequest` web api对给定的url进行获取请求。与GET不同的是，**POST的数据是放在`send()`里的**。POST设置了相应的头部，`request.setRequestHeader('Content-type', 'application/json; charset=utf-8');`这时需要将发送的数据通过`JSON.stringify()`转换为字符串。


#### nthArg

```
const nthArg = n => (...args) => args.slice(n)[0];
```
目的：创建一个获取索引n处的参数的函数。

解析：使用`Array.slice()`来截取从索引n到数组末尾的部分，然后取截取数组的第一项。这一项就是需要获取的参数。n支持负数，从后往前数。如果索引大于`length - 1`,那么就会截取一个空数组，空数组的第一项是`undefined`。

```
const third = nthArg(2);
third(1, 2, 3); // 3
third(1, 2); // undefined
const last = nthArg(-1);
last(1, 2, 3, 4, 5); // 5
```

#### parseCookie

```
const parseCookie = str =>
  str
    .split(';')
    .map(v => v.split('='))
    .reduce((acc, v) => {
      acc[decodeURIComponent(v[0].trim())] = decodeURIComponent(v[1].trim());
      return acc;
    }, {});
```
目的：解析HTTP `Cookie`头部字符串并返回所有`cookie`的名称 - 值对的对象。

解析：首先使用`split(';')`将`cookie`分隔为有键值对组成的数组。然后使用`split('=')`将键值对分开。使用`reduce()`将键与对应的值包含到新建的对象。`decodeURIComponent()`是为了解码转码后的转义字符。`trim()`是用来去除字符串的前后空格，因为`cookie`标准写法是分号后带一个空格。

```
parseCookie('foo=bar; equation=E%3Dmc%5E2'); // { foo: 'bar', equation: 'E=mc^2' }
```

#### prettyBytes

```
const prettyBytes = (num, precision = 3, addSpace = true) => {
  const UNITS = ['B', 'KB', 'MB', 'GB', 'TB', 'PB', 'EB', 'ZB', 'YB'];
  if (Math.abs(num) < 1) return num + (addSpace ? ' ' : '') + UNITS[0];
  const exponent = Math.min(Math.floor(Math.log10(num < 0 ? -num : num) / 3), UNITS.length - 1);
  const n = Number(((num < 0 ? -num : num) / 1000 ** exponent).toPrecision(precision));
  return (num < 0 ? '-' : '') + n + (addSpace ? ' ' : '') + UNITS[exponent];
};
```
目的：将字节数转换为可读的字符串。

```
prettyBytes(1000); // "1 KB"
prettyBytes(-27145424323.5821, 5); // "-27.145 GB"
prettyBytes(123456789, 3, false); // "123MB"
```


#### randomHexColorCode

```
const randomHexColorCode = () => {
  let n = (Math.random() * 0xfffff * 1000000).toString(16);
  return '#' + n.slice(0, 6);
};
```
目的：生成一个随机的十六进制颜色代码。

解析：使用`Math.random()`和`toString(16)`生成一个随机的16进制数字。然后截取前6位作为随机十六进制颜色。

```
randomHexColorCode(); // "#6c8cc8"
```

#### serializeCookie

```
const serializeCookie = (name, val) => `${encodeURIComponent(name)}=${encodeURIComponent(val)}`;
```
目的：将Cookie的名称 - 值对序列化为Set-Cookie头字符串。

解析：使用字符串模板进行拼接，并使用`encodeURIComponent()`转码字符串。

```
serializeCookie('foo', 'bar'); // 'foo=bar'
```

#### timeTaken

```
const timeTaken = callback => {
  console.time('timeTaken');
  const r = callback();
  console.timeEnd('timeTaken');
  return r;
};
```
目的：测量一个函数的执行时间。

解析：使用了`console.time()`和`console.timeEnd()`来统计两者之间的代码的执行时间。传入相同的字符串参数来进行标记。

```
timeTaken(() => Math.pow(2, 10));

// timeTaken: 0.0390625ms
// 1024
```

#### toDecimalMark

```
const toDecimalMark = num => num.toLocaleString('en-US');
```
目的：使用`toLocaleString()`浮点运算转换为[小数点](https://en.wikipedia.org/wiki/Decimal_separator)标记形式。使用逗号来分隔数字。


```
toDecimalMark(12305030388.9087); // "12,305,030,388.909"
```

#### validateNumber

```
const validateNumber = n => !isNaN(parseFloat(n)) && isFinite(n) && Number(n) == n;
```

目的：检测给定值是否为有效数字，如果是，则返回`true`；否则返回`false`。

解析：首先使用`parseFloat()`将参数转换为数字，并使用`isNaN()`并取非来判断参数转换为数字后不是`NaN`；使用`isFinite()`来判断参数是否为有限而不是无穷；使用`Number()`将参数转为数字，并与原参数进行比较，这里会进行隐式转换。具体规则是：如果`==`操作符第一参数为数字，则将第二参数进行数字的隐式转换，这里如果两者相等，就表明是一个有效数字。

可以看到，这里用到了2个数值转换的方法：`parseFloat()`和`Number()`。具体的转换规则红皮书里有详细的介绍，也可以看我的这一篇[笔记](https://qukun.com.cn/2017/12/22/javascript%E9%AB%98%E7%BA%A7%E7%A8%8B%E5%BA%8F%E8%AE%BE%E8%AE%A1%E8%AF%BB%E4%B9%A6%E7%AC%94%E8%AE%B0(%E4%B8%80)/#4%E6%95%B0%E5%80%BC%E8%BD%AC%E6%8D%A2)。

```
validateNumber('10'); // true
```

#### yesNo

```
const yesNo = (val, def = false) =>
  /^(y|yes)$/i.test(val) ? true : /^(n|no)$/i.test(val) ? false : def;
```

目的：如果字符串参数是`y`/`yes`就返回`true`；如果字符串参数是`n`/`no`就返回`false`。

解析：通过`RegExp.test()`来匹配参数。

```
yesNo('Y'); // true
yesNo('yes'); // true
yesNo('No'); // false
yesNo('Foo', true); // true
```


