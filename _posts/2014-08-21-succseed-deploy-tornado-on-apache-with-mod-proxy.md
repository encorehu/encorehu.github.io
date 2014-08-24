---
layout: post
permalink: /succseed-deploy-tornado-on-apache-with-mod-proxy
title: 使用Apache部署Tornado
---

###方法一: 使用mod_wsgi, 这个配置少, 但是我没有成功


在httpd.conf中添加一行

	LoadModule wsgi_module modules/mod_wsgi.so

可以到 http://www.lfd.uci.edu/~gohlke/pythonlibs/#mod_wsgi

这个页面下载windows版本的mod_wsgi.so


我失败的主要错误是: 日志中有这么几行

	Traceback (most recent call last):, referer: http://mysite.com/
	  File "C:\\Python27\\lib\\site-packages\\tornado-4.1.dev1-py2.7-win32.egg\\tornado\\wsgi.py", line 227, in __call__, referer: http://mysite.com/
	    raise Exception("request did not finish synchronously"), referer: http://mysite.com/
	Exception: request did not finish synchronously, referer: http://mysite.com/

这个主要是在我的RequestHandler中的get函数, 使用了`gen.coroutine`修饰, 因为里面使用了`yield momoko.Op(...)`这种操作pgsql数据库的语句, 结果就导致wsgi没有完成finish()函数.
即便在 get函数的最后, 显式的调用 self.finish() 都没用.

	class IndexHandler(BaseHandler):
	    #@tornado.web.asynchronous 没用
	    @gen.coroutine #这个不是罪魁祸首
	    def get(self):
	        results_count=yield momoko.Op(...) # 这个数据库操作是罪魁祸首
	        logger.error('fsfadsfasdfads')   #不知道怎么记录到文件
	        kwargs={
	            'results_count':results_count,
	        }
	        logger.error('fffffffffffffffffffff')
	        #self.render('bbs/index.html',**kwargs)  #没用
	        self.write('fffffffffff')   #没用
	        self.finish()  #显式调用都没用

查阅了很多资料, 我发现了 http://stackoverflow.com/questions/21459642/how-to-use-async-tornado-api-inside-tornado-wsgi-wsgicontainer/21460482#21460482

我觉得问题可能正如文中所说, 如果使用了wsgi, 那么一切使用了gen.coroutine的这种异步编程模式的代码, 都会不正常, 因为wsgi并不支持异步编程. 所有使用了gen.coroutine修饰符的tornado异步代码都会执行不正确或者不正常, 必须使用同样功能的同步代码来完成你的get或者post操作, 也就是说阻塞在wsgi中是不可避免的, 而且, 如果想把tornado的项目使用wsgi来使他跑起来, 最后就是那些异步代码可能执行不了.我现在遇到的问题就是raise Exception("request did not finish synchronously"), 解决的办法只有方法二了, 就是tornado自己运行, 然后使用apache或者nginx的反向代理来完成转发web请求.

###方法二: 使用proxy代理, 配置多, 但是行了

####Apache 虚拟主机配置

	<VirtualHost *:80>
	    ServerAdmin admin@mysite.com
	    ServerName mysite.com
	    ErrorLog "logs/mysite-error.log"
	    CustomLog "logs/mysite-access.log" common
	    UseCanonicalName On
	    DocumentRoot "C:/Projects/mysite.com/"

	    ProxyRequests Off
		<Proxy balancer://mysite>
		    BalancerMember http://127.0.0.1:8888
		    BalancerMember http://127.0.0.1:8889
		    BalancerMember http://127.0.0.1:8890
		    BalancerMember http://127.0.0.1:8891
		</Proxy>
		ProxyPass / balancer://mysite/
	</VirtualHost>

####Apache 加载这些模块, proxy要用, 其他的配置保持不动

	LoadModule lbmethod_byrequests_module modules/mod_lbmethod_byrequests.so
	LoadModule proxy_module modules/mod_proxy.so
	LoadModule proxy_balancer_module modules/mod_proxy_balancer.so
	LoadModule proxy_connect_module modules/mod_proxy_connect.so
	LoadModule proxy_http_module modules/mod_proxy_http.so
	LoadModule slotmem_plain_module modules/mod_slotmem_plain.so
	LoadModule slotmem_shm_module modules/mod_slotmem_shm.so

在8888端口启动tornado项目之后, 这种方法成功的看到了网页
然后, 还可以在不同的端口, 多启动几个进程
