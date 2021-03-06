---
title: 浏览器-输入url系列
date: 2020-03-13 15:03:39
tags: 浏览器
---

> 共是五个步骤

### 1 浏览器的搜索框
浏览器会根据输入的内容，去匹配是用对应的引擎搜索内容，还是直接跳转url。
详细的讲，就是
- 搜索内容+默认搜索引擎，形成新的URL
- 符合URL规则的话，在输入内容基础上加上协议，形成新的URL

### 2 dns解析
- 自己的理解：路由解析，找到背后的ip地址，路由缓存，域名缓存，为什么第二次加载会更快，跟这个有关。
- dns：一个域名与ip地址相互映射的分布式数据库
- 我们无法记住的ip，那一个个长相平凡但是堆积在一起就是记不住的几个数字，是的，dns帮我们记住了。他以电话簿的形式，记住了许许多多的ip地址，映射到了所有网站上。
- 在客户端输入 URL 后，会有一个递归查找的过程，从浏览器缓存中查找->本地的hosts文件查找->找本地DNS解析器缓存查找->本地DNS服务器查找，这个过程中任何一步找到了都会结束查找流程。
- 本地dns服务器找不到的时候，去查询其他根dns服务器，顶级域服务器，权威dns服务器
- 查找的过程是有优化的地方：
    + DNS存在着多级缓存，从离浏览器的距离排序的话，有以下几种: 浏览器缓存，系统缓存，路由器缓存，IPS服务器缓存，根域名服务器缓存，顶级域名服务器缓存，主域名服务器缓存。 
    + 在域名和 IP 的映射过程中，给了应用基于域名做负载均衡的机会，可以是简单的负载均衡，也可以根据地址和运营商做全局的负载均衡。
    + 补充：域名与ip之间有映射关系，但是大多数情况下却不是一一对应的，我们可以将多个ip映射到同一个域名，因为用户是通过域名去进行访问的，具体访问哪个地址，则有DNS服务器的映射记录和一定的负载均衡算法来决定，用户拿到具体ip后再去访问，而这个过程对用户来说完全是透明的，通过DNS分流完成了第一步的负载均衡：

### 3 Https，ssl协议，Tcp建立连接
浏览器通过http/https协议，建立tcp连接，三次握手建立请求，传输连接建立完毕。

### 4 服务器处理请求，返回结果
服务器，解析请求头，对比是否过期字段，缓存字段，返回对应的状态码和内容。返回所有内容后，可以关闭连接了亲。

### 5 浏览器解析/渲染 
dom树解析，转换css单位成通用单位，cssdom解析，绘制。优先视口。

- 构建 DOM 树、样式计算、布局阶段、分层、栅格化和显示
    + 构建DOM树：html内容解析成DOM树（这里面用到HTML的parser）
    + css样式表，转为浏览器可以读懂的CSS规则树
    + 由DOM树和CSS规则树，创建render树，计算布局信息，包括所有元素的尺寸、位置、还有position、z-index、overflow
    + 绘制render树，绘制页面像素
    + GPU绘制，显示到显示屏


小知识点：
- HTML的parser：
    + 转换：HTML编码成字符
    + 分词：给字符们打上标记Tokens
    + 词法：tokens转换成包含属性和规则的对象
    + DOM树创建

参考：
- [文章](https://mp.weixin.qq.com/s/DLq_GIkdnuOayThfi3jI0A)
- [https](https://segmentfault.com/a/1190000012196642)
- [https](https://zhuanlan.zhihu.com/p/26682342)
- [负载均衡](https://blog.csdn.net/weixin_34050389/article/details/93027673)
- [If-Unmodified-Since](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/If-Unmodified-Since)
- [If-Modified-Since](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/If-Modified-Since)
- [Expires](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Expires)
- [Cache-Control](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Cache-Control)
- [浏览器相关](https://mp.weixin.qq.com/s?__biz=MzI0MzIyMDM5Ng==&mid=2649825731&idx=1&sn=2c0835cebfee3a44e82d9efeb54f6e43&chksm=f175eb00c602621649fd46d7c9de87f7f08a4cdaa9e08808b938948584ca6977e24a5f5336ce&scene=21#wechat_redirect)
- [全面](https://segmentfault.com/a/1190000013662126)
- [浏览器进程/线程](https://segmentfault.com/a/1190000012925872)

