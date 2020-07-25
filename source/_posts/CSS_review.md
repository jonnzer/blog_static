---
title: CSS准备
date: 2020-04-08 17:21:35
tags: 面试
---

### 1. 介绍下BFC及其应用
+ 1.1 BFC是什么:
它是css盒模型的css渲染模式。
有着盒子对齐，外边距重叠，清除浮动，防止文字环绕，多列最后列宽度自定义（不挤掉）

+ 1.2 怎么新建一个BFC:
一个新的BFC可以通过给容器添加任何一个触发BFC的CSS样式，如overflow: scroll, overflow: hidden, display: flex, float: left,或者 display: table来创建。
display:table可能会产生一些问题
overflow:scroll可能会显示不必要的滚动条
float:left将会把元素置于容器的左边，其他元素环绕着它
overflow:hidden将会剪切掉溢出的元素

参考链接：
[w3cPlus](https://www.w3cplus.com/css/understanding-block-formatting-contexts-in-css.html)
[MDN](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Block_formatting_context)


### 2. A: display:none; B: visibility:hidden; C: opacity: 0的区别
+ 展示程度上
都不显示
+ dom可操作上
A不可操作，B不可操作，C可操作
+ 继承性
A和C的父盒子设置了对应的属性A和C，则他们的子代也是不可见，不可操作。B的子代是可以设置显示的
+ 性能
A改变了结构，引起文档回流，性能消耗最大
B引起元素重绘，消耗中等
C消耗最低

参考链接：
[张鑫旭BLOG](https://www.zhangxinxu.com/wordpress/2012/02/css-overflow-hidden-visibility-hidden-disabled-use/)