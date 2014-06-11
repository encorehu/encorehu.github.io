---
layout: post
permalink: /google-chrome-browser-history
title: google chrome浏览器历史记录
---

在statckoverflow上找到了资料: http://stackoverflow.com/a/10698783

它引用自这里: http://computer-forensics.sans.org/blog/2010/01/21/google-chrome-forensics/

##google浏览器的访问时间

last_visit_time 是采用的webkit的计时方法, 和普通的unix时间戳(起点时间是1970-01-01 00:00:00)不一样, 它的起点时间是1601-01-01 00:00:00

这里: http://www.withparadox2.com/archives/39 也有介绍

换算成unix时间戳的方法是

    round( ( $last_visit_time - 11644473600000000 ) / 1000000 ) )
