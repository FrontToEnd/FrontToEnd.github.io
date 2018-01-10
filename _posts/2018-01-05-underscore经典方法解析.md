---
layout:     post                   
title:      underscore经典方法解析        
subtitle:   学习优秀的函数处理方式
date:       2018-01-05
author:     chuck
header-img: img/home-bg-basic.jpg
catalog: true                      
tags:                               
    - JavaScript
    - underscore
---


### Underscore源码经典方法解析

#### 函数节流
```
_.throttle = function(func,wait,options) {
    var context,args,result;
    
    //setTimeout的返回值，初始值先置为null
    var timeout = null;
    
    //时间戳
    var previous = 0;
    
    //如果未传入options参数，则置为空对象
    if (!options) 
    options = {};
    
    //下面setTimeout需要调用的函数
    var later = function() {
        // 如果 options.leading === false
        // 则每次触发回调后将 previous 置为 0
        // 否则置为当前时间戳，_.now()就是获取当前时间戳的方法
        previous = options.leading === false ? 0 : _.now();
        //将handler置为null，setTimeout执行完后，再赋值新值给timeout
        timeout = null;
        
        result = func.apply(context,args);
        
        if (!timeout) 
        //防止内存泄漏
        context = args = null;
    };
    
    //节流方法返回的函数
    return function() {
        
        //记录当前的时间戳
        var now = _.now();
        
        if (!previous && options.leading === false)
        // 第一次执行回调时，previous为0，以后都为上次的时间戳
        // options.leading === false表示着第一个回调不是立即执行，也就是等待相应的毫秒数后再执行
        // 当两者条件都满足时，将值设置为now的时间戳
        previous = now;
        
        // 距离下次触发func需要等待的事件
        var remaining = wait - (now - previous);
        context = this;
        args = arguments;
        
        // 当满足条件时，进入if分支
        // 到间隔时间或者第一次调用时，remaining <= 0
        // 当客户端系统时间调整过，可能会出现remaining > wait，是为了有更好的兼容性
        if (remaining <= 0 || remaining > wait) {
            if (timeout) {
                //如果上一次的handler还存在，则清除引用
                clearTimeout(timeout);
                timeout = null;
            }
            
            // 重置前一次触发的时间戳
            previous = now;
            
            //执行传入的函数
            result = func.apply(context,args);
            
            if (!timeout)
            //如果timeout为null，则清除引用
            context = args = null;
        } else if (!timeout && options.trailing !== false) {
            //如果定时器存在，并且不需要最后一次触发options.trailing === false则不会进入该分支
            
            // 间隔remaining毫秒后触发later
            timeout = setTimeout(later,remaining);
        }
        
        return result;
    };
};
```

#### 函数防抖


```
// 设置wait表示连续事件结束后指定毫秒数后触发
// immediate为true表示连续事件结束后立即触发，忽略第二参数wait
_.debounce = function(func,wait,immediate) {
    var timeout,args,context,timestamp,result;
    
    var later = function() {
            // 当last大于等于wait时，就会执行func
            var last = _.now() - timestamp;
            
            // 如果间隔还没到wait毫秒数进入分支
            if (last < wait && last >=0) {
                // 继续延迟wait - last毫秒后执行
                timeout = setTimeout(later,wait - last);          
            } else {
                timeout = null;
                if (!immediate) {
                    result = func.apply(context,args);
                    if (!timeout)
                    //清除引用
                    context = args = null;
                }
            }
    };
    
    // 闭包返回的函数
    return function() {
        
        //指定this指向
        context = this;
        args = arguments;
        
        // 每次触发函数时，更新时间戳，该变量在later方法中有用到
        timestamp = _.now();
        
        // 如果callNow为true，则表示立即触发
        // immediate为传入的参数，如果为true，则表示想要立即触发
        // 如果去掉!timeout的条件，则会每次调用函数都会立即触发
        // 如果timeout为null，则表示第一次触发，所以callNow为true代表着第一次立即触发
        var callNow = immediate && !timeout;
        
        // wait毫秒数之后触发later方法
        if (!timeout)
        timeout = setTimeout(later,wait);
        
        //如果是立即调用，则调用传入的函数，并且解除引用
        if (callNow) {
            result = func.apply(context,args);
            context = args = null;
        }
        
        return result;
    };
};
```

#### 数组map方法


