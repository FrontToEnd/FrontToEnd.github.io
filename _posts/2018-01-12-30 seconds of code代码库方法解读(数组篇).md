---
layout:     post                   
title:      30 seconds of code代码库方法解读(数组篇) 
subtitle:   实现办法实在让人想不到
date:       2018-01-12
author:     chuck
header-img: img/home-bg-array.jpg
catalog: true                      
tags:                               
    - JavaScript
    - ES6
    - 源代码
---

## 30 seconds of code

> Curated collection of useful JavaScript snippets that you can understand in 30 seconds or less.


> 收集一些有用的JS代码片段(全部使用ES6书写)

这是一个最近比较火的代码库，主要是用原生方法来实现一些很实用的函数，本文就根据[官网](https://30secondsofcode.org/)的描述，将库内的方法逐个分析，希望从中获取到一些很有价值的代码。

### Array

#### chunk


```
const chunk = (arr, size) =>
  Array.from({ length: Math.ceil(arr.length / size) }, (v, i) =>
    arr.slice(i * size, i * size + size)
  );
```
目的：通过指定的size来分隔一个数组。

解析：使用`Array.from()`来创建一个新的数组，数组的长度为参数长度除以size并向上取整，技巧就是from()方法接收第二个参数，新数组中的每个元素会执行该回调函数。所以就相当于 `Array.from(obj).map(mapFn, thisArg)`，回调函数使用`slice()`进行分隔数组，同时`slice()`每次会返回一个新的数组，最后就巧妙的达到目的。


```
chunk([1, 2, 3, 4, 5], 2); // [[1,2],[3,4],[5]]
```

#### compact


```
const compact = arr => arr.filter(Boolean);
```

目的：从一个数组中移除假值。 (false, null, 0, "", undefined, and NaN).

解析：使用`Array.filter()`进行过滤，会执行指定的回调函数，这里执行的回调函数是`Boolean()`，该函数会返回`false`，所以可以这样过滤掉所有的假值。


```
compact([0, 1, false, 2, '', 3, 'a', 'e' * 23, NaN, 's', 34]); // [ 1, 2, 3, 'a', 's', 34 ]
```

#### countOccurrences


```
const countOccurrences = (arr, val) => arr.reduce((a, v) => (v === val ? a + 1 : a + 0), 0);
```
目的：统计数组中某值的出现次数。

解析：使用`Array.reduce()`来进行累加，`Array.reduce()`指定第二参数，那么上述代码中的`a`的初始值就是0，如果不指定的话，`a`的初始值就是数组`arr`的第一项了。然后通过遍历将每个值与指定值`val`比较，如果相等，则计数`a`加一，否则加零。这样就可以统计`val`出现的次数。


```
countOccurrences([1, 1, 2, 1, 2, 3], 1); // 3
```

#### deepFlatten


```
const deepFlatten = arr => [].concat(...arr.map(v => (Array.isArray(v) ? deepFlatten(v) : v)));
```
目的：将数组深层次展开。

解析：该方法使用了递归。首先使用`concat()`进行连接，因为`concat()`的参数可以是非数组。然后使用`map()`方法对每一项进行遍历，如果是数组，则递归，如果不是数组，则返回由该元素组成的新数组。最后通过展开运算符`...`将数组展开为非数组。


```
deepFlatten([1, [2], [[3], 4], 5]); // [1,2,3,4,5]
```

#### difference


```
const difference = (a, b) => {
  const s = new Set(b);
  return a.filter(x => !s.has(x));
};
```
目的：返回两数组不同的元素(b中没有的元素)。

解析：首先根据b参数新建一个`Set`集合(去重)，然后在a参数上使用`Array.filter()`，保留b参数中没有的元素并返回该新数组。


```
difference([1, 2, 3], [1, 2, 4]); // [3]
```

#### differenceWith


```
const differenceWith = (arr, val, comp) => arr.filter(a => val.findIndex(b => comp(a, b)) === -1);
```
目的：过滤掉数组中比较函数返回false的所有值。

解析：使用`Array.filter()`和`Array.findIndex()`找到合适的值。


```
differenceWith([1, 1.2, 1.5, 3, 0], [1.9, 3, 0], (a, b) => Math.round(a) === Math.round(b)); // [1, 1.2]
```

#### distinctValuesOfArray


```
const distinctValuesOfArray = arr => [...new Set(arr)];
```
目的：数组去重。

解析：使用Set进行去掉重复的值，然后使用展开运算符`...`展开为数组内的值。


```
distinctValuesOfArray([1, 2, 2, 3, 4, 4, 5]); // [1,2,3,4,5]
```

#### dropElements


```
const dropElements = (arr, func) => {
  while (arr.length > 0 && !func(arr[0])) arr = arr.slice(1);
  return arr;
};
```
目的：移除数组中的值，直到回调函数返回`true`，然后返回剩余元素组成的新数组。

解析：使用`while`循环，判断当数组长度不为0，并且不满足`func`条件时，去除数组的第一项，并将新数组重新赋值给`arr`。只要满足func条件的，就直接返回剩余元素组成的数组。


```
dropElements([1, 2, 3, 4], n => n >= 3); // [3,4]
```

#### dropRight


```
const dropRight = (arr, n = 1) => arr.slice(0, -n);
```
目的：移除右侧`n`个元素，并返回新数组。默认移除右侧一个元素。

解析：`slice()`第二个参数支持负值，表示原数组的倒数第几个元素。这样可以实现去除右侧n个元素。


```
dropRight([1, 2, 3]); // [1,2]
dropRight([1, 2, 3], 2); // [1]
dropRight([1, 2, 3], 42); // []
```

#### everyNth


```
const everyNth = (arr, nth) => arr.filter((e, i) => i % nth === nth - 1);
```
目的：返回数组中的每个第n个元素。

解析：使用`filter()`返回特定元素的新数组。nth - 1是因为nth与i相差1，一个是第几个元素，一个是索引。


```
everyNth([1, 2, 3, 4, 5, 6], 2); // [ 2, 4, 6 ]

// 该方法第二个参数如果传入小数，那么返回值就会异常。是不是加上round()方法更好点？
everyNth([1, 2, 3, 4, 5, 6], 1.5); // [3, 6]
```

#### filterNonUnique


```
const filterNonUnique = arr => arr.filter(i => arr.indexOf(i) === arr.lastIndexOf(i));
```
目的：过滤掉数组中只出现过一次的值。

解析：巧妙的使用`arr.indexOf(i) === arr.lastIndexOf(i)`来判断某元素是否只出现过一次。从左数和从右数都是同一位置就可以确定只出现过一次。


```
filterNonUnique([1, 2, 2, 3, 4, 4, 5]); // [1,3,5]
```

#### flatten


```
const flatten = arr => [].concat(...arr);
```
目的：铺平数组。（一层）

解析：使用一个新的数组调用`Array.concat()`和展开运算符`...`来包含数组的浅层嵌套。


```
flatten([1, [2], 3, 4]); // [1,2,3,4]

[...[1,[2],3,4]] // [1,[2],3,4]
```

#### flattenDepth


```
const flattenDepth = (arr, depth = 1) =>
  depth != 1
    ? arr.reduce((a, v) => a.concat(Array.isArray(v) ? flattenDepth(v, depth - 1) : v), [])
    : arr.reduce((a, v) => a.concat(v), []);
```
目的：将数组铺平到指定深度。默认1

解析：使用递归，每个深度级别减1深度。 使用`Array.reduce()`和`Array.concat()`来合并元素或数组。 基本情况下，深度等于1的时候停止递归。 省略第二个元素，深度只能展平到1的深度（单个展平）。


```
flattenDepth([1, [2], 3, 4]); // [1,2,3,4]
```

#### forEachRight


```
const forEachRight = (arr, callback) =>
  arr
    .slice(0)
    .reverse()
    .forEach(callback);
```
目的：从数组的最后一个元素开始，为每个数组元素执行一次提供的函数。

解析：使用`reverse()`将数组元素翻转，由于该方法会修改原数组，所以使用`slice(0)`将数组复制一份，防止对原数组造成影响。最后使用`forEach()`迭代翻转后的数组并返回结果。


```
forEachRight([1, 2, 3, 4], val => console.log(val)); // '4', '3', '2', '1'
```

#### groupBy


```
const groupBy = (arr, func) =>
  arr.map(typeof func === 'function' ? func : val => val[func]).reduce((acc, val, i) => {
    acc[val] = (acc[val] || []).concat(arr[i]);
    return acc;
  }, {});
```

目的：基于特定函数将数组分组。

解析：使用`Array.map()`将数组的值映射到函数或属性名称。 使用`Array.reduce()`创建一个对象，其中的keys是从映射结果中产生的。


```
groupBy([6.1, 4.2, 6.3], Math.floor); // {4: [4.2], 6: [6.1, 6.3]}
groupBy(['one', 'two', 'three'], 'length'); // {3: ['one', 'two'], 5: ['three']}
```

#### indexOfAll


```
const indexOfAll = (arr, val) => {
  const indices = [];
  arr.forEach((el, i) => el === val && indices.push(i));
  return indices;
};
```
目的：返回特定值在数组中所有的索引，如果不存在，返回`[]`

解析：遍历数组每一项，如果与特定值全等，则将当前索引添加到要返回的数组中。


```
indexOfAll([1, 2, 3, 1, 2, 3], 1); // [0,3]
indexOfAll([1, 2, 3], 4); // []
```

#### initialize2DArray


```
const initialize2DArray = (w, h, val = null) =>
  Array.from({ length: h }).map(() => Array.from({ length: w }).fill(val));
```
目的：初始化给定宽、高、值的二维数组。

解析：使用`Array.from()`生成h项，其中每个项都是一个长度为w的新数组。如果未提供该值，则默认为`null`。使用`Array.fill()`对数组填充特定值。


```
initialize2DArray(2, 2, 0); // [[0,0], [0,0]]

// 根据该方法，可以得到一个初始化数组的办法
Array.from({length:n}).fill(null); //初始化一个长度为n，值为null的数组
```

#### initializeArrayWithRange


```
const initializeArrayWithRange = (end, start = 0, step = 1) =>
  Array.from({ length: Math.ceil((end + 1 - start) / step) }).map((v, i) => i * step + start);
```
目的：初始化一个数组，其中包含开始和结束在内的具有指定范围的数字，也可以指定阶段数字。

解析：使用`Array.from({ length: Math.ceil((end + 1 - start) / step) })`创建一个指定长度的数组，然后遍历每一项都加上`start`值和`step`。


```
initializeArrayWithRange(5); // [0,1,2,3,4,5]
initializeArrayWithRange(7, 3); // [3,4,5,6,7]
initializeArrayWithRange(9, 0, 2); // [0,2,4,6,8]
```

#### initializeArrayWithValues


```
const initializeArrayWithValues = (n, val = 0) => Array(n).fill(val);
```
目的：初始化并使用特定值填充一个数组。

解析：`Array(n)`初始化一个长度为n的数组。是否用`Array.from({length:n})`更好？因为`Array(n)`有时候会有歧义。


```
initializeArrayWithValues(5, 2); // [2,2,2,2,2]
```

#### intersection


```
const intersection = (a, b) => {
  const s = new Set(b);
  return a.filter(x => s.has(x));
};
```
目的：返回由两个数组都存在的元素组成的新数组。与`difference`方法刚好相反。


```
intersection([1, 2, 3], [4, 3, 2]); // [2,3]
```

#### isSorted


```
const isSorted = arr => {
  const direction = arr[0] > arr[1] ? -1 : 1;
  for (let [i, val] of arr.entries())
    if (i === arr.length - 1) return direction;
    else if ((val - arr[i + 1]) * direction > 0) return 0;
};
```

目的：如果数组按升序排序，则返回1;如果按降序排序，则返回-1;如果未排序，则返回0。

解析：计算前两个元素的排序方向。 使用`Object.entries()`循环访问数组对象并将它们成对比较。如果到达最后一个元素，则返回`direction`的值。如果是正序，那么`direction`初始值为1，当前项减去下一项肯定小于等于0，所以如果大于0，则可以判定为乱序。倒序的逻辑亦然。


```
isSorted([0, 1, 2, 2]); // 1
isSorted([4, 3, 2]); // -1
isSorted([4, 3, 5]); // 0
```

#### longestItem

```
const longestItem = (...vals) => [...vals].sort((a, b) => b.length - a.length)[0];
```
目的：允许任意数量的可迭代对象或者具有`length`属性的对象，并返回长度最长的那个对象。

解析：使用`Array.sort()`将所有参数根据`length`进行排序，并返回排好序数组的第一个元素（长度最长）。


```
longestItem('this', 'is', 'a', 'testcase'); // 'testcase'
longestItem(...['a', 'ab', 'abc']); // 'abc'
longestItem(...['a', 'ab', 'abc'], 'abcd'); // 'abcd'
longestItem([1, 2, 3], [1, 2], [1, 2, 3, 4, 5]); // [1, 2, 3, 4, 5]
longestItem([1, 2, 3], 'foobar'); // 'foobar'
```

#### maxN


```
const maxN = (arr, n = 1) => [...arr].sort((a, b) => b - a).slice(0, n);
```
目的：从提供的数组中返回n个最大元素。 如果n大于或等于提供的数组长度，则返回原始数组（按降序排列）。

解析：使用`Array.sort()`与展开运算符`...`结合来创建数组的浅复制并按降序对其进行排序。使用`slice()`来截取前n个最大元素。默认返回1个最大元素。


```
maxN([1, 2, 3]); // [3]
maxN([1, 2, 3], 2); // [3,2]
```

#### minN


```
const minN = (arr, n = 1) => [...arr].sort((a, b) => a - b).slice(0, n);
```
目的：与`maxN`目的相反，从提供的数组中返回n个最小元素。

解析：使用`sort()`的回调函数返回`a - b`来实现升序排序，即a比b小，a就在b前面。而`maxN`的恰好相反，是`b - a`，实现降序排序。


```
minN([1, 2, 3]); // [1]
minN([1, 2, 3], 2); // [1,2]
```

#### nthElement


```
const nthElement = (arr, n = 0) => (n > 0 ? arr.slice(n, n + 1) : arr.slice(n))[0];
```

目的：返回数组的第n个元素。默认n为0

解析：使用`Array.slice()`来截取需要的数组。如果n大于0，那么就返回由当前值组成的新数组，如果n为0，则浅复制当前数组并返回，最后取新数组的第一项。疑问：直接`arr.slice(n, n + 1)`不是更具有通用性吗，哪怕n为0，也可以截取第一项。而且该方法不会修改原数组。


```
nthElement(['a', 'b', 'c'], 1); // 'b'
nthElement(['a', 'b', 'b'], -3); // 'a'
```

#### partition


```
const partition = (arr, fn) =>
  arr.reduce(
    (acc, val, i, arr) => {
      acc[fn(val, i, arr) ? 0 : 1].push(val);
      return acc;
    },
    [[], []]
  );
```

目的：根据所提供的函数对每个元素的布尔值将这些元素分成两个数组。如果提供的函数返回true，则放在二维数组的第一个数组中；否则，放在第二个数组中。


```
const users = [{ user: 'barney', age: 36, active: false }, { user: 'fred', age: 40, active: true }];
partition(users, o => o.active); // [[{ 'user': 'fred',    'age': 40, 'active': true }],[{ 'user': 'barney',  'age': 36, 'active': false }]]
```

#### pick


```
const pick = (obj, arr) =>
  arr.reduce((acc, curr) => (curr in obj && (acc[curr] = obj[curr]), acc), {});
```
目的：从对象中挑选给定的键值对。

解析：如果数组中的项存在于obj中，那么就将键值对赋值到一个空对象中。使用了逗号操作符`,`，每次都会返回`acc`，以供下次使用。


```
pick({ a: 1, b: '2', c: 3 }, ['a', 'c']); // { 'a': 1, 'c': 3 }
```

#### pull


```
const pull = (arr, ...args) => {
  let argState = Array.isArray(args[0]) ? args[0] : args;
  let pulled = arr.filter((v, i) => !argState.includes(v));
  arr.length = 0;
  pulled.forEach(v => arr.push(v));
};
```
目的：改变原始数组以过滤出指定的值。会改变原数组。

解析：首先判断剩余参数组成的数组`args`的第一项是否为数组，如果是，那么就取用第一项，如果不是数组，就取用`args`这个数组。通过`filter()`和`includes()`过滤出`arr`中参数未指定的所有项组成的数组`pulled`，重写`arr`数组通过遍历将`pulled`中元素赋值与`arr`。

需要注意的是，`args`是数组。


```
let myArray = ['a', 'b', 'c', 'a', 'b', 'c'];
pull(myArray, 'a', 'c'); // myArray = [ 'b', 'b' ]
```

#### pullAtIndex


```
const pullAtIndex = (arr, pullArr) => {
  let removed = [];
  let pulled = arr
    .map((v, i) => (pullArr.includes(i) ? removed.push(v) : v))
    .filter((v, i) => !pullArr.includes(i));
  arr.length = 0;
  pulled.forEach(v => arr.push(v));
  return removed;
};
```
目的：改变原始数组过滤出指定索引的值。会改变原数组。

解析：与`pull`很相近，不同的是，该方法会将指定索引对应的值使用新数组返回。


```
let myArray = ['a', 'b', 'c', 'd'];
let pulled = pullAtIndex(myArray, [1, 3]); // myArray = [ 'a', 'c' ] , pulled = [ 'b', 'd' ]
```

#### reducedFilter


```
const reducedFilter = (data, keys, fn) =>
  data.filter(fn).map(el =>
    keys.reduce((acc, key) => {
      acc[key] = el[key];
      return acc;
    }, {})
  );
```

目的：根据条件过滤一个对象数组，同时过滤未指定的键。

解析：使用`filter`过滤出符合fn的数组，通过遍历将指定的键与对应的值赋值到新的空对象中并返回。


```
const data = [
  {
    id: 1,
    name: 'john',
    age: 24
  },
  {
    id: 2,
    name: 'mike',
    age: 50
  }
];

reducedFilter(data, ['id', 'name'], item => item.age > 24); // [{ id: 2, name: 'mike'}]
```

#### remove


```
const remove = (arr, func) =>
  Array.isArray(arr)
    ? arr.filter(func).reduce((acc, val) => {
        arr.splice(arr.indexOf(val), 1);
        return acc.concat(val);
      }, [])
    : [];
```
目的：从数组中移除让指定函数返回`false`的元素。


```
remove([1, 2, 3, 4], n => n % 2 == 0); // [2, 4]
```

#### sample


```
const sample = arr => arr[Math.floor(Math.random() * arr.length)];
```

目的：从数组返回一个随机元素。

解析：获取一个[0,arr.length)的范围并向下取整，也就是[0,arr.length - 1]的范围。这刚好是数组的索引范围。于是就可以巧妙的获取随机元素。


```
sample([3, 7, 9, 11]); // 9
```

#### sampleSize


```
const sampleSize = ([...arr], n = 1) => {
  let m = arr.length;
  while (m) {
    const i = Math.floor(Math.random() * m--);
    [arr[m], arr[i]] = [arr[i], arr[m]];
  }
  return arr.slice(0, n);
};
```
目的：从数组中获取指定个数的随机元素，最多返回与原数组数组相同大小的新数组。

解析：使用 `Fisher-Yates` 算法将数组打乱顺序，简单的说就是每次将指定位和小于指定未的随机位置调换顺序。然后获取数组的前`n`项并返回。`n`默认为1。


```
sampleSize([1, 2, 3], 2); // [3,1]
sampleSize([1, 2, 3], 4); // [2,3,1]
```

#### shuffle


```
const shuffle = ([...arr]) => {
  let m = arr.length;
  while (m) {
    const i = Math.floor(Math.random() * m--);
    [arr[m], arr[i]] = [arr[i], arr[m]];
  }
  return arr;
};
```
目的：打乱数组排序。

解析：与`sampleSize`很相似，只不过是直接返回新数组，而不是进行截取。


```
const foo = [1, 2, 3];
shuffle(foo); // [2,3,1], foo = [1,2,3]
```

#### similarity


```
const similarity = (arr, values) => arr.filter(v => values.includes(v));
```

目的：返回由出现在两个数组中的元素组成的数组。

解析：通过遍历`arr`过滤出`values`数组包括的元素。并返回新数组。


```
similarity([1, 2, 3], [1, 2, 4]); // [1,2]
```

#### sortedIndex


```
const sortedIndex = (arr, n) => {
  const isDescending = arr[0] > arr[arr.length - 1];
  const index = arr.findIndex(el => (isDescending ? n >= el : n <= el));
  return index === -1 ? arr.length : index;
};
```
目的：返回指定值应该插入到数组中的最低索引，以保持其按照顺序排序。

解析：首先通过判断参数数组第一项与最后一项的大小来判断是否是降序(这就预示着数组传入的时候就需要按照某种顺序排序，否则判断就不准确)。然后通过`findIndex`来返回索引值，如果索引值为-1，那么就返回数组长度的这个索引(当前数组最后一位的下一位)。


```
sortedIndex([5, 3, 2, 1], 4); // 1
sortedIndex([30, 50], 40); // 1
```

#### symmetricDifference


```
const symmetricDifference = (a, b) => {
  const sA = new Set(a),
    sB = new Set(b);
  return [...a.filter(x => !sB.has(x)), ...b.filter(x => !sA.has(x))];
};
```
目的：返回两个数组之间的对称差异。


```
symmetricDifference([1, 2, 3], [1, 2, 4]); // [3,4]
```

#### tail


```
const tail = arr => (arr.length > 1 ? arr.slice(1) : arr);
```
目的：返回数组除第一个元素之外的所有元素。

解析：使用`slice(1)`来截取首项元素以后的所有元素。如果数组长度小于等于1，直接返回原数组。


```
tail([1, 2, 3]); // [2,3]
tail([1]); // [1]
```

#### take


```
const take = (arr, n = 1) => arr.slice(0, n);
```
目的：获取从头开始的n个元素的数组。


```
take([1, 2, 3], 5); // [1, 2, 3]
take([1, 2, 3], 0); // []
```

#### takeRight


```
const takeRight = (arr, n = 1) => arr.slice(arr.length - n, arr.length);
```
目的：获取从尾部开始的n个元素的数组。

解析：还是使用`slice(arr.length - n, arr.length)`来截取数组。疑问：直接`slice(arr.length - n)`不也可以截取指定位置到末尾的数组吗？感觉不需要指定第二个参数。


```
takeRight([1, 2, 3], 2); // [ 2, 3 ]
takeRight([1, 2, 3]); // [3]
```

#### union


```
const union = (a, b) => Array.from(new Set([...a, ...b]));
```
目的：返回由两个数组元素组成的并去重的新数组。

解析：使用`Set`集合进行数组去重，然后通过`from()`将`Set`集合转为数组(因为`Set`集合有length属性)。


```
union([1, 2, 3], [4, 3, 2]); // [1,2,3,4]
```

#### without


```
const without = (arr, ...args) => arr.filter(v => !args.includes(v));
```
目的：过滤掉数组中指定的值。

解析：遍历数组，判断每项是否包含在由剩余参数组成的数组中，过滤出由不包括的值组成的数组。


```
without([2, 1, 2, 3], 1, 2); // [3]
```

#### zip


```
const zip = (...arrays) => {
  const maxLength = Math.max(...arrays.map(x => x.length));
  return Array.from({ length: maxLength }).map((_, i) => {
    return Array.from({ length: arrays.length }, (_, k) => arrays[k][i]);
  });
};
```
目的：创建一个二元数组，根据原始数组中的位置进行分组。


```
zip(['a', 'b'], [1, 2], [true, false]); // [['a', 1, true], ['b', 2, false]]
zip(['a'], [1, 2], [true, false]); // [['a', 1, true], [undefined, 2, false]]
```

#### zipObject


```
const zipObject = (props, values) =>
  props.reduce((obj, prop, index) => ((obj[prop] = values[index]), obj), {});
```
目的：给定一组有效的属性标识符和一组值，返回一个将属性关联到值的对象。

解析：将第一个数组与第二个数组的每一项一一对应形成对象的键值对`obj[prop] = values[index]`，然后使用`reduce()`对每一项进行操作。


```
zipObject(['a', 'b', 'c'], [1, 2]); // {a: 1, b: 2, c: undefined}
zipObject(['a', 'b'], [1, 2, 3]); // {a: 1, b: 2}
```







