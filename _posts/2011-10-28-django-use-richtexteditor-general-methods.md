---
layout: post
permalink: /django-use-richtexteditor-general-methods
title: Django项目中集成富文本编辑器的通用方法,适合KindEditor,xhEditor,NicEditor,wymeditor等
---

# Django项目中集成富文本编辑器的通用方法,适合KindEditor,xhEditor,NicEditor,wymeditor等 #


首先,请参考我以前写的一篇博客:如何把nicEditor集成到django中使用

http://blog.csdn.net/huyoo/article/details/4382317]

这篇文章中的做法就是一种较为通用的做法.

现在按照这种做法来把 KindEditor 集成到django项目中使用:

1. 代码的组织

在项目根目录下,使用manage.py startapp rte 新建一个文件夹,叫做rte,意思是RichTextEditor的缩写.然后在rte目录下新建kindeditor文件夹,如果你想用NicEditor就新建一个niceditor,可能还有xheditor等等.每个下面放一个__init__.py表明他们是可以被导入的模块.

目录结构就是这样:

    rte/

    │  models.py

    │  tests.py

    │  views.py

    │  __init__.py

    │  __init__.pyc

    │

    ├─kindeditor

    │      widgets.py

    │      widgets.pyc

    │      __init__.py

    │      __init__.pyc

    │

    └─niceditor

            widgets.py

            widgets.pyc

            __init__.py

            __init__.pyc





还有就是模板的放置,我的做法是放在templates下

    templates

    |-- editor

    |    kindeditor.html

    |    niceditor.html

    |    xheditor.html





等等诸如此类

2.自定义widget

就是建立一个KindEditor类，继承于Textarea（forms.Textarea),代码如下：

文件名称:rte/kindeditor/widgets.py

内容:

    from django import forms

    from django.conf import settings

    from django.utils.safestring import mark_safe

    from django.template.loader import render_to_string

    from django.template import RequestContext

    from django.utils.translation import ugettext_lazy as _

    class KindEditor(forms.Textarea):

        class Media:

            css = {

                  'all': (  settings.MEDIA_URL + 'editor/kindeditor-4.0.1/themes/default/default.css',

                            settings.MEDIA_URL + 'editor/kindeditor-4.0.1/plugins/code/prettify.js',),

            }

            js  = (

                    "%seditor/kindeditor-4.0.1/kindeditor.js" % settings.MEDIA_URL,

                    settings.MEDIA_URL + 'editor/kindeditor-4.0.1/plugins/code/prettify.js',

            )

        def __init__(self, attrs = {}):

            #attrs['style'] = "width:800px;height:400px;visibility:hidden;"

            super(KindEditor, self).__init__(attrs)

        def render(self, name, value, attrs=None):

            rendered = super(KindEditor, self).render(name, value, attrs)

            context = {

                'name': name,

                'MEDIA_URL':settings.MEDIA_URL,

            }

            return rendered  + mark_safe(render_to_string('editor/kindeditor.html', context))




以上的KindEditor版本,我用的是当前最新的4.0.1的版本.

3.'editor/kindeditor.html'这个模板的内容:

主要内容还是像最上面介绍的那篇文章中一样,只是一点js代码,用来把textarea转成一个在线html编辑器.使用的就是KindEditor的功能,主要内容如下:

    <script type="text/javascript">

    KindEditor.ready(function(K) {

        var options = {

            cssPath : ('{{MEDIA_URL}}editor/kindeditor-4.0.1/themes/default/default.css',

                        '{{MEDIA_URL}}editor/kindeditor-4.0.1//plugins/code/prettify.css')

            width : '680px',

            height : '400px',

            filterMode : true

        };

        var editor = K.create('textarea[name="content"]', options);

    });

    </script>




注意,一定要使用KindEditor这个变量来初始化富文本编辑器,Kindeditor官方网站上有的文档没有及时更新,如果使用什么KE.show的,或者大写K的,都不要用,chrome下调试就会提示KE未定义,K未定义等等.以上options中的内容请参考KindEditor的官方文档,这些东西也可以在widgets.py中预先定义好,但是我觉得不如在模板文件中定义方便.



4.在某个app的forms.py中定义一个form,不管前台后台都能用:

文件名示例:ddtcms\blog\forms.py

文件内容:

    from django import forms

    from django.utils.translation import ugettext_lazy as _

    from ddtcms.blog.models import Blog

    from rte.kindeditor.widgets import KindEditor

    class MyBlogAdminForm(forms.ModelForm):

        content   = forms.CharField(label=_(u"Content"), widget=KindEditor(attrs={'rows':15, 'cols':100}),required=True)

        class Meta:

            model = Blog




这个一定要使用forms.ModelForm,这样才能在admin中和view中使用,如果只是是在view中使用,这可以不用forms.ModelForm.



其他app的forms.py中要使用KindEditor的地方都是按照上面第4条说的弄.东西到这里基本上结束了,是不是很方便?

像xheditor,wymeditor等等都可以像这样弄,大家可以自己试试.方法就按本文介绍的来.



附:码农Ken写了一篇"Django轻松使用富文本编辑器-KindEditor":

http://www.cnblogs.com/ken-zhang/archive/2010/12/16/django_widget_kindeditor.html]

这篇文章的问题主要是:

1.kindeditor升级了,他的还没动,所以直接复制使用会发现KE未定义.

2.问题就出在js和模板继承上.

那个文章要修改的地方有:js部分,因为KE好像在4.0.1版本中未定义,需要这样用了:

    <script type="text/javascript">

    KindEditor.ready(function(K) {

    var options = {

        cssPath : ('{{MEDIA_URL}}editor/kindeditor-4.0.1/themes/default/default.css',

                    '{{MEDIA_URL}}editor/kindeditor-4.0.1/plugins/code/prettify.css')

        width : '680px',

        height : '400px',

        filterMode : true

    };

    var editor = K.create('textarea[name="content"]', options);

    });

    </script>




因为要使用code语法高亮,kindeditor使用的是prettyprint这个模块,所以这里加了prettify.css

-------------

还有那个模板,继承自admin/change_form.html

会导致一种错误,Caught KeyError while rendering: 'opts' in admin interface.

这个问题在StackOverflow也有人问起:

[http://stackoverflow.com/questions/7377324/caught-keyerror-while-rendering-opts-in-admin-interface]


 但是解答不一定能让人满意,把那个模板放到{\%block content\%}中或许不报错,但是界面就乱了.

本来我是打算按照码农的文章中的方法来把KindEditor应用到我的django项目中的,不过调试了几个小时之后,没有成功.

我最后是按照上面写的老方法重新改写了widgets,还是像我以前封装niceditor那样,widget自己render.

正所谓,老方法造新篇,老经验解新难题!!!

各位看官,请多提意见!