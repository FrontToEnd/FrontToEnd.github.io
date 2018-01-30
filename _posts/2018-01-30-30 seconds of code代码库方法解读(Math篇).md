---
layout:     post                   
title:      30 seconds of code代码库方法解读(浏览器与时间篇)
subtitle:   实现办法实在让人想不到
date:       2018-01-30
author:     chuck
header-img: img/home-bg-array.jpg
catalog: true                      
tags:                               
    - JavaScript
    - ES6
    - 源代码
---


### Math

#### average

```
const average = (...nums) => [...nums].reduce((acc, val) => acc + val, 0) / nums.length;
```
目的：返回所有参数的平均数。

解析：浅复制参数数组后使用`reduce()`进行累加，并提供初始值0。累加完后除以参数个数获得平均数。

```
average(...[1, 2, 3]); // 2
average(1, 2, 3); // 2
```

#### averageBy

```
const averageBy = (arr, fn) =>
  arr.map(typeof fn === 'function' ? fn : val => val[fn]).reduce((acc, val) => acc + val, 0) /
  arr.length;
```

目的：使用提供的函数将每个元素映射到一个值后，返回一个数组的平均值。

解析：先调用`map()`对数组的每一项调用指定fn，返回处理后的数组后，再进行累加获取平均值。

```
averageBy([{ n: 4 }, { n: 2 }, { n: 8 }, { n: 6 }], o => o.n); // 5
averageBy([{ n: 4 }, { n: 2 }, { n: 8 }, { n: 6 }], 'n'); // 5
```

#### digitize

```
const digitize = n => [...`${n}`].map(i => parseInt(i));
```
目的：将一个数字转换为数组数组。

解析：首先使用**字符串模板**将给定参数转换为字符串，因为字符串有`length`属性，可以进行迭代。然后展开为由单个字符组成的数组。最后通过对每一项遍历调用`parseInt()`将每一项转换为整数数字。并返回由数字组成的数组。

```
digitize(123); // [1, 2, 3]
```

#### distance

```
const distance = (x0, y0, x1, y1) => Math.hypot(x1 - x0, y1 - y0);
```

目的：返回两点之间的距离。

解析：使用`Math.hypot()`返回它的所有参数的平方和的平方根。众所周知，两点间的距离就是**两点差值绝对值的平方和的平方根**。也可以理解为使用了勾股定理。

需要注意的是，该新特性属于 **ECMAScript 2015（ES6）**规范，在使用时请注意浏览器兼容性。

```
distance(1, 1, 2, 3); // 2.23606797749979
```

#### factorial


```
const factorial = n =>
  n < 0
    ? (() => {
        throw new TypeError('Negative numbers are not allowed!');
      })()
    : n <= 1 ? 1 : n * factorial(n - 1);
```
目的：计算指定数字的阶乘。

解析：如果n小于0，直接抛出错误。如果n小于等于1，则直接返回1，否则通过递归调用n-1。

```
factorial(6); // 720
```

#### fibonacci

```
const fibonacci = n =>
  Array.from({ length: n }).reduce(
    (acc, val, i) => acc.concat(i > 1 ? acc[i - 1] + acc[i - 2] : i),
    []
  );
```
目的：生成一个包含斐波那契数列的数组，直到第n项。

解析：首先初始化一个长度为n的数组。然后数组的前两项分别为0和1，然后从第三项开始每一项为前两项的和。

```
fibonacci(6); // [0, 1, 1, 2, 3, 5]
```

#### gcd

```
const gcd = (...arr) => {
  const _gcd = (x, y) => (!y ? x : gcd(y, x % y));
  return [...arr].reduce((a, b) => _gcd(a, b));
};
```
目的：计算最大公约数。

```
gcd(8, 36); // 4
gcd(...[12, 8, 32]); // 4
```

#### hammingDistance

```
const hammingDistance = (num1, num2) => ((num1 ^ num2).toString(2).match(/1/g) || '').length;
```
目的：计算两个值之间的汉明距离。

