---
layout: post
permalink: /python-web-dev-suggestions
title: 用python开发web的几个建议
---

# 用python开发web的几个建议 #


﻿﻿各位新来的童鞋:
非常欢迎你来到本群参与Python Web开发基础话题方面的各种讨论, 在参加讨论前, 请花5分钟看看下面的指引:

### 1. 选用哪个Python Web框架? ###

这个完全是根据你的喜好来的, 现在大家都在用的是Django, Flask, Web.py, Tornado,pylons以及其他的框架(框架确实太多了).

如果说你是新手, 建议你先用用django试试, 那些说django不好的人, 其实都用过django, 发现了很多不足之后, 才能说django不好, 所以, 不管你怎么不喜欢django, 先用用, 真的不喜欢了, 我觉得你可以用用Flask, Flask的各个组件都可以自己选, 很灵活.

如果你是老手了, 我建议你选择webpy或者tornado, 充分发挥你自己的能力.

如果你贪图小巧, 仅仅为了测试, 你可以试试 bottle这个框架, 我觉得有点和flask类似吧, 而且仅仅只有一个py文件.

还有一个国产的python web框架, uliweb, 大婶的作品, 喜好的可以试试.使用ulipad开发uliweb项目, 绝配了.

### 2. 使用什么开发工具来开发Python Web项目 ###

对于工具控来说, 不用工具是不舒服的, 也是不人道的.
很多人推荐使用JetBrains PyCharm, 你说﻿什么? 要注册码? 这能难倒中国人吗?如果你有正版洁癖, 那算了, 你还是用免费的吧, 好像还有很多选择, eclipse+pydev, sublime, WingIDE, pyscripter等等. java转过来的人, 还是用eclipse+pydev吧.

对于喜好高级文本编辑器的人, 可以选择 Emacs, Vim , UltraEdit, Notepad++...

总之一句话, 选你所爱.

### 3.使用什么服务器部署Python Web 项目 ###

以下是几个尝试:
linux下:
 - nginx+uwsgi(我已经用上了) - nginx+fcgi(群里有人用这个) - apache+mod_wsgi
windows下:

 - iis? 貌似还没有谁成功用上 - apache+mod_wsgi(我测试过, ok) - apache+mod_python(mod_python已经停止维护了,并且不稳定; apache建议使用mod_wsgi, 放弃mod_python) - nginx+fcgi(这个我也测试过, 可以用, 但是nginx只能启动一个进程, 启动2个就会死掉, 所以nginx在windows下表现不佳)
### 4.使用python3还是python2? ###

这真的是个问题!

我的回答是, 如果你没有历史欠账, 就是你以前没有开发过python的任何程序, 就用最新的python3吧, django1.5可以在python3下使用了. 现在python3在国外使用的人慢慢多起来了, 就好像国内开始学习python的人慢慢多起来一样. 想学django1.5的, 建议用python3.

如果用过, 还是用python2.7为宜, 毕竟目前基于python2.x的程序和库比较多. 要进入企业工作的, 还是学python2.x吧.

### 5.python3和python2 的主要区别是什么? ###

老话题了, 看看别人已经写好的:

http://www.cnblogs.com/codingmylife/archive/2010/06/06/1752807.html] Python3.x和Python2.x的区别
http://jythoner.iteye.com/blog/290135] Python3.0与Python2.X的区别

### 6. 还有什么问题? ###

嗯, 就是怎么提问, 这个建议看看<提问的艺术>: 请到本群的专用论坛看: http://www.i8d.net/forum.php?mod=viewthread&tid=5178]

### 7.还有哪些资源? ###
 - 到群共享看看 - 看看我整理的一些链接(我用django多点, 所以大多是django链接): http://ddtcms.com/link/]  - 到豆瓣相关小组看看 - 到CNblogs.com, csdn.net, ChinaUnix.net相关板块看看

国外的:

到github.com, code.google.com, stackoverflow.com和django, flask, tornado, webpy等框架的官方网站看看.