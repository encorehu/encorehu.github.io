---
layout: post
permalink: /lcs-caculate
title: 最长公共子串计算
---

# 最长公共子串计算 #


最长公共子串
就是给定N个字符串, 求出这N个字符串之间最长的公共部分(连续的),就是最长公共子串.
通常情况下, 大家给出的算法只是计算2个字符串之间的最长公共子串.

例如 abcde和bcdf之间的最长公共子串就是bcd

当然网上最多的代码例子是计算 最长公共子序列(不连续的相同字符组合成的序列)的代码.

网上有现成的几个python实现的计算最长公共子串的代码

代码1: http://www.cnblogs.com/ChenxofHit/archive/2011/03/11/1981568.html] 最长公共子序列和最长子字符串_python_算法与数据结构
作者的分析可能是对的, 例子的计算也是对的, 但是稍微换几个例子, 作者的代码就出错了. 因此得出结论, 该作者的代码是错误的.

代码2:http://biancheng.dnbcw.info/python/348696.html LCS 最长公共子串
实际上, 这个代码和上面的那个差不多, 可能是抄的, 而且稍微给几个不同的例子测试, 就出错了. 结论是代码是错误的.
而且还有个问题是, 最长公共字串和最长公共子序列搞混淆了.

代码3:http://bbs.chinaunix.net/thread-1344481-1-1.html Perl版有个有趣的题目：寻找最大公共串
这个代码好像是可行的, 但是计算时间太长, 字符串越长, 计算时间就越长. 作者的后续:http://blog.csdn.net/shaohui/article/details/784577 求公共子串问题以及其改进算法

代码4: http://hi.baidu.com/khzmylyasdhjmwr/item/8c63fcc6c0dc3420a1b50a25] [python] 两个字符串的最大公共子串
这个代码我没有验证, 是从代码3引出来的文章.

代码5: http://blog.csdn.net/huanhuolang/article/details/6146804] 最长递增子列、最长公共子序列 python实现
例子结果是正确的, 其他的没测试.

代码6: http://www.cnblogs.com/zhangchaoyang/articles/2012070.html] 最大子序列、最长递增子序列、最长公共子串、最长公共子序列、字符串编辑距离
代码未验证, 看起来好厉害的样子

代码7: http://www.chengxuyuans.com/Python/41644.html] 字符串相似性算法【最长公共字符串算法】 【LCS】
代码未验证

最长公共子序列
代码1: http://zh.wikipedia.org/wiki/%E6%9C%80%E9%95%BF%E5%85%AC%E5%85%B1%E5%AD%90%E5%BA%8F%E5%88%97] 最长公共子序列

和值最大子串