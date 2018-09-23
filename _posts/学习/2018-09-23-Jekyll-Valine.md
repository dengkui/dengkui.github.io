---
layout: post
title:  Jekyll博客添加Valine评论系统
category: 学习
tags:  软件学习
keywords: Jekyll Valine
description: 为Jekyll博客添加Valine评论系统
---

作为一个博客网站，自从多说挂了，网易云评论撤了，disqus不太友好，就一直缺少一个友善有力的评论系统，但是偶然间发现了Valine这款神器，既能实现方便友好的评论，并且外观也还简洁，于是乎，果断添加到自己博客中。

以下简要叙述一下如何在我的Jekyll博客中添加该评论系统（一个小白的捣鼓历程）：

1. 注册账号获取账号信息
  
    **Valine**评论系统依托于[Leancloud](https://leancloud.cn/ "Leancloud")，我们先注册一个账号，并创建应用，选择开发版即可以（免费），点击设置中的应用Key获取自己的App ID、App Key。具体流程以及截图参考这篇文章[Jekyll 添加 Valine评论](https://lindexi.github.io/lindexi/post/jekyll-%E6%B7%BB%E5%8A%A0-Valine-%E8%AF%84%E8%AE%BA.html "test")即可，在此不再复述。

2. 添加网站评论

    在我的博客中，需要添加评论的都是采用的`post.html`页面，因此可以将评论代码添加到该页面，我的方式是在`_includes`中创建`comments.html`文件，然后在`post.html`中`content`后面添加引用代码{% include comments.html %}，实现在文章下方添加评论。

    `comments.html`页面的代码如下,将App ID和App Key替换为自己的信息：

        <script src="//cdn1.lncld.net/static/js/3.0.4/av-min.js"></script>
        <script src='//unpkg.com/valine/dist/Valine.min.js'></script>
        <div id="comment"></div>
	    <script>
         var valine = new Valine();
         valine.init({
             el:'#comment',
             appId:'App ID',
             appKey:'App Key',
             notify:true,
		     path: '{{ page.url }}',
             placeholder:'快到碗里来^_^'
        })
        </script>
3.提交更改信息

    将更新后的文件提交到github中，小伙伴就能刷出评论框了。

小伙伴们快来使用吧，O(∩_∩)O哈哈~

## 参考资料

[1] [Jekyll 添加 Valine评论](https://lindexi.github.io/lindexi/post/jekyll-%E6%B7%BB%E5%8A%A0-Valine-%E8%AF%84%E8%AE%BA.html "test")

[2] [Valine介绍](https://valine.js.org/ "Valine介绍")

[3] [Valine -- 一款极简的评论系统](https://ioliu.cn/2017/add-valine-comments-to-your-blog/)