```
// 遍历数组或对象的每个元素，对每个元素执行迭代方法
// 将结果保存到新数组并返回

_.map = _.collect = function(obj,iteratee,context) {
    
    // 根据iteratee的不同来返回不同的迭代函数
    // cb是underscore很重要的函数之一，下次稍加分析
    iteratee = cb(iteratee,context);
    
    //通过短路运算，如果obj是对象，那么就获取keys值数组
    var keys = !isArrayLike(obj) && _.keys(obj),
    
    // 如果keys存在就取对象keys值数组的长度，否则就去参数数组的长度
    length = (keys || obj).length,
    
    // 新建一个长度为length的数组
    results = Array(length);
    
    for (var index = 0; index < length; index++) {
    
        // 如果 obj 为对象，则 currentKey 为对象键值 key
        // 如果 obj 为数组，则 currentKey 为 index 值
        var currentKey = keys ? keys[index] : index;
        
        results[index] = iteratee(obj[currentKey],currentKey,obj);
    }
    
    return results;
}
```

#### 数组的every方法


```
// 与原生发放类似，我们来看下怎么优雅的实现类似功能

_.every = _.all = function(obj,predicate,context) {
    predicate = cb(predicate,context);
    
    // 与上面的相似，巧妙的将数组与对象的length用短路运算计算出来
    var keys = !isArrayLike(obj) && _.keys(obj),
    length = (keys || obj).length;
    
    for (var index = 0; index < length; index++) {
        var currentKey = keys ? keys[index] : index;
        
        // 如果有一项不满足，就返回false，否则就返回true
        if (!predicate(obj[currentKey],currentKey,obj))
        return false;
    }
    
    return true;
}
```

#### 数组max方法

该方法用于寻找数组的最大值


```
_.max = function(obj,iteratee,context) {
    var result = -Infinity,lastComputed = -Infinity,value,computed;
    
    // 如果没有迭代，直接寻找最大值
    if (iteratee == null && obj != null) {
        
        // 如果是数组，则直接寻找数组本身
        // 如果是对象，则寻找最大value值
        obj = isArrayLike(obj) ? obj : _.values(obj);
        
        for (var i = 0, length = obj.length; i < length; i++) {
            value = obj[i];
            if (value > result) {
                result = value;
            }
        }
    } else {
        iteratee = cb(iteratee,context);
        _.each(obj,function(value,index,list){
            computed = iteratee(value,index,list);
            
            // 只有在迭代结果大于上次存储的极大值时，才会进入分支赋值
            if (computed > lastComputed || computed === -Infinity && result === -Infinity) {
                result = value;
                lastComputed = computed;
            }
        });
    }
    
    return result;
};
```

#### 数组shuffle方法

该方法用于将数组打乱顺序


```
_.shuffle = function(obj) {
    var set = isArrayLike(obj) ? obj : _.values(obj);
    var length = set.length;
    
    // 打乱顺序后返回的数组
    var shuffled = Array(length);
    
    for (var index = 0, rand; index < length; index++) {
        
        // _.random方法用于返回[0,index]的任意整数
        rand = _.random(0,index);
        // 可以理解为先将shuffled数组位于[0,index]任意位置的元素放至[index]处，然后将set[index]处的元素放至空缺的那个位置。
        if (rand !== index) {
            shuffled[index] = shuffled[rand];
        }
        shuffled[rand] = set[index];
    }
    return shuffled;
}
```

#### 数组unique方法

该方法用于数组去重，如果参数`isSorted`为true，表明数组有序，会执行另一种比较办法。


```
_.uniq = _.unique = function(array,isSorted,iteratee,context) {
    
    //如果没有传入isSorted参数
    //方法转为_unique(array,false,undefined,iteratee)
    if (!_.Boolean(isSorted)) {
        context = iteratee;
        iteratee = isSorted;
        isSorted = false;
    }
    
    if (iteratee != null)
    iteratee = cb(iteratee,context);
    
    //结果数组
    var result = [];
    
    //用来存放已经出现过的值，如果下一个元素在该数组中，则表明是重复值
    var seen = [];
    
    for (var i = 0, length = getLength(array); i < length; i++) {
        var value = array[i],
            computed = iteratee ? iteratee(value,i,array) : value;
        
        // 如果已排过序，则只需要与前一个值相比，不相等则放入结果数组中    
        if (isSorted) {
            // !i是为了让i === 0时，直接push
            if (!i || seen ！== computed) result.push(value);
            seen = computed;
        } else if (iteratee) {
            
            if (!_.contains(seen,computed)) {
            
                //如果seen数组中不包含computed,则将value推入结果数组
                seen.push(computed);
                result.push(value;)
            }
        } else if (!_.contains(result,value)) {
        
            // 如果结果数组中不包含value，则可以push
            result.push(value);
        }
    }
    return result;
}
```

#### 对象keys方法

该方法返回对象的keys组成的数组


