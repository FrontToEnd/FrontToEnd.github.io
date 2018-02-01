---
layout:     post                   
title:      30 seconds of code代码库方法解读(Object篇)
subtitle:   实现办法实在让人想不到
date:       2018-02-01
author:     chuck
header-img: img/home-bg-array.jpg
catalog: true                      
tags:                               
    - JavaScript
    - ES6
    - 源代码
---


### Object

#### bindAll

```
const bindAll = (obj, ...fns) =>
  fns.forEach(
    fn => (
      (f = obj[fn]),
      (obj[fn] = function() {
        return f.apply(obj);
      })
    )
  );
```
目的：使用`Array.forEach()`返回一个函数，该函数使用`Function.apply()`将给定的上下文（obj）应用于指定的每个函数的fn。


```
var view = {
  label: 'docs',
  click: function() {
    console.log('clicked ' + this.label);
  }
};
bindAll(view, 'click');
jQuery(element).on('click', view.click); // Logs 'clicked docs' when clicked.
```

#### deepClone

```
const deepClone = obj => {
  let clone = Object.assign({}, obj);
  Object.keys(clone).forEach(
    key => (clone[key] = typeof obj[key] === 'object' ? deepClone(obj[key]) : obj[key])
  );
  return clone;
};
```
目的：对一个对象进行深复制。

解析：首先使用`Object.assign()`创建浅复制后的副本。然后遍历该副本，如果某键值对是引用类型，那么进行递归调用，如果是基本类型，则将当前值赋值给副本同名的key。

```
const a = { foo: 'bar', obj: { a: 1, b: 2 } };
const b = deepClone(a); // a !== b, a.obj !== b.obj
```

#### equals

```
const equals = (a, b) => {
  if (a === b) return true;
  if (a instanceof Date && b instanceof Date) return a.getTime() === b.getTime();
  if (!a || !b || (typeof a != 'object' && typeof b !== 'object')) return a === b;
  if (a === null || a === undefined || b === null || b === undefined) return false;
  if (a.prototype !== b.prototype) return false;
  let keys = Object.keys(a);
  if (keys.length !== Object.keys(b).length) return false;
  return keys.every(k => equals(a[k], b[k]));
};
```
目的：对两个值进行深入比较来确定是否完全相等。

解析：我们来看下这个版本的`equals()`如何实现：

1. 如果两参数是`Date`类型，那么就通过`getTime()`获取时间戳，如果时间戳不相等，则表明参数不相等；
2. 如果两参数是6种假值，比如空字符串等等，则进行恒等比较。如果两参数为函数，也进行恒等比较；
3. 如果任一参数为`null`或`undefined`,就直接返回`false`。因为第一个判断就直接否认了两参数同时为`null`或`undefined`的情况，而且`null`或`undefined`只与自身严格相等；
4. 如果两参数的原型不同，则表示不相等；
5. 使用`Object.keys()`检查两个值是否具有相同的键数，然后使用`Array.every()`检查第一个值中的每个键是否存在于第二个键中，如果 它们通过递归调用则是等价的。


```
equals({ a: [2, { e: 3 }], b: [4], c: 'foo' }, { a: [2, { e: 3 }], b: [4], c: 'foo' }); // true
```

#### findKey

```
const findKey = (obj, fn) => Object.keys(obj).find(key => fn(obj[key], key, obj));
```
目的：返回满足提供的测试函数的第一个键。 否则返回`undefined`。

解析：使用`Array.find()`方法查找适合回调函数的第一个key。如果查到合适的，直接返回，并终止查找。

```
findKey(
  {
    barney: { age: 36, active: true },
    fred: { age: 40, active: false },
    pebbles: { age: 1, active: true }
  },
  o => o['active']
); // 'barney'
```

#### forOwn

```
const forOwn = (obj, fn) => Object.keys(obj).forEach(key => fn(obj[key], key, obj));
```
目的：迭代对象的所有属性，并调用回调函数。


```
forOwn({ foo: 'bar', a: 1 }, v => console.log(v)); // 'bar', 1
```

#### functions

```
const functions = (obj, inherited = false) =>
  (inherited
    ? [...Object.keys(obj), ...Object.keys(Object.getPrototypeOf(obj))]
    : Object.keys(obj)
  ).filter(key => typeof obj[key] === 'function');
```
目的：返回由对象可枚举属性名称组成的数组。如果第二参数为true，则包括其原型链上的属性。

解析：使用`Object.keys()`获取对象自身属性，使用`Object.getPrototypeOf()`获取对象原型链上的属性。最后过滤出类型为`function`的属性数组。

```
function Foo() {
  this.a = () => 1;
  this.b = () => 2;
}
Foo.prototype.c = () => 3;
functions(new Foo()); // ['a', 'b']
functions(new Foo(), true); // ['a', 'b', 'c']
```

#### get

```
const get = (from, ...selectors) =>
  [...selectors].map(s =>
    s
      .replace(/\[([^\[\]]*)\]/g, '.$1.')
      .split('.')
      .filter(t => t !== '')
      .reduce((prev, cur) => prev && prev[cur], from)
  );
```
目的：从给定对象中获取指定的属性。

