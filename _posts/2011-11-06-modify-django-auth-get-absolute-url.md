---
layout: post
permalink: /modify-django-auth-get-absolute-url
title: 如何修改django auth.user 的默认get_absolute_url值
---

# 如何修改django auth.user 的默认get_absolute_url值 #


    #Django#User的urls设置问题在你使用了UserProfile之后将会出现一个问题,就是User Model 的get_absolute_url在django的auth app中默认是/users//, 如果你已经扩展实现了一个UserProfile的话,你可能需要使用userprofile.get_absolute_url来使用,那么你需要重新定义auth.user的绝对url了.

    auth.models 中的user的get_absolute_url是这样定义的:

    view plain

    def get_absolute_url(self):

        return "/users/%s/" % urllib.quote(smart_str(self.username))



    这里定死了吧,但是django还是给了方法来修改它.

    方法就是:在settings.py中增加一个设置:ABSOLUTE_URL_OVERRIDES

    view plain

    ABSOLUTE_URL_OVERRIDES = {

        'blogs.weblog': lambda o: "/blogs/%s/" % o.slug,

        'news.story': lambda o: "/stories/%s/%s/" % (o.pub_year, o.slug),

    }



    所以对auth.user来说,要修改就按照下面的来:



    view plain

    ABSOLUTE_URL_OVERRIDES = {

     'auth.user': lambda u: "/member/profile/%s/" % u.username,

     #其他的设置

    }





    官方文档:https://docs.djangoproject.com/en/1.1/ref/settings/#absolute-url-overrides

    https://code.djangoproject.com/wiki/ReplacingGetAbsoluteUrl