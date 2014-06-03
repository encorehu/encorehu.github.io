---
layout: post
permalink: /how-to-use-python-html5lib
title: [译]如何使用Python模块 html5lib
---

# [译]如何使用Python模块 html5lib #


打开 IDLE,将会显示一个空白的界面.

在顶行输入以下代码以导入 "html5lib" 模块:

    import html5lib



    from html5lib import treebuilders, treewalkers, serializer

    import urllib2

创建一个新的 HTML 5 parser, 用来读取一个 HTML website. 输入以下代码声明一个新的 parser:

    parser = html5lib.HTMLParser()

通过传递地址到 urllib2.urlopen 函数来打开一个网站,例如, 如果你要打开 "www.example.com", 输入以下代码:

    url = urllib2.urlopen("http://www.example.com").read()

传递网站到 HTML 5 parser 来接收到一个 tree representation. 保存这个 representation 到一个变量 "tree" 中, 代码如下:

    tree = parser.parse(url)


创建一个 tree walker 如下:

    treeWalker = treewalkers.getTreeWalker("dom")


使用这个treewalker遍历整个 tree.这个 tree walker 将返回一个覆盖该html5网站的信息流. 遍历整个tree的代码如下:

    stream = treeWalker(tree)

序列化信息流以便你输出到console.你可以使用以下2条语句来序列化信息流:

    serial = serializer.htmlserializer.HTMLSerializer(omit_optional_tags=False)

    output = serial.serialize(stream)

对信息流的序列化输出遍历如下:

    for element in output:

在上面一句后面缩进下面的语句,并写上一个打印函数如下:

        print(element)


按F5执行程序.脚本将打开并解析一个 HTML 5 网页. 脚本然后序列化页面的树形结构并输出到console. 输出可能会因为你选择的网页不同而有所变化,可能会类似于下面的东西:

    Welcome to a web page!