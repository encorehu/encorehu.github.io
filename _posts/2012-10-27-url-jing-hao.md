---
layout: post
permalink: /url-jing-hao
title: 谈谈url和井号(#号)吧
---

# 谈谈url和井号(#号)吧 #



URL的井号    http://www.ruanyifeng.com/blog/2011/03/url_hash.html


比如，Google发现新版twitter的URL如下：
　　http://twitter.com/#!/username
就会自动抓取另一个URL：

　　http://twitter.com/?_escaped_fragment_=/username


通过这种机制，Google就可以索引动态的Ajax内容。


这个是什么意思呢, 昨天我看了好多资料, 才发现 原来是google说很多网站使用了ajax技术, 但是通过用户的浏览器操作使用ajax操作得到的数据, 是不能被google索引的, 因此, google提出了一种技术方案, 凡是需要被google索引的资料, 就在url中使用#!这两个符号来提醒google, 这个是个ajax响应的页面, 我的网站允许你来抓取, 并且在后台响应了 ?_escaped_fragment_=参数




也就是说 你的网站需要响应url中#!后面的参数, 提供一个页面给google抓取

　　http://twitter.com/#!/username
就会自动抓取另一个URL：
　　http://twitter.com/?_escaped_fragment_=/username
你的服务器需要自己写好响应这个?_escaped_fragment_=参数的 函数

因为#!是漂亮的url, 而 ?_escaped_fragment_=看起来很难看, 因此, google只是通过替换的方式来抓取url中有#!的链接, 服务器这需要响应那个丑陋的url, 当然, 用户看不到这些.


当然这个可以算是seo的知识了.

因为你的网站的ajax内容如果需要google索引的话, 你就需要按照google的技术规范来做.
通常情况下, google不会索引你的ajax内容.



至于为什么ajax的要弄个#号, 这个是因为, url的 #后面的字符串通常认为是docment 的hash, 可以代表页面的不同状态, 而这个hash可以用ajax获取并操作, 比如获取之后,可以和服务器交互.
window.location.hash读取#值

window.location.hash这个属性可读可写。读取时，可以用来判断网页状态是否改变；写入时，则会在不重载网页的前提下，创造一条访问历史记录。


onhashchange事件
这是一个HTML 5新增的事件，当#值发生变化时，就会触发这个事件。IE8+、Firefox 3.6+、Chrome 5+、Safari 4.0+支持该事件。



这些就是ajax操作#后的hash的方法
具体你可以看看阮一峰的博客, 还有博客中链接的那个httpwatch的文章