---
layout: post
permalink: /use-python-indeticon-generate-github-like-icons.md
title: 使用python identicon库创建类似与Github的头像
---

Identicon在很多大型IT网站上可以见到,比如Github,Sourceforge,Stackoveflow等等, 刚刚注册的账号的个人信息的默认图标​都​是​一​些​看​上​去​像​七​巧​板​拼​凑​的​图​案​,​对​称​又​变​化​多​端​。

​本​人​也​是​因​为​好​奇​才​在​网​上​搜​了​这​个​算​法​,​主​要​是​哈​希​算​法​,​把​邮​箱​或​者​I​P​的​信​息​图​形​化​,​很​直​观​。

这个算法有很多版本, php的, .net的, python的, ruby的, 因为咱们社区是python的, 所以这里给大家发一个python版本的.

##使用示例:

1. 直接到 https://github.com/shnjp/identicon 下载identicon.py,
2. 将identicon.py放到你能找到的地方.
3. 然后在相同的目录里新建一个test.py
4. 打开test.py

输入以下代码:


    import identicon
    img= identicon.render_identicon('123123', 16)
    img.show()

这样就能够看到图像了, 大小是3*16=48. 即图片大小是48X48像素的尺寸.如果报错了, 就是你没有安装python的图像处理模块PIL, 安装之后再试.

##保存图像

上面的代码只是简单的使用, 还没保存.保存代码如下:

    import identicon
    img= identicon.render_identicon('123123', 16)
    img.save('123123.png')

会在相同的目录保存一个png格式的图片

##批量生成图片

代码如下:


    import identicon
    def gen_identicon(code,size):
        img= identicon.render_identicon(code, 16)
        #img.show()
        img.save('%s_%s.png'%(code,size))

    for x in xrange(10000000,10000000+5):
        gen_identicon(x, 16)
    for x in xrange(20000000,20000000+5):
        gen_identicon(x, 16)
    for x in xrange(40000000,40000000+5):
        gen_identicon(x, 16)
    for x in xrange(80000000,80000000+5):
        gen_identicon(x, 16)
    for x in xrange(160000000,160000000+5):
        gen_identicon(x, 16)

说明:

    identicon.render_identicon(code, 16)

这里的code是一个数值, 或者字符串数值, 如果code比较大, 比如code=10000000, 生成的图片就是彩色的. code比较小, 比如code=1~100之间的, 生成的图片就是黑白色的.

完.
