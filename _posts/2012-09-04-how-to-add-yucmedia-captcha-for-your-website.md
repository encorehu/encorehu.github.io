---
layout: post
permalink: /how-to-add-yucmedia-captcha-for-your-website
title: 怎样给你的Django网站项目添加宇初验证码
---

# 怎样给你的Django网站项目添加宇初验证码 #


为了解决留言本垃圾留言的不断产生，我给我的留言本加了验证码。本站启用的是宇初验证码插件。

此插件是由深圳市宇初网络技术有限公司开发的清晰云验证码，旨在提升用户体验、增强网站安全性。安装此插件后可以在后台指定论坛的注册，登陆zzzz，发帖，回复等地方开启清晰云验证码。

当然对于php的网站来说，宇初公司已经提供了对应的文件来支持，Discuz论坛有个文章介绍了：http://www.discuz.net/forum.php?mod=viewthread&tid=2742120

里面的图片示意http://att.discuz.net/data/attachment/forum/201205/04/163706r23rrb3nzpdn883e.jpg

## 一、先来看看宇初验证码的安装 ##

先在线体验一把吧：宇初官方体验]  本站留言本体验] 需要用鼠标左键点击才能看到！！！

看到了吧，这个是将一个广告展示给你看，然后输入要求的文字（通常是变色的文字），即可。

先到宇初验证码的官方网站上去注册一个账号，然后在帮助中心]里，就可以找到相关的开发文档。

宇初验证码的安装是很容易的：

前端代码：

现在你需要用到宇初验证码的表单中添加一个js脚本，控制某个特定 id 的input，例如这样一个input：<input id="id_example" type="text" size="10">当鼠标点击这个 id_example 的input的时候，该脚本开始在input输入框上方显示一个宇初验证码广告，广告中的文字就是你需要输入的验证码。

    <input id="id_example" name="example" type="text" size="10">

    <script src="http://api.yucmedia.com/script/script.js?key=(sitekey)

        &inputid=(输入框id,注意此处是输入框的id值非name值，也就是上面说的id_example)

        &offtop=0&offleft=0

        &zbkey=(位置版本标示符)">

    </script>



1.1这个位置版本标示符zbkey，指的是用户提交的验证码的时候是在网站的什么位置的简短标示，同时标示了服务器端的处理脚本类型：

    如果是php语言的站点请填写登录dphp，注册zphp,发帖fphp,回帖hphp;

    如果是jsp语言的站点请填写登录djsp，注册zjsp,发帖fjsp,回帖hjsp;aspnet和asp同理



据我所知asp的是dasp,zasp,fasp,hasp,而aspnet的不知道是daspx还是daspnet

当然我自己弄了个官方没有的python版本的后端代码，我将这个zbkey做了和php语言的类似定义：

    如果是python语言的站点，我就写 登录dpy， 注册zpy,发帖fpy,回帖hpy.


也是一目了然的.

1.2上面的offtop=0和offleft=0,指的是宇初验证码广告距离 输入框的位移.改变offtop和offleft的值可以调节验证码的显示位置，默认为验证码输入框正上方靠左.

1.3 这个sitekey是一个很重要的数据.它就是你的网站的标识符, 你在宇初注册了之后, 你的网站会得到唯一的一个sitekey,用来确认验证码是从你的网站请求的.

前端代码装好了就是这个样子,示例:

    <input id="id_example" name="example" type="text" size="10">

    <script src="http://api.yucmedia.com/script/script.js?key=0sx9xfdo3p3ijx3y2xlwvg3qy&inputid=id_example&offtop=0&offleft=0&zbkey=zjsp"></script>


## 二 宇初验证码网站后端代码的安装 ##

后段代码主要是从官方下载对应网站程序语言的开发包，从帮助中心里面查找对应的包来安装。

共有：

    +PHP开发网站

    +JSP开发网站帮助指南

    +.NET开发网站帮助指南

    +ASP开发网站帮助指南

可以发现上面没有python的开发包。那该怎么办？

办法就是仿照php和asp的代码，自己写一个网站后端处理函数。

我对php和asp都有一定的了解，只能算是能够读懂吧。我主要从asp代码来看宇初验证码的后端响应情况：

1.提交验证码，网站程序后端响应该HTTP POST提交过来的数据。