```
_.keys = function(obj) {
    
    //  如果传入的不是对象，则返回空数组 
    if (!_.isObject(obj)) return [];
    
    // 如果浏览器支持Object.key()方法
    // 则使用原生方法
    if (nativeKeys) return nativeKeys(obj);
    
    var keys = [];
    
    for (var key in obj) {
        
        // 判断key是在obj上还是原型链上,会遍历原型链上的属性
        //hasOwnProperty
        if (_.has(obj,key)) keys.push(key);
        
        // 为了处理IE < 9时，不能用for in枚举某些值
        if (hasEnumBug) collectNonEnumProps(obj,keys);
    }
    return keys;
}
```

#### 对象values方法

将对象所有values值放入数组并返回，不包括原型链上


```
_.values = function(obj) {
    var keys = _.keys(obj);
    var length = keys.length;
    var values = Array(length);
    for (var i = 0; i< length; i++) {
        values[i] = obj[keys[i]];
    }
    return values;
}
```

#### 对象的map方法

通过迭代来改变对象的values值，相当于数组的map方法


```
_.mapObject = function(obj,iteratee,context) {
    iteratee = cb(iteratee,context);
    
    var keys = _.keys(obj),
        length = keys.length,
        results = {}, // 返回的对象副本
        currentKey;
        
    for (var index = 0; index < length; index++) {
        currentKey = keys[index];
        
        // 将对象的每一项进行迭代，然后赋值给新的对象副本
        results[currentKey] = iteratee(obj[currentKey],currentKey,obj);
    }
    return results;
}
```

#### 对象的isMatch方法

该方法用于判断object对象中是否有attrs中所有key-value键值对，并返回布尔值


```
_.isMatch = function(object,attrs) {
    // 获取attrs的keys数组
    var keys = _.keys(attrs),length = keys.length;
    
    // 如果object为空，返回attrs长度的非操作
    //  如果attrs不为空，!length为false，意味着object没有包含attrs所有键值对
    // 如果attrs为空,!length为true，也就是空对象包含空对象所有键值对
    if(object == null) return !length;
    
    // 防止object为非对象
    var obj = Object(object);
    
    for (var i = 0; i < length; i++) {
        var key = keys[i];
        // 如果对于某个key，两者的值不相同
        // 或者attrs的某个key不存在于object内，就表示object不包含attrs所有的键值对，并返回false
        // 否则就返回true
        if (attrs[key] !== obj[key] || !(key in obj)) return false;
    }
    return true;
}
```

#### 源代码最多的eq方法