解析：使用`String.replace()`将`[]`替换为`.`，然后使用`.`分割成数组，并过滤掉数组项为空的情况。然后使用`reduce()`获取指定的属性。由于`selectors`可以传多个，所以使用`map`进行遍历，对每一个指定的属性执行上述方法。最终结果是由获取到的属性值组成的数组。

```
const obj = { selector: { to: { val: 'val to select' } }, target: [1, 2, { a: 'test' }] };
get(obj, 'selector.to.val', 'target[0]', 'target[2].a'); // ['val to select', 1, 'test']

 'selector.to.val'
      .replace(/\[([^\[\]]*)\]/g, '.$1.')
      .split('.')
      .filter(t => t !== '')
      
// 上述例子会返回["selector", "to", "val"]
```

#### invertKeyValues

```
const invertKeyValues = (obj, fn) =>
  Object.keys(obj).reduce((acc, key) => {
    const val = fn ? fn(obj[key]) : obj[key];
    acc[val] = acc[val] || [];
    acc[val].push(key);
    return acc;
  }, {});
```
目的：反转对象的键值对。


```
invertKeyValues({ a: 1, b: 2, c: 1 }); // { 1: [ 'a', 'c' ], 2: [ 'b' ] }
invertKeyValues({ a: 1, b: 2, c: 1 }, value => 'group' + value); // { group1: [ 'a', 'c' ], group2: [ 'b' ] }
```

#### lowercaseKeys

```
const lowercaseKeys = obj =>
  Object.keys(obj).reduce((acc, key) => {
    acc[key.toLowerCase()] = obj[key];
    return acc;
  }, {});
```
目的：根据指定对象新建一个对象，将所有key小写。

解析：使用`reduce()`逐个处理由指定对象的key组成的数组。使用`String.toLowerCase()`将key转为小写。

```
const myObj = { Name: 'Adam', sUrnAME: 'Smith' };
const myObjLower = lowercaseKeys(myObj); // {name: 'Adam', surname: 'Smith'};
```

#### mapKeys

```
const mapKeys = (obj, fn) =>
  Object.keys(obj).reduce((acc, k) => {
    acc[fn(obj[k], k, obj)] = obj[k];
    return acc;
  }, {});
```
目的：创建一个对象，该对象的`key`由指定对象的`key`调用回调函数生成，并具有相同的`value`。


```
mapKeys({ a: 1, b: 2 }, (val, key) => key + val); // { a1: 1, b2: 2 }
```

#### mapValues

```
const mapValues = (obj, fn) =>
  Object.keys(obj).reduce((acc, k) => {
    acc[k] = fn(obj[k], k, obj);
    return acc;
  }, {});
```
目的：与`mapKeys`方法刚好相反，创建一个对象，并处理相应的value。

```
const users = {
  fred: { user: 'fred', age: 40 },
  pebbles: { user: 'pebbles', age: 1 }
};
mapValues(users, u => u.age); // { fred: 40, pebbles: 1 }
```

#### matches

```
const matches = (obj, source) =>
  Object.keys(source).every(key => obj.hasOwnProperty(key) && obj[key] === source[key]);
```
目的：比较两个对象，来确定第一个对象是否完全包含第二个对象的键值对。

解析：遍历由第二对象所有key组成的数组，判断key是否存在于第一对象自身中，并且拥有相同的键值对。

```
matches({ age: 25, hair: 'long', beard: true }, { hair: 'long', beard: true }); // true
matches({ hair: 'long', beard: true }, { age: 25, hair: 'long', beard: true }); // false
```

#### merge

```
const merge = (...objs) =>
  [...objs].reduce(
    (acc, obj) =>
      Object.keys(obj).reduce((a, k) => {
        acc[k] = acc.hasOwnProperty(k) ? [].concat(acc[k]).concat(obj[k]) : obj[k];
        return acc;
      }, {}),
    {}
  );
```
目的：将两个或更多对象组合起来创建一个新的对象。


```
const object = {
  a: [{ x: 2 }, { y: 4 }],
  b: 1
};
const other = {
  a: { z: 3 },
  b: [2, 3],
  c: 'foo'
};
merge(object, other); // { a: [ { x: 2 }, { y: 4 }, { z: 3 } ], b: [ 1, 2, 3 ], c: 'foo' }
```

#### objectFromPairs

```
const objectFromPairs = arr => arr.reduce((a, v) => ((a[v[0]] = v[1]), a), {});
```
目的：根据给定的键值对创建一个新的对象。

解析：通过新建空对象，并`a[v[0]] = v[1]`，形成新的键值对。

```
objectFromPairs([['a', 1], ['b', 2]]); // {a: 1, b: 2}
```

#### objectToPairs

```
const objectToPairs = obj => Object.keys(obj).map(k => [k, obj[k]]);
```
目的：与`objectFromPairs`刚好相反，此方法是由给定对象创建一个由对象键值对数组组成的数组。


