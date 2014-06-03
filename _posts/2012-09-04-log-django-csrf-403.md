---
layout: post
permalink: /log-django-csrf-403
title: 记一次CSRF 403 错误
---

# 记一次CSRF 403 错误 #


今天又发现了csrf 403错误。问题找了2天，最后还是找到了。

既不是我的view写的不好，也不是我的装饰符的问题，最后还是在模板中发现没有写{csrf_token}

叫我怎么总结呢？ 这个说到底就是我使用了一个开源app导致的结果。没有仔细研究它，并且使用了它自带的模板，升级到django1.3之后，这个csrf 403的错误就会是一个噩梦，建议任何使用django1.3并开启了csrf的人，都要好好检查一下你的模板，只要是有表单form的，最好都加上csrf_token。