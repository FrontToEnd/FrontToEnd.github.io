---
layout:     post                   
title:      30 seconds of code代码库方法解读(Type篇)
subtitle:   学习使用ES6实现的代码片段
date:       2018-02-06
author:     chuck
header-img: img/home-bg-code.jpg
catalog: true                      
tags:                               
    - JavaScript
    - ES6
    - 源代码
---


### Type

#### getType

```
const getType = v =>
  v === undefined ? 'undefined' : v === null ? 'null' : v.constructor.name.toLowerCase();
```
目的：返回值的原生类型。

解析：主要使用了获取值的构造函数名称的方法。如果值恒等于`undefined`或`null`，则返回其本身；否则返回值的构造函数名称字符串的小写。

```
getType(new Set([1, 2, 3])); // 'set'
'chuck'.constructor.name.toLowerCase();  // 'string'
```

#### is

```
const is = (type, val) => val instanceof type;
```
目的：检查提供的值是否为指定的类型（不适用于文字）。


```
is(Array, [1]); // true
is(ArrayBuffer, new ArrayBuffer()); // true
is(Map, new Map()); // true
is(RegExp, /./g); // true
is(Set, new Set()); // true
is(WeakMap, new WeakMap()); // true
is(WeakSet, new WeakSet()); // true
is(String, ''); // false
is(String, new String('')); // true
is(Number, 1); // false
is(Number, new Number(1)); // true
is(Boolean, true); // false
is(Boolean, new Boolean(true)); // true
```

#### isArrayLike

```
const isArrayLike = val => {
  try {
    return [...val], true;
  } catch (e) {
    return false;
  }
};
```
目的：检查给定值是否是类数组。(是否可迭代)

解析：使用扩展运算符`...`检查给定值是否可迭代。如果可迭代，就返回逗号运算符的最后一位`true`;如果不可迭代，则会进入`catch`，并返回`false`。

```
isArrayLike(document.querySelectorAll('.className')); // true
isArrayLike('abc'); // true
isArrayLike(null); // false
```

#### isBoolean

```
const isBoolean = val => typeof val === 'boolean';
```
目的：检查给定参数是否是原生布尔元素。

解析：使用了`typeof`类型检查操作符，如果给定参数类型是`boolean`，就表明是布尔值。

```
isBoolean(null); // false
isBoolean(false); // true
```

#### isEmpty

```
const isEmpty = val => val == null || !(Object.keys(val) || val).length;
```
目的：如果某个值是空的对象，集合，映射或集合，没有可枚举的属性或任何不被视为集合的类型，则返回`true`。

解析：检查给定的值是否为`null`或`undefined`。如果不为上述两个值，则判断该值的`length`属性是否等于0。

```
isEmpty(new Map()); // true
isEmpty(new Set()); // true
isEmpty([]); // true
isEmpty({}); // true
isEmpty(''); // true
isEmpty([1, 2]); // false
isEmpty({ a: 1, b: 2 }); // false
isEmpty('text'); // false
isEmpty(123); // true - type is not considered a collection
isEmpty(true); // true - type is not considered a collection
```

#### isFunction

```
const isFunction = val => typeof val === 'function';
```
目的：判断给定参数是否为`function`。同样的，采用`typeof`操作符。还有`isNull`、`isNumber`、`isUndefined`、`isString`、`isSymbol`都采用了类似的方法，不再赘述。

```
isFunction('x'); // false
isFunction(x => x); // true
```

#### isNil

```
const isNil = val => val === undefined || val === null;
```
目的：如果给定值是`undefined`或`null`就返回`true`，否则返回`false`。

解析：使用严格相等`===`来判断。

```
isNil(null); // true
isNil(undefined); // true
```

#### isObject

```
const isObject = obj => obj === Object(obj);
```
目的：判断给定的参数是否为对象。

解析：使用`Object()`构造函数为给定值创建对象包裹，如果obj为`null`或`undefined`，则创建并返回一个空对象。否则返回一个给定值类型的对象。所以**传入原始类型的值**，肯定会返回`false`。

```
isObject([1, 2, 3, 4]); // true
isObject([]); // true
isObject(['Hello!']); // true
isObject({ a: 1 }); // true
isObject({}); // true
isObject(true); // false
```

#### isObjectLike

```
const isObjectLike = val => val !== null && typeof val === 'object';
```
目的：判断一个值是否是类对象。

解析：如果一个值不为`null`，并且其类型是`object`，那么就认定该值是类对象。这应该就是经典的鸭子类型。

```
isObjectLike({}); // true
isObjectLike([1, 2, 3]); // true
isObjectLike(x => x); // false
isObjectLike(null); // false
```

#### isPlainObject

```
const isPlainObject = val => !!val && typeof val === 'object' && val.constructor === Object;
```
目的：检查提供的值是否是Object构造函数创建的对象。

解析：首先检查提供的值是否为真值，并且类型是否为'object'，使用`Object.constructor`确保该值的`constructor`是否为`Object`。

```
isPlainObject({ a: 1 }); // true
isPlainObject(new Map()); // false
```

#### isPrimitive

```
const isPrimitive = val => !['object', 'function'].includes(typeof val) || val === null;
```
目的：确认传入的值是否为原始值。

解析：使用`Array.includes()`并取反来判断传入的值是否是原始值，非原始值类型包括'object'和'function'。如果传入值为引用类型，则会走到短路运算的第二个参数：是否恒等于`null`。这主要是为了区分`typeof null === 'object'。`

```
isPrimitive(null); // true
isPrimitive(50); // true
isPrimitive('Hello!'); // true
isPrimitive(false); // true
isPrimitive(Symbol()); // true
isPrimitive([]); // false
```


#### isPromiseLike

```
const isPromiseLike = obj =>
  obj !== null &&
  (typeof obj === 'object' || typeof obj === 'function') &&
  typeof obj.then === 'function';
```
目的：如果一个对象看起来像`Promise`，返回`true`。否则返回`false`。

解析：这里也是采用的鸭子类型的方式。如果`obj`不为`null`，并且类型为`object`或者`function`，并且`obj`的`then`属性的类型为`function`。

```
isPromiseLike({
  then: function() {
    return '';
  }
}); // true
isPromiseLike(null); // false
isPromiseLike({}); // false
```

#### isValidJSON

```
const isValidJSON = obj => {
  try {
    JSON.parse(obj);
    return true;
  } catch (e) {
    return false;
  }
};
```
目的：检查提供的对象是否是有效的JSON。

解析：使用`JSON.parse()`和`try...catch`语句来判断给定对象是否是有效JSON。如果调用`JSON.parse()`出错，就会捕获错误并返回`false`。

```
isValidJSON('{"name":"Adam","age":20}'); // true
isValidJSON('{"name":"Adam",age:"20"}'); // false
isValidJSON(null); // true
```


