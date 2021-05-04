---
title: SpringBoot项目发送邮件
date: 2018-01-01 20:00:48
categories: 
- SpringBoot
tags:
- SpringBoot
---

使用SpringBoot的spring-boot-starter-mail发哦送邮件示例

<!-- more -->

## 导入依赖
``` vbscript-html
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-mail</artifactId>
		</dependency>
```

## 配置
``` less
spring:
  mail:
    # 邮件服务地址
    host: smtp.163.com
    # 端口,可不写默认25  发送qq邮件使用465
    port: 465 
    # 编码格式
    default-encoding: utf-8
    # 用户名
    username: xxx@163.com
    # 授权码，就是我们刚才准备工作获取的代码
    password: xxx
    # 其它参数
    properties:
      mail:
        smtp:
          # 如果是用 SSL 方式，需要配置如下属性,使用qq邮箱的话需要开启
          ssl:
            enable: true
            required: true
          # 邮件接收时间的限制，单位毫秒
          timeout: 10000
          # 连接时间的限制，单位毫秒
          connectiontimeout: 10000
          # 邮件发送时间的限制，单位毫秒
          writetimeout: 10000
```

## 编写邮件发送服务接口
1. 接口
``` stylus

public interface MailService {

    public void sendSimpleMail(String to, String subject, String content);

    public void sendHtmlMail(String to, String subject, String content);

    public void sendAttachmentsMail(String to, String subject, String content, String filePath);

    public void sendInlineResourceMail(String to, String subject, String content, String rscPath, String rscId);
    
    public void sendMimeMessge(String to, String subject, String content, Map<String, String> rscIdMap) ;

}
```
2. 实现类
``` stylus
package com.my.service.impl;

import java.io.File;
import java.util.Map;

import javax.mail.MessagingException;
import javax.mail.internet.MimeMessage;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.core.io.FileSystemResource;
import org.springframework.mail.SimpleMailMessage;
import org.springframework.mail.javamail.JavaMailSender;
import org.springframework.mail.javamail.MimeMessageHelper;
import org.springframework.stereotype.Component;

import com.my.service.MailService;

@Component
public class MailServiceImpl implements MailService {
	private static final Logger logger = LoggerFactory.getLogger(MailServiceImpl.class);

	@Autowired
	private JavaMailSender mailSender;
	
	 private static final String SENDER = "xxx@163.com";

	@Override
	public void sendSimpleMail(String to, String subject, String content) {
		SimpleMailMessage message = new SimpleMailMessage();
		message.setFrom(SENDER);
		message.setTo(to);
		message.setSubject(subject);
		message.setText(content);
		try {
			mailSender.send(message);
		} catch (Exception e) {
			logger.error("发送简单邮件时发生异常!", e);
		}

	}

	@Override
	public void sendHtmlMail(String to, String subject, String content) {
		MimeMessage message = mailSender.createMimeMessage();
        try {
            //true表示需要创建一个multipart message
            MimeMessageHelper helper = new MimeMessageHelper(message, true);
            helper.setFrom(SENDER);
            helper.setTo(to);
            helper.setSubject(subject);
            helper.setText(content, true);
            mailSender.send(message);
        } catch (MessagingException e) {
            logger.error("发送MimeMessge时发生异常！", e);
        }

	}

	@Override
	public void sendAttachmentsMail(String to, String subject, String content, String filePath) {
		 MimeMessage message = mailSender.createMimeMessage();
	        try {
	            //true表示需要创建一个multipart message
	            MimeMessageHelper helper = new MimeMessageHelper(message, true);
	            helper.setFrom(SENDER);
	            helper.setTo(to);
	            helper.setSubject(subject);
	            helper.setText(content, true);

	            FileSystemResource file = new FileSystemResource(new File(filePath));
	            String fileName = file.getFilename();
	            helper.addAttachment(fileName, file);

	            mailSender.send(message);
	        } catch (MessagingException e) {
	            logger.error("发送带附件的MimeMessge时发生异常！", e);
	        }

	}

	 @Override
	    public void sendInlineResourceMail(String to, String subject, String content, String rscPath, String rscId){
	        MimeMessage message = mailSender.createMimeMessage();

	        try {
	            MimeMessageHelper helper = new MimeMessageHelper(message, true);
	            helper.setFrom(SENDER);
	            helper.setTo(to);
	            helper.setSubject(subject);
	            helper.setText(content, true);

	            FileSystemResource res = new FileSystemResource(new File(rscPath));
	            helper.addInline(rscId, res);

	            mailSender.send(message);
	            logger.info("嵌入静态资源的邮件已经发送。");
	        } catch (MessagingException e) {
	            logger.error("发送嵌入静态资源的邮件时发生异常！", e);
	        }
	    }
	
	 @Override
	    public void sendMimeMessge(String to, String subject, String content, Map<String, String> rscIdMap) {
	        MimeMessage message = mailSender.createMimeMessage();
	        try {
	            //true表示需要创建一个multipart message
	            MimeMessageHelper helper = new MimeMessageHelper(message, true);
	            helper.setFrom(SENDER);
	            helper.setTo(to);
	            helper.setSubject(subject);
	            helper.setText(content, true);

	            for (Map.Entry<String, String> entry : rscIdMap.entrySet()) {
	                FileSystemResource file = new FileSystemResource(new File(entry.getValue()));
	                helper.addInline(entry.getKey(), file);
	            }
	            mailSender.send(message);
	        } catch (MessagingException e) {
	            logger.error("发送带静态文件的MimeMessge时发生异常！", e);
	        }
	    }

}
```

## 单元测试
``` stylus
@SpringBootTest
class MailSpringBootApplicationTests {

	@Autowired
    private MailService mailService;

    private static final String TO = "xxx@qq.com";
    private static final String SUBJECT = "测试邮件";
    private static final String CONTENT = "test content";
	
	@Test
	void contextLoads() {
	}
	
	 /**
     * 测试发送普通邮件
     */
    @Test
    public void sendSimpleMailMessage() {
        mailService.sendSimpleMail(TO, SUBJECT, CONTENT);
    }

    /**
     * 测试发送html邮件
     */
    @Test
    public void sendHtmlMessage() {
        String htmlStr = "<h1>Test</h1>";
        mailService.sendHtmlMail(TO, SUBJECT, htmlStr);
    }

    /**
     * 测试发送带附件的邮件
     * @throws FileNotFoundException
     */
    @Test
    public void sendAttachmentMessage() throws FileNotFoundException {
        File file = ResourceUtils.getFile("classpath:test.txt");
        String filePath = file.getAbsolutePath();
        mailService.sendAttachmentsMail(TO, SUBJECT, CONTENT, filePath);
    }

    /**
     * 测试发送带附件的邮件
     * @throws FileNotFoundException
     */
    @Test
    public void sendPicMessage() throws FileNotFoundException {
        String htmlStr = "<html><body>测试：图片1 <br> <img src=\'cid:pic1\'/> <br>图片2 <br> <img src=\'cid:pic2\'/></body></html>";
        Map<String, String> rscIdMap = new HashMap<>(2);
        rscIdMap.put("pic1", ResourceUtils.getFile("classpath:img/pic01.jpg").getAbsolutePath());
        rscIdMap.put("pic2", ResourceUtils.getFile("classpath:img/pic02.jpg").getAbsolutePath());
        mailService.sendMimeMessge(TO, SUBJECT, htmlStr, rscIdMap);
    }

}
```


## 报错
1. Could not connect to SMTP host: smtp.163.com, port: 25
``` undefined
解决方案
1. 解封25号端口（不推荐）；
1. 使用其他端口（465号端口，推荐）。
```




