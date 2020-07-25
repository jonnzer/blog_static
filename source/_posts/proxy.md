---
title: proxy
date: 2020-06-14 14:50:21
tags: ES6
---

`vue`源码的render部分，曾用到`proxy`来代理vue的实例。

### 1 概念：
`proxy` 原意就有代理的意思。它是外界访问对象的一层`代理控制`，也是对访问的一种`改写和过滤`。属于一种`元编程`。

### 2 作用：
可以从获取和设置两种时机去代理指定对象

### 3 代码结构：

标准结构：
> var proxy = new Proxy (target, handler);  // target是拦截的目标  handler是拦截行为


```javascript
    var obj = new Proxy({
        get: function(target, key, receiver) {
            console.log(`getting ${key}`)
            return Reflect.get(target, key, receiver)
        },
        set: function(target, key, value, receiver) {
            console.log(`setting ${key}`)
            return Reflect.set(target, key, value, receiver)
        }
    })

    obj // 默认是{}    
    obj.a = 1 // setting a
    obj.a = 2 // setting a
    console.log(obj.a) // getting a
```

### 4. 要注意的点：
+ 要使proxy起作用，需对proxy的实例 进行操作，而不是针对原目标对象。

+ 与原型的应用：
```javascript
var proxy = new Proxy({}, {
    get: function(target, property) {
      return 35
    },
})
let _obj = Object.create(proxy)
_obj.time // 35

// proxy对象是_obj的原型，_obj上没有time这个属性，就顺着原型链找到proxy，proxy上的任何属性都会返回35
```

### 5. proxy还支持get、set之外的api补充

```javascript
    var handler = {
      get: function(target, name) {
        if (name === 'prototype') {
          return Object.prototype;
        }
        return 'Hello, ' + name;
      },
    
      apply: function(target, thisBinding, args) { // proxy实例作为函数调用时触发
        return args[0];
      },
    
      construct: function(target, args) { // proxy作为构造函数时触发
        return {value: args[1]};
      }
    };
    
    var fproxy = new Proxy(function(x, y) {
      return x + y;
    }, handler);
    
    fproxy(1, 2) // 1
    new fproxy(1, 2) // {value: 2}
    fproxy.prototype === Object.prototype // true
    fproxy.foo === "Hello, foo" // true
```

```javascript
    // get 拦截 负数索引
    function createArray(...elements) {
      let handler = {
         get: function(target, property, receiver) { 
             let index = Number(property) // -1 会作为属性传进来
             if (index < 0) {
                 property = String(target.length + index) // property\target 是可以修改的 奇怪...
             }
             return Reflect.get(target, property, receiver) // Reflect is what
         }
     }
     let target = []
     target.push(...elements)
     return new Proxy(target, handler)
    }
    arr = createArray('a', 'b', 'c')
    arr[-1] // 'c'
```

### 6. 属性需可配置，configuable 和 可写writable，否则proxy实例访问代理这个属性会报错 (也是一条可以阻止proxy的法子)
```javascript
    const target = Object.defineProperties({}, {
      foo: {
        value: 123,
        writable: false,
        configurable: false
      },
    });
    
    const handler = {
      get(target, propKey) {
        return 'abc';
      }
    };
    
    const proxy = new Proxy(target, handler);
    
    proxy.foo
    // Uncaught TypeError: 'get' on proxy: property 'foo' is a read-only and non-configurable data property on the proxy target but the proxy did not return its actual value (expected '123' but got 'abc')
```

--- 
引申了解：
+ 元编程的概念
+ proxy的get和set，与 Object.defineProperty 相比，有什么区别
    - 答：[jueim](https://juejin.im/post/5be4f7cfe51d453339084530) proxy的应用场景更多，但是define的兼容性更好
    - 答：[issue](https://github.com/YvetteLau/Blog/issues/25)
    - 答：[两者实现双向绑定差异](https://juejin.im/post/5acd0c8a6fb9a028da7cdfaf)
---
参考文章：
[阮一峰es6 proxy章节](https://es6.ruanyifeng.com/#docs/proxy)
[深入浅出es6系列](https://www.infoq.cn/article/es6-in-depth-proxies-and-reflect)
[segmentFault](https://www.infoq.cn/article/es6-in-depth-proxies-and-reflect)
[元编程](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Meta_programming)