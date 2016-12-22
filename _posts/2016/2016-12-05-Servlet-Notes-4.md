---
layout: post
title: Servlet学习笔记之四(Servlet过滤器)
categories:
- 编程
tags:
- Java
---

## Servlet过滤器

Servlet过滤器可以动态拦截请求和响应，以变换或使用包含在请求或响应中的信息。可以将一个或多个Servlet过滤器附加到一个或一组Servlet。Servlet过滤器也可以附加到jsp和html文件。调用servlet前调用所有附加的Servlet过滤器。

Servlet 过滤器是可用于 Servlet 编程的 Java 类，可以实现以下目的：

- 在客户端的请求访问后端资源之前，拦截这些请求。
- 在服务器的响应发送回客户端之前，处理这些响应。

根据规范建议的各种类型的过滤器：

- 身份验证过滤器（Authentication Filters）。
- 数据压缩过滤器（Data compression Filters）。
- 加密过滤器（Encryption Filters）。
- 触发资源访问事件过滤器。
- 图像转换过滤器（Image Conversion Filters）。
- 日志记录和审核过滤器（Logging and Auditing Filters）。
- MIME-TYPE 链过滤器（MIME-TYPE Chain Filters）。
- 标记化过滤器（Tokenizing Filters）。
- XSL/T 过滤器（XSL/T Filters），转换 XML 内容。

过滤器通过 Web 部署描述符（web.xml）中的 XML 标签来声明，然后映射到您的应用程序的部署描述符中的 Servlet 名称或 URL 模式。

## Servlet过滤器方法

过滤器是一个实现了java.servlet.Filter接口的Java类。该接口定义了三个方法：

- public void doFilter(ServletRequest, ServletResponse, FilterChain),该方法完成实际的过滤操作，当客户端请求方法与过滤器设置匹配的URL时，Servlet容器将先调用过滤器的doFilter方法。FilterChain用户访问后续过滤器。
- public void init(FilterConfig filterConfig),web 应用程序启动时，web 服务器将创建Filter 的实例对象，并调用其init方法，读取web.xml配置，完成对象的初始化功能，从而为后续的用户请求作好拦截的准备工作（filter对象只会创建一次，init方法也只会执行一次）。开发人员通过init方法的参数，可获得代表当前filter配置信息的FilterConfig对象。
- public void destroy(),Servlet容器在销毁过滤器实例前调用该方法，在该方法中释放Servlet过滤器占用的资源。

### FilterConfig使用

Filter的init方法中提供了一个FilterConfig对象，在web.xml中的配置如下：

```
<filter>
	<filter-name>LoginFilter</filter-name>
	<filter-class>com.runoob.test.LogFilter</filter-class>
	<init-param>
		<param-name>Site</param-name>
		<param-value>菜鸟教程</param-value>
	</init-param>
</filter>
```

在 init 方法使用 FilterConfig 对象获取参数：

{% highlight Java %}
public void  init(FilterConfig config) throws ServletException {
	// 获取初始化参数
	String site = config.getInitParameter("Site");
	// 输出初始化参数
	System.out.println("网站名称: " + site);
}
{% endhighlight %}

## Servlet 过滤器实例

我们先定义一个Servlet：

{% highlight Java %}
package com.vosamo.serv;

import java.util.Enumeration;
import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Servlet implementation class DisplayHeader
 */
public class DisplayHeader extends HttpServlet {
	private static final long serialVersionUID = 1L;

    /**
     * @see HttpServlet#HttpServlet()
     */
    public DisplayHeader() {
        super();
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
	    response.setContentType("text/html;charset=UTF-8");
		PrintWriter out = response.getWriter();
		String title = "HTTP Header 请求实例 - 菜鸟教程实例";
        String docType =
            "<!DOCTYPE html> \n";
            out.println(docType +
            "<html>\n" +
            "<head><meta charset=\"utf-8\"><title>" + title + "</title></head>\n"+
            "<body bgcolor=\"#f0f0f0\">\n" +
            "<h1 align=\"center\">" + title + "</h1>\n" +
            "<table width=\"100%\" border=\"1\" align=\"center\">\n" +
            "<tr bgcolor=\"#949494\">\n" +
            "<th>Header 名称</th><th>Header 值</th>\n"+
            "</tr>\n");

        Enumeration headerNames = request.getHeaderNames();

        while(headerNames.hasMoreElements()) {
            String paramName = (String)headerNames.nextElement();
            out.print("<tr><td>" + paramName + "</td>\n");
            String paramValue = request.getHeader(paramName);
            out.println("<td> " + paramValue + "</td></tr>\n");
        }
        out.println("</table>\n</body></html>");
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		doGet(request, response);
	}

}
{% endhighlight %}

然后定义一个绑定到该Servlet的Filter：

{% highlight Java %}
package com.vosamo.serv;

import java.io.IOException;
import javax.servlet.Filter;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;

/**
 * Servlet Filter implementation class DisplayHeaderFilter
 */
public class DisplayHeaderFilter implements Filter {

    /**
     * Default constructor.
     */
    public DisplayHeaderFilter() {
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see Filter#destroy()
	 */
	public void destroy() {
		// TODO Auto-generated method stub
	}

	/**
	 * 该方法完成实际的过滤操作，当客户端请求与过滤设置匹配的URL时，Servlet容器将先调用
	 * 过滤器的doFilter方法；FilterChain用于访问后续过滤器
	 * @see Filter#doFilter(ServletRequest, ServletResponse, FilterChain)
	 */
	public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
		// TODO Auto-generated method stub
		// place your code here
	    System.out.println("网站地址： http://www.runoob.com");
		// pass the request along the filter chain
		chain.doFilter(request, response);
	}

	/**
	 * init方法只在web应用启动时执行一次,使用FilterConfig对象获取参数
	 * @see Filter#init(FilterConfig)
	 */
	public void init(FilterConfig fConfig) throws ServletException {
		// TODO Auto-generated method stub
	    String site = fConfig.getInitParameter("Site");
	    System.out.println("网站名称： " + site);
	}

}
{% endhighlight %}

## 过滤器映射

```
<filter>
  <display-name>DisplayHeaderFilter</display-name>
  <filter-name>DisplayHeaderFilter</filter-name>
  <filter-class>com.vosamo.serv.DisplayHeaderFilter</filter-class>
  <init-param>
    <param-name>Site</param-name>
    <param-value>菜鸟教程</param-value>
  </init-param>
</filter>
<filter-mapping>
  <filter-name>DisplayHeaderFilter</filter-name>
  <servlet-name>DisplayHeader</servlet-name>
  <url-pattern>/servletTest/DisplayHeader</url-pattern>
</filter-mapping>
```

这样访问`/servletTest/DisplayHeader`时，会被过滤器DisplayHeaderFilter拦截，然后执行doFilter方法，最后返回响应。

Web 应用程序可以根据特定的目的定义若干个不同的过滤器。假设您定义了两个过滤器 AuthenFilter 和 LogFilter。web.xml 中的 filter-mapping 元素的顺序决定了 Web 容器应用过滤器到 Servlet 的顺序。