解析：我也是头一次听说汉明距离，引用维基百科的解释就是：在信息论中，两个等长字符串之间的**汉明距离**（英语：Hamming distance）是两个字符串对应位置的不同字符的个数。换句话说，它就是将一个字符串变换成另外一个字符串所需要替换的字符个数。

对两个字符串进行异或运算，并统计结果为1的个数，那么这个数就是汉明距离。


```
hammingDistance(2, 3); // 1
// 10 变换为 11 需要替换1个字符，所以汉明距离为1
```

#### inRange

```
const inRange = (n, start, end = null) => {
  if (end && start > end) end = [start, (start = end)][0];
  return end == null ? n >= 0 && n < start : n >= start && n < end;
};
```
目的：检查给定数字是否在给定范围内。

解析：如果未指定`end`，范围就是`0`到`start`。如果指定了`end`，但是小于`start`的话，将两者位置互换。然后根据上述范围进行判断。

需要注意的是，该方法的判断是左闭右开。

```
inRange(3, 4); // true
inRange(2, 3, 5); // false
```

#### isDivisible

```
const isDivisible = (dividend, divisor) => dividend % divisor === 0;
```
目的：检查第一个数字参数是否可被第二个数字参数整除。

解析：直接使用求余操作符操作，如果余数为0，则可整除。

```
isDivisible(6, 3); // true
```

#### isEven

```
const isEven = num => num % 2 === 0;
```
目的：判断给定数字是否为偶数，返回对应布尔值。

解析：对指定数字求2的余数，如果为0，则为偶数，并返回`true`。否则返回`false`。

```
isEven(3); // false
```

#### isPrime

```
const isPrime = num => {
  const boundary = Math.floor(Math.sqrt(num));
  for (var i = 2; i <= boundary; i++) if (num % i == 0) return false;
  return num >= 2;
};
```
目的：检查指定数字是否为质数。

解析：该检测方法采用了试除法。此一方法会测试n是否为任一在2与√n之间的整数之倍数。如果是，则表明不是质数，同时返回`false`。

如果n不是2与√n之间的任意整数之倍数，同时n大于2，则n为质数。因为按照质数的定义，质数需大于1。

```
isPrime(11); // true
```

#### lcm

```
const lcm = (...arr) => {
  const gcd = (x, y) => (!y ? x : gcd(y, x % y));
  const _lcm = (x, y) => x * y / gcd(x, y);
  return [...arr].reduce((a, b) => _lcm(a, b));
};
```
目的：返回参数的最小公倍数。


```
lcm(12, 7); // 84
lcm(...[1, 3, 4, 5]); // 60
```

#### maxBy

```
const maxBy = (arr, fn) => Math.max(...arr.map(typeof fn === 'function' ? fn : val => val[fn]));
```
目的：返回由特定函数处理过后的数组中所有元素的最大值。

解析：遍历数组调用指定函数，将返回的数组展开并使用`Math.max()`获取最大值。

```
maxBy([{ n: 4 }, { n: 2 }, { n: 8 }, { n: 6 }], o => o.n); // 8
maxBy([{ n: 4 }, { n: 2 }, { n: 8 }, { n: 6 }], 'n'); // 8
```

#### minBy

```
const minBy = (arr, fn) => Math.min(...arr.map(typeof fn === 'function' ? fn : val => val[fn]));
```
目的：与上面的maxBy相反，获取最小值。

#### median

```
const median = arr => {
  const mid = Math.floor(arr.length / 2),
    nums = [...arr].sort((a, b) => a - b);
  return arr.length % 2 !== 0 ? nums[mid] : (nums[mid - 1] + nums[mid]) / 2;
};
```
目的：返回数组的中位数。

解析：首先将数组按正序进行排序，然后获取中位数索引`mid`。如果数组长度是奇数，则取最中间的那一项；否则取当前项与前一项的平均值。

```
median([5, 6, 50, 1, -5]); // 5
```

#### percentile

