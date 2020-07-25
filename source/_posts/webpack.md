---
title: webpack的后花园
date: 2019-12-11 17:17:00
tags: 工具 webpack
---

### 1.webpack底层
    它是一个打包工具。
    webpack本身，在不借助loader的情况下，只会打包js。
    
    （引用）webpack处理应用程序时，会递归的构建一个依赖关系图，其中包括应用程序需要的每个模块，然后把所有模块打包成一个或多个bundle。
### 2.几个核心概念
+ 入口
+ 出口
    - 入口和出口很好理解，入口就是打包谁，出口就是要输出的文件。
     entry:
     output:{ path, filename }
+ loader
    - 帮助webpack识别非js文件，包括css，图片等。
    - 示例：
        test: 作用等同于正则里面的匹配
        use: 使用loader
    ```javascript
        rules: [
                {
                    test: /\.js$/,
                    use: [
                        'babel-loader',
                        {
                            loader: 'eslint-loader',
                            options: {
                                emitWarning: true,
                                emitError: true,
                                failOnError: true
                            }
                        },
                    ],
                    exclude: /node_modules/
                },
                {
                    test: /\.css$/, use: ["style-loader","css-loader"]
                },
                {
                    test: /\.(png|jpg)$/,
                    loader: 'url-loader?limit=8192'
                },
                {
                    test: /\.vue$/,
                    use:[
                        {
                            loader: 'vue-loader',
                            options: {
                                cssSourceMap:false,
                                transformToRequire:{},
                            },
                        },
                        {
                            loader: 'eslint-loader',
                            options: {
                                emitWarning: true,
                                emitError: true,
                                failOnError: true,

                            }
                        },
                    ],
                    exclude: /node_modules/
                },
            ]
    ```
+ plugins
我还没认真弄懂plugins，它好像有很多好用的功能。比如压缩js或者生成好的静态资源增加版本号。待研究 ***@todo***
--- 
常见问题：
1、webpack打包原理？
2、loader，plugins分别是？
3、热更新原理是？
4、webpack性能优化的方法？

--- 
+ webpack的打包原理：(树状依赖)
    - 利用babel完成代码转换,并生成单个文件的依赖.
    - 从入口开始递归分析，并生成依赖图谱
    - 将各个引用模块打包为一个`立即执行`函数
    - 将最终的bundle文件写入bundle.js中

+ loader其实就是一个`Node.js` 模块，这个模块需要导出一个函数







    