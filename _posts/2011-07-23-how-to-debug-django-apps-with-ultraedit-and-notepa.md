---
layout: post
permalink: /how-to-debug-django-apps-with-ultraedit-and-notepa
title: Django:我是怎样仅用浏览器(ie,ff)和文本编辑器(ultraedit,notepad++)来调试django程序的
---

# Django:我是怎样仅用浏览器(ie,ff)和文本编辑器(ultraedit,notepad++)来调试django程序的 #

			django实际上给我们提供了它的调试信息的,一旦出错,在debug=true的情况下,会暴露出很多的信息的.

比如说那个文件,行号,以及这些函数调用的顺序等等,都是可以看到的.

而其代码还可以折叠和展开,所以还是很方便的.

断点的情况,我是这样来弄的,直接在代码需要断点的地方,插入一个简单行:

debug()

这个函数是没有定义的全局函数,在python和django中都找不到这个函数,既然这个函数不存在,那么在代码正确的时候,你插入这么一行,它不报错才怪.

如果你插入这个函数的位置在你的出错的地方的前面,那么,你就拦截了你的错误了,django提前报debug()不是全局函数的错误了,那么在local vars那个链接处,点击它就可以看到debug()函数前面的变量的值了.

举例来说:

view plain

if step == 1:

    s=form

    debug()

    return render_to_response('equipment/done.html',

                {'form_data': [form.cleaned_data for form in form_list],}

    )

上面的代码如果说你的代码在render_to_response(...)这里出错了,那么你在它前面插入一行debug(),它就会提前报错,而且,点击local vars那里,你可以看到s=form这一行中变量s的具体值.

总的来说,使用浏览器和文本编辑器和上面的插入非法函数有意中断的方法,可以用来调试django程序,而不需要别的什么编译器之类.

希望能够给你一点启发和帮助.