---
layout:     post                   
title:      JavaScript数组方法总结  
subtitle:   数组的方法确实很多
date:       2018-01-11
author:     chuck
header-img: img/home-bg-array.jpg
catalog: true                      
tags:                               
    - JavaScript
    - Array
---

每每用到数组方法的时候，都会多多少少产生一些困惑，比如`slice`和`splice`方法，总是傻傻分不清楚，而且最关键的是，总分不清哪些方法是修改过原数组的，所以，我打算将所有的数组方法一一记录下来，以备查阅。

### Array方法

#### concat()

`concat()`方法用于合并两个或多个数组。此方法不会更改现有数组，而是返回一个新数组。[Array.prototype.concat()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/concat)

语法：

```
var new_array = old_array.concat(value1[, value2[, ...[, valueN]]])
```

`concat`方法不会改变`this`或任何作为参数提供的数组，而是返回一个浅拷贝，它包含与原始数组相结合的相同元素的副本。所以可以通过调用`array.concat()`来实现浅拷贝。

注意点：

1. 返回拼接的新数组；

2. **不修改原数组和参数数组**；

3. 参数可以是非数组。


```
var alpha = ['a', 'b', 'c'];
var numeric = [1, 2, 3];

alpha.concat(numeric);
// result in ['a', 'b', 'c', 1, 2, 3]
```

#### every()

`every() `方法测试数组的所有元素是否都通过了指定函数的测试。[Array.prototype.every()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/every)

语法：

```
arr.every(callback[, thisArg])
```

`every` 方法为数组中的每个元素执行一次 callback 函数，直到它找到一个使 callback 返回 `false`（表示可转换为布尔值 false 的值）的元素。如果发现了一个这样的元素，every 方法将会立即返回 `false`。否则，callback 为每一个元素返回 `true`，`every` 就会返回 `true`。

`every` 遍历的元素范围在第一次调用 callback 之前就已确定了。在调用 `every` 之后添加到数组中的元素不会被 callback 访问到。

注意点：

1. 若全部通过测试，函数返回值 true，中途退出，返回 false；

2. **不对原数组产生影响**。


```
function isBigEnough(element, index, array) {
  return (element >= 10);
}
var passed = [12, 5, 8, 130, 44].every(isBigEnough);
// passed is false
// 因为8 >= 10 返回false
passed = [12, 54, 18, 130, 44].every(isBigEnough);
// passed is true
```

#### some()

`some()` 方法测试数组中的某些元素是否通过由提供的函数实现的测试。[Array.prototype.some()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/some)

该方法与`every()`相似，只不过一个是一个通过就行，一个是全部通过才行。

`some` 为数组中的每一个元素执行一次 callback 函数，直到找到一个使得 callback 返回一个“真值”（即可转换为布尔值 true 的值）。如果找到了这样一个值，`some` 将会立即返回 `true`。否则，`some` 返回 `false`。callback 只会在那些”有值“的索引上被调用，不会在那些被删除或从来未被赋值的索引上调用。

注意点：

1. 若有一个值通过测试，方法就返回值 true；

2. **不对原数组产生影响**。

```
function isBigEnough(element, index, array) {
  return (element >= 10);
}
var passed = [2, 5, 8, 1, 4].some(isBigEnough);
// passed is false
passed = [12, 5, 8, 1, 4].some(isBigEnough);
// passed is true
// 12 >= 10返回true
```



#### filter()

`filter()`方法创建一个新数组, 其包含通过所提供函数实现的测试的所有元素。 

`filter` 为数组中的每个元素调用一次 callback 函数，并利用所有使得 callback 返回 `true` 或 等价于 `true` 的值的元素创建一个新数组。

该方法与`every()`有些类似，只不过`filter()`会将为`true`的值放到一个新数组里并返回。

语法:

```
var new_array = arr.filter(callback[, thisArg])
```

注意点:

1. 返回一个满足过滤条件的新数组；

2. **不会改变原数组**。


```
function isBigEnough(value) {
  return value >= 10;
}

var filtered = [12, 5, 8, 130, 44].filter(isBigEnough);

// filtered is [12, 130, 44]
```

#### forEach()

`forEach()`方法对数组的每个元素执行一次提供的函数。[Array.prototype.forEach()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)

`forEach` 方法按升序为数组中含有效值的每一项执行一次callback 函数，那些已删除（使用`delete`方法等情况）或者未初始化的项将被跳过（但不包括那些值为 `undefined` 的项）（例如在稀疏数组上）。

