---
title: JS模块化
date: 2020-01-04 14:29:41
tags: 模块化
---

> 模块化的简单包装

```javascript
var module = (function() { // 利用了自执行函数的封闭性
    var _count = 0;
    var m1 = function() {
        alert(_count)
    }
    var m2 = function() {
        alert(_count + 1)
    }
 
    return {
        m1: m1,
        m2: m2
    }
})()
```


###  1 JS模块化发展历史

- CommonJS 和 AMD CMD 鼎足之势 （前） ES6 （后）
（知名框架 Node.js、RequireJS前置加载 和 Seajs就近加载 分别对应前三个）
- 谈谈现阶段：
    + ES6标准发布后，module成为标准，标准使用是以`export`指令导出接口，以`import`引入模块。
    + 在我们一贯的node模块中，我们依然采用的是`CommonJS`规范，使用`require`引入模块，使用`module.exports`导出接口。是`同步`加载 JS 脚本。这么做在服务端毫无问题，因为文件都存在磁盘上，然而浏览器的特性决定了 JS 脚本需要异步加载，否则就会失去响应，因此 CommonJS 规范`无法直接在浏览器`中使用。

### 2 对于es6模块的看法（参考阮一峰老师）
+ `es6标准`将成为服务器和浏览器端模块化的`通用解决方法` 
+ `运行时加载`：代码运行到这个语句的时候才会加载 
+ `编译时加载`：打包工具编译js时，就会去分析模块之间的依赖关系，所以不能放在任何条件代码块里。层级最优先
+ es6模块中的this指向是undefined，不要轻易用this, CommonJs中的this指向当前模块
+ export的使用，如果需导出的模块前面没有export，那在export的时候就要加上{}
    ```javascript
    function fn() {}
    export {fn}
    ```
+ export导出语句是动态绑定的，如果导出的值发生变化，那import进的值也会随之变化
    ```javascript

    // exportJs
    export var foo = 'bar';
    setTimeout(function(){ () => foo = 'baz'; },500);

    那么foo变量值将在500ms后变成baz字符串，这点与`commonJS`不一样，commonJs是会缓存值。
    ```
+ `import`命令具有提升效果，会提升到整个模块的头部并首先执行，但还是建议放在头部写比较好。
    ```javascript
        foo();
        import {foo} from 'module'; // right 
    ```
+ *号指定整体输出的一个对象
    ```javascript
        // circle.js
        export funciton fn1(){

        }

        export function fn2(){

        }

        // main.js 需要引入cilcle.js
        import * as circle from './circle';
        
        // 调用：
        circle.fn1();
        circle.fn2();

    ```
+ export 的default输出，默认输出
    ```javascript
        // exportJs
        export default function(){
            console.log('foo')
        }

        // importJs
        import customFn from './exportJS'
        customFn();
    ```
+ import函数大括号的写法时机
 export后面接着default的时候，不用大括号，没接着的时候是要大括号的。
    ```javascript
    // 无 default
    export function fn1() {}
    import { fn1} from 'exportJs';
    
    // 有default
    export default function fn2() {}
    import fn2 from 'exportJs'
    ```
    
+ 因为静态分析没有运行时加载那么“动态化”，所以，import有个提案，目前可以在webpack使用，可以动态输入一个较为狭窄的路径，然后返回一个promise对象
这个情况下的import是支持`按需加载`，`条件加载`，和`动态的模块路径`。
 ```javascript
    const main = document.querySelector('main')
    import('./someModule/${someVariable}.js')
        .then(module => {
            module.loadPageInfo(main)
        })
        .catch(err => {
            mian.textContent = err.message;                                                 
        })
 ```

+ script标签的defer和async属性差异，两者都是取消了js的同步属性。
但是defer是`渲染完再执行`，而async是`下载完就执行`。

+ 待验证：es6模块在网页上的加载
    ```javascript
        <script type="module">
            import utils from './util.js';
        </script>
    ```

+ es6模块，导出的变量不可以重新赋值，但可以添加或修改属性，就是内存地址不能改，里面存的内容随你改，待验证。
    ```javascript
    // lib.js
    export let obj = {}
    
    // main.js
    import { obj } from 'lib'

    obj.prop = 123; // right
    obj = {}        // typeError
    ```
+ 使用`import`命令加载`commonJS`模块，Node会自动将`module.exports`当成模块的默认输出，即等同于`export default`
  ```javascript
    // b.js
    module.exports = null;

    // es.js
    import foo from './b.js'

    import * as bar from './b.js'
    // bar = {default: null}

    import { somethimg } from 'fs' // error！！！

    import * as express from 'express'
    const app = express.default() // right
  ```

+ `commonJs`的加载原理
commonJs每个模块都是一个脚本文件，Node内部用require命令第一次加载就会执行这个脚本，然生成一个对象，这个会缓存，即是多次require，不清缓存下，都取第一次。
    ```javascript
        {
            id: '',         // 模块名
            exports: {},    // 导出模块, 
            loaded: true    // 是否加载过
        }
    ```
+ 循环加载：模块之间相互引用
CommonJs会出现，输出已执行的部分，未执行的部分不会输出
es6模块则不会报错

参考文章：
[掘金](https://juejin.im/post/58882a42128fe100684ad9de)
阮一峰es6 模块篇章