---
title: MVVM实现（Object.defineProperty && 观察订阅）
date: 2020-07-23 21:01:50
tags: MVVM 框架
---

### 1. MVVM特点
![MVVM注解图1](/MVVM/MVVM1.png)
![MVVM注解图2](/MVVM/MVVM2.png)

### 2. MVVM的功能：
+ 给所有data属性值都设置数据劫持，绑定好属性观察。（*也是深度响应原理*）




---

### 知识储备
1、 `Object.defineProperty` 给对象设置某属性，并且可以设置setter，用于绑定属性。

```js
let obj = {}
Object.defineProperty(obj, 'school',{
    configurable: true, // 让 delete 更新属性值 生效
    enumberable: true,
    get(){ // 取值
        return 123
    },
    set(val){ // 设置值时触发
        console.log('setting ')
        obj.school = val // 超出内存 mag size
    }
})
console.log(obj)
```

