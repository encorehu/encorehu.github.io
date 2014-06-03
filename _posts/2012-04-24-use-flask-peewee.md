---
layout: post
permalink: /use-flask-peewee
title: 使用Flask和peewee来开发的一点记录
---

# 使用Flask和peewee来开发的一点记录 #


按照朋友的指示, 开始使用flask+peewee来做开发, 主要需要安装flask-0.8, peewee-0.9.4, wtforms-1.0.2,jinja2-2.6,wtf-peewee-0.1.3,werkzeug-0.8.3, 已经装好了, 祝我goodluck.

没有想到, 先安装了pypi上的peewee 0.4.0之后, 其他的东西自动安装, 最后peewee0.9.4覆盖了0.4.0之后,这个peewee0.9.4居然不能正确导入.

    >>> import peewee



    Traceback (most recent call last):

      File "<stdin>", line 1, in <module>

      File "C:\python25\lib\site-packages\peewee-0.9.4-py2.5.egg\peewee.py", line 19

    64

        return datetime.datetime(*time.strptime(value, '%Y-%m-%d %H:%M:%S')[:6], mic

    rosecond=usec)





           ^

    SyntaxError: invalid syntax