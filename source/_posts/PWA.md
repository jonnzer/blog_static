---
title: PWA
date: 2020-02-06 17:19:35
tags: 工具
---

### 1.PWA是什么？（`Progressive Web App`）
书面上意思：渐进式网络应用
特点：
+ 稳定：不好的网络环境下也有好的展现数据能力（`service worker`）
+ 过渡自然的动画交互，良好的滚动，响应快
+ 像原生一般丝滑，可以推送，可安装
+ android支持度好，apple支持度差

### 1.1 关键技术
+ service worker : 一个存在于客户端和服务器之间的代理，可以控制缓存和应对资源请求,预缓存关键资源。所以它对安全的要求也很高，需要在Https环境下运行。
+ manifest: （可以理解为一个可设置的入口文件）
​              能够将你浏览的网页添加到你的手机屏幕上
​              在 Android 上能够全屏启动，不显示地址栏 （ 由于 Iphone 手机的浏览器是 Safari ，所以不支持哦）
​              控制屏幕 横屏 / 竖屏 展示
​              定义启动画面
​              可以设置你的应用启动是从主屏幕启动还是从 URL 启动
​              可以设置你添加屏幕上的应用程序图标、名字、图标大小


### 2.可以用来干嘛呢
+ 推送，这点需要安卓去验证，目前PWA的支持度仅是安卓比较好。ios的google浏览器也不支持
+ 离线缓存。 可以设置缓存的资源，即使离线也可以访问



### 3. DEMO
demo已放在mineScript文件夹下
github地址是：[demo](https://github.com/jonnzer/good_snippet/tree/master/PWA)

+ 代码结构：
- sw.js: 引入serviceWorker.js，定义缓存资源，部署整个sw的生命周期代码
- manifest.json: 一个定义好背景颜色，展示图片，icon，的入口文件。
- 建议部署在https环境下的cdn。

参考链接：
[官网](https://developers.google.com/web/progressive-web-apps/)
[掘金1](https://juejin.im/post/5ae2f82f6fb9a07acd4d761e)
[serviceWorker的调试](https://www.villainhr.com/page/2017/01/08/Service%20Worker%20%E5%85%A8%E9%9D%A2%E8%BF%9B%E9%98%B6)