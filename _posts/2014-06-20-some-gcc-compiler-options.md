---
layout: post
permalink: /some-gcc-compiler-options
title: gcc编译器的一些注意事项
---

###第1点: 动态链接库\*.a必须放在obj/\*.o的后面


它引用自这里: http://stackoverflow.com/questions/19901934/strange-linking-error-dso-missing-from-command-line

例如

    gcc   -Wall   -g -O2 -export-dynamic -o utilities ovs.o pctl.o libopenvswitch.a librte_eal.a

###linux /usr/bin/ld cannot find -lxxxx

这个是找不到libxxxx库, 一般将lib省略了

###第2点:undefined reference to symbol 'sqrt@@FBSD_1.0'

    /usr/local/bin/ld: obj/bitcoinrpc.o: undefined reference to symbol '_Znam'
    //usr/lib/libc++.so.1: error adding symbols: DSO missing from command line

解决办法: http://archive.ambermd.org/201304/0492.html

    -l stdc++ 即可


    /usr/local/bin/ld: obj/addrman.o: undefined reference to symbol 'sqrt@@FBSD_1.0'
    //lib/libm.so.5: error adding symbols: DSO missing from command line

这种是缺少链接库, 就是需要加-l 参数,  -l 库名

###第3点:如果是为了把多行连成一行文字, 需要行尾加一个空格再加一个反斜杠, 但是反斜杠后面不能再有空格

    LIBS=a \
       -l stdc++ \
       -l abc

   最后一行不要加反斜杠 \