那么post提交了那些数据呢，通过分析前端html代码，发现提交了用户输入的验证码、一个BMserialnum字段数据，一个yucmedia_verifyinfo字段数据，后面的2个都是隐藏字段，其中BMserialnum是一长串包含了你的sitekey的字符串，而yucmedia_veifyinfo则通常情况下是空值。这个BMserialnum看起来应该叫做   保密序列号。

2.网站后端响应，根据用户提交的数据，同时获得 用户的ip、特定id的输入框的输入值（上面的id_example中的输入，也就是用户输入的验证码）、sitekey（网站标识）、idenkey（应该是身份表示吧）、yuc_serialnum（就是上面的BMserialnum），还有zbkey。通过将前面的6个参数提交给一个函数yucmedia_verify，得到一个返回值，前面4个字符到底是true还是其他，如果是true，就证明用户输入的验证码是正确的，反之就是错误的。

处理过程就是，先获取6个必须的参数，一个参数为空就不提交参数到yucmedia_verify函数，就提示验证码的输入信息不全。

然后yucmedia_verify内部干的事情就是：

将通过6个参数（必须是utf-8编码格式的，如果你在宇初注册的时候将你的站点代码填写为utf-8的话）构造一个http请求，到宇初的服务器查询这个验证码是否正确。得到一个字符串，作为yucmedia_verify函数的返回值。然后在用户的处理过程中获取前4个字符来判断是否通过验证。

后面的就是balabala无关重要的东西了。

3.验证全部通过，网站后端代码就跳转到指定的url，完成这次验证输入提交的数据处理过程。


## 三 为django项目编写python版的宇初处理代码 ##

按照上一部描述的流程和相关的asp代码（yucmedia(asp)/yucmedia.asp）：

    <%

    Function yucmedia_verify(sitekey,serialnum,userip,userresponse,idenkey,zbkey)

      Dim url

      url="http://api.yucmedia.com/script/verify"

      url=url&"?sitekey="&server.URLEncode(sitekey)

      url=url&"&serialnum="&server.URLEncode(serialnum)

      url=url&"&userip="&server.URLEncode(userip)

      url=url&"&userresponse="&toUTF8(userresponse)

      url=url&"&idenkey="&server.URLEncode(idenkey)

      url=url&"&zbkey="&zbkey

      Dim Http

      set Http=server.createobject("MSXML2.ServerXMLHTTP")

      Http.open "POST",url,false

      Http.send()

    yucmedia_verify=Http.responseText



    End Function









    Function toUTF8(szInput)

        Dim wch,uch,szRet

        Dim x

        Dim nAsc,nAsc2,nAsc3



        If szInput = "" Then

            toUTF8 = szInput

            Exit Function

        End If



         For x = 1 To Len(szInput)



            wch = Mid(szInput,x,1)



            nAsc = AscW(wch)

            If nAsc < 0 Then nAsc = nAsc + 65536



            If (nAsc And &HFF80) = 0 Then

                szRet = szRet & wch

            Else

                If (nAsc And &HF000) = 0 Then

                    uch = "%" & Hex(((nAsc \ 2 ^ 6)) or &HC0) & Hex(nAsc And &H3F or &H80)

                    szRet = szRet & uch

                Else



                    uch = "%" & Hex((nAsc \ 2 ^ 12) or &HE0) & "%" & _

                                Hex((nAsc \ 2 ^ 6) And &H3F or &H80) & "%" & _

                                Hex(nAsc And &H3F or &H80)

                    szRet = szRet & uch

                End If

            End If

        Next



        toUTF8 = szRet

    End Function





    %>
    可以看到,这里有个函数叫做yucmedia_verify, 一共有6个参数,这6个参数,php版本和asp版本的顺序是不一样的,我在自己编写python版本的yucmedia_verify函数时使用了php的参数顺序,但是传递参数的时候,是使用asp的顺序, 调试了好久.

里面有个to_utf8的函数就是转换成utf-8编码格式的,原因是宇初的api貌似是接受utf-8格式的请求.

