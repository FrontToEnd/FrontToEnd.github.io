---
layout:     post                   
title:      30 seconds of code代码库方法解读(String篇)
subtitle:   实现办法实在让人想不到
date:       2018-02-05
author:     chuck
header-img: img/home-bg-array.jpg
catalog: true                      
tags:                               
    - JavaScript
    - ES6
    - 源代码
---


### String

关于字符串的方法，很多都用到了正则，然而我的正则知识储备实在少的可怜，这里就记录一下我已经理解了的方法，等拜读了老姚的正则迷你书后再好好的总结一下。

#### byteSize

```
const byteSize = str => new Blob([str]).size;
```
目的：以字节为单位返回字符串的长度。

解析：将指定字符串转换为`Blob`对象，然后获取其`size`属性。

```
byteSize('😀'); // 4
byteSize('Hello World'); // 11
```

#### capitalize

```
const capitalize = ([first, ...rest], lowerRest = false) =>
  first.toUpperCase() + (lowerRest ? rest.join('').toLowerCase() : rest.join(''));
```
目的：将字符串第一个字母大写。

解析：首先`first`是一个单字符，`rest`是一个除去首字符组成的数组。对`first`执行`toUpperCase()`转为大写；然后对`rest`执行`join('')`拼接为字符串，如果第二参数为`true`，将其余字符串转为小写(`toLowerCase()`)。

```
capitalize('fooBar'); // 'FooBar'
capitalize('fooBar', true); // 'Foobar'
```

#### capitalizeEveryWord

```
const capitalizeEveryWord = str => str.replace(/\b[a-z]/g, char => char.toUpperCase());
```
目的：将每个单词的首字母大写。

解析：该方法主要采用了正则进行替换，这里解释下正则的含义：先看`\b`，`\b`的含义是单词边界，也就是`\w`和`\W`直接的位置。等等，`\w`又是什么鬼？`\w`是字符组`[0-9a-zA-Z_]`的简写形式。然后是`[a-z]`,首先`[]`表示的是匹配单个位置，也就是纵向匹配。所以整体来看，就是全局匹配单词边界并且接下来的一个位置是a-z。并将匹配到的字符转换为大写。

```
capitalizeEveryWord('hello world!'); // 'Hello World!'
```

#### escapeHTML

```
const escapeHTML = str =>
  str.replace(
    /[&<>'"]/g,
    tag =>
      ({
        '&': '&amp;',
        '<': '&lt;',
        '>': '&gt;',
        "'": '&#39;',
        '"': '&quot;'
      }[tag] || tag)
  );
```
目的：将HTML中特定字符串进行转义。包括(`&``<``>``'``"`)。

解析：主要还是使用了`String.replace()`进行替换。

```
escapeHTML('<a href="#">Me & you</a>'); // '&lt;a href=&quot;#&quot;&gt;Me &amp; you&lt;/a&gt;'
```

#### isAbsoluteURL

```
const isAbsoluteURL = str => /^[a-z][a-z0-9+.-]*:/.test(str);
```
目的：判断一个URL是否是绝对路径。

解析：通过正则来判断URL是否以指定字符开头。如果是，则说明是绝对URL，并返回`true`。

```
isAbsoluteURL('https://google.com'); // true
isAbsoluteURL('ftp://www.myserver.net'); // true
isAbsoluteURL('/foo/bar'); // false
```

#### mask

```
const mask = (cc, num = 4, mask = '*') =>
  ('' + cc).slice(0, -num).replace(/./g, mask) + ('' + cc).slice(-num);
```
目的：用指定的掩码字符替换除最后num个字符以外的所有字符。num默认为4。掩码字符默认是星号`*`。

解析：这里巧妙的使用了`slice()`进行字符串的分段。首先将[0,length-num]的字符替换为指定的掩码字符，然后和[length-num,length]的字符进行拼接。如果num为负，去掉length即可。

```
mask(1234567890); // '******7890'
mask(1234567890, 3); // '*******890'
mask(1234567890, -4, '$'); // '$$$$567890'
```

#### palindrome

```
const palindrome = str => {
  const s = str.toLowerCase().replace(/[\W_]/g, '');
  return (
    s ===
    s
      .split('')
      .reverse()
      .join('')
  );
};
```
目的：如果指定字符串是回文，就返回`true`，否则返回`false`。

解析：首先通过正则去除所有`\W`。然后通过自身与调转过的进行恒等比较，如果相等则为回文。并返回`true`。调转使用了经典的办法`s.split('').reverse().join('')`，因为字符串没有提供调转的原生办法，而数组有。

```
palindrome('taco cat'); // true
```

#### reverseString

```
const reverseString = str => [...str].reverse().join('');
```
目的：翻转一个字符串。

解析：可以直接通过`[...str]`将字符串`str`转为数组是因为扩展运算符可以扩展一切可迭代对象，而字符串具有Iterator属性，属于可迭代对象。

```
reverseString('foobar'); // 'raboof'
```


#### sortCharactersInString

```
const sortCharactersInString = str => [...str].sort((a, b) => a.localeCompare(b)).join('');
```
目的：按照字母的顺序排序字符串中的字符。

解析：使用`String.localeCompare()`进行两字符的比较。该方法返回一个数字来指示一个参考字符串是否在排序顺序前面或之后或与给定字符串相同。然后`sort()`根据返回值进行排序。最后再将数组拼接为字符串。

```
sortCharactersInString('cabbage'); // 'aabbceg'
```

#### splitLines

```
const splitLines = str => str.split(/\r?\n/);
```
目的：将多行字符串转换为一行数组。

解析：使用特定正则将字符串分割为数组。

```
splitLines('This\nis a\nmultiline\nstring.\n'); // ['This', 'is a', 'multiline', 'string.' , '']
```

#### stripHTMLTags

```
const stripHTMLTags = str => str.replace(/<[^>]*>/g, '');
```
目的：从字符串中移除HTML标签。


```
stripHTMLTags('<p><em>lorem</em> <strong>ipsum</strong></p>'); // 'lorem ipsum'
```

#### truncateString

```
const truncateString = (str, num) =>
  str.length > num ? str.slice(0, num > 3 ? num - 3 : num) + '...' : str;
```
目的：截断一个字符串到指定长度。

解析：如果字符串长度不大于需要截断的长度，则直接返回字符串本身；如果大于，则与`...`进行拼接。

```
truncateString('boomerang', 7); // 'boom...'
```

#### unescapeHTML

```
const unescapeHTML = str =>
  str.replace(
    /&amp;|&lt;|&gt;|&#39;|&quot;/g,
    tag =>
      ({
        '&amp;': '&',
        '&lt;': '<',
        '&gt;': '>',
        '&#39;': "'",
        '&quot;': '"'
      }[tag] || tag)
  );
```
目的：与`escapeHTML`目的相反，将指定字符进行解码。

```
unescapeHTML('&lt;a href=&quot;#&quot;&gt;Me &amp; you&lt;/a&gt;'); // '<a href="#">Me & you</a>'
```


