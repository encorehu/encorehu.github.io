---
layout: post
permalink: /install-openerp70-on-freebsd
title: FreeBSD 上安装OpenERP7.0
---

暂时只用ports安装, 通过源码安装还没尝试, 意想源码安装可能更方便


##通过ports 安装

 - 自动创建用户组 openerpd 和用户 openerpd,
 - 自动添加rc.d运行脚本
 - ports安装的版本是openerp-server-7.0_1

##ports 安装

    cd /usr/ports/finance/openerp-server
    make install

经过漫长的下载, 选择组件等等, 终于安装完毕, 安装失败后, 请重复执行

    make install

最后发现还是安装失败, 就检查是哪个包没有编译成功, 然后使用

    pkg install 包名 装上这个包

然后再执行

    make install

最后肯定装好了, 如果还是装不好, 那就没办法了.

装好后会显示如下内容:

    ===> Creating users and/or groups.
    Using existing group 'openerpd'.
    Using existing user 'openerpd'.
    Installing openerp-server-7.0_1,1... done
    ####################################
    Now you can run the program with /usr/local/bin/openerp-server,
    If you want start it when the system boot, please add this line
    to your /etc/rc.conf:
        openerpd_enable="YES"
    If postgresql is not configured, openerp-server will not start.
    You can try something like this :
    [root]  # su - <PGSQL>
    [PGSQL] $ openerp_dbuser=db_user #look in /usr/local/etc/openerp-server.conf
    [PGSQL] $ openerp_dbname=db_name #look in /usr/local/etc/openerp-server.conf
    [PGSQL] $ createuser $openerp_dbuser
    [PGSQL] $ createdb --owner=${openerp_dbuser} --encoding=UTF-8 --locale=en_EN.UTF-8 ${openerp_dbname} "OpenERP initial database"
    Setuping you first database
    ---------------------------
    Point your browser to http://localhost:8069/ and click "Manage Databases", the
    default master password is "admin".
    ####################################



##修改/etc/rc.conf:

    openerpd_enable="YES" 添加这一行, 以便开机自动启动

##复制 openerp-server.conf.sample 为 openerp-server.conf

    cp /usr/local/etc/openerp-server.conf.sample /usr/local/etc/openerp-server.conf

##编辑 openerp-server.conf

    vi /usr/local/etc/openerp-server.conf

将:
将默认的

    syslog=True

改为

    syslog=False

以便将openerp-server在运行过程中的日志写到 日志/var/log/openerp/openerp-server.log 这个文件, 方便调试
如果不改, 将不会看到/var/log/openerp/openerp-server.log这个日志文件, 日志全都跑到 /var/log/messages 这个系统日志文件里了.


将:
    [options]
    # Basic daemon configuration options ##################
    #root_path = /usr/local/lib/python2.7/site-packages/openerp/
    #addons_path = /usr/local/lib/python2.7/site-packages/openerp/addons

上面两行改为下面两行

    root_path = /usr/local/openerp/
    addons_path = /usr/local/openerp/addons

然后将 root_path = /usr/local/openerp/ 这个目录里的文件全部复制到 /usr/local/lib/python2.7/site-packages/openerp/, 是为了将ports安装的时候, 自动忽略了一些xml文件重新复制过去

    root@example:/usr/local/openerp # cp -rf ./ /usr/local/lib/python2.7/site-packages/openerp

然后将 addons_path = /usr/local/openerp/addons 这个目录里的文件全部复制到 /usr/local/lib/python2.7/site-packages/openerp/addons, 是为了将ports安装的时候, 自动忽略了一些xml文件重新复制过去

    root@example:/usr/local/openerp # cd addons
    root@example:/usr/local/openerp # cp -rf ./  /usr/local/lib/python2.7/site-packages/openerp/addons


##创建数据库openerp
数据库使用postgresql数据库

# 切换到 pgsql 系统用户, 如果postgresql装好了, 就有系统用户 pgsql

    [root]  # su - pgsql

    [PGSQL] $ openerp_dbuser=openerp #跟配置文件一致 /usr/local/etc/openerp-server.conf
    [PGSQL] $ openerp_dbname=openerp #跟配置文件一致 /usr/local/etc/openerp-server.conf
    [PGSQL] $ createuser $openerp_dbuser
    [PGSQL] $ createdb --owner=${openerp_dbuser} --encoding=UTF-8 --locale=c ${openerp_dbname} "OpenERP initial database"

然后，使用pg数据库的超级用户pgsql登录数据库控制台，设置openerp用户的密码，完成后退出控制台。

    [PGSQL] $ psql -U pgsql -d postgres
    \password openerp
    # 退出数据库的shell控制台
    \q


##补装几个python模块:

    easy_install mock
    easy_install unittest2



##运行openerp, 执行命令

    /usr/local/etc/rc.d/openerpd start

##停止openerp, 执行命令

    /usr/local/etc/rc.d/openerpd stop

##重启openerp, 执行命令(这个命令不一定起作用, 需要使用kill -9 pid结束后再start)

    /usr/local/etc/rc.d/openerpd restart

##查看openerp的运行状态

    /usr/local/etc/rc.d/openerpd status

##查看openerp运行的pid

    cat /var/run/openerp/openerp-server.pid
