---
title: 数组常见技巧
date: 2020-05-26 13:40:18
tags: Array 
---

### 1. 数组降维
二维数组降维成一维数组（引自vue源码）
> 利用原理：
(1) `concat`的属性 : 如果concat方法的参数是一个元素，该元素会被直接插入到新数组中；如果参数是一个数组，该数组的各个元素将被插入到新数组中；
(2) `apply`的优化：`Array.prototype.concat.apply`([], children) 等同于 [].concat(demoArr)


```javascript
let demoArr = [1,2,3,4,5,[7,8],[22,23,43]]
export function simpleNormalizeChildren (children) {
  for (let i = 0; i < children.length; i++) {
    if (Array.isArray(children[i])) {
      return Array.prototype.concat.apply([], children)
    }
  }
  return children
}

simpleNormalizeChildren(demoArr)  
// result =>  [1, 2, 3, 4, 5, 7, 8, 22, 23, 43]
```

#### 扩展：

多维数组递归降维(借助了`递归`的能力)
```javascript
let children = [1, [2,3], [4, [5, 6, [7, 8]]], [9, 10]];
function simpleNormalizeChildren(children) {
  for (let i = 0; i < children.length; i++) {
    if (Array.isArray(children[i])) {
      children = Array.prototype.concat.apply([], children);
      for(let j =0; j<children.length; j++) {
        simpleNormalizeChildren(children)
      }
    }
  }
  return children;
}
simpleNormalizeChildren(children); 
// result =>  [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

### 2. 数组去重
（1） ES6 `set`方法 (Set 对象允许你存储任何类型的唯一值)
```javascript
function unique (arr) {
  return Array.from(new Set(arr))
}
var arr = [1,1,'true','true',true,true,15,15,false,false, undefined,undefined,
 null,null, NaN, NaN,'NaN', 0, 0, 'a', 'a',{},{}];
console.log(unique(arr))
 //[1, "true", true, 15, false, undefined, null, NaN, "NaN", 0, "a", {}, {}] 
```
<div class="tip">
  set方法无法识别重复的{}空对象
</div> 

（2）双重for循环，splice去重
```javascript
function unique(arr){            
        for(var i=0; i<arr.length; i++){
            for(var j=i+1; j<arr.length; j++){
                if(arr[i]==arr[j]){         //前者等于后者的时候，splice方法删除后者
                    arr.splice(j,1);
                    j--; 
                }
            }
        }
return arr;
}
var arr = [1,1,'true','true',true,true,15,15,false,false, undefined,undefined,
     null,null, NaN, NaN,'NaN', 0, 0, 'a', 'a',{},{}];
    console.log(unique(arr))
    //[1, "true", 15, false, undefined, NaN, NaN, "NaN", "a", {…}, {…}]     

```
<div class="tip">
NaN和{}没有去重，两个null直接消失了
</div>


(3) indexOf（新建了一个空数组，遍历旧数组，如果空数组里没遇到重复的遍历项，则添加） 
```javascript
function unique(arr) {
    if (!Array.isArray(arr)) {
        console.log('type error!')
        return
    }
    var array = [];
    for (var i = 0; i < arr.length; i++) {
        if (array.indexOf(arr[i]) === -1) {
            array.push(arr[i])
        }
    }
    return array;
}
var arr = [1,1,'true','true',true,true,15,15,false,false, undefined,undefined,
           null,null, NaN, NaN,'NaN', 0, 0, 'a', 'a',{},{}];
console.log(unique(arr))
   // [1, "true", true, 15, false, undefined, null, NaN, NaN, "NaN", 0, "a", {…}, {…}]  
```
<div class="tip">
NaN、{}没有去重</div>

(4) sort排序后再去重（sort后一样的元素的会在旁边，只需与身边的对比）高性能，推荐👍
```javascript
  function unique(arr) {
    var seen; // 动态指针
    // 浅拷贝后 排序targetArr 排序有要求的话，需要sort传入自定义的排序函数
    var targetArr = Array.prototype.concat.call(arr).sort() 
    var result = [];
    // 与上一位对比 因为index为0的时候没有上一位，所以直接插入结果数组返回
    for (var i = 0; i <targetArr.length; i++) { 
       // 这里 0转为boolean是false，!0即true
      // 这个if是，如果是第一位，或者，动态指针与遍历到的不一样，即是匹配到新元素
      if (!i || seen !== targetArr[i]) {
        result.push(targetArr[i])
      }
      // 更新指针
      seen = targetArr[i]
    }
    return result
  }

  // test
  var arr = [1,1,'true','true',true,true,15,15,false,false, undefined,undefined,
           null,null, NaN, NaN,'NaN', 0, 0, 'a', 'a',{},{}];
  console.log(unique(arr));
```

<div class="tip">
  sort方法需要注意看使用场景，特殊类型的时候，sort会有坑。
</div>

(5) underScore 思路版 （提供三个参数）这个思路杂糅上面的方法
 `array`： 必填参数，去重目标数组
`isSorted`: 表示目标数组是否已经排序过了，true的话，将会采用方法（4）那样的高效对比。false的话，用indexOf
`iteratee`: 自定义对目标数组的操作

```javascript
function unique(array, isSorted, iteratee) {
    var res = [];
    var seen = [];

    for (var i = 0, len = array.length; i < len; i++) {
        var value = array[i];
        var computed = iteratee ? iteratee(value, i, array) : value;
        if (isSorted) {
            if (!i || seen !== computed) {
                res.push(value)
            }
            seen = computed;
        }
        else if (iteratee) {
            if (seen.indexOf(computed) === -1) {
                seen.push(computed);
                res.push(value);
            }
        }
        else if (res.indexOf(value) === -1) {
            res.push(value);
        }        
    }
    return res;
}

