---
layout: post
title: Servlet学习笔记之三(Servlet表单、请求和响应)
categories:
- 编程
tags:
- Java
---

在交互过程中，经常需要将一些数据通过浏览器发送给web服务器，最后传递给后台程序处理。最常用的提交数据方法是GET和POST。

## GET方法

GET方法是浏览器向Web服务器提交信息的默认方法。是通过请求字符串的形式传递数据，如下：

```
http://www.test.com/hello?key1=value1&key2=value2
```

GET方法提交数据有大小限制：请求字符串不得超过1024个字符。另外，GET方法不会对提交的数据加密，相对来说不够安全，所以GET方法更多是用来从服务器获取数据。

## POST方法

POST方法是比较可靠的提交数据的方法，和GET方法不同，POST方法不是把数据作为请求字符串放在URL中提交，而是把这些数据作为一个单独的消息放在消息体中提交。

HTTP协议把请求分为三部分：状态行、请求头、消息主体。类似于下面这样：

```
<method>
<request-url>
<version>
<headers>
<entity-body>
</entity-body>
</headers>
</version>
</request-url>
</method>
```

协议规定POST提交的数据必须放在消息主体（entity-body）中，但协议没有规定数据必须使用什么编码方式。数据提交之后，服务端能够成功解析才行，服务端通常根据请求头中的Content-Type字段来获知消息主体中的数据是何种编码方式，再对消息主体进行解析。

常见的消息主体编码方式有四种：

**application/x-www-form-urlencoded**

  这应该是最常见的 POST 提交数据的方式了。浏览器的原生 form 表单，如果不设置 enctype 属性，那么最终就会以 application/x-www-form-urlencoded 方式提交数据。请求类似于下面这样:

  ```
  POST http://www.example.com HTTP/1.1
  Content-Type: application/x-www-form-urlencoded;charset=utf-8
  title=test&content=hello
  ```

  提交的数据按照 key1=val1&key2=val2 的方式进行编码。

**multipart/form-data**

我们使用表单上传文件时，必须让 form 的 enctyped 等于这个值。

**application/json**

JSON 格式支持比键值对复杂得多的结构化数据，形式上更加灵活。而且，后台也易于解析。提交JSON数据形式一般为：

```
POST http://www.example.com HTTP/1.1

Content-Type: application/json;charset=utf-8

{"title":"test","sub":[1,2,3]}
```

这种方案，可以方便的提交复杂的结构化数据，特别适合 RESTful 的接口。

**text/xml**

XML-RPC协议就是一种使用 HTTP 作为传输协议，XML 作为编码方式的远程调用规范。

## 使用Servlet获取表单数据

- getParameter()：您可以调用 request.getParameter() 方法来获取表单参数的值
- getParameterValues()：如果参数出现一次以上，则调用该方法，并返回多个值，例如复选框
- getParameterNames()：如果您想要得到当前请求中的所有参数的完整列表，则调用该方法
