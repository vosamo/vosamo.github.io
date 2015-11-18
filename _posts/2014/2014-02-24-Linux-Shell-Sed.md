---
layout: post
title: Linux sed命令
categories:
- 计算机
- 操作系统
tags:
- Linux
- Shell
---

>Linux中sed命令可以对数据进行替换，删除，新增，选取特定行等功能。功能强大，使用方便


## sed的用法如下：

**sed '[n1[,n2]]function'**

**说明：**n1,n2不见得会存在，一般代表选择进行动作的行数

function有以下这些参数：

- a:新增，a后面可以接字符串，这些字符串会在新的一行（目前的下一行）出现；
- c:替换，c的后面可以连接字符串，这些字符串可以替换n1,n2之间的行；
- d:删除
- i:插入，i后面可以接字符串，这些字符串会在新的一行(目前的上一行)出现；
- p:打印，将某个选择的书据打印出来；
- s:替换，通常s可以搭配正则表达式。
## 举例：
### 1 以行为单位的新增/删除功能

将/etc/passwd的内容列出并打印行号，同时将2-5行删除
nl /etc/passwd | sed '2,5d'
承上，在第二行后加上"drink tea?"字样
nl /etc/passwd |sed '2a drink tea?'

### 2 以行为单位的替换与显示功能
将第2到5行的内容替换成“NO 2-5 number”
nl /etc/passwd |sed '2,5c NO 2-5 number'

列出文件的5-7行
nl /etc/passwd |sed -n '5.7p'
加上-n表示使用安静模式，如果不加的话5-7行内容会重复输出。

### 3 部分数据的查找与替换功能
*sed 's/要被替换的字符串/新的字符串/g'*

**示例：**

- 利用ifconfig命令查找etho的网络地址，并取出其中的地址字段，最后结果为10.0.0.1这种格式

ifconfig eth0
得到结果为：

![](http://i.imgur.com/i4JNd9A.jpg)

利用grep查找“inet addr”
```
ifconfig eth0 |grep 'inet addr'
```
得到

inet addr:192.168.227.129  Bcast:192.168.227.255 Mask:255.255.255.0

最后利用sed命令删掉前后多余的内容
```
ifconfig eth0 |grep 'inet addr'|sed 's/^.*inet addr://g' | \sed 's/Bcast.*$//g'
```
