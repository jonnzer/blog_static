---
title: H5-insertAdjacentElement
date: 2019-03-11 22:42:25
tags: H5
---

###### insertAdjacentElement作用是？
该方法将一个给定的元素节点`插入到`*相对于被调用的元素*的给定的`一个位置`。

###### 如何用呢？
```js
element1.insertAdjacentElement(position, element2);
```
参数positon:
+ `beforebegin`: 在该元素本身的前面.
+ `afterbegin`:只在该元素当中, 在该元素第一个子孩子前面.
+ `beforeend`:只在该元素当中, 在该元素最后一个子孩子后面.
+ `afterend`: 在该元素本身的后面.

element1：不动的元素
element2：被插入的元素

###### 具体例子
```js
let fileWrap = document.querySelector(`#${this.fileWrapId}`);
fileWrap.insertAdjacentElement('afterbegin', elem); // elem为插入的元素
```

###### 参考链接：
[mdn](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/insertAdjacentElement)