语法:

```
array.forEach(callback(currentValue, index, array){
    //do something
}, this)
```

注意点：

1. 函数没有返回值，或者说返回`undefined`；

2. **不对原数组产生影响**；

3. 没有办法中止或者跳出 forEach 循环，除了抛出一个异常。


```
function logArrayElements(currentValue, index, array) {
    console.log("a[" + index + "] = " + currentValue);
}

var result = [2, 5, 9].forEach(logArrayElements);
// a[0] = 2
// a[1] = 5
// a[2] = 9
result //undefined
```

#### indexOf()

`indexOf()`方法返回在数组中可以找到一个给定元素的**第一个索引**，如果不存在，则返回-1。[Array.prototype.indexOf()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/indexOf)

语法：

```
arr.indexOf(searchElement[, fromIndex = 0])
```
其中，`fromIndex`参数表示开始查找的位置。如果该索引值大于或等于数组长度，意味着不会在数组里查找，返回-1。如果参数中提供的索引值是一个负值，则将其作为数组末尾的一个抵消，即-1表示从最后一个元素开始查找，-2表示从倒数第二个元素开始查找 ，以此类推。 

注意点：

1. 返回值是找到元素的索引值或 -1；

2. **不对原数组产生影响**；

3. 使用严格相等(===)来进行判断。（题外话：switch case用的也是严格相等来比较。


```
let a = [2, 9, 7, 8, 9]; 
a.indexOf(2); // 0 
a.indexOf(6); // -1
```

#### join()

`join()`方法将数组（或一个类数组对象）的所有元素连接到一个字符串中。[Array.prototype.join()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/join)

语法：

```
str = arr.join()
// 默认为 ","

str = arr.join("")
// 分隔符为空字符串 ""

str = arr.join(separator)
// 分隔符

```

注意点：

1. 返回拼接的字符串，如果 arr.length 为0，则返回空字符串；

2. **不对原数组产生影响**。


```
var a = ['Wind', 'Rain', 'Fire'];
var myVar1 = a.join();      // myVar1的值变为"Wind,Rain,Fire"
var myVar2 = a.join(', ');  // myVar2的值变为"Wind, Rain, Fire"
var myVar3 = a.join(' + '); // myVar3的值变为"Wind + Rain + Fire"
var myVar4 = a.join('');    // myVar4的值变为"WindRainFire"
```

#### lastIndexOf()


`lastIndexOf()`方法返回指定元素（也即有效的 JavaScript 值或变量）在数组中的最后一个的索引，如果不存在则返回 -1。从数组的后面向前查找，从 `fromIndex` 处开始。[Array.prototype.lastIndexOf()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/lastIndexOf)

其实就是`indexOf()`反过来使用。

语法：

```
arr.lastIndexOf(searchElement[, fromIndex = arr.length - 1])
```
其中，`fromIndex`参数表示从此位置开始逆向查找。默认为数组的长度减 1，即整个数组都被查找。如果该值大于或等于数组的长度，则整个数组会被查找。如果为负值，将其视为从数组末尾向前的偏移。即使该值为负，数组仍然会被从后向前查找。如果该值为负时，其绝对值大于数组长度，则方法返回 -1，即数组不会被查找。

注意点：

1. 返回找到的第一个元素的索引；

2. **不对原数组产生影响**；

3. 使用严格相等来进行判断。


```
var array = [2, 5, 9, 2];
var index = array.lastIndexOf(2);
// index is 3
index = array.lastIndexOf(7);
// index is -1
index = array.lastIndexOf(2, 3);
// index is 3
index = array.lastIndexOf(2, 2);
// index is 0
index = array.lastIndexOf(2, -2);
// index is 0
index = array.lastIndexOf(2, -1);
// index is 3
```

#### map()

`map()`方法创建一个新数组，其结果是该数组中的每个元素都调用一个提供的函数后返回的结果。[Array.prototype.map()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/map)

`map` 方法会给原数组中的每个元素都按顺序调用一次  callback 函数。callback 每次执行后的返回值（包括 `undefined`）组合起来形成一个新数组。 callback 函数只会在有值的索引上被调用；那些从来没被赋过值或者使用 `delete` 删除的索引则不会被调用。

语法：

```
let new_array = arr.map(function callback(currentValue, index, array) { 
    // Return element for new_array 
}[, thisArg])
```

注意点：

1. 返回一个经过回调函数处理的新数组；

