---
layout: post
permalink: /django-pinyin-seo-methods
title:  用Django实现网页Url使用汉语拼音PinYin作为SEO优化的实现方法
---

#  用Django实现网页Url使用汉语拼音PinYin作为SEO优化的实现方法 #



拼音作为Seo优化,主要的使用者是中文的使用者.而拼音seo优化的使用场合主要有:



## 1.拼音域名. ##

对中国人来说, 拼音域名相对英文域名好记, 一个简单好记的域名对于一个创业网站来说，无疑是成功了一半。比如“baidu”、“taobao”、“xunlei”、“douban”等等双字节拼音域名便于记忆和传播，可谓是好域名中的典范，对推动这些网站的发展做出了巨大贡献。所以精挑细选一个好的拼音域名显得非常重要。

　　  对于个人站长来说，与其辛辛苦苦绞尽脑汁想个响亮全球的域名，不如选择一个容易优化的域名。大的品牌站优化方式和小的个人站长不同，大站倾向与内页优化，主页主要是确定品牌的排名。比如土豆，优酷, 起点，红袖，晋江，这类大站，其域名就需要考虑品牌效果，并且让用户能够方便记忆。 而个人站长投资少，权重低，充分利用首页权重是上策。这种情况下，拼音域名的优势就十分明显。

　　 拼音域名,可以分为两类,一类是汉字首拼字母域名,如玄幻小说网(xhxsw)，小说阅读网(xsydw)，,另一类是汉字全拼, 比如xuanhuanxiaoshuowang，xiaoshuoyueduwang。 如果拼音首拼和全拼都被人注册了, 就想办法弄个全拼&#43;首拼的域名, 比如www.xiaoshuoyd.com], 作为小说阅读网的域名, xiaoshuo这个关键词的搜索量还行,而且包含在域名里面，所以，就算没有完整的拼音域名，可以用类&#20284;的来替代，但域名的开头最好是所需关键词的开头。(本节整理自网络.)

2.拼音链接. 将文章的url使用汉字标题的拼音作为url链接, 这个在用户使用拼音搜索的时候, 可以实现SEO的优化.主要还是2种拼音链接,一种是汉字首拼字母,另一种是汉字全拼拼音.

本文就主要是讨论用Django实现网页链接Url使用汉语拼音PinYin作为SEO优化的实现方法.

用汉字作为url, 关键和难点就在于将汉字转为拼音, 所以, 本文主要是考虑如何将汉字转为拼音, 并且用在django中.

## 2.1 使用python实现汉字转拼音模块 ##

这个使用一个字典和转化程序就行.网上有一个署名为caocao的人做了一个python的汉字转拼音的模块.最原始的程序参考这篇文章:将GBK汉字转化为拼音的Python小程序 .

 http://blog.csdn.net/nethermit/article/details/156193] 和这里:http://www.cnblogs.com/caocao/archive/2005/09/13/235705.html] ,这个排版好些.

文中用到的convert.txt就是字典,但是作者的博客中没有提供下载.我通过网络搜索,从其他途径下载到了整个压缩包,包括字典和转换程序.该程序是基于GBK的, 要把汉字分成高低位来处理,一般人理解不了,我做了点改进就是把字典转化为utf-8的,因为在django中使用的代码就是utf-8的. 然后把他的程序也做了改进, 改成处理unicode字符,因为Unicode字符只需要处理一个字符,不用把字符分开成两半.

