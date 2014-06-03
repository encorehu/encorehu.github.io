---
layout: post
permalink: /how-to-fix-south-cannot-freeze-field
title: 如何解决South在schemamigration的时候出现Cannot freeze field的问题
---

# 如何解决South在schemamigration的时候出现Cannot freeze field的问题 #


本文从自己的实际经验出发, 讲解了south在 执行manage.py schemamigration book --initial 这种初始化命令的时候, 碰到自定义字段不能freeze的错误, 是如何解决的, 问题的根源在于south不是在执行整个项目, 而是对自定义字段的引用方式有所限定, 需要在项目中的引用保持一致性, 免得south分不清楚这个自定义字段到底属于哪个python模块.

最近又打算将South用起来跟踪自己的django app中的models修改了, 但是使用manage.py schemamigration book --initial 命令的时候出现 Cannot freeze field 'member.profile.country' 的错误. 以前解决过这个问题, 但是忘记怎么解决了, 好在能想起来部分, 所以摸索了一会, 终于发现怎么解决了.

问题出现如下

D:\Projects\ddtcms.com\dev\ddtcms>manage.py schemamigration book --initial
 ! Cannot freeze field 'member.profile.country'
 ! (this field has class ddtcms.member.fields.CountryField)

 ! South cannot introspect some fields; this is probably because they are custom

 ! fields. If they worked in 0.6 or below, this is because we have removed the
 ! models parser (it often broke things).
 ! To fix this, read http://south.aeracode.org/wiki/MyFieldsDontWork


那么这个问题是如何出现的呢, 检查了代码, 没有发现什么错误, django app运行的时候也没出现什么差错, 那么为什么south就解决不了这个自定义的field呢? 经过仔细的排查, 发现是路径的关系, 也可以说是命名空间的关系.

在我的项目中, 代码中对app的引用比较混杂, 比如这个member app. 在settings.py中是用ddtcms.member这样引用的, 但是在那个自定义的countryfield中是直接引用的member, 然后在south free 自定义filed的指导性规则中是这么写的:

    #if 'south' in settings.INSTALLED_APPS:

    from south.modelsinspector import add_introspection_rules

    #add_introspection_rules(

    #    [

    #        (

    #            [CountryField], # Class(es) these apply to

    #            [],         # Positional arguments (not used)

    #            {           # Keyword argument

    #                "blank": ["blank", {"default": True}],

    #                "max_length": ["max_length", {"default": 2}],

    #            },

    #        ),

    #    ], ["^member\.fields\.CountryField"])

    #If your field inherits directly from another Django field - say CharField

    #- and doesn't add any new keyword arguments,

    #there's no need to have any rules in your add_introspection_rules;

    #you can just tell South that the field is alright as it is:

    add_introspection_rules([], ["^member\.fields\.CountryField"])
    这些注释掉的代码其实也可以用, 因为这个countryfield是继承于 charfield的, 可以直接不用添加任何指引代码.

上面的

    add_introspection_rules([], ["^member\.fields\.CountryField"])

    这个是从 member打头开始引用的. 因此, 需要其他的地方在引用到这个countryfield的时候, 不要以ddtcms.member的方式来引用, 而要直接使用member.xxxxxx之类来引用member app中的自定义字段, 否则 south 就会在migration的时候出错.


等到我发现问题的所在之后, 我将全项目中所有需要引用member的地方的ddtcms.member都换成了member, 然后重新执行 manage.py schemamigration book --initial 这个命令, 结果一切都解决了, 错误消失了.

    D:\Projects\ddtcms.com\dev\ddtcms>manage.py schemamigration book --initial

    + Added model book.Book

    + Added model book.Volume

    + Added model book.Chapter

    + Added model book.Publisher

    + Added model book.Edition

    + Added model book.Author

    + Added M2M table for book on book.Author

    Created 0001_initial.py. You can now apply this migration with: ./manage.py migr

    ate book