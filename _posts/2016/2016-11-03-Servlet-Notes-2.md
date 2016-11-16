---
layout: post
title: Servlet学习笔记之二(Servlet实例)
categories:
- 编程
tags:
- Java
---

Servlet是接受http请求进行处理并作出响应的实现了 javax.servlet.Servlet 接口的Java类。下面我们在Eclipse中创建一个工程，实现一个Servlet。

## 创建工程

在Eclipse中，依次选择File > New > Other > Web > Dynamic Web Project创建一个Web工程，工程名为servletTest。然后选择File > New > Other > Web > Servlet，创建一个Servlet类。

代码如下：

{% highlight Java %}
package com.vosamo.serv;

import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Servlet implementation class ServTest
 */
public class ServTest extends HttpServlet {
	private static final long serialVersionUID = 1L;
	private String message;

    /**
     * @see HttpServlet#HttpServlet()
     */
    public ServTest() {
        super();
        // TODO Auto-generated constructor stub
    }
	/* (non-Javadoc)
     * @see javax.servlet.GenericServlet#init()
     */
    @Override
    public void init() throws ServletException {
        // TODO Auto-generated method stub
        message = "Hello World! Hello Inspur!";
    }
    /**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// 设置响应内容类型
	    response.setContentType("text/html");

	      // 实际的逻辑是在这里
	      PrintWriter out = response.getWriter();
	      out.println("<h1>" + message + "</h1>");	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		doGet(request, response);
	}

}

{% endhighlight %}

在WEB-INF下，web.xml文件中可以看到Servlet类的配置：

```
<servlet>
   <description></description>
   <display-name>ServTest</display-name>
   <servlet-name>ServTest</servlet-name>
   <servlet-class>com.vosamo.serv.ServTest</servlet-class>
 </servlet>
 <servlet-mapping>
   <servlet-name>ServTest</servlet-name>
   <url-pattern>/ServTest</url-pattern>
 </servlet-mapping>
```

## 访问Servlet

启动Tomcat，在浏览器中输入`http://localhost:8080/servletTest/ServTest`可以看到message内容。
