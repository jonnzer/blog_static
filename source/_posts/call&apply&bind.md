---
title: call && apply && bind
date: 2020-04-09 17:22:35
tags: 面试 JS
---

#### 1. apply
调用一个具有`给定this值`的函数，以及`作为一个数组（或类似数组对象）`提供的参数。
语法：
```javascript
    function.apply(thisArg, [argsArray])
```

<div class="tip">
        如果这个函数处于非严格模式下，`thisArg`指定为 `null` 或 `undefined` 时会自动替换为`指向全局对象`，原始值会被包装！！！
</div>

 基础用法
```javascript
    const numbers = [5, 6, 2, 3, 7];
    const max = Math.max.apply(null, numbers);
    console.log(max);// expected output: 7
```

将A数组里的元素都添加到B数组 （apply有种`循环参数数组`的能力）
```javascript
    Array.push.apply(BArray,AArray)
    // 等同于
    BArray.push(...AArray)
```

#### 2. call
调用一个具有`给定this值`的函数，以及一个或多个参数。
语法：
```javascript
    function.call(thisArg, arg1, arg2, ...) 
```

用来继承
```javascript
    function Product(name, price) {
      this.name = name;
      this.price = price;
    }
    
    function Food(name, price) {
      Product.call(this, name, price);
      this.category = 'food';
    }
    
    console.log(new Food('cheese', 5).name);
    // expected output: "cheese"
```

绑定上下文
```javascript
    function greet() {
      var reply = [this.animal, 'typically sleep between', this.sleepDuration].join(' ');
      console.log(reply);
    }
    
    var obj = {
      animal: 'cats', sleepDuration: '12 and 16 hours'
    };
    
    greet.call(obj);  // cats typically sleep between 12 and 16 hours
```

非严格模式下，不传值则默认上下文是全局对象
```javascript
    var sData = 'Wisen';
    
    function display() {
      console.log('sData value is %s ', this.sData);
    }
    
    display.call();  // sData value is Wisen   这里上下文是window
```

#### 3.bind
该方法创建一个新的函数，在 `bind() `被调用时，这个`新函数`的 `this 被指定为 bind() 的第一个参数`，而其余参数将作为新函数的参数，供调用时使用。
也就是说，返回一个原函数的拷贝，并拥有指定的 this 值和初始参数。
语法：
```javascript
    function.bind(thisArg[, arg1[, arg2[, ...]]])
```

<div class="tip">
1 使用`new`构造函数时，忽略`thisArg` 
---※---
2 在`setTimeout`中回调函数使用时，`thisArg`的任何值都将转化成`object`
---※---
3 如果`thisArg`为`null或undefined`，`执行作用域中的this`将作为新的`thisArg`
</div> 


基础用法
```javascript
    const module = {
      x: 42,
      getX: function() {
        return this.x;
      }
    };
    
    const unboundGetX = module.getX;
    console.log(unboundGetX());
    // expected output: window.x => undefined
    
    const boundGetX = unboundGetX.bind(module);
    console.log(boundGetX());
    // expected output: 42
```

偏函数
bind() 的另一个最简单的用法是使一个函数拥有预设的初始参数。
只要将这些参数（如果有的话）作为 bind() 的参数写在 this 后面。
当绑定函数被调用时，这些参数会被插入到目标函数的参数列表的开始位置，传递给绑定函数的参数会跟在它们后面。
```javascript
function list() {
  return Array.prototype.slice.call(arguments);
}

function addArguments(arg1, arg2) {
    return arg1 + arg2
}

var list1 = list(1, 2, 3); // [1, 2, 3]

var result1 = addArguments(1, 2); // 3

// 创建一个函数，它拥有预设参数列表。
var leadingThirtysevenList = list.bind(null, 37);

// 创建一个函数，它拥有预设的第一个参数
var addThirtySeven = addArguments.bind(null, 37); 

var list2 = leadingThirtysevenList(); 
// [37]

var list3 = leadingThirtysevenList(1, 2, 3); 
// [37, 1, 2, 3]

var result2 = addThirtySeven(5); 
// 37 + 5 = 42 

var result3 = addThirtySeven(5, 10);
// 37 + 5 = 42 ，第二个参数被忽略
```

有意思的代码
```javascript
var unboundSlice = Array.prototype.slice;
var slice = Function.prototype.apply.bind(unboundSlice);
 // 这里的Function.prototype.apply 其实就是apply函数 
slice(arguments);
// 等同于 apply.bind(unboundSlice); 也就是slice === Array.prototype.slice
```


假如不支持`bind`的时候
```javascript
// Does not work with `new (funcA.bind(thisArg, args))`
if (!Function.prototype.bind) (function(){
  var slice = Array.prototype.slice;
  Function.prototype.bind = function() {
    var thatFunc = this, thatArg = arguments[0]; // 缓存调用函数，this指向，其它参数
    var args = slice.call(arguments, 1);
    if (typeof thatFunc !== 'function') {
      // closest thing possible to the ECMAScript 5
      // internal IsCallable function
      throw new TypeError('Function.prototype.bind - ' +
             'what is trying to be bound is not callable');
    }
    return function(){
      var funcArgs = args.concat(slice.call(arguments)) // 其它参数合并传入参数，并成统一的函参
      return thatFunc.apply(thatArg, funcArgs);
    };
  };
})();
```

#### 4. 三者同否
+ apply是传参数数组(参数不确定)，call是传参数(参数确定)
+ apply、call、bind 三者都是为了`改变上下文`，改变函数`内部this`的指向
+ 通过apply这种方式，您将面临超过JavaScript引擎的参数长度限制的风险
+ 多次bind也只是bind第一次绑定的值

参考链接：
[MDN 关于 apply](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)
[MDN 关于 bind](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)
[ChokCocoblog](https://www.cnblogs.com/coco1s/p/4833199.html)
[模拟实现](https://muyiy.cn/blog/3/3.3.html#call-%E5%92%8C-apply)