```
const percentile = (arr, val) =>
  100 * arr.reduce((acc, v) => acc + (v < val ? 1 : 0) + (v === val ? 0.5 : 0), 0) / arr.length;
```
目的：使用百分数来计算给定数组中有多少数字小于或等于给定值。

解析：通过`reduce()`进行累加，如果给定值大于当前项，则累加1；如果给定值等于当前项，则累加0.5；否则加0。将返回结果除以数组长度并乘以100，最终结果为百分比。

```
percentile([1, 2, 3, 4, 5, 6, 7, 8, 9, 10], 6); // 55
```

#### primes

```
const primes = num => {
  let arr = Array.from({ length: num - 1 }).map((x, i) => i + 2),
    sqroot = Math.floor(Math.sqrt(num)),
    numsTillSqroot = Array.from({ length: sqroot - 1 }).map((x, i) => i + 2);
  numsTillSqroot.forEach(x => (arr = arr.filter(y => y % x !== 0 || y == x)));
  return arr;
};
```
目的：获取给定数字下所有的质数。


```
primes(10); // [2,3,5,7]
```

#### randomIntArrayInRange

```
const randomIntArrayInRange = (min, max, n = 1) =>
  Array.from({ length: n }, () => Math.floor(Math.random() * (max - min + 1)) + min);
```
目的：在给定的范围内返回由n个随机整数组成的数组。

解析：首先生成一个长度为n的空数组，同时遍历数组使每一项通过`Math.floor(Math.random() * (max - min + 1)) + min`生成随机数。

```
randomIntArrayInRange(12, 35, 10); // [ 34, 14, 27, 17, 30, 27, 20, 26, 21, 14 ]
```

#### randomIntegerInRange

```
const randomIntegerInRange = (min, max) => Math.floor(Math.random() * (max - min + 1)) + min;
```
目的：返回给定范围的随机整数。


```
randomIntegerInRange(0, 5); // 2
```

#### randomNumberInRange

```
const randomNumberInRange = (min, max) => Math.random() * (max - min) + min;
```

目的：返回指定范围内的随机数。

```
randomNumberInRange(2, 10); // 6.0211363285087005
```

#### round

```
const round = (n, decimals = 0) => Number(`${Math.round(`${n}e${decimals}`)}e-${decimals}`);
```
目的：将给定参数四舍五入到指定位数。

解析：使用字符串模板将变量`n`扩大`decimals`倍，然后调用`Math.round()`进行四舍五入,然后再缩小`decimals`倍。这样就达到了指定位数的目的。

```
round(1.005, 2); // 1.01
```

#### sum

```
const sum = (...arr) => [...arr].reduce((acc, val) => acc + val, 0);
```
目的：求和。

解析：使用`reduce()`进行累加。


```
sum(...[1, 2, 3, 4]); // 10
```

#### sumBy

```
const sumBy = (arr, fn) =>
  arr.map(typeof fn === 'function' ? fn : val => val[fn]).reduce((acc, val) => acc + val, 0);
```
目的：对每一项执行指定函数后进行累加求和。


```
sumBy([{ n: 4 }, { n: 2 }, { n: 8 }, { n: 6 }], o => o.n); // 20
sumBy([{ n: 4 }, { n: 2 }, { n: 8 }, { n: 6 }], 'n'); // 20
```

#### toSafeInteger

```
const toSafeInteger = num =>
  Math.round(Math.max(Math.min(num, Number.MAX_SAFE_INTEGER), Number.MIN_SAFE_INTEGER));
```
目的：将指定值转换为安全整数。在JS中安全指数由两个常量来保存，分别是：`Number.MAX_SAFE_INTEGER--->9007199254740991`和`Number.MIN_SAFE_INTEGER--->-9007199254740991`

解析：使用`Math.min()`和`Math.max()`来排除比安全整数更大或更小的值。然后四舍五入取整。

```
toSafeInteger('3.2'); // 3
toSafeInteger(Infinity); // 9007199254740991
toSafeInteger(-Infinity); // -9007199254740991
```


