---
layout: post
permalink: /start-leran-android-dev
title: 小试了一下Android开发的环境搭建
---

# 小试了一下Android开发的环境搭建 #


今天我也想起来要开发android程序了,呵呵, 赶赶潮流...因为我虽然以前安装过java, 但是实际上没有任何java开发经验, 所以:

一 搜索了 android 程序开发, 找到一个视频教程 密西西比河谷州立大学：Android应用程序开发http://v.163.com/special/opencourse/developingandroidapplications.html

二 按照视频教程中提到的东西, 到java的官方网站下载了jdk7 http://www.oracle.com/technetwork/java/javase/downloads/index.html Java Platform (JDK) 7u10 然后装好.

三 到eclipse官方网站下载了一个eclipse开发工具, 朋友叫我下载3.6的, 说官方网站4.2的版本装起来很麻烦, 找了半天, 只看到个3.7的, 后来按照这个链接 http://www.eclipse.org/indigo/ 找到了一个下载页面, 不过很多版本可以供下载, 说是不需要下载ee那种企业版的版本的, 就下载Eclipse IDE for Java Developers 的就行,就是第二个下载链接, 适合个人开发. 下载完了装好.

四 到android官方网站下载 ADT(Android Developer Tools), 300多M, http://developer.android.com/sdk/index.html 下完解压一看, 这个adt的文件夹中自带了一个eclipse, 查资料才知道, 这个adt已经自带了eclipse开发工具. 我第三步中白费劲了下载eclipse弄半天了. 当然, android官方网站说了, 已经装有eclipse的, 只需要设置好adt的sdk路径, 基本上就差不多了.

五 解压adt到D:\Program Files  目录, 按照android官方网站的说明, 直接到 adt-bundle-windows-x86 目录下的eclipse目录下执行 eclipse.exe, 结果弹出一个框, 说 Failed to create the Java Virtual Machine. , 无语了, 因为我第三步装好了, 还按照教程开发了java程序 helloworld, 百度了一下, 找到 http://www.xwuxin.com/?p=547 把找到eclipse目录下的eclipse.ini文件中两个256M的数据改为128M, 重新启动eclipse.exe, 嗯, 可以运行了.

以后再写我怎么开发android程序的.