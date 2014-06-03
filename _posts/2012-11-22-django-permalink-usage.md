---
layout: post
permalink: /django-permalink-usage
title: Django的models.permalink的使用介绍
---

# Django的models.permalink的使用介绍 #


看得懂英文的, 请看 官方的文档和下面两篇:

    Use Django's Permalink Decorator with Generic Views https://gist.github.com/2775049



    Django's wonderful permalink decorator http://www.achanceofbrainshowers.com/blog/tech/2010/11/29/djangos_permalink_decorator/


看不懂的, 我就建议你看下面这篇:

    http://mxjloveyou.blog.163.com/blog/static/1762546892012231105635330/ 看这篇 讲的很清楚了 Django另一个隐含函数permalink


permalink 不仅仅是对一个叫做get_absolute_url的函数进行修饰, 还可以对很多东西进行修饰

比如
object.get_edit_url
object.get_json_url
object.get_download_url 等等, 你能想到的一些你的object常用的url都能够修饰

然后在你的模板文件中直接调用即可