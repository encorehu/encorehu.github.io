---
layout: post
permalink: /django-13-server-zidingyi-staticfiles-images-css
title: django1.3中的static files 如何serve自己手动放进去的images,css,js等文件,解决404错误
---

# django1.3中的static files 如何serve自己手动放进去的images,css,js等文件,解决404错误 #


我们知道,django1.3开始django使用了一个叫做staticfiles的contrib app.

这个app主要是用来server静态文件的,与media的区别就是,staticfiles是网站本身需要用到的images,css,js,而media就是定义为网站用户上传的头像,图片,文件等等.这样一区分,就容易分开管理文件了.static的优点就是集中管理django网站项目各个app使用的静态文件.

豆瓣有一篇介绍的比较详细:django 1.3 中显示图片，使用静态文件的问题(django static files) http://www.douban.com/note/152041308/

    manage.py collectstatic

这一命令将散落在各个app子目录下的static文件夹中的静态文件复制到一个目录(STATIC_ROOT,我用的是/static/,就是项目根目录下的static文件夹),同时被复制过来的还有STATICFILES_DIRS里面的文件.

我这里就不废话多说别人已经说过的东西了.

我要说的是,如何serve自己手动放进去的images,css,js等文件.

因为我发现我以前django1.2之前的版本,所有网站使用的文件都是放在media_root下面的.现在有了staticfiles,肯定还是放在static文件夹下面,但是把一些公用的images,css文件夹直接复制到static下面后,居然发现不能serve这些静态文件.浏览器中直接访问 http://localhost:8000/static/images/logo.jpg ,就会出现404错误.

我在网上找过一些这方面的资料,但是官方网站上说的是不要手动放置任何文件到static目录下.既然我的那些images是公用的文件,又不属于任何一个app,那就只有想别的办法了.

新浪网上的朋友有一篇:django 框架下关于引用静态文件的配置方法 http://blog.sina.com.cn/s/blog_6b43aa770100taai.html

里面说到的解决办法是:


解决办法:


1.通常在django下所有的静态文件（.img|.css|.js|other）都放在一个static文件夹下

2.在后来的处理当中，发现static文件夹上一层还需要一个文件夹，不要直接放在根目录下.

STATICFILES_DIRS是一个可以引起注意的配置选项,我试着把它修改为:



    STATICFILES_DIRS = (

        STATIC_ROOT,

    )




但是它报错说,STATIC_ROOT不能出现在STATICFILES_DIRS之中.

官网文档提到是STATICFILES_DIRS中配置的目录其实是tuple,例如:



    STATICFILES_DIRS = (

        # ...

        ("downloads", "/opt/webfiles/stats"),

    )


以上来自官网文档:https://docs.djangoproject.com/en/dev/ref/contrib/staticfiles/

这个"downloads"其实是一个prefix,相对于STATIC_URL用的.所以可以使用http://localhost:8000/static/downloads  来访问他定义的目录下的资源.

所以我的尝试就开始于这里:



    # Additional locations of static files

    STATICFILES_DIRS = (

        # Put strings here, like "/home/html/static" or "C:/www/django/static".

        # Always use forward slashes, even on Windows.

        # Don't forget to use absolute paths, not relative paths.

        ("images", os.path.join(STATIC_ROOT,'images').replace('\\','/')),

        ("css",    os.path.join(STATIC_ROOT,'css').replace('\\','/')),

        ("js",     os.path.join(STATIC_ROOT,'js').replace('\\','/')),

    )




做了这些之后,OK了.

直接访问http://localhost:8000/static/images/logo.jpg 浏览器中能看到了.

行文至此,似乎多说的必要了.各位请拍砖.