该方法用于准确的比较传入的两个参数是否完全相等，具体分析可以看看[冴羽](https://github.com/mqyqingfeng)写的[JavaScript专题之如何判断两个对象相等](https://github.com/mqyqingfeng/Blog/issues/41)

```
var eq = function(a, b, aStack, bStack) {
    // Identical objects are equal. `0 === -0`, but they aren't identical.
    // See the [Harmony `egal` proposal](http://wiki.ecmascript.org/doku.php?id=harmony:egal).
    // a === b 时
    // 需要注意 `0 === -0` 这个 special case
    // 0 和 -0 被认为不相同（unequal）
    // 至于原因可以参考上面的链接
    if (a === b) return a !== 0 || 1 / a === 1 / b;

    // A strict comparison is necessary because `null == undefined`.
    // 如果 a 和 b 有一个为 null（或者 undefined）
    // 判断 a === b
    if (a == null || b == null) return a === b;

    // Unwrap any wrapped objects.
    // 如果 a 和 b 是 underscore OOP 的对象
    // 那么比较 _wrapped 属性值（Unwrap）
    if (a instanceof _) a = a._wrapped;
    if (b instanceof _) b = b._wrapped;

    // Compare `[[Class]]` names.
    // 用 Object.prototype.toString.call 方法获取 a 变量类型
    var className = toString.call(a);

    // 如果 a 和 b 类型不相同，则返回 false
    // 类型都不同了还比较个蛋！
    if (className !== toString.call(b)) return false;

    switch (className) {
      // Strings, numbers, regular expressions, dates, and booleans are compared by value.
      // 以上五种类型的元素可以直接根据其 value 值来比较是否相等
      case '[object RegExp]':
      // RegExps are coerced to strings for comparison (Note: '' + /a/i === '/a/i')
      case '[object String]':
        // Primitives and their corresponding object wrappers are equivalent; thus, `"5"` is
        // equivalent to `new String("5")`.
        // 转为 String 类型进行比较
        return '' + a === '' + b;

      // RegExp 和 String 可以看做一类
      // 如果 obj 为 RegExp 或者 String 类型
      // 那么 '' + obj 会将 obj 强制转为 String
      // 根据 '' + a === '' + b 即可判断 a 和 b 是否相等
      // ================

      case '[object Number]':
        // `NaN`s are equivalent, but non-reflexive.
        // Object(NaN) is equivalent to NaN
        // 如果 +a !== +a
        // 那么 a 就是 NaN
        // 判断 b 是否也是 NaN 即可
        if (+a !== +a) return +b !== +b;

        // An `egal` comparison is performed for other numeric values.
        // 排除了 NaN 干扰
        // 还要考虑 0 的干扰
        // 用 +a 将 Number() 形式转为基本类型
        // 即 +Number(1) ==> 1
        // 0 需要特判
        // 如果 a 为 0，判断 1 / +a === 1 / b
        // 否则判断 +a === +b
        return +a === 0 ? 1 / +a === 1 / b : +a === +b;

      // 如果 a 为 Number 类型
      // 要注意 NaN 这个 special number
      // NaN 和 NaN 被认为 equal
      // ================

      case '[object Date]':
      case '[object Boolean]':
        // Coerce dates and booleans to numeric primitive values. Dates are compared by their
        // millisecond representations. Note that invalid dates with millisecond representations
        // of `NaN` are not equivalent.
        return +a === +b;

      // Date 和 Boolean 可以看做一类
      // 如果 obj 为 Date 或者 Boolean
      // 那么 +obj 会将 obj 转为 Number 类型
      // 然后比较即可
      // +new Date() 是当前时间距离 1970 年 1 月 1 日 0 点的毫秒数
      // +true => 1
      // +new Boolean(false) => 0
    }


    // 判断 a 是否是数组
    var areArrays = className === '[object Array]';

    // 如果 a 不是数组类型
    if (!areArrays) {
      // 如果 a 不是 object 或者 b 不是 object
      // 则返回 false
      if (typeof a != 'object' || typeof b != 'object') return false;

      // 通过上个步骤的 if 过滤
      // !!保证到此的 a 和 b 均为对象!!

      // Objects with different constructors are not equivalent, but `Object`s or `Array`s
      // from different frames are.
      // 通过构造函数来判断 a 和 b 是否相同
      // 但是，如果 a 和 b 的构造函数不同
      // 也并不一定 a 和 b 就是 unequal
      // 比如 a 和 b 在不同的 iframes 中！
      // aCtor instanceof aCtor 这步有点不大理解，啥用？
      var aCtor = a.constructor, bCtor = b.constructor;
      if (aCtor !== bCtor && !(_.isFunction(aCtor) && aCtor instanceof aCtor &&
                               _.isFunction(bCtor) && bCtor instanceof bCtor)
                          && ('constructor' in a && 'constructor' in b)) {
        return false;
      }
    }

    // Assume equality for cyclic structures. The algorithm for detecting cyclic
    // structures is adapted from ES 5.1 section 15.12.3, abstract operation `JO`.

    // Initializing stack of traversed objects.
    // It's done here since we only need them for objects and arrays comparison.
    // 第一次调用 eq() 函数，没有传入 aStack 和 bStack 参数
    // 之后递归调用都会传入这两个参数
    aStack = aStack || [];
    bStack = bStack || [];

    var length = aStack.length;

    while (length--) {
      // Linear search. Performance is inversely proportional to the number of
      // unique nested structures.
      if (aStack[length] === a) return bStack[length] === b;
    }

    // Add the first object to the stack of traversed objects.
    aStack.push(a);
    bStack.push(b);

    // Recursively compare objects and arrays.
    // 将嵌套的对象和数组展开
    // 如果 a 是数组
    // 因为嵌套，所以需要展开深度比较
    if (areArrays) {
      // Compare array lengths to determine if a deep comparison is necessary.
      // 根据 length 判断是否应该继续递归对比
      length = a.length;

      // 如果 a 和 b length 属性大小不同
      // 那么显然 a 和 b 不同
      // return false 不用继续比较了
      if (length !== b.length) return false;

      // Deep compare the contents, ignoring non-numeric properties.
      while (length--) {
        // 递归
        if (!eq(a[length], b[length], aStack, bStack)) return false;
      }
    } else {
      // 如果 a 不是数组
      // 进入这个判断分支

      // Deep compare objects.
      // 两个对象的深度比较
      var keys = _.keys(a), key;
      length = keys.length;

      // Ensure that both objects contain the same number of properties before comparing deep equality.
      // a 和 b 对象的键数量不同
      // 那还比较毛？
      if (_.keys(b).length !== length) return false;

      while (length--) {
        // Deep compare each member
        // 递归比较
        key = keys[length];
        if (!(_.has(b, key) && eq(a[key], b[key], aStack, bStack))) return false;
      }
    }

    // Remove the first object from the stack of traversed objects.
    // 与 aStack.push(a) 对应
    // 此时 aStack 栈顶元素正是 a
    // 而代码走到此步
    // a 和 b isEqual 确认
    // 所以 a，b 两个元素可以出栈
    aStack.pop();
    bStack.pop();

    // 深度搜索递归比较完毕
    // 放心地 return true
    return true;
  };
```

