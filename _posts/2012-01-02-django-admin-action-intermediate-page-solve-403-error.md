---
layout: post
permalink: /django-admin-action-intermediate-page-solve-403-error
title: 怎样在djangoadmin的action选择后出现一个中间页面并解决403csrf验证错误
---

# 怎样在djangoadmin的action选择后出现一个中间页面并解决403csrf验证错误 #


django的那个admin actions提供了针对queryset的批量操作，比如批量删除，批量改变某些objects的属性等等，一般情况下是选择了一个action，点击执行之后，就在后台自动执行了，没有一个中间页面供你提供点额外的数据输入或者选择什么的。用起来有一点点不方便。

这几天我正好面临这个问题。问题来源于以下情景：

很多文章从属于某个分类，当确定了文章的分类之后，需要更改某篇文章的分类，就点编辑，修改分类后，再保存，文章数量少的话，这个不是问题。当你需要修改分类的文章多了之后，需要批量修改某些文章的分类，这个时候一篇一篇的修改文章的分类就显得繁琐和低效，所以要使用admin action批量修改分类了。

简单的admin action可以直接将文章的分类修改成特定的某个分类，比如都修改成category=webdesign这个特定的分类就很好写，但是分类很多的时候，你要修改的分类可能是不确定的，比如100个分类，你不可能写100个admin actions来分别针对某个category来修改，所以这个时候就需要出现一个中间页面，提供你需要修改的category 列表供你选择，选择完了之后，你就让后台根据你选择的category来批量更新你需要修改的文章了。

至于怎么创建一个简单的action，请看django doc：https://docs.djangoproject.com/en/dev/ref/contrib/admin/actions/]

怎么创建一个中间页面呢，这里有一篇同样的提问供参考：http://stackoverflow.com/questions/3605185/django-how-to-create-a-complex-admin-action-that-requires-additional-informatio] （Django: How to create a complex admin action that requires additional information?），而答案在这里：http://www.hoboes.com/Mimsy/hacks/django-actions-their-own-intermediate-page/] （Django actions as their own intermediate page
）

另外，提供几个参考页面：
 - http://stackoverflow.com/search?page=2&tab=relevance&q=%5bdjango-admin%5d%20action] - http://stackoverflow.com/questions/2170521/django-extending-another-apps-modeladmin] - http://stackoverflow.com/questions/4825921/overriding-django-model-pys-delete-method-with-extra-arguments]
按照上面的答案的那一篇文章来做的话，如果你的django版本中得csrf保护没开的话，可能会得到你想要的效果。如果你是用django1.3的话，估计要针对csrf来增加{\%csrf_token\%}到模板中了，并且在自定义的admin action中，需要修改一点东西，否则会出现403 forbidden，csrf token 验证错误。这个是我参考 django admin app中得到的：

    return render_to_response('link/change_category.html',
                                {'links': queryset, 'form': form, 'path':request.get_full_path()},
                                context_instance=RequestContext(request))

也就是说，render_to_response 这个函数中，要想从新得到 csrf token ，必须使用context_instance=RequestContext(request)，以便模板link/change_category.html中的{\%csrf_token\%}能够被赋值，否则就会导致csrf_token的值为空，导致csrf token的验证失败。