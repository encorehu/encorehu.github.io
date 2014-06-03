---
layout: post
permalink: /what-happend-when-django-staticfiles-settings-debug-is-true-mode
title: django staticfiles DEBUG设置为True或者False的关键内幕
---

# django staticfiles DEBUG设置为True或者False的关键内幕 #

			Django staticfiles 详解
1. settings.DEBUG=True
使用runserver的时候, handler=StaticFilesHandler, 所有的staticfiles, 也就是静态文件, 会使用该handler来处理, 具体的处理方式就是 按照settings.StaticDirs中的配置来serve staticfiles.

2. settings. DEBUG=False
使用runserver, handler = 普通的django.core.handler.wsgi.WsgiHandler, 该 handler 不处理staticfiles. 啥意思呢, 就是说你可以使用nginx apache等webserver来serve staticfiles 静态文件.