看看php版的代码:

    <?php

    /*

    This Is The YucMedia Captcha Api Scripts

    All Rights Reserved

    */



    /*

    YucMedia Captcha校验函数



    $sitekey--是注册激活后获取的网站身份识别码，是一个25位的字符串。

    $serialnum--是会话流水号。

    $userresponse--是用户输入的验证码值。

    $idenkey--也是注册激活后获取的网站身份校验码，也是一个25位的字符串。

    $remoteip--用户ip地址。

    $zbkey--网站位置标识符。

    */

    function  geturlendata($argv){

    $flag = 0;

    $params = "";

    foreach ($argv as $key=>$value)

    {

    if ($flag!=0)

    {

    $params .= "&";

    $flag = 1;

    }

    $params.= $key."=";

    $params.= urlencode($value);

    $flag = 1;

    }

    return $params;

    }



    function yucmedia_verify($sitekey,$remoteip,$serialnum,$userresponse,$siteidenkey,$zbkey){

    $urlendata=geturlendata(array(

                                  'sitekey'=>$sitekey,

                                     'userip'=>$remoteip,

                                      'serialnum'=>$serialnum,

                                        'userresponse'=>$userresponse,

                                         'idenkey'=>$siteidenkey,

    									   'zbkey'=>$zbkey

                                               ));

    $length = strlen($urlendata);



    $post = fsockopen('api.yucmedia.com',80,$errno,$errstr,10) or exit($errstr."—>".$errno);



    $header = "POST /script/verify HTTP/1.0\r\n";

    $header .= "Host:api.yucmedia.com\r\n";

    $header .= "Content-Type: application/x-www-form-urlencoded\r\n";

    $header .= "Content-Length: ".$length."\r\n";

    $header .= "User-Agent: yucmedia/PHP\r\n";

    $header .= "Connection: Close\r\n\r\n";

    $header .= $urlendata."\r\n";



    fputs($post,$header);



    while(!feof($post))

    {

    $response.= fgets($post,1024);

    }

    fclose($post);

    $response = explode("\r\n\r\n", $response, 2);

    return $response[1];

    }

    ?>
    php版的还构造了user-agent:为yucmedia/PHP,我的那个python版的代码也不示弱,我也构造了yucmedia/Python的 user-agent

这两个版本的api作用的过程,我在上面第二节说过了.这里就不罗嗦了.下面贴上我写的python版的代码,敬请批评指正.

    # -*- coding: utf-8 -*-

    # Author:	huyoo353

    # email:	huyoo353@126.com

    #



    import urllib

    import urllib2



    def yucmedia_verify(sitekey,serialnum,userip,userresponse,idenkey,zbkey):

    	""" 这个函数 php 和asp 的参数顺序不一致, php userip在serialnum前面, asp 则相反.

    	YucMedia Captcha校验函数



    	sitekey--是注册激活后获取的网站身份识别码，是一个25位的字符串。

    	serialnum--是会话流水号。

    	userresponse--是用户输入的验证码值。

    	idenkey--也是注册激活后获取的网站身份校验码，也是一个25位的字符串。

    	remoteip--用户ip地址。

    	zbkey--网站位置标识符。登录dpy，注册zpy,发帖fpy,回帖hpy;

    	"""

    	url="http://api.yucmedia.com/script/verify"

    	query_data={

    		'sitekey':      sitekey,

    		'serialnum':    serialnum.encode('utf-8'),

    		'userip':       userip,

    		'userresponse': userresponse,

    		'idenkey':      idenkey,

    		'zbkey':        zbkey,

    	}

    	# urlencode 函数必须接受非 Unicode 字符, 所以query_data在传入之前

    	# 必须都转码成utf-8,或者gb2312之类的编码

    	data       = urllib.urlencode(query_data)



    	user_agent = 'yucmedia/python' # 仿php的代理头 'Mozilla/4.0 (compatible; MSIE 5.5; Windows NT)'

    	headers    = { 'User-Agent':   user_agent,

    		           'Host':         'api.yucmedia.com',

                       'Content-Type': 'application/x-www-form-urlencoded',

                     }

    	req        = urllib2.Request(url, data, headers)

    	# 尝试去打开宇初的验证api地址, 并返回验证结果

    	try:

    		response   = urllib2.urlopen(req)

    		resp_text  = response.read()

    	except URLError, e:

    		resp_text  = ''





    	return resp_text


## 四 将宇初验证码为django项目专门模块化 ##

通过在captcha app下增加yucmedia验证码，来模块化使用yucmedia验证码。
yucmedia目前设计是仿照现有的captcha来创建form必须的widget，
并且重写form的clean() 方法来验证用户输入的验证码,但是这种情况的缺点是必须要
大规模的修改现有的表单form和增加必要的view处理.

## 五 添加form处理验证函数 ##

待续。。。