我的convert程序:



    # -*- coding: utf-8 -*-

    # ------------------------------------------------------------

    # Script   Name: convert.py

    # Creation Date: 2010-09-21  02:12

    # Last Modified: 2011-11-12 18:38:13

    # Copyright (c)2011, DDTCMS Project

    # Purpose: This file used for DDTCMS Project

    # ------------------------------------------------------------

    #####################################

    #   Written by caocao               #

    #   Modified by huyoo353@126.com    #

    #   caocao@eastday.com              #

    #   http://nethermit.yeah.net       #

    #####################################

    # python.

    import sys,os

    import re

    import string

    class CConvert:

    	def __init__(self):

    		self.has_shengdiao = False

    		self.just_shengmu  = False

    		self.spliter = '-'

    		"Load data table"

    		try:

    			fp=open(os.path.join(settings.PROJECT_DIR, 'utils', 'convert-utf-8.txt'))

    		except IOError:

    			print "Can't load data from convert-utf-8.txt\nPlease make sure this file exists."

    			sys.exit(1)

    		else:

    			self.data=fp.read().decode("utf-8")# decoded data to unicode

    			fp.close()



    	def convert1(self, strIn):

    		"Convert Unicode strIn to PinYin"

    		length, strOutKey, strOutValue, i=len(strIn), "", "", 0

    		while i<length:

    			code1 =ord(strIn[i:i+1])

    			if code1>=0x4e02 and code1<=0xe863:

    				strTemp   = self.getIndex(strIn[i:i+1])

    				if not self.has_shengdiao:

    					strTemp  = strTemp[:-1]

    				strLength = len(strTemp)

    				if strLength<1:strLength=1

    				strOutKey   += string.center(strIn[i:i+1], strLength)+" "

    				strOutValue += self.spliter + string.center(strTemp, strLength) + self.spliter

    			else:#ascii code;

    				strOutKey+=strIn[i:i+1]+" "

    				strOutValue+=strIn[i:i+1] + ' '

    			i+=1

    			#############################

    			#txlist = utf8String.split()

    			#out=convert.convert(utf8String)

    			#l=[]

    			#for t in map(convert.convert, txlist):

    			#	l.append(t[0])

    			#v = '-'.join(l).replace(' ','').replace(u'--','-').strip('-')

    			#############################

    		return [strOutValue, strOutKey]



    	def getIndex(self, strIn):

    		"Convert single Unicode to PinYin from index"

    		if strIn==' ':return self.spliter

    		if set(strIn).issubset("'\"`~!@#$%^&*()=+[]{}\\|;:,.<>/?"):return self.spliter # or return ""

    		if set(strIn).issubset("－—！#＃%％&＆（）*，、。：；？？　@＠＼{｛｜}｝~～‘’“”《》【】+＋=＝×￥·…　".decode("utf-8")):return ""

    		pos=re.search("^"+strIn+"([0-9a-zA-Z]+)", self.data, re.M)

    		if pos==None:

    			return strIn

    		else:

    			if not self.just_shengmu:

    				return pos.group(1)

    			else:

    				return pos.group(1)[:1]



    	def convert(self, strIn):

    		"Convert Unicode strIn to PinYin"

    		if self.spliter != '-' and self.spliter !='_' and self.spliter != '' and self.spliter != ' ':

    			self.spliter = '-'

    		pinyin_list=[]

    		for c in strIn :

    			pinyin_list.append(self.getIndex(c))

    		pinyin=''

    		for p in pinyin_list:

    			if p==' ':

    				pinyin+= self.spliter

    				continue

    			if len(p)<2:# only shengmu,just get one char,or number

    				#if p.isdigit():

    				#	pinyin += p + ' '

    				#else:

    				#	pinyin += p + ' '

    				pinyin += p + ' '

    			else:

    				if not self.has_shengdiao: p = p[:-1]

    				pinyin += self.spliter + p + self.spliter

    		pinyin = pinyin.replace(' ','') \

    				.replace(self.spliter+self.spliter,self.spliter) \

    				.strip(self.spliter+' ').replace(self.spliter+self.spliter,self.spliter)

    		return pinyin




上文代码中,convert1()函数是原来作者提供的,我给它改了名字.我把我写的convert替换了原来的函数名, 并且把字典文件改成了utf-8编码的.所以要简单些.

convert函数提供了

self.has_shengdiao = False

self.just_shengmu = False

self.spliter = '-'

用来配置参数, has_shengdiao,是汉字转拼音的时候带上拼音,

just_shengmu ,是仅仅把汉字字串转为汉字首拼字母串, 为false的时候,转为全拼字符串.

spliter是用来分隔汉字的.为空的时候,不把转换结果用spliter分开, 默认使用"-"(横线来连接汉字拼音PINYIN,因为W3C推荐在Url中使用-作为连字符,而不是下划线_来连接字符串,因为下划线作为链接连接字符串的时候,url在地址栏中好像断掉了一样,_下划线经常看不到.)

字典文件,本博客也提供不了,但是大家可以搜索下载原作的的字典, 然后使用支持utf-8转换的软件转换一下就行了.

有需要的也可以和我联系.