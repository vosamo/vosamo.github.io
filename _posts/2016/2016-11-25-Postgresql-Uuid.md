---
layout: post
title: PostgreSQL使用uuid类型主键
categories:
- 数据库
tags:
- PostgreSQL
---

PostgreSQL内置uuid类型，性能不错，但是PostgreSQL默认没有安装uuid相关操作函数，需要手动导入。

操作步骤：

1. 导入uuid相关函数

你如果安装了postgresql数据库的话，可以在找到：`/usr/pgsql-9.4/share/extension/uuid-ossp--1.0.sql`

将其导入到你需要使用uuid的数据库：

`psql -d dbname -U dbuser -f /usr/pgsql-9.4/share/extension/uuid-ossp--1.0.sql`


2. 创建extension

导入成功之后会得到一个提示信息：`create extension "uuid-ossp"`

进入你的数据库：`psql dbname`

执行语句：`create extension "uuid-ossp";`

3. 创建uuid类型字段

创建数据表的时候可指定id类型为uuid:

`id uuid NOT NULL DEFAULT uuid_generate_v4()`