console.log(unique(array3, false, function(item){
    return typeof item == 'string' ? item.toLowerCase() : item
})); // [1, "a", 2]
```

(6) 利用Object的key是唯一的
```javascript
function distinct(array) {
    var obj = {};
    return array.filter(function(item, index, array){
        return obj.hasOwnProperty(typeof item + item) 
        ? false
        : (obj[typeof item + item] = true)
    })
}
```
真是叹为观止，妙！妙在哪？
+ `array.filter`本身就兼职`遍历，过滤，返回新数组`于一身，干净利落
+ Object的`key确实是唯一`的，如果重复时候，只能是覆盖value的
+ filter过滤条件需要的`true和false`就由 Obj是否包含这个`type+value`为key  来决定，
如果之前没存进obj，可以任意设置一个value，就是三元运算符里的`obj[typeof item + item] = true`


---

###  API补充：
1. `reduce`

升序让数组每一项都调用传入函数，可以设置初始值。
四个参数
`Accumulator` (acc) (累计器)
`Current Value` (cur) (当前值)
`Current Index` (idx) (当前索引)
`Source Array `(src) (源数组)

```javascript
arr.reduce(callback(accumulator, currentValue[, index[, array]])[, initialValue])```
```javascript
// 统计出现次数
var names = ['Alice', 'Bob', 'Tiff', 'Bruce', 'Alice'];
// allNames为Accumulator累积器，name为当前值
var countedNames = names.reduce(function (allNames, name) { 
  if (name in allNames) {
    allNames[name]++;
  }
  else {
    allNames[name] = 1;
  }
  return allNames;
}, {});
// countedNames is:
// { 'Alice': 2, 'Bob': 1, 'Tiff': 1, 'Bruce': 1 }
```


```javascript
  var pipe = (function(){ // proxy结合reduce实现 链式调用函数封装 需是get结尾
        return function (value) { // value是参数 闭包缓存funcStack数组
            var funcStack = [];
            var oproxy = new Proxy({} , {
                get : function (pipeObject, fnName) {
                    console.log('fnName is:' + fnName)
                    if (fnName === 'get') {
                        return funcStack.reduce(function (val, fn) {
                            return fn(val);
                        },value);
                    }
                    funcStack.push(window[fnName]);
                    console.log(funcStack)
                    return oproxy;
                }
            });
            return oproxy;
        }
    }());

    var double = n => n * 2;
    var pow    = n => n * n;
    var reverseInt = n => n.toString().split("").reverse().join("") | 0;

    console.log(pipe(3)['double'].pow.get);
    // pipe(3).double.pow.reverseInt.get; // 63 
```

2. `slice`
类数组 => 真数组
```javascript
    function list() {
      // 也可以写成 [].slice.call(arguments) 
      return Array.prototype.slice.call(arguments); 
    }
    
    var list1 = list(1, 2, 3); // [1, 2, 3]
```

3. `Array.from`
从一个类似数组或可迭代对象创建一个新的，`浅拷贝`的数组实例。
```javascript
Array.from(arrayLike[, mapFn[, thisArg]])
```
`arrayLike`：想要转换成数组的伪数组对象或可迭代对象。比如拥有一个 length 属性和若干索引属性的任意对象和Map、set等。
`mapFn` 可选：如果指定了该参数，新数组中的每个元素会执行该回调函数。
`thisArg` 可选：可选参数，执行回调函数 mapFn 时 this 对象。

```javascript
const someNumbers = { '0': 10, '1': 15, length: 2 };
Array.from(someNumbers, value => value * 2); // => [20, 30] 这里利用了mapFn的能力
```
```javascript
function sumArguments() {
    return Array.from(arguments).reduce((sum, num) => sum + num);
}
sumArguments(1, 2, 3); // => 6 //这里利用了转化伪数组成真数组的能力
```

4. `Array.concat()` 不传参数的时候，是一种浅拷贝的行为

5. `Array.filter(function(){})`  如名，filter，过滤，该方法创建一个新数组, 其包含通过所提供函数实现的测试的所有元素。不改变原数组

```javascript
  var newArray = arr.filter(callback(element[, index[, array]])[, thisArg])
```
`callback`: 用来测试数组元素的函数 return true时 保留元素 否则不保留。
`index`: 当前处理索引
`array`: 调用了filter的数组本身


```javascript
 // 创建了一个新数组，该数组的元素由原数组中值大于 10 的元素组成
function isBigEnough(element) { 
  return element >= 10;
}
var filtered = [12, 5, 8, 130, 44].filter(isBigEnough);
```


### 参考
[MDN - slice](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/slice)
[MDN - reduce](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)
[MDN - typeof](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/typeof)
[MDN - Array.from](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/from)
[掘金 reduce](https://juejin.im/post/5b4d35406fb9a04fd55ac064)
[掘金 Array.from](https://juejin.im/post/5d66b019f265da03a715e5d7)
[拉钩博文](https://www.lagou.com/lgeduarticle/86449.html)
[冴羽 Array去重 issue](https://github.com/mqyqingfeng/Blog/issues/27)
[阮一峰 reduce transduce 关于 函数编程](http://www.ruanyifeng.com/blog/2017/03/reduce_transduce.html)
