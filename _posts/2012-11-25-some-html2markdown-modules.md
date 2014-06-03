---
layout: post
permalink: /some-html2markdown-modules
title: 将html转换为markdown格式(html2markdown)
---

# 将html转换为markdown格式(html2markdown) #


几个 python 的将html转换为markdown格式的模块索引:

https://github.com/denmark/html2markdown 这个基于 HTMLParser

http://www.codefu.org/wiki/Main/Html2markdown 这个也是基于  HTMLParser

https://github.com/bleach/pw2jekyll 这个是楼上的 codefu的一个使用示例.


当然, 使HTMLParser 来解析带有中文的html文件的时候, 通常会发生 UnicodeDecodeError , 这个原因是因为 HTMLParser 的正则不能处理一些 非正规的html和未正常用引号表示的属性值, 还有就是它的正则没有包含中文字符.

具体的原因请看: http://bbs.csdn.net/topics/320115585

还有这里: http://hi.baidu.com/archersc/item/d21e08160c713c9e99ce33cd