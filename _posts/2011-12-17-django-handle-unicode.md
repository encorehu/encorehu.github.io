---
layout: post
permalink: /django-handle-unicode
title: Django 处理 unicode的方法
---

# Django 处理 unicode的方法 #


比如说,随便一个文件,


    from django.utils.encoding import force_unicode,smart_unicode, smart_str, DEFAULT_LOCALE_ENCODING



    try:



        line = smart_unicode(line,DEFAULT_LOCALE_ENCODING)



    except UnicodeDecodeError:



        line = "" # ignore this line


