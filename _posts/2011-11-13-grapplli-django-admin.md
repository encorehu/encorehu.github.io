---
layout: post
permalink: /grapplli-django-admin
title: 用grappelli美化Django Admin后台管理界面
---

# 用grappelli美化Django Admin后台管理界面 #


这几天在网上发现一个django app:grappelli,该app可以用来美化Django Admin后台管理界面, 把一向丑陋的Django Admin后台界面弄得很酷很好看.

以下有一篇别人写好的文章,先供大家参考:

参考资料:

使用django-grappelli改善默认的django-admin后台 http://blog.sina.com.cn/s/blog_634bc2230100obx5.html

不过,上面的这篇文章还是有不足的地方, 仅仅是因为没有跟上最新的django1.3的步伐了.其中说到的5.2 配置启动参数，指定的adminmedia到grapelli静态文件目录,这句话我在django1.3基础上怎么也实现不了.所以还是自己到网上找资料才解决,以下是我的记录:

##
 ##

## 1.下载grappelli ##

地址:https://github.com/sehmaschine/django-grappelli] 点download,windows下zip格式,linux下tar.gz格式.

##
 ##

## 2.安装grappelli和配置 ##

### 1),设置settings.py中的INSTALLED_APPS: ###

    INSTALLED_APPS = (

        'django.contrib.auth',

        'django.contrib.contenttypes',

        'django.contrib.sessions',

        'django.contrib.sites',

        'django.contrib.messages',

        'django.contrib.staticfiles',

        'grappelli',#这里grapplli 必须位于django.contrib.admin之前

        'django.contrib.admin',

        'django.contrib.admindocs',

    )



### 2)设置ADMIN_MEDIA_PREFIX,而不是采用上面的参考资料中的设置adminmedia到grapplli什么的. ###


    #ADMIN_MEDIA_PREFIX = '/static/admin/'





    ADMIN_MEDIA_PREFIX = STATIC_URL + "grappelli/"
    这个的作用就是把admin的静态文件,由原来的admin目录,改为映射到static目录下的grapplli.

### 3)设置Url ###

        (r'^admin/doc/', include('django.contrib.admindocs.urls')),



        (r'^grappelli/',include('grappelli.urls')),



        # Uncomment the next line to enable the admin:

        (r'^admin/', include(admin.site.urls)),

同settings中配置的一样,grapplli的url映射,必须在admin之前.

### 4)收集静态资源 ###

通过运行命令:

    manage.py collectstatic

此命令,收集grapplli app目录下的static目录中的所有静态资源(CSS,js,images)到你配置的STATIC目录
下的grapplli目录下去.

##
 ##

## 3.测试grapplli应用 ##

manage.py runserver

然后打开 http://localhost:8000/admin/ ,可以看到登录界面了.是不是很酷呢?

看看效果图:


我已经做好了一个Demo,适合django1.3的,稍后放到 google code上去.