---
layout: post
permalink: /python-403-forbidden
title: Python抓取github出现403 forbidden的记录
---

# Python抓取github出现403 forbidden的记录 #


今天,  尝试使用python通过github的api抓取一些app的基础数据,比如作者和简介啥的, 开始几个还挺顺利, 后来就直接 403 forbidden了, 网上找了很多资料, 看这一篇 : http://www.douban.com/note/249043281/ 我终于知道原因了. 原来不是我没有设置好user-agent, 而是github v3 版的api限制了每个小时的抓取数量, 并且超过限制后, 直接封掉你的IP了.

折腾了好久, 写了一个获取jsonp的python脚本,  获取到一点代码:

    '{

      "meta": {

        "X-RateLimit-Remaining": "0",

        "status": 403,

        "X-GitHub-Media-Type": "github.beta",

        "X-RateLimit-Limit": "60"

      },

      "data": {

        "message": "API Rate Limit Exceeded for 113.XXX.121.XX"

      }

    }'
    看来是真的把我拉黑了, 如果想重新开始, 就需要等一个小时之后再开始抓了.

超过了github api 的limit限制之后, 就是403 禁止访问, 等了好久 发现是404 找不到, 后来能访问了, 返回的内容是很短的乱码, 用浏览器倒是很正常的显示了json内容. 不过也有次数限制.

至于为什么后来(大概是一个小时之后) 能抓了, 但是显示的是乱码, 原来我发现github返回的东西是gzip的, 后来又从网上找到如何解压gzip的资料, 终于能抓数据了, 只是匿名用户, 一个小时只能抓60次数据, 如果想高频率匿名抓数据, 就必须申请一个github api的 clientID, 算了, 有时间下回再弄吧.