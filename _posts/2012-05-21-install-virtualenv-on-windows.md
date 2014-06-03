---
layout: post
permalink: /install-virtualenv-on-windows
title: windows下安装virtualenv
---

# windows下安装virtualenv #

			1. 安装 Pywin32

即 python的windows API包，地址：

    http://sourceforge.net/projects/pywin32/files/


源码包：

     http://pypi.python.org/pypi/virtualenv#downloads

2. 安装virtalenv

执行命令：
easy_install virtualenv
即可安装

或者进入解压后的源码包，运行 setup.py install 也能安装。
完了之后，命令行进入任何一个目录，运行 virtualenv 环境名称
例如：
    C:\Download\dev>virtualenv ttt

    New python executable in ttt\Scripts\python.exe

    Installing setuptools................done.

    Installing pip...................done.

    C:\Download\dev\ttt>tree

    卷 SYSTEM 的文件夹 PATH 列表

    卷序列号码为 00000000 xxxxxxxx

    C:.

    ├─Include

    ├─Lib

    │  ├─distutils

    │  ├─encodings

    │  └─site-packages

    │      └─pip-1.1-py2.5.egg

    │          ├─EGG-INFO

    │          └─pip

    │              ├─commands

    │              └─vcs

    └─Scripts
    3. 设置虚拟环境

创建一个叫“foobar”的虚拟环境，或者说是一个沙盒

virtualenv foobar

4. 激活环境

这会让foobar虚拟环境下的site-packages成为默认的版本