```
objectToPairs({ a: 1, b: 2 }); // [['a',1],['b',2]]
```

#### omit

```
const omit = (obj, arr) =>
  Object.keys(obj)
    .filter(k => !arr.includes(k))
    .reduce((acc, key) => ((acc[key] = obj[key]), acc), {});
```
目的：省略对象中指定key的对应键值对。

解析：将对象的key组成数组并将指定的key过滤掉，然后新建对象，将对应的键值对赋值给新的对象。

```
omit({ a: 1, b: '2', c: 3 }, ['b']); // { 'a': 1, 'c': 3 }
```

#### omitBy

```
const omitBy = (obj, fn) =>
  Object.keys(obj)
    .filter(k => !fn(obj[k], k))
    .reduce((acc, key) => ((acc[key] = obj[key]), acc), {});
```
目的：创建由给定函数返回`falsey`的属性组成的对象。也就是省略掉给定函数返回`true`的属性。

解析：与`omit`相似，只不过在过滤的时候调用指定函数。

```
omitBy({ a: 1, b: '2', c: 3 }, x => typeof x === 'number'); // { b: '2' }

// 同时也说明typeof 的优先级高于恒等 ===
```

#### orderBy

```
const orderBy = (arr, props, orders) =>
  [...arr].sort((a, b) =>
    props.reduce((acc, prop, i) => {
      if (acc === 0) {
        const [p1, p2] = orders && orders[i] === 'desc' ? [b[prop], a[prop]] : [a[prop], b[prop]];
        acc = p1 > p2 ? 1 : p1 < p2 ? -1 : 0;
      }
      return acc;
    }, 0)
  );
```
目的：根据指定key对数组进行排序。

```
const users = [{ name: 'fred', age: 48 }, { name: 'barney', age: 36 }, { name: 'fred', age: 40 }];
orderBy(users, ['name', 'age'], ['asc', 'desc']); // [{name: 'barney', age: 36}, {name: 'fred', age: 48}, {name: 'fred', age: 40}]
orderBy(users, ['name', 'age']); // [{name: 'barney', age: 36}, {name: 'fred', age: 40}, {name: 'fred', age: 48}]
```

#### pick

```
const pick = (obj, arr) =>
  arr.reduce((acc, curr) => (curr in obj && (acc[curr] = obj[curr]), acc), {});
```
目的：根据指定key从对象中挑选键值对。

解析：判断指定的key是否存在于obj对象中，只要存在，就将键值对赋值给新对象。

`omit`与`pick`刚好相反，前者是去除指定key，后者是挑选出指定key。相同的是都使用了`reduce()`；不同的是，前者使用`filter()`过滤掉指定key，后者使用`in`判断指定key是否存在于对象中。

```
pick({ a: 1, b: '2', c: 3 }, ['a', 'c']); // { 'a': 1, 'c': 3 }
```

#### pickBy

```
const pickBy = (obj, fn) =>
  Object.keys(obj)
    .filter(k => fn(obj[k], k))
    .reduce((acc, key) => ((acc[key] = obj[key]), acc), {});
```
目的：与`omitBy`相反。挑选出符合回调函数的键值对。

```
pickBy({ a: 1, b: '2', c: 3 }, x => typeof x === 'number'); // { 'a': 1, 'c': 3 }
```

#### shallowClone

```
const shallowClone = obj => Object.assign({}, obj);
```
目的：对对象进行浅复制。

解析：使用`Object.assign({},obj)`进行对象的浅复制。

```
const a = { x: true, y: 1 };
const b = shallowClone(a); // a !== b
```

#### size

```
const size = val =>
  Array.isArray(val)
    ? val.length
    : val && typeof val === 'object'
      ? val.size || val.length || Object.keys(val).length
      : typeof val === 'string' ? new Blob([val]).size : 0;
```
目的：获取数组、对象或字符串的长度。

解析：如果是数组，`Array.isArray()`返回`true`，那么通过`length`属性来获取`size`；如果是对象(通过`typeof`来判断，与&&运算会排除掉`val`为`null`的情况)，通过获取size或length属性，或者对象的key属性的个数来获取`size`；如果是字符串，通过Blob对象的size属性来获取`size`(不是很明白为啥不使用`length`属性获取)。如果都不是，则返回0。

而且巧妙的使用2个三元运算符，避免频繁使用if判断，这个值得学习。

```
size([1, 2, 3, 4, 5]); // 5
size('size'); // 4
size({ one: 1, two: 2, three: 3 }); // 3
```

#### truthCheckCollection

```
const truthCheckCollection = (collection, pre) => collection.every(obj => obj[pre]);
```
目的：检查指定key(第二参数)在第一参数中的值是否都是真值(truthy)。

解析：使用`Array.every()`遍历每个数组项，只有当所有项返回`true`时，`Array.every()`才会返回`true`。

```
truthCheckCollection([{ user: 'Tinky-Winky', sex: 'male' }, { user: 'Dipsy', sex: 'male' }], 'sex'); // true
```


