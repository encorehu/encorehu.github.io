---
layout: post
permalink: /ddtcms-code-release-method
title: 理清ddtcms.com和DDTCMS发行版本之间的关系
---

# 理清ddtcms.com和DDTCMS发行版本之间的关系 #

			目前的ddtcms.com确实是基于DDTCMS-v0.3建立的, 但是那个DDTCMS-v0.3是发行版本, 也就是released给其他用户使用的, 是给public用的, 这个和网站目前使用的django程序不一样.

网站的django程序确实修复了一些bug, 同时也对网页界面的显示进行了完善, 但是不一定表示网站用到的代码就一定会发布到DDTCMS中去, 就是说, 网站以后使用的代码和DDTCMS发行版本的代码不会一样了. 以前我完全将二者混淆了. 现在还是区分开来为妙, 不能完全的交会徒弟, 饿死师傅啊.

那么, 网站中的什么代码会发布到新的DDTCMS中去呢?
这个就是DDTCMS的设计中提到的, 网站的方向和DDTCMS一致的话, 那些代码就会发布到DDTCMS中去, 而不一致的方面或者应用就不会发布到DDTCMS中去.

那么DDTCMS的设计主要侧重于那些方面呢?
这个最初的想法就是做一个新闻或者文章系统, 具备目前一些主流的cms的基本功能, 做到功能一样或者更好一点, 当然在设计上还是要基于django的基本功能来, 不能超越django目前提供的功能, 和python能够实现的功能之上.