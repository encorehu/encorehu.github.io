---
layout: post
permalink: /let-ucg-more-quick
title: 使用South对Django项目的数据库进行迁移的简明教程
---

# 使用South对Django项目的数据库进行迁移的简明教程 #


众所周知, django的数据库在model建好之后, 使用manage.py syncdb 这个命令就可以将数据模型同步到django 项目的数据库中, 建立对应的表来反应你创建的model模型.

然而问题在于, model创建好了不久, 我们又想对model修改, 比如说, 增加了一个字段, 或者删除了一个字段, 这个时候再运行manage.py syncdb , 发现一点反应都没有, 不管怎么说, django自己的解决方案就是reset. 步骤如下:

    manage.py dumpdata --format=xml appname>appname.xml #这个是将某个appname的数据先导出来备份为appname.xml, 是xml格式的
    manage.py reset appname # 这个是你修改了appname中的model之后, 想更新数据库中对应的表, 只好reset, 会问你yes还是no, 输入yes或者no来确认
    manage.py loaddata appname.xml # reset appname中所有的model之后, 再重新恢复数据, 当然你得保证你的数据对新的model仍然有效
    
这个是我以前修改了数据表之后的常用做法, 只适合与sqlite3数据库, 因为我后来换postgresql之后, 发现这招不行了, 光是reset就可以出现很多错误, 主要是索引被破坏, 表和表之间的关联太多, postgresql对这些处理得比sqlite3严格很多, 导致最后失败.

这种方案只能说是万不得已的方案.

那么south的出现就是为了解决这个问题的.

....未完待续

那么如何用south呢?

下载south, 安装south不用我说了吧, 直接从django项目开始吧

装好了south0.7.6之后, 在INSTALL_APPS中添加

        "...",

        "south",

        "...",
    
然后, 运行一次命令, manage.py syncdb 因为你装好south到django的apps中之后, 再运行这个命令的话, 会直接调用south的同名syncdb命令, 它拦截了django自带的 syncdb命令, 首先是在系统的数据库中建立了一个django-migrations表还是south-migrations表我记得不太清了, 反正第一步就是建表, 建south需要的表.

manage.py syncdb

然后, 如果你需要对某个app的数据模型models进行跟踪和随后的迁移, 就要这样做了:

### 一种情况: 你的项目是崭新的, 重新开始的(也就是说除了django自带的app之外, 你还没有开始创建自己的app) ###

good, 这个就不需要操心了,

1、创建一个空数据库, 你使用sqlite3的话, 就用django自带命令manage.py syncdb 创建数据库, 将django自己需要的数据表创建到数据库中.
2、将south添加到INSTALLED_APPS, 在那些django自带的app后面, 如果你觉得south够重要, 可以放到django自带的admin auth之前, 处于第一个的位置, 这个对项目没影响的, 放在django自带的app后面是啥意思呢, 就是说django 的自带app不需要south对其进行跟踪models的修改了, 可能你需要的只是升级django.
3、运行manage.py syncdb 命令(这个已经被south的替换了)，它将south需要的数据表加入到数据库中
4、将你新创建的apps添加到INSTALLED_APPS
5、对每个app分别运行“python manage.py schemamigration appname --initial”，它将在每个app的目录下创建migration目录和相应的文件
6、然后运行“python manage.py migrate appname”，这一步将app的数据表加入到数据库中

### 第二种情况: 你从不知道哪里下载了一个django项目的源代码, 里面有了很多的app, 但是没有建立数据库 ###

1、创建一个空数据库, 如果你用sqlite3, 可以不必创建.
2、将south添加到INSTALLED_APPS, 放置的位置你可以参考上一个情况.
3、对每个app分别运行“ manage.py schemamigration appname --initial”，它将在每个app的目录下创建migration目录和相应的文件
4、运行manage.py syncdb 命令(这个已经被south的替换了)，它将django django apps和south需要的数据表加入到数据库中
5、然后运行“manage.py migrate appname”，这一步将app的数据表加入到数据库中

### 第三种情况: 你很久前就开始开发django项目了, 但是现在才发现south是个好东西, 并且要开始用了, 也就是说数据库是现成的, 里面已经有了很多数据表了, 而且极有可能, 数据都一大堆了,现在要迁移到South. ###

1、将south加入到INSTALLED_APPS中
2、运行manage.py syncdb，它将south的数据表加入到数据库中
3、对每个app分别运行 manage.py schemamigration appname --initial，它将在每个app的目录下创建migration目录和相应的文件
4、对每个app分别运行“ manage.py migrate appname 0001 --fake”，该命令不会对数据库做任何操作，只是欺骗一下south，让它在south_migrationhistory表中添加一些记录以便于下次你想创造migration文件的时候所有东西都已搞定。

以上三种情况, 按照以上步骤, 基本上就完成了django项目的models迁移到south的管理之下了, 以后要做的事情就是同步数据表和models的变化.

