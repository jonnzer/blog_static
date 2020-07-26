---
title: JS执行顺序 || EventLoop
date: 2019-12-03 15:08:36
tags: JS原理
---

### 1.EventLoop
EventLoop就是那只看不见的手，由它主宰着JS的一切执行。
JS的异步代码，遇到时会被放置在一个小圈子里，当把同步代码执行完，就从这圈子里取出来异步代码执行。
```javascript
console.log('script start');
setTimeout(function() { // 置底进程
  console.log('from setTimeout');
},0)
console.log('script end');
// 执行顺序：script start => script end => from setTimeout
```

### 2.微任务 && 宏任务
优先级：同步代码 > 微任务 > 宏任务 > 异步代码
宏任务：script , setTimeout, setInterval, setImmediate, i/0 , UI rendering ,requestAnimationFrame
微任务：process.nextTick, promise, Object.observe, MutationObserver,await

+ new Promise(fn) // 也属于微任务，执行优先级高
+ 且process.nextTick优先级大于promise.then
+ await后的async的fn 也会立刻执行 因为这是一个promise返回

```javascript
console.log('1');

setTimeout(function() { // setTimeout1
    console.log('2');
    process.nextTick(function() { // process2
        console.log('3');
    })
    new Promise(function(resolve) { // promise2
        console.log('4');
        resolve();
    }).then(function() { // then2
        console.log('5')
    })
})
process.nextTick(function() { // process1
    console.log('6');
})
new Promise(function(resolve) {
    console.log('7'); // new Promise立即执行
    resolve();
}).then(function() { // then1
    console.log('8')
})

setTimeout(function() { // setTimeout2
    console.log('9');
    process.nextTick(function() { // process3
        console.log('10');
    })
    new Promise(function(resolve) { // promise3
        console.log('11');
        resolve();
    }).then(function() { // then3
        console.log('12')
    })
})

// 输出顺序 ：
// 第一步：
// 
```

执行完 1 和 7后，现在看下，身边有这些任务

| 宏任务Event | 微任务Event Queue |
| ------------- |:-------------:| 
| setTimeout1   | process1      | 
| setTimeout2   | then1      |  

现在第一轮的主线跑完了，轮到第一轮的微任务了，
所以，接下来执行的是process1和then1，也就是
1，7，6，8， 

第一轮结束，
第二轮开始。

接下来是开启新的一轮eventLoop。开始查询到新的宏任务setTimeout1；

| 宏任务Event | 微任务Event Queue |
| ------------- |:-------------:| 
| setTimeout1   |  process2     | 
| setTimeout2   |  then2        | 
 
1，7，6，8，2，4，3，5

这下好了，setTimeout1宏任务完成了。
接下来，就剩setTimeout2宏任务。

| 宏任务Event | 微任务Event Queue |
| ------------- |:-------------:| 
|               |  process3     | 
| setTimeout2   |  then3        |  

1，7，6，8，2，4，3，5，9，11，10，12


### 3. Vue的nextTick

> 异步更新内部是最重要的就是nextTick方法，它负责将异步任务加入队列和执行异步任务。
VUE  也将它暴露出来提供给用户使用。在数据修改完成后，立即获取相关DOM还没那么快更新，使用nextTick便可以解决这一问题。

> watcher:每个监视者都有他们自己的id，当没有记录到对应的监视者.
即第一次进入逻辑，否则是重复的监视者，则不会进入。这一步就是实现监视者去重的点。

> new Promise > MutationObserver > setImmediate > setTimeout

> 异步队列更新dom和数据，执行callback回调

```js
<input v-if="show" type="text" ref="myInput">

// js

data() {
    show: false,
},
mounted() {
    this.show = true
    
    this.$refs.myInput.focus() // error

    this.$nextTick(function() { // 获取dom的更新，成功执行
        this.$refs.myInput.focus()
    })
},

```


参考链接：
[掘金](https://juejin.im/post/5aa8a07cf265da238a3022a4)
[掘金2](https://juejin.im/post/59e85eebf265da430d571f89)
[segment](https://segmentfault.com/a/1190000019494012)
[blog](cxymsg.com/guide/eventLoop.html#前言)
[promise async await](https://segmentfault.com/a/1190000015057278)
[vue nextTick原理](https://github.com/answershuto/learnVue/blob/master/docs/Vue.js%E5%BC%82%E6%AD%A5%E6%9B%B4%E6%96%B0DOM%E7%AD%96%E7%95%A5%E5%8F%8AnextTick.MarkDown#nexttick)
