---
layout: post
permalink: /unicode-character-zero-width-space-8203
title: 意想不到的零宽字符8203空白字符
---

# 意想不到的零宽字符8203空白字符 #


今天又遇到了&#8203; 这个字符了, 出现在<body>的前面, css对它失效, 怎么都对不齐. 它会导致css和js失效.

主要是在 html 文件的末尾标记<html>的后面有一个零宽空白字符. 这是一个用于换行分隔的字符, 在编辑器里一般看不到这个字符, 但是编辑的时候如果光标在它的右边, 按backspace退格键还是能够将它删除.

详见: http://stackoverflow.com/questions/2973698/whats-html-character-code-8203]