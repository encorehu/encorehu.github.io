---
layout: post
permalink: /django-signals-usage-example
title: 使用Django的signals用来同步发送信息示例
---

# 使用Django的signals用来同步发送信息示例 #


这几天又重温了django 的signals同步发送信息的功能.

下面就这一功能做一个简单的使用介绍.

首先, 先看几篇资料:

 django中的signals http://blog.csdn.net/largetalk/article/details/7002407   这一篇列出了django signals的基本代码, 和内部函数.

使用Django的 signals 和 contenttypes 实现新鲜事功能 http://jinhao.iteye.com/blog/218346 这一篇介绍了django signals可以实现或者作为哪种功能来使用.

别的不多说了.

下面, 我也简单对这一个重要的工具啰嗦几句.

首先, 我们知道, django的signals内部自己定义了9个信号量, 就是9种事情会触发信号发生传递.

哪八种呢? 咱们还是直接复制定义吧:

在django/db/models/signal.py中,有这么9种:


    from django.dispatch import Signal



    class_prepared = Signal(providing_args=["class"])



    pre_init = Signal(providing_args=["instance", "args", "kwargs"])

    post_init = Signal(providing_args=["instance"])



    pre_save = Signal(providing_args=["instance", "raw", "using"])

    post_save = Signal(providing_args=["instance", "raw", "created", "using"])



    pre_delete = Signal(providing_args=["instance", "using"])

    post_delete = Signal(providing_args=["instance", "using"])



    post_syncdb = Signal(providing_args=["class", "app", "created_models", "verbosity", "interactive"])



    m2m_changed = Signal(providing_args=["action", "instance", "reverse", "model", "pk_set", "using"])


下面简单注释几个:

    class_prepared 类信号, 某个class准备好了



    pre_init 实例信号, 某个实例马上要初始化了, 就发出pre_init信号.

    post_init 实例信号, 某个实例已经初始化了, 就发出post_init信号.



    pre_save 实例信号, 某个实例即将要保存入库了, 就发出 pre_save 信号.

    post_save 实例信号, 某个实例即已经保存入库了, 就发出 post_save 信号.



    pre_delete 实例信号, 某个实例即将要删除了, 就发出 pre_delete 信号,就是通知别的app, 我要死了.

    post_delete 实例信号, 某个实例即已经删除了, 就发出 post_delete 信号, 就是通知别的app, 我已经被删掉

    了.



    post_syncdb 数据库同步信号, 数据库已经同步完成了, 就发出 post_syncdb信号.



    m2m_changed 实例之间的多对多关系改变的信号, 也是完成后发出.


signals 系统自带了这9个, 你自己还可以自定义 新的消息.
我用过一回, 是这样用的:
发布新闻的同时发布一个 消息,
格式是: XXX用户 发布了一条新闻: 标题XXXXXXX

消息在什么时候发, 什么地方发, 消息被谁接受? 这些都是要考虑的问题.

先定义一个Note 的class (note/models.py )
我的消息是一个app来接收的, 我命名为 note

    class Note(models.Model):

        user = models.ForeignKey(User,blank=True, null=True, editable=False)

        title = models.CharField(max_length=120,null=True,blank=True)

        content = models.TextField(max_length=800)




再在note/models.py 增加一个handler就是处理器, 新闻发布了就发消息, 这边note接收, handler处理, 一旦发了信息 就会触动这个事件 向这个表中插入数据.保存发过来的消息

至于为什么要写个note, 就是为了接收全站的消息, 不仅仅是news可以发消息, 任何app都可以发消息给他

 - 在news/signals.py中自定义一个消息latest_news_created  - 然后在views中发送,  - 在note/models.py写好接收的handler  - 就可以处理了

因为服务器运行的时候, 首先是检测models.py中的model定义是否正确. 所以这种信号接收器, 还是写在models中比较好
写在别的文件中, 可能不会运行. 因为没有被别的文件import过一回的话, 是不会运行的.

    from ddtcms.news.models import News

    from ddtcms.news.signals import latest_news_created



    def note_handler(sender, **kwargs):

        ......

        new_note=Note()

        new_note.title = title

        new_note.user = user

        new_note.content = the_content

        new_note.save()

    latest_news_created.connect(note_handler, sender=News)



那我为什么又新定义一个latest_news_created呢, 其实model在保存的时候,自动发送 post_save的, 只要post_save.connect到这个handler就行了

实际上就是admin中修改的时候, 这个post_save消息也会发送, 如果监听post_save, 那么note app会接收到无数的消息, 所以觉得消息重复了, 可能是我的handler写的不好, 没有过滤  所以这个是暂时这么做的

目前虽然只监听了news这个app,但是它不是只能监听news这个表,你需要connect(handler, News/BLog........)

如果你监听 post_save信号的话, 所有app的models在保存的时候都会发消息.都会被捕捉到
但是你可能每个model定义都不一样啊, 所以你可以自定义一个消息, 这个消息发出的时候是按照固定格式往外发消息的, 就是说他发出的字典含有几个固定的字段 你就可以在handler中很简单的处理了.

那我要监听其它表时 如何调用呢
就拿latest_entry_created_handler 这个来说
比如你在note中定义一个msg1(Signal) 使用title, content, url这三个参数, 来发消息
在note中再定义一个msg1_handler, 只处理title, content, url这三个参数, 你就可以完成任务了

在其他的任何app中, 你import这个msg1的signal, 在需要的地方, 发送该消息就行了
在需要的地方, 发送该消息就行了？ 如何调用
先定义好你的消息:

    latest_news_created = Signal(providing_args=["instance","user","title","content","slug"])

假如我一个news/views.py   def addnews(request):
在news_post(request):里面
我想在添加新闻时 监听下

    kwargs = {}

    kwargs['instance'] = instance

    kwargs['user'] = instance.deliverer

    kwargs['title'] = _("%s Posted A new News: %s") % (instance.deliverer.username,instance.title)

    kwargs['content'] = _("%s Posted A new News,go and view it now <a href='%s'> %s </a> " ) % (instance.deliverer.username,instance.get_absolute_url(),instance.title)

    kwargs['slug'] = "news%d-%s" % (instance.id,datetime.datetime.now().strftime("%Y%m%d%H%M%S"))

    #responses = latest_news_created.send(sender=instance.__class__,**kwargs)

    #print responses

    latest_news_created.send(sender=instance.__class__,**kwargs)



最后一句就是发消息了

当然这个目前是写在news/signasl.py中了, 以后可以改到note app里面, 在需要的地方调用就行了latest_news_created.send(sender=instance.__class__,**kwargs)

这里面的sender很重要, 可以在handler中处理

为了后期好维护, 可以将singal的定义都放到note 这个app中.

这个东西的应用很多, 比如可以同步到微博啊, 站内新鲜事啊, 用户通知, 用户操作记录等等, 只要你想得到的地方, 都可以用.