---
layout:     post                   
title:      underscore经典方法解析    
subtitle:   学习一下优秀的函数处理方式
date:       2018-01-05
author:     chuck
header-img: img/home-bg-basic.jpg
catalog: true                      
tags:                               
    - underscore
    -源码分析
    -函数节流
    -函数防抖
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



