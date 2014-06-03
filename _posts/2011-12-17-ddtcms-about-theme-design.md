---
layout: post
permalink: /ddtcms-about-theme-design
title: Django:DDTCMS关于皮肤Themes的想法
---

# Django:DDTCMS关于皮肤Themes的想法 #


    写于2009-5-18,一直没有共享出来.

    主要是考虑到django的特殊方式

    themes 可以看做是最终用户看到的界面，包括版式布局layout，页面的配色styles，文字字体、大小等等

    考虑到每个人的喜好，可以在客户端的cookie或者session中设置用户选择的喜欢的theme。

    themes的目录：

    每一个theme建立一个目录

    themes/

    ../default/

    ../another/

    ../other/



    等等 theme目录下有templates

    由于涉及到theme的不同，不可能不改模板渲染的代码，因此，在settings中还是将themes根目录包含到templates的设置中去

    然后在渲染模板的时候，添加theme的路径到模板名称中去：

    return render_to_response('news/news_index.html', locals(),

                                  context_instance=RequestContext(request))

    要改为：

    return render_to_response(theme_name+'/templates/'+'news/news_index.html', locals(),

                                  context_instance=RequestContext(request))

    之类的东西。

    之所以弄个这个就是我的每个theme目录下的目录结构打算这样设计

    theme_name/

    ../templates

    ../css

    ../js

    ../images

    然后在theme_name/templates/下添加各个模块的模板代码（实际上起到布局的作用），



    settings:

    TEMPLATE_DIRS = (

        # Put strings here, like "/home/html/django_templates" or "C:/www/django/templates".

        # Always use forward slashes, even on Windows.

        # Don't forget to use absolute paths, not relative paths.

        #'./templates/',

        './media/themes/yaml/templates/',改为'./media/themes/',即可

    )



    css目录下就可以添加别的styles了，

    css/

    ../yellow

    ../red

    之类



    render_templates theme_name and template_name

    最后的最后，我还是决定将templates目录从media/themes/theme_name/目录下拿出来，还是回归到pylogs作者的那种目录的组织模式，采用

    templates/theme_name/...的目录组织结构，仔细想了想模板的加载原理，这两种做法没有什么大不同，唯一我考虑的就是dbtemplates这个app，这个

    app是直接加载每个app下的templates和templates根目录下的app的各个模板的，如果放到media下的themes目录中，就不能正常加载模板了，那么dbtemplates的作用就要大打折扣了。这是我不想看到的，所以，想来想去，还是决定用pylogs作者的目录结构来作为我的这个关于themes的总结想法。