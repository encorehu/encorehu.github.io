---
layout: post
permalink: /ddtcms-coding-style
title: DDTCMS编码规划
---

# DDTCMS编码规划 #

			  2010-1-12 20:19:52
**view 视图编码规划**
添加项目 new_class
删除     del_class
编辑     edit_class
查看     view_class

**html模板名称**

同样
new,del,edit,view,list

**url设计**

===
2010-1-12 20:49:14
上面的意图为了统一编码

但是还有一个想法就是,向django的编码靠拢
比如说edit改成 change

事情完成了,url中用done,view中用complete.(not completed)

发表用post(ed),delete(d),add,显示show
审核 approve(d)