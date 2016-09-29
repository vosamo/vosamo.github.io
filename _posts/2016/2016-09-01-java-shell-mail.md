---
layout: post
title: 在Java中调用linux sendmail命令发邮件
categories:
- 编程
tags:
- java
---

项目中有一个需求是监控系统性能并发送邮件，考虑到配置代理邮箱发送邮件太重量级，并且繁琐复杂，频繁发送邮件还有IP被禁之虞。所以选择sendmail来发送邮件，简单粗暴。

## sendmail的使用方法

linux下安装sendmail和sendmail.service，启动sendmail.service。貌似使用mail命令发送邮件更多，使用sendmail命令发送html格式邮件的方法如下：

```
echo '<html><body><h4>当前系统中产生一条告警信息，请您知晓</h4></body></html>'|formail -I "From: service" -I "To:$mailList" -I "MIME-Version:1.0" -I "Content-type:text/html;charset=utf-8" -I "Subject:monitor" |sendmail -toi
```
$mailList是收件人地址，如果由多个收件人，用","分隔。有时使用这个命令会被当作垃圾邮件，这时使用`sendmail -toi $mailList`会解决。其他诸如乱码问题可通过设置编码来解决，参考[**sendmail+formail乱码**](http://blog.sina.com.cn/s/blog_4097063801018v6r.html)这篇文章。
`formail`命令用来组装邮件头。可以看一下formail之后的邮件格式：

```
From: service
To: test@163.com
MIME-Version:1.0
Content-type:text/html;charset=utf-8
Subject:monitor

<html><body><h4>当前系统中产生一条告警信息，请您知晓</h4></body></html>
```

所以我们可以把邮件按照上述格式写到文件里，然后通过`cat mail.txt|sendmail -toi`发送。

如果你的hostname为默认的localhost的话，你可能会得到一个错误：`dsn=5.0.0, stat=Service unavailable`，这是由于默认使用root@localhost.localdomain发件，绝大多数邮箱都会拒收。

解决办法：修改hostname，修改`/etc/hosts`文件，添加你的hostname;修改`/etc/sysconfig/network`文件，改成`HOSTNAME=yourhostname`，重启sendmail.service服务即可。

## 通过java调用sendmail

通过java调用shell命令的方法是`Runtime.getRuntime().exec(command)`，下面直接上代码：

{% highlight java %}
package com.vosamo;

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.text.MessageFormat;
import java.text.SimpleDateFormat;
import java.util.*;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class OSMailSender {
    private List<String> to;
    private String from;
    private String subject;
    private String content;
    public OSMailSender(List<String> to, String from, String subject, String content) {
        this.to = to;
        this.from = from;
        this.subject = subject;
        this.content = content;
    }
    public String listToString(List<String> toList) {
        if (toList==null) {
            return null;
        }
        StringBuilder result=new StringBuilder();
        boolean flag=false;
        for (String to : toList) {
            if (flag) {
                result.append(",");
            }else {
                flag=true;
            }
            result.append(to);
        }
        return result.toString();
    }
    public void callShell(String shellString) {
        String[] command = {"/bin/sh", "-c", shellString};
        System.out.println(command.toString());
        try {  
            Process process = Runtime.getRuntime().exec(command);
            BufferedReader input = new BufferedReader(new InputStreamReader(process.getInputStream()));
            String line = "";  
            while ((line = input.readLine()) != null) {  
                line += line;    //获取命令执行结果
            }  
            input.close();
            int exitValue = process.waitFor();    //获取命令执行返回状态码
            if (0 != exitValue) {
                System.out.println("call shell failed. error code is :" + exitValue);
                System.out.println("Reason: " + line);
            }  
        } catch (Throwable e) {  
            System.out.println("call shell failed. " + e);  
        }  
    }
    public void send() {
        String mailMessage = 
            "SUBJECT: {0} \n"    //邮件标题
            + "TO: {1} \n"       //收件人
            + "FROM: {2} \n"     //发件人
            + "MIME-VERSION: 1.0 \n"
            + "Content-type: text/html \n"
                + "{3}";    //邮件正文
        String receiver = listToString(to);
        mailMessage = MessageFormat.format(mailMessage, this.subject, receiver, this.from, this.content);
        String mailCommand = "echo '" + mailMessage + "'| sendmail -toi";
        callShell(mailCommand);
    }
    public static void main(String[] args) {
        List<String> toList = new ArrayList<String>();
        toList.add("test1@163.com");
        toList.add("test2007@163.com");
        String from = "buptlsl@163.com";
        String subject = "Unicorn监控平台告警 级别：严重警告";
        String content = "<html><body><h4>当前系统中产生一条告警信息，请您知晓</h4></body></html>";
        OSMailSender sender = new OSMailSender(toList, from, subject, content);
        sender.send();
    }
}

{% endhighlight %}