2. **不对原数组产生影响**。


```
var numbers = [1, 4, 9];
var roots = numbers.map(Math.sqrt);
// roots的值为[1, 2, 3], numbers的值仍为[1, 4, 9]
```

#### reduce()

`reduce()`方法对累加器和数组中的每个元素（从左到右）应用一个函数，将其减少为单个值。[Array.prototype.reduce()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)

语法:

```
arr.reduce(callback[, initialValue])
```
具体参数意义：

`initialValue`

[可选] 用作第一个调用 callback的第一个参数的值。 如果没有提供初始值，则将使用数组中的第一个元素。 在没有初始值的空数组上调用 reduce 将报错。

`callback`

回调函数接受四个参数，依次是 `accumulator`（上次处理的结果），`currentValue`（当前元素的值），`index`（当前元素索引），`array`（调用 `reduce` 的数组）。

注意：如果没有提供initialValue，reduce 会从索引1的地方开始执行 callback 方法，跳过第一个索引。如果提供initialValue，从索引0开始。

下面我们再具体看看`reduce`是如何运行的：


```
[0, 1, 2, 3, 4].reduce(function(accumulator, currentValue, currentIndex, array){
    return accumulator + currentValue;
});
```
我们就以上述例子为例来说明一下，callback 被调用四次，每次调用的参数和返回值如下表：


| callback | accumulator | currentValue | currentIndex | array | return value |
| --- | --- | --- | --- | --- | --- |
| first call | 0 | 1 | 1 | [0, 1, 2, 3, 4] | 1 |
| second call | 1 | 2 | 2 | [0, 1, 2, 3, 4] | 3 |
| third call | 3 | 3 | 3 | [0, 1, 2, 3, 4] | 6 |
| fourth call | 6 | 4 | 4 | [0, 1, 2, 3, 4] | 10 |

最终，返回值是最后一次回调函数返回的值(10)。

注意点：

1. 返回最后一次回调函数返回的值；

2. **不对原数组产生影响**。

#### reduceRight()

`reduceRight()`方法接受一个函数作为累加器（accumulator）和数组的每个值（从右到左）将其减少为单个值。[Array.prototype.reduceRight()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/ReduceRight)

该方法与 `Array.prototype.reduce()` 的执行方向相反。其他没什么区别。不再赘述。

注意点:

1. 返回最后一次回调函数返回的值；

2. **不对原数组产生影响**。


-------

#### push()

`push()`方法将一个或多个元素添加到数组的末尾，并**返回新数组的长度**。[Array.prototype.push()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/push)

`push` 方法有意具有通用性。该方法和 `call() `或 `apply() `一起使用时，可应用在类似数组的对象上。`push` 方法根据 `length` 属性来决定从哪里开始插入给定的值。如果 `length` 不能被转成一个数值，则插入的元素索引为 0，包括 `length` 不存在时。当 `length` 不存在时，将会创建它。

注意点:

1. 新添加的元素位于数组末尾；

2. 方法返回值为新数组的长度；

3. **push()方法会改变原数组的长度。**


```
var sports = ["soccer", "baseball"];
var total = sports.push("football", "swimming");

console.log(sports); 
// ["soccer", "baseball", "football", "swimming"]

console.log(total);  
// 4
```

#### pop()

pop()方法从数组中删除最后一个元素，并返回该元素的值。此方法更改数组的长度。[Array.prototype.pop()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/pop)

注意点：

1. 该方法删除数组最后一个元素；

2. 返回的值是删除的值(如果数组为空，返回`undefined`)；

3. **pop()方法会改变原数组的长度**；

4. 该方法不需要传参数，传入参数会忽略。


```
let a = [1, 2, 3];
a.length; // 3

a.pop(); // 3

console.log(a); // [1, 2]
a.length; // 2
```

#### unshift()

`unshift()`方法将一个或多个元素添加到数组的开头，并返回新数组的长度。[Array.prototype.unshift()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/unshift)

该方法与`push()`方法是相反的操作。与`pop()`方法可以组成一个队列的操作，一个在数组头部添加值，一个在数组末尾删除值。

注意点:

1. 新添加的元素位于数组头部；

2. 方法返回值为新数组的长度；

3. **unshift()方法会改变原数组的长度。**


```
let a = [1, 2, 3];
a.unshift(4, 5);

console.log(a);
// [4, 5, 1, 2, 3]
```

#### shift()

