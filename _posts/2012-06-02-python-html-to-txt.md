---
layout: post
permalink: /python-html-to-txt
title: [原创]一个将html转为txt的python脚本
---

# [原创]一个将html转为txt的python脚本 #


我写的一个python版的html转txt的脚本, 流式处理, 仿照HTMLParser的,但是功能就比他简单多了.还有些错误以后再处理.

什么也不说了, 代码就是最好的文档.

如下:

    # -*- coding: cp936 -*-



    import urllib2

    url = 'http://www.sohu.com/'

    req = urllib2.Request(url)

    req.add_header('User-Agent', 'Mozilla/4.0 (compatible; MSIE 5.5; Windows NT)')

    print 'Connecting %s ...' % url

    try:

        resp  = urllib2.urlopen(req)

    except urllib2.URLError:

        print 'Connect to %s FAILED' % url

    htmlcode= resp.read()

    try:

        lll = htmlcode.decode('gb18030')

    except UnicodeDecodeError:

        lll = htmlcode



    l1=len(lll)

    data=[]

    piece=''

    script_tag=False

    style_tag=False

    for x in lll:



        if x =='<':

            start_tag=True

            end_tag=False

            data_tag=False



            piece=''

            continue

        if x =='>':

            if script_tag:

                data_tag = False

            else:

                data_tag=True

            if style_tag:

                data_tag = False

            else:

                data_tag=True

            start_tag=False

            end_tag=True

            #script_tag   = False

            #print 'piece:','-'*10,piece

            continue

        else:

            if piece.lower() =='script':

                script_tag   = True

                script_start = True

                script_end   = False

                data_tag     = False



                #print '进入Script区域'

                #continue

            elif piece.lower()  =='/script':

                script_tag   = False

                script_start = False

                script_end   = True

                data_tag     = True

                #print '离开script区域'



            if piece.lower() =='script':

                style_tag   = True

                style_start = True

                style_end   = False

                data_tag     = False



                #print '进入style区域'

                #continue

            elif piece.lower()  =='/script':

                style_tag   = False

                style_start = False

                style_end   = True

                data_tag     = True

                #print '离开style区域'



            else:

                #print 'piece:',piece

                script_start = False

                script_end   = False

                #data_tag     = False



            if script_tag and end_tag:

                continue

            elif style_tag and end_tag:

                continue

            else:

                piece+=x





            if data_tag:

                if x not in '\r\n\t ':

                    data.append(x.strip())

                else:

                    data.append(x)











    aaa=''.join(data).strip() \

          .replace('\t\t',' ') \

          .replace('\t',' ') \

          .replace('\r','\n') \

          .replace('  ',' ') \

          .replace('  ',' ') \

          .replace('  ',' ') \

          .replace('  ',' ') \

          .replace(' ',' ') \

          .replace('  ','\n  ')\

          .replace(' ','')\

          .replace('\n\n','\n') \

          .replace('\n\n','\n') \

          .replace('\n\n','\n') \

          .replace('\n\n','\n') \

          .replace('\n\n','\n') \

          .replace('\n\n','\n')





    l2=len(aaa)

    #print repr(aaa)

    print aaa

    print "原网页长度 %d ,清理后 %d, 压缩比%0.2f%%" % (l1,l2, 100.00 * l2/l1)



    from HTMLParser import HTMLParser



    class MyHTMLParser(HTMLParser):



        def handle_starttag(self, tag, attrs):

            print "Encountered the beginning of a %s tag" % tag



        def handle_endtag(self, tag):

            print "Encountered the end of a %s tag" % tag







    my=MyHTMLParser()



    my.feed(lll)

    my.close()

    # 这个 HTMLParser 的派生来自于python的文档, 但是会出错, 我的脚本不出错.