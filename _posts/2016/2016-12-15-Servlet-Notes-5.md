---
layout: post
title: Servlet学习笔记之五(Servlet Cookie和Session处理)
categories:
- 编程
tags:
- Java
---

# Cookie

Cookie是存储在客户端计算机上的文本文件，并保留了各种跟踪信息。Cookie通常设置在HTTP 头信息中。识别返回用户包括三个步骤：

- 服务器脚本向浏览器发送一组 Cookie。例如：姓名、年龄或识别号码等。
- 浏览器将这些信息存储在本地计算机上，以备将来使用。
- 当下一次浏览器向 Web 服务器发送任何请求时，浏览器会把这些 Cookie 信息发送到服务器，服务器将使用这些信息来识别用户。

Servlet 就能够通过请求方法 request.getCookies() 访问 Cookie，该方法将返回一个 Cookie 对象的数组。

## Servlet Cookie方法

Servlet有许多操作Cookie的方法，比较重要的有以下几个方法：

- public void setMaxAge(int expiry)：设置cookie的过期时间，以秒为单位。不设置的话，cookie默认只在当前会话有效。
- public int getMaxAge()：返回cookie的生存时间，默认情况下，-1表示cookie将继续下去，直到浏览器关闭。
- public String getName()：返回cookie的名字，名字在cookie创建后不能改变。
- public void setValue(String newValue)：设置与cookie关联的值。
- public String getValue()：获取与cookie关联的值。

## 通过Servlet设置Cookie

分三步：

1. 创建一个Cookie对象，使用带有Cookie名称和值的构造函数，名称和值都是字符串。

    Cookie cookie = new Cookie("key","value");
2. 设置最大生存时间，下面将设置一个有效期为24小时的cookie：

    cookie.setMaxAge(60\*60\*24);

3. 发送cookie到HTTP响应头：response.addCookie(cookie);

## 通过Servlet删除Cookie

删除一个cookie很简单：读取欲删除的cookie；使用setMaxAge()设置cookie的生存期为0；把这个cookie添加到响应头。

# Session

HTTP 是一种"无状态"协议，这意味着每次客户端检索网页时，客户端打开一个单独的连接到 Web 服务器，服务器会自动不保留之前客户端请求的任何记录。

## HttpSession 对象

Servlet 提供了 HttpSession 接口，该接口提供了一种跨多个页面请求或访问网站时识别用户以及存储有关用户信息的方式。Servlet 容器使用这个接口来创建一个 HTTP 客户端和 HTTP 服务器之间的 session 会话。会话持续一个指定的时间段，跨多个连接或页面请求。通过调用 HttpServletRequest 的公共方法 getSession() 来获取 HttpSession 对象。

`HttpSession session = request.getSession();`

需要在向客户端发送任何文档内容之前调用 request.getSession()。

## Session跟踪实例

{% highlight Java %}
import java.io.IOException;
import java.io.PrintWriter;
import java.text.SimpleDateFormat;
import java.util.Date;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

/**
 * Servlet implementation class SessionTrack
 */
@WebServlet("/SessionTrack")
public class SessionTrack extends HttpServlet {
	private static final long serialVersionUID = 1L;

	public void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException
	{
		// 如果不存在 session 会话，则创建一个 session 对象
		HttpSession session = request.getSession(true);
		// 获取 session 创建时间
		Date createTime = new Date(session.getCreationTime());
		// 获取该网页的最后一次访问时间
		Date lastAccessTime = new Date(session.getLastAccessedTime());

	    //设置日期输出的格式  
	    SimpleDateFormat df=new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");  

		String title = "Servlet Session 实例 - 菜鸟教程";
		Integer visitCount = new Integer(0);
		String visitCountKey = new String("visitCount");
		String userIDKey = new String("userID");
		String userID = new String("Runoob");

		// 检查网页上是否有新的访问者
		if (session.isNew()){
			title = "Servlet Session 实例 - 菜鸟教程";
		 	session.setAttribute(userIDKey, userID);
		} else {
		 	visitCount = (Integer)session.getAttribute(visitCountKey);
		 	visitCount = visitCount + 1;
		 	userID = (String)session.getAttribute(userIDKey);
		}
		session.setAttribute(visitCountKey,  visitCount);

		// 设置响应内容类型
		response.setContentType("text/html;charset=UTF-8");
		PrintWriter out = response.getWriter();

		String docType = "<!DOCTYPE html>\n";
		out.println(docType +
		        "<html>\n" +
		        "<head><title>" + title + "</title></head>\n" +
		        "<body bgcolor=\"#f0f0f0\">\n" +
		        "<h1 align=\"center\">" + title + "</h1>\n" +
		         "<h2 align=\"center\">Session 信息</h2>\n" +
		        "<table border=\"1\" align=\"center\">\n" +
		        "<tr bgcolor=\"#949494\">\n" +
		        "  <th>Session 信息</th><th>值</th></tr>\n" +
		        "<tr>\n" +
		        "  <td>id</td>\n" +
		        "  <td>" + session.getId() + "</td></tr>\n" +
		        "<tr>\n" +
		        "  <td>创建时间</td>\n" +
		        "  <td>" +  df.format(createTime) +
		        "  </td></tr>\n" +
		        "<tr>\n" +
		        "  <td>最后访问时间</td>\n" +
		        "  <td>" + df.format(lastAccessTime) +
		        "  </td></tr>\n" +
		        "<tr>\n" +
		        "  <td>用户 ID</td>\n" +
		        "  <td>" + userID +
		        "  </td></tr>\n" +
		        "<tr>\n" +
		        "  <td>访问统计：</td>\n" +
		        "  <td>" + visitCount + "</td></tr>\n" +
		        "</table>\n" +
		        "</body></html>");
	}
}
{% endhighlight %}