`shift()`方法从数组中删除第一个元素，并返回该元素的值。此方法更改数组的长度。[Array.prototype.shift()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/shift)

该方法与`pop()`方法执行相反操作。同样的，`shift()`和`push()`也可以组成一个队列操作。前者将数组头部元素删除，后者将新元素添加到元素尾部。

注意点：

1. 该方法删除数组第一个元素；

2. 返回的值是删除的值(如果数组为空，返回`undefined`)；

3. **shift()方法会改变原数组的长度**；

4. 该方法不需要传参数，传入参数会忽略。


```
let a = [1, 2, 3];
let b = a.shift();

console.log(a); 
// [2, 3]

console.log(b); 
// 1
```

-------


#### reverse()

`reverse()` 方法将数组中元素的位置颠倒。

第一个数组元素成为最后一个数组元素，最后一个数组元素成为第一个。[Array.prototype.reverse()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/reverse)

注意点:

1. 函数返回值是修改了的原数组；

2. **reverse()会修改原数组**。

3. 该方法不需要传参数，传入参数会忽略。


```
var a1 = [1, 2, 3];
var a2 = a1.reverse();
a1 //[3, 2, 1]
a2 //[3, 2, 1]
a1 === a2; //true
```

#### slice()

`slice()` 方法返回一个从开始到结束（不包括结束）选择的数组的一部分浅拷贝到一个新数组对象。**原始数组不会被修改**。(题外话，英文单词的意思是薄片，部分，所以就是把数组的一部分进行浅复制。可以使用`arr.slice()`来实现浅复制的操作。[Array.prototype.slice()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/slice)

语法:

```
arr.slice();
// [0,end]

arr.slice(begin);
// [begin,end]

arr.slice(begin,end);
// [begin,end)
```

注意点：

1. 返回浅拷贝后的新数组；

2. **slice()方法不会改变原数组**。

3. 可以不传参数，也可以传入1个参数或2个参数。传入2个参数时是左闭右开。


```
var a1 = [1, 2, 3, 4, 5];
var a2 = a1.slice(1, 3);
a1 //[1, 2, 3, 4, 5]
a2 //[2, 3]
```

#### splice()

`splice()` 方法通过删除现有元素和/或添加新元素来更改一个数组的内容。**该方法会修改原数组。**[Array.prototype.splice()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/splice)


语法：

```
array.splice(start, deleteCount, item1, item2, ...)
```

参数含义:

`start​`

指定修改的开始位置（从0计数）。如果超出了数组的长度，则从数组末尾开始添加内容；如果是负值，则表示从数组末位开始的第几位（从1计数）；若只使用start参数而不使用deleteCount、item，如：array.splice(start) ，表示删除[start，end]的元素。

`deleteCount` 可选

整数，表示要移除的数组元素的个数。如果 deleteCount 是 0，则不移除元素。这种情况下，至少应添加一个新元素。如果 deleteCount 大于start 之后的元素的总数，则从 start 后面的元素都将被删除（含第 start 位）。
如果deleteCount被省略，则其相当于(arr.length - start)。

`item1, item2, ...` 可选

要添加进数组的元素,从start 位置开始。如果不指定，则 splice() 将只删除数组元素。

返回值为由被删除的元素组成的一个数组。如果只删除了一个元素，则返回只包含一个元素的数组。如果没有删除元素，则返回空数组。

注意点：

1. 返回分割的元素组成的数组；

2. **会对数组进行修改，原数组会减去分割数组。**


```
var a1 = [1, 2, 3, 4];
var a2 = a1.splice(1, 2);
a1 //[1, 4]
a2 //[2, 3]
a1 = [1, 2, 3, 4];
a2 = a1.splice(1, 2, 5, 6);
a1 //[1, 5, 6, 4]
```

#### sort()

`sort()` 方法在适当的位置对数组的元素进行排序，并返回数组。 `sort` 排序不一定是稳定的。默认排序顺序是根据字符串Unicode码点。[Array.prototype.sort()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)

如果没有指定回调函数参数，那么元素会按照转换为的字符串的诸个字符的Unicode位点进行排序。例如 "Banana" 会被排列到 "cherry" 之前。数字比大小时，9 出现在 80 之前，但这里比较时数字会先被转换为字符串，所以 "80" 比 "9" 要靠前。

如果指明了参数 ，那么数组会按照调用该函数的返回值排序。即 a 和 b 是两个将要被比较的元素：

* 如果 compareFunction(a, b) 小于 0 ，那么 a 会被排列到 b 之前；
* 如果 compareFunction(a, b) 等于 0 ， a 和 b 的相对位置不变。备注： ECMAScript 标准并不保证这一行为，而且也不是所有浏览器都会遵守（例如 Mozilla 在 2003 年之前的版本）；
* 如果 compareFunction(a, b) 大于 0 ， b 会被排列到 a 之前。
* compareFunction(a, b) 必须总是对相同的输入返回相同的比较结果，否则排序的结果将是不确定的。

注意点:

1. 返回排序后的数组。原数组已经被排序后的数组代替；

2. **sort()方法会改变原数组**。

```
var numbers = [4, 2, 5, 1, 3];
numbers.sort(function(a, b) {
  return a - b;
});
console.log(numbers);

// [1, 2, 3, 4, 5]
```

-------

下面是ES6新增的数组的方法，也来总结一下。

#### copyWithin()

`copyWithin()` 方法浅复制数组的一部分到同一数组中的另一个位置，并返回它，而不修改其大小。[Array.prototype.copyWithin()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/copyWithin)

语法：

```
arr.copyWithin(target, start, end)
```
参数含义：

`target`

0 为基底的索引，复制序列到该位置。如果是负数，target 将从末尾开始计算。
如果 target 大于等于 arr.length，将会不发生拷贝。如果 target 在 start 之后，复制的序列将被修改以符合 arr.length。

`start`

0 为基底的索引，开始复制元素的起始位置。如果是负数，start 将从末尾开始计算。
如果 start 被忽略，copyWithin 将会从0开始复制。

`end`

0 为基底的索引，开始复制元素的结束位置。copyWithin 将会拷贝到该位置，但不包括 end 这个位置的元素。如果是负数， end 将从末尾开始计算。
如果 end 被忽略，copyWithin 将会复制到 arr.length。


```
[1, 2, 3, 4, 5].copyWithin(-2);
// [1, 2, 3, 1, 2]

[1, 2, 3, 4, 5].copyWithin(0, 3);
// [4, 5, 3, 4, 5]

[1, 2, 3, 4, 5].copyWithin(0, 3, 4);
// [4, 2, 3, 4, 5]

[1, 2, 3, 4, 5].copyWithin(-2, -3, -1);
// [1, 2, 3, 3, 4]
```

该方法与`fill()`方法实际用途都是用于定型数组。平常情况下，一般用不到该方法。

注意点：

1. 返回修改了的原数组；

2. **会对数组进行修改**，且是浅拷贝；

3. 参数可负，负值时倒推，且 end 为空表示数组长度。

#### fill()

`fill()`方法用一个固定值填充一个数组中从起始索引到终止索引内的全部元素。[Array.prototype.fill()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/fill)

语法:

```
arr.fill(value, start, end)
```

具体要填充的元素区间是 [start, end) , 一个半开半闭区间.

fill 方法接受三个参数 value, start 以及 end. start 和 end 参数是可选的, 其默认值分别为 0 和 this 对象的 length 属性值.

如果 start 是个负数, 则开始索引会被自动计算成为 length+start, 其中 length 是 this 对象的 length 属性值. 如果 end 是个负数, 则结束索引会被自动计算成为 length+end.

注意点:

1. 返回修改后的数组。

2. fill()方法会改变原数组。

```
[1, 2, 3].fill(4)            // [4, 4, 4]
[1, 2, 3].fill(4, 1)         // [1, 4, 4]
[1, 2, 3].fill(4, 1, 2)      // [1, 4, 3]
[1, 2, 3].fill(4, 1, 1)      // [1, 2, 3]
[1, 2, 3].fill(4, -3, -2)    // [4, 2, 3]
[1, 2, 3].fill(4, NaN, NaN)  // [1, 2, 3]
Array(3).fill(4);            // [4, 4, 4]
```

#### find()和findIndex()

`find()`方法返回数组中满足提供的测试函数的第一个元素的值。**否则返回 `undefined`**。
`findIndex()`方法返回数组中满足提供的测试函数的第一个元素的索引。**否则返回`-1`**。

两个方法都接收两个参数:一个是回调函数；另一个是可选参数，用于指定回调函数中this的值。回调函数的参数与传入`map()`方法的参数相同。

如果给定的值满足定义的标准，回调函数应返回`true`，一旦回调函数返回`true`，两个方法都会立即停止搜索数组剩余的部分。

注意点：

1. `find()`方法返回查找到的值；

2. `findIndex()`方法返回查找到的值的索引；

3. **`find()`方法和`findIndex()`方法不会改变原数组**。

下例参考尼古拉斯大神的书籍《深入理解ES6》P.217

```
let numbers = [25,30,35,40,45];

console.log(numbers.find(n = n > 33));  // 35
console.log(numbers.findIndex(n = n > 33));  // 2
```

-------

下面三个方法跟数组迭代器有关，包括`keys()、values()和entries()`。

#### keys()

`keys()` 方法返回一个新的Array迭代器，它包含数组中每个索引的键。[Array.prototype.keys()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/keys)


```
var arr = ["a", "b", "c"];
var iterator = arr.keys();

console.log(iterator.next()); // { value: 0, done: false }
console.log(iterator.next()); // { value: 1, done: false }
console.log(iterator.next()); // { value: 2, done: false }
console.log(iterator.next()); // { value: undefined, done: true }
```

注意点：

1. 函数返回一个迭代器对象；

2. **不会改变原数组**。

#### values()

`values() `方法返回一个新的 Array Iterator 对象，该对象包含数组每个索引的值。[Array.prototype.values()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/values)

**Chrome 未实现，Firefox未实现，Edge已实现。**


```
let arr = ['w', 'y', 'k', 'o', 'p'];
let eArr = arr.values();
console.log(eArr.next().value); // w
console.log(eArr.next().value); // y
console.log(eArr.next().value); // k
console.log(eArr.next().value); // o
console.log(eArr.next().value); // p
```

注意点：

1. 函数返回一个迭代器对象；

2. **不会改变原数组**；

3. 主流浏览器未支持，不要使用。

#### entries()

`entries()` 方法返回一个新的Array Iterator对象，该对象包含数组中每个索引的键/值对。[Array.prototype.entries()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/entries)


```
var arr = ["a", "b", "c"];
var iterator = arr.entries();
// undefined

for (let e of iterator) {
    console.log(e);
}

// [0, "a"] 
// [1, "b"] 
// [2, "c"]
```

注意点：

1. 函数返回一个迭代器对象；

2. **不会改变原数组**。


-------

下面三个方法是Array上的方法，而非原型上的方法，包括`from()、isArray()和of()`。所以**不存在是否改变原数组的问题**。

#### from()

`Array.from()` 方法从一个类似数组或可迭代对象中创建一个新的数组实例。最常用的用法是将获取的`NodeList`类数组转换为真正的数组，从而使用数组原型上的方法。[Array.from()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/from)

`Array.from()` 可以通过以下方式来创建数组对象：

* 伪数组对象（拥有一个 length 属性和若干索引属性的任意对象）
* 可迭代对象（可以获取对象中的元素,如 Map和 Set 等）

注意点:

1. from() 的 length 属性为 1 。

2. 返回一个新的数组实例。


```
Array.from('foo');  //拥有length属性
// ["f", "o", "o"]

let m = new Map([[1, 2], [2, 4], [4, 8]]); // 可迭代对象
Array.from(m); 
// [[1, 2], [2, 4], [4, 8]]

function f() {
  return Array.from(arguments);// 类数组
}

f(1, 2, 3);

// [1, 2, 3]
```

#### isArray()

`Array.isArray()` 用于确定传递的值是否是一个 Array。如果对象是 Array ，则返回true，否则为false。[Array.isArray()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/isArray)


```
Array.isArray([1, 2, 3]);  
// true
Array.isArray({foo: 123}); 
// false
Array.isArray("foobar");   
// false
Array.isArray(undefined);  
// false
```

注意点：

1. 返回值为布尔值。


**Polyfill：**


```
if (!Array.isArray) {
  Array.isArray = function(arg) {
    return Object.prototype.toString.call(arg) === '[object Array]';
  };
}
```

#### of()

`Array.of()` 方法创建一个具有可变数量参数的新数组实例，而不考虑参数的数量或类型。[Array.of()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/of)

`Array.of()` 和 `Array` 构造函数之间的区别在于处理整数参数：`Array.of(7)` 创建一个具有单个元素 7 的数组，而 `Array(7) `创建一个包含 7 个 undefined 元素的数组。


```
Array.of(7);       // [7] 
Array.of(1, 2, 3); // [1, 2, 3]
Array.of(undefined); // [undefined]

Array(7);          // [ , , , , , , ]
Array(1, 2, 3);    // [1, 2, 3]
```

注意点：

1. 返回新的 `Array` 实例。

