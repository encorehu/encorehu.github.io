---
layout: post
permalink: /django-select_related-note1
title: Django select_related需要注意的一个小问题,报DoesNotExist错误
---

# Django select_related需要注意的一个小问题,报DoesNotExist错误 #


我们知道 django的 select_related 是用来查询外键引用对象的属性的, 是在查询的时候就一次性将外键对象的属性查出来的

例如 Person, Article2个类, 关系如下:

author       = models.ForeignKey(Person)

则, Article.objects.select_related().get(pk=10) 这种就是讲id=10 的文章的作者的信息也一并查询出来了,

在后面就减少了查询数据库的次数.

我碰到的问题就是 如果这个外键指向的Person对象不存在, 或者这个外键指向的是None, 那么就会报DoesNotExist 错误

        try:

        p = Article.objects.select_related().get(pk=article_id)

        except Article.DoesNotExist:

            raise Http404


这个DoesNotExist 并不是Article不存在, 而是Article的外键指向的对象不存在.就是说Article是存在的, 但是却报404错误, 这种情况的出现可能是批量导入数据的时候, 数据库中Article和Person的关系没有自动生成.

解决的办法是补上该Article指向的外键数据, 或者直接去掉这个select_related,改为all()

    p = Article.objects.all().get(pk=article_id)
    那么这个 select_related() 查询集到底是啥呢? 经过试验, 发现是pk不为空的查询集, 如果pk=None的话, 就不会进入到select_related() 的查询集中, 所以有些pk为空的数据就查不到了.