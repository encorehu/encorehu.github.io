---
layout: post
permalink: /python-urllib-urlretrieve-download-files
title: urllib.urlretrieve下载文件的一点研究
---

# urllib.urlretrieve下载文件的一点研究 #



	urllib.urlretrieve下载文件





	urlretrive的原型好像是urllib.urlretrieve(url[, filename[, reporthook[, data]]])



	urllib.urlretrieve(url,filename)下载网络文件,第一个元素就是目标url,第二个参数是保存的文件绝对路径(含文件名),返回值是一个tuple(filename,header),其中的filename就是第二个参数filename.如果urlretrieve仅提供1个参数,返回值的filename就是产生的临时文件名,函数执行完毕后该临时文件会被删除.



	后面2个参数，就是一个回调函数和数据，应该可以用来分段下载文件。最后一个data是get和post的参数



	python里urllib.urlretrive下载的问题，下载的url需要cookie，比如：http://site.com/file?file=xxx],用urlopen打开是正确的，但是urlretrieve下载下来文件里面的内容是错的,是出错需要登录那个网页的代码。 urllib.urlretrieve 的函数原型中，看不到要用cookie的影子，不知道网上的说法怎么来的





	我在遇到urllib.urlretrieve下载文件下载不了的时候，我不知道怎么办，只好再弄一层try：except增加 urlopen重新下载


    try：

            urllib.urlretrieve

    except:

            urllib.urlopen











	urllib.urlretrieve 应该是作为 网际快车之类的用，功能比较简单的直接下载 不与服务器交互的 函数。前几天，我用urllib.urlretrieve来批量下载别人网上的图片，它的好处就是可以直接把下载的url，保存到第二个参数filename，如果用urlopen的话，你肯定要多写一个保存数据到指定文件名的函数。