很多人可能会说, 这些都他吗什么玩意, 说的不明不白的, 看都看不懂. 什么叫他妈的创建migration目录和相应的文件, 他妈的什么叫欺骗south? 在south_migrationhistory表里添加的什么玩意? 还下次你想创建migration文件? 下次我为什么要创建migration文件? 老子下次不用south, 总行了吧. 确实, 上面三个情况, 除了标题是我写的, 其余的步骤都是在网上抄的, 我觉得对新手来说, 确实是写的含混不清的.没解释清楚.

我来简单解释一下那些命令吧:

迁移过程中:

    manage.py schemamigration youappname --initial

#schemamigration 命令带 --initial 参数, 指的是south分析你的app中的models 的表结构, 因为这个schemamigration本身就是单词schema+migration的组合, schema是结构 架构的意思, 这个命令就是先分析现存的models的可以生成的表结构, 并进行初始化. 初始化了如下东西:

在appname目录下面创建一个migrations的子目录, 构成如下目录结构:

    ----appname

    --------migratioins

    --------templates

    --------templatetags

    --------其他文件

同时该命令在appname/migrations目录下添加了__init__.py 和一个0001_initial.py文件

0001_initial.py文件中用python的方式, 写了你的app 中models转换为数据表的python语句, 主要是初始化, 实际上就是根据models中的django models字段, 来初始化创建数据库中app需要的数据表

### 数据库中没有你的app的数据表 ###

那么只需要执行下面的

 manage.py migrate appname

就可以将appname/migrations/ 目录下的0001_initial.py 中创建数据表的语句执行, 从而在数据库从真正创建 app的models对应的数据表, 也就是说, 如果app是新建的, 新加入的, 直到执行了 manage.py migrate appname 的时候, 才真正在数据库中创建了app需要的数据表, 而如果像我们最开始开发django app一样, 想使用manage.py syncdb 来对新加入的app创建数据表, 是没有什么反应的,并没有将数据表创建到数据库中. 这个原因是south拦截了django自己的那个syncdb命令, 必须要求你使用south的migrate命令, 将migrations目录下的迁移命令执行后才更新数据库中的数据表.

### 如果数据库中已经有了app的数据表 ###

那么执行  manage.py migrate appname 的话, 仍然会执行 0001_initial.py来更新数据表, 但是会报错, 一般情况就是说, appname_xxxx数据表已经存在, 等等报错信息.

这个是因为manage.py migrate appname是执行appname/migrations/目录下的所有迁移命令, 当然包括0001_initial.py了.

既然数据表已经存在了, 这个0001_initial命令又不得不执行, 但是执行的时候又会出错, 怎么办?

现在回头来想想 south_migrationhistory 表, 这个表就是记录了你 migrate了什么的数据表, 如果这个south_migrationhistory 表中记录了appname/migrations/0001_initial.py 已经执行过了, 那么, 你以后运行 manage.py migrate appname 的时候, 虽然是调用 appname/migrations/目录下的所有迁移命令, 但是因为south_migrationhistory 表中已经记录了 0001_initial.py 命令已经执行了, 所以, 以后再调用 manage.py migrate appname的时候, 就会自动跳过 0001_initial.py 这个迁移命令.

那么问题是现在这个 0001_initial.py 在manage.py migrate appname的时候, 老是报 数据表已经存在, 根本就执行不了, 也就进不了 south_migrationhistory 这个历史运行表了, 怎么办, 答案很多人这个时候都知道了就是先骗过south, 也就是说先fake执行一下 , fake是虚假的意思.使用命令

manage.py migrate  appname 0001_initial.py  --fake

这个命令的意思就是, 这个迁移命令先虚假执行, 在 south_migrationhistory  记录好, 0001_initial.py 这个迁移命令已经执行过了, 以后就不要再执行了.

以后? 是指的什么?

就是指的以后每次对models更改后，数据库中的数据表还是老的旧的, 跟不上models的变化, 需要将models的变化同步迁移到数据表中, 那么对south的migrate命令来说, 就是需要你给它创建几个可以运行的迁移命令了, 其实这个命令的生成很简单, 也很自动化, 只需要再次执行那个结构 架构分析的命令, 带上一个别的参数就行了.如下:
manage.py schemamigration appname --auto     #结构分析, 检测对models的更改, 比如添加了一个字段为fieldname

同时在appname/migrations/ 目录下生成0002_auto__add_fieldname.py

这样就生成了一个针对 models更新的 迁移命令文件

如果这个时候, 你又添加了一个字段为 fieldname1, 再次分析models的变化, 执行manage.py schemamigration appname --auto

会在appname/migrations/ 目录下生成0003_auto__add_fieldname1.py  的迁移文件

那你要问了, 这都修改2次models了 , 怎么同步到数据表呢.

很简单, 既然 0001_initial.py 中创建数据表的命令不需要执行, 因为最原始的0001_initial的命令从创建的表, 和数据库中现存的表的结构是一致的, 所以不需要执行, 一执行就报错, 因此使用fake的方式记录到  south_migrationhistory 表中, 以后的所有对models的更改, 只需要 manage.py migrate appname 即可自动跳过0001_initial的建表命令了. 自动执行尚未记录在  south_migrationhistory 表中的 更改models的命令: 0002_auto__add_fieldname.py  0003_auto__add_fieldname1.py 命令.