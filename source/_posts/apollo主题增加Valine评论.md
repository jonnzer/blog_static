---
title: apollo主题增加Valine评论
date: 2020-07-11 19:08:18
tags: Apollo Hexo Valine 
---

#### 初衷
大道至简
最近刚迁移主题到`Apollo`，相当清新的主题，具体的参考[github](https://github.com/pinggod/hexo-theme-apollo)

#### 评论系统的选择
从支持的表情与颜值上来看，果断选择了`Valine`
效果图可以看博客下方😊

<div class="tip">
    因为Apollo主题已经关闭issue，所以只能自己去增加评论的初始化代码了
</div> 

#### 魔改
1 `themes/apollo/_config.yml`: 
    ```yml
        valine:
            # 开启 Valine 评论
            enable: true
            # 设置应用 id 和 key
            appId: 您的appId
            appKey: 您的appkey
    ```
2 `themes/apollo/source/js/Valine.min.js`： 拷贝Valinecdn路径，缓存到本地，以防万一
3 `themes/apollo/layout/partial/head.jade`: 增加引用`script(src=url_for("js/Valine.min.js"))`
4 `themes/apollo/layout/partial/comment.jade`: 增加主题的评论判断
     ```JavaScript
        if theme.valine
            div#vcomments
            //script(src="//unpkg.com/valine/dist/Valine.min.js")
            script.
                new Valine({ // 里面的参数配置具体参考Valine官网
                    el: '#vcomments',
                    appId: `#{theme.valine.appId}`,
                    appKey: `#{theme.valine.appKey}`,
                    placeholder: '快来评论吧(#^.^#)',
                    avatar: 'wavatar'
                });
      ```
     
---

参考
[Valine前后所需介绍](https://www.playpi.org/2019032001.html)
[Valine官网](https://valine.js.org/)
