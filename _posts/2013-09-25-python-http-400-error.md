---
layout: post
permalink: /python-http-400-error
title: pythone访问网页遇到http 400 错误
---

# pythone访问网页遇到http 400 错误 #


今天尝试使用python请求一个网页, 模拟浏览器进行的.

结果服务器返回 HTTP Error 400: Bad Request

真是百思不得其解啊, 我觉得我的模拟没啥问题啦呀.

后来仔细调试, 发现是一个分号的问题.

"Accept-Encoding":"gzip;deflate", 这个分号改为逗号就解决了.

http 400一般是发送给服务器的语法错误, 像上面这个, 只是一个逗号和分号的区别

如果服务器容错好, 当然没问题, 但是容错不好的话, 就会出问题.

像gzip, 如果服务器没使用gzip, 那就可能发现不了这个问题, 如果使用了, 就会出现.