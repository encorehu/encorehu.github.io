---
layout: post
permalink: /django-storage-upload-file-image-usage
title: Django 文件图片上传保存处理以及storage 使用手册
---

# Django 文件图片上传保存处理以及storage 使用手册 #


django 提供了2个文件型的Field( FileField 和 ImageField), FileField用来保存普通文件, ImageField用来保存图像.

这2个Field的后面都使用了 django的storage. 从继承关系上讲, ImageField 继承自 FileField, 所以, 研究FileField即可了解文件域的特性.

下面从Model, View, 和Form三个角度来分析文件的上传, 保存等等操作.

首先定义models. 话题简化模型

    Class Topic(models.Model):

        title = CharField(max_length=20)

        image = ImageField()

那么在views中, 或者不在view里, 仅仅在django的shell中, 如何上传和保存这个文件呢?

    def topic_image(request):

        ...

客户端浏览器上传的图片, django是保存在 request.FILES 这个字典中的.

假设form里, 这个文件的字段名称是 topic_image, 那么, 操作这个客户端上传来的文件只需要操作 request.FILES['topic_image']  就行了

那么 request.FILES['topic_image'] 是什么东西呢, 通过看文档和源码, 就知道这个是对上传来的文件流的一个包装.

http协议里的文件都是什么 ----boundary....文件流---- 之类的东西, 这个 request.FILES['topic_image']在django中实际上是一个 class InMemoryUploadedFile, 是对http协议中文件部分的解析后生成了InMemoryUploadedFile实例,

现在将 request.FILES['topic_image'] 赋值给 topic_image 变量

topic_image = request.FILES['topic_image']

这里的topic_image实际上是一个File类的继承之类的实例, 也就是说, InMemoryUploadedFile继承自File, topic_image是 InMemoryUploadedFile的一个实例.

将这个上传来的, 目前在服务器内存中的这个文件保存下来, 使用Topic model 中ImageField的自带方法

    from your.models import Topic

    topic_image = request.FILES['topic_image']



    topic = Topic(title='tttttt')

    img = topic.image.save('imagename.jpg', topic_image, save = False)

    topic.save()

注意: 上面的img = topic.image.save('imagename.jpg', topic_image, save = False)就完成了图片文件的保存.

实际上它调用了 storage来保存这个文件.

而storage在保存文件的时候, 调用了 Storage.save(name, content)函数, 然后Storage.save(name, content)函数, 又调用了Storage._save(name, content). 注意下划线.

这里的name是保存到服务器上的文件名(可以理解为路径), content 参数是一个File类的实例, 不能仅仅是一个文件的指针fp, 或者是文件的内容(fp.read()), 而是对文件句柄和内容都进行包装之后的File类的实例.

因为 Storage.save(name, content)最终必须要调用 Storage.chunks() 这个函数, 但是fp文件指针和, fp.read()文件内容都没有这个方法,导致最后调用失败.

不过, Django提供了很多个File类:

    django\core\files\base.py 中定义了 File, ContentFile

    django\core\files\uploadedfile.py 中定义了 UploadedFile, TemporaryUploadedFile, InMemoryUploadedFile, SimpleUploadedFile



继承关系是:

    File                                File 基类

    ----ContentFile                     继承自 File

    ----UploadFile                      继承自 File, 主要用于Form表单来保存文件, 一个Form.save()就可以搞定了.

    --------TemporaryUploadedFile       继承自 UploadFile, 这个是用于保存文件到临时文件夹

    --------InMemoryUploadedFile        继承自 UploadFile, 这个是把上传的文件保存到内存中, 在保存到硬盘之前, 可以进行其他操作

    ------------SimpleUploadedFile      继承自 InMemoryUploadedFile, 默认为简单的文本型数据上传, 使用StringIO
    仅仅在view中, 使用model的某个FileField 来保存上传来的文件话, 如果是从request.FILES中获取的话, 就是InMemoryUploadedFile.
InMemoryUploadedFile的chunks() 实际上是假的, 因为既然读取到内存了, 那么就不用再像处理上传500M文件那样, 接收一段(chunk) 保存一段了, 因为它已经上传到服务器的内存了, 然后可以直接从内存中保存到服务器硬盘了.

当然, 这个保存到服务器也和django的最大上传文件的大小设置有关系, 如果 max_size 是2M的话, 那么只要文件小于2M, 就会直接使用InMemoryUploadedFile 来保存文件, 如果文件大小超过了 max_size, 那么就会变成一个使用chunk来接收文件的File类, 这里主要要参考 UploadFileHandler中的处理.

由于我没看源码, 这里可以猜测它为  TemporaryUploadedFile(相当于写入临时文件, 然后在复制, 移动到需要保存的位置)

也就是说, 用户上传文件

如果 文件大于上传最大的文件(这个肯定和服务器内存容量有关系, 你肯定不希望用户上传2G的文件一下把你的内存全部占用, 可能你会匀出2M内存来处理小文件)

----> 使用 TemporaryUploadedFile, 一边接收文件流, 接收一段就写入临时文件, 直到全部接收完毕

如果文件 小于上传限制(比如2M)

----> 直接使用内存来保存文件 即, InMemoryUploadedFile, 这个时候, 根本不需要操作什么 chunks, 但是还是提供了chunks函数, 只不过直接返回了整个文件的范围, 不像TemporaryUploadedFile, 它是返回一段段的数据, 数据大小是django中配置的最大chunk值.

好, 现在厘清了 TemporaryUploadedFile 和 InMemoryUploadedFile 的关系, 简单说, 超大文件使用TemporaryUploadedFile, 小文件使用 InMemoryUploadedFile.

那么上面的例子, 一个话题有个图片, 这个图片是用来指示话题的, 让人一看到图片就知道这个话题是和什么相关, 因此使用的是小图片.

它的保存, 仅仅使用了 ImageField.save(name, content), 就完成了, 即:

    topic.image.save(name, content) # 这里的content, 实际上即是上面说的 InMemoryUploadedFile 的一个实例.

而这里的topic.image, 就是 ImageField 的实例.

调用过程:

    1.ImageField.save(name, content)

    2.---->Storage.save(name, content)

    3.-------->Storage._save(name, content)

    4.------------->name 会变成 Storage.get_avaiable_name(name), # 如果name已存在, 在name后边加下划线变成 name_.jpg

    5.------------->Storage.chunks(name, content)

这几个过程中, content 都是指的 File 之类的实例, 这里是 InMemoryUploadedFile.

未完待续...