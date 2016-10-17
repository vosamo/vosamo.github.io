---
layout: post
title: 在Java中验证邮箱用户名、密码是否正确
categories:
- 编程
tags:
- java
---

开发中有个需求是配置邮箱，在配置过程中需要对用户填写的邮箱地址、用户名、密码等信息进行验证，该怎么做呢？

经过搜索和查看JavaMail API文档，发现Session类和Transport类很可疑，应该是用于和邮件服务器连接获取会话的。Session类的getInstance方法用来获取一个会话对象；调用getTransport方法获取一个Transport对象，该对象有一个connect方法用来连接邮箱服务器。

{% highlight java %}
package com.inspur.utils;

import java.util.HashMap;
import java.util.Map;
import java.util.Properties;

import javax.mail.MessagingException;
import javax.mail.NoSuchProviderException;
import javax.mail.Session;
import javax.mail.Transport;

public class CheckSmtp {
    private String host;
    private String username;
    private String password;
    private Integer port;
    private Boolean use_ssl;
    private Boolean use_tls;
    public CheckSmtp(String host, String username, String password, Integer port, Boolean use_ssl, Boolean use_tls) {
        super();
        this.host = host;
        this.username = username;
        this.password = password;
        this.port = port;
        this.use_ssl = use_ssl;
        this.use_tls = use_tls;
    }
    public Map<String, String> check() {
        Map<String, String> res = new HashMap<String, String>();
        Properties props = new Properties();
        props.put("mail.smtp.host", host);
        props.put("mail.smtp.port", port);
        props.put("mail.smtp.auth", "true");

        Session session = Session.getInstance(props);
//        Transport transport = null;
        try {
            Transport transport = session.getTransport("smtp");
            transport.connect(host, username, password);
            transport.close();
            System.out.println("Connected to smtp host!");
        } catch (NoSuchProviderException e) {
            // TODO Auto-generated catch block
            System.out.println(e.getMessage());
            res.put("status", "error");
            res.put("message", e.getMessage());
            return res;
        } catch (MessagingException e) {
            // TODO Auto-generated catch block
            System.out.println(e.getMessage());
            res.put("status", "error");
            res.put("message", e.getMessage());
            return res;
        }
        res.put("status", "success");
        res.put("message", "sender mail config success");
        return res;
    }
}
{% endhighlight %}

## 参考资料：

- [JavaMail Session文档](https://javamail.java.net/nonav/docs/api/javax/mail/Session.html)
- [JavaMail SMTP服务器](http://www.yiibai.com/javamail_api/javamail_api_smtp_servers.html)
- [java实现基于SMTP发送邮件的方法](http://www.jb51.net/article/69597.htm)

