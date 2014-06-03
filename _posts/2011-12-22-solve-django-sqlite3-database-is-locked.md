---
layout: post
permalink: /solve-django-sqlite3-database-is-locked
title: 解决django的sqlite3的database is locked
---

# 解决django的sqlite3的database is locked #


今天在用admin后台添加一篇博客，最后居然报错：DatabaseError at  ... database is locked, 因为我的数据库使用的是sqlite3，所以不支持大量的访问是有可能的，但是目前仅仅我一个人访问的话，居然报错，我就纳闷了，用google搜索了DatabaseError at  database is locked 和django database is locked，找到的信息就是说，sqlite3不适合于多线程访问，就是不适合threding访问。我那些程序原来好好的，就是今天添加博客文章突然不行了，于是接着搜索答案。最后找到这么一篇：

    http://www.cse.msu.edu/~wangyua6/blog/?p=1516]  [django]SQLite database is locked 解决办法



因为内容很短，而且可能有些人访问不了，干脆全部贴在下面算了：
Oct 012011

今天玩django，用了django推荐的SQLite，我勒个去，居然在写入数据库时，出现了“Database is locked”的错误。无奈啊，放狗搜了，这里]，这里]和这里]，不过尝试了都解决不了。

结果悲剧的发现，是SQLite的数据库文件的权限不行。。。改成666就OK了。。。

148 views
doubletony] 发表于 20:55
作者的这几句话，提醒了我，我马上登录服务器查看权限，原来我的sqlite3数据库也是只有-rw-r--r--权限，改为666后，就没有数据库被锁定的错误了。原来出错了还要刷新好几下或者等待几分钟才能访问，现在不用了。

https://code.djangoproject.com/ticket/9409]

或者按照 https://docs.djangoproject.com/en/dev/ref/databases/#database-is-locked-errors] 试试

    DATABASES = {

        'default': {

            'ENGINE': 'django.db.backends.sqlite3', # Add 'postgresql_psycopg2', 'postgresql', 'mysql', 'sqlite3' or 'oracle'.

            'NAME':   os.path.join(YOUR_DIR,'data.db'),      # Or path to database file if using sqlite3.

            'USER': '',                      # Not used with sqlite3.

            'PASSWORD': '',                  # Not used with sqlite3.

            'HOST': '',                      # Set to empty string for localhost. Not used with sqlite3.

            'PORT': '',                      # Set to empty string for default. Not used with sqlite3.

          'OPTIONS': {

            'timeout': 20,

        }

        }

    }





就是在DATABASES中的字段添加：

          'OPTIONS': {

            'timeout': 20,

        }