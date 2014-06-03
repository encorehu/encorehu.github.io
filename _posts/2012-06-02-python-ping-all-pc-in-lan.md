---
layout: post
permalink: /python-ping-all-pc-in-lan
title: 用python实现ping局域网中的所有ip
---

# 用python实现ping局域网中的所有ip #


最开始我就是简单的想到了使用 系统命令 ping 来解决这个问题:

首先使用ping ip, 然后用一个 for 循环来解决

    import os

    for x in range(1,256):

        ip='192.168.1.%d'%x

        cmd='ping %s' %ip

        os.system(cmd)


当然,不喜欢看到cmd黑色窗口的,可以使用subprocess

    import subprocess

    import re

    p = subprocess.Popen(["ping.exe", 'google.com'],

                                             stdin = subprocess.PIPE,

                                             stdout = subprocess.PIPE,

                                             stderr = subprocess.PIPE,

                                             shell = True)



    out = p.stdout.read()

    regex = re.compile("Minimum = (\d+)ms, Maximum = (\d+)ms, Average = (\d+)ms", re.IGNORECASE)

    print regex.findall(out)

未完待续,

参考资料:https://github.com/jedie/python-ping

http://hi.baidu.com/billschen/item/35976cf6c34828dd6325d2e6 python 学习:socket应用 实现ping功能

python写的网络ping值评测工具 http://hi.baidu.com/billschen/item/35976cf6c34828dd6325d2e6

http://stackoverflow.com/questions/316866/ping-a-site-in-python