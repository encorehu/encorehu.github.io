---
layout: post
permalink: /how-to-use-south-migrate-re-create-tables-after-django-database-table-delete
title: Django数据表删除后如何使用south来migrate重新建表
---

# Django数据表删除后如何使用south来migrate重新建表 #


通常的情况是在django的开发过程中, 修改model之前备份数据, 修改数据model定义后, 使用south 的schemamigration -auto 自动生成migration, 然后使用了数据库工具把数据表全部删除, 这个时候, 怎么使用south的migrate重新在数据库里创建该app的表格呢?

以news为例

1. manage.py dumpdata --format=json news>news.json

2. 修改news.models 里的某个 model

3.使用south追踪修改: manage.py schemamigration  news --auto

4.使用manage.py dbshell 进数据库, 删掉news的所有数据表, DROP TABLE ......

5.这个时候使用manage.py syncdb是不能自动在数据库中创建news的相关表格的.

6方法是使用manage.py schemamigration news --initial, 重新初始化 可能会得到 0003_initial(你以前有0001_initial, 0002_auto...)

7. manage.py migrate news 0001 --fake

8 manage.py migrate news 0002 --fake

9 manage.py migrate news(经过fake上面2步, 这一步才开始建表)

10 manage.py loaddata news.json 把数据重新导入数据库