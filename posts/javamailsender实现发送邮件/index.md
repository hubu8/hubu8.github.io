# javaMailSender实现发送邮件


<!--more-->

## 1. 环境准备

### 1.1 邮件服务

SpringBoot邮件服务的搭建流程为：

1. 引入邮件服务依赖
2. 配置邮件发送邮箱服务器、账户、密码等信息
3. 使用SpringBoot内置javaMailSender工具实现邮件发送方法

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-mail</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

```
spring.mail.host=smtp.163.com（host）
spring.mail.username=邮箱地址
spring.mail.password=邮箱密码
spring.mail.port=25
spring.mail.default-encoding=UTF-8
spring.mail.properties.mail.smtp.socketFactoryClass=javax.net.ssl.SSLSocketFactory
spring.mail.properties.mail.smtp.debug=true
```

### 1.2 Thymeleaf环境

Thymeleaf的使用流程可以简单概括为：

1. 引入Thymeleaf依赖信息
2. 配置文件中设置Thymeleaf基础属性
3. resource/templates下定义Thymeleaf使用的html文件
4. 定义接口服务指向Thymeleaf模板文件，并将属性数据传参
5. Thymeleaf文件中使用相关语法展示后端传参内容

## 2. 模板邮件实现

### 2.1 Thymeleaf

定义邮件模板首先使用Thymeleaf定义一个静态的html文件，并利用Thymeleaf与服务接口变量的绑定特性，动态的改变邮件模板中的参数内容。 如定义一个html邮件模板文件：

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<p>【东源学院】尊敬的东源学院用户：</p>
<p>我们收到了一项请求，要求通过您的电子邮件<span th:text="${email}">您的账户，您的【东源学院】验证码为：</span></p>
<h2 style="text-align: center;" th:text="${code}"></h2>
<p>如果您并未请求此验证码，则可能是他人正在尝试访问以下 【东源学院】 帐号：<span th:text="${email}"。>请勿将此验证码转发给或提供给任何人。</span></p>
<p>验证码有效时间为5分钟</p>
<div style="color: #ff1a0e">一起努力创造辉煌</div>
<hr>
<p>此致</p>
<p>东源科技团队敬上</p>
</body>
</html>
```

- 其中`th:text="${email}"`是利用了Thymeleaf模板引擎的特性，获取服务返回的参数信息，并展示到html页面中。

### 2.1 TemplateEngine

为了使用定义的邮件模板，并将整个html文件内容作为html的格式邮件发送，这时就需要用到` org.thymeleaf.TemplateEngine`包中的TemplateEngine类。 可以按照如下方式获取模板文件中的html内容，并填充内容：

```java
@Autowired
private JavaMailSender javaMailSender;
@Autowired
private TemplateEngine templateEngine;

@Override
public boolean sendEmail(String email, String templateName, Map<String, Object> param) {
    MimeMessage mimeMessage = javaMailSender.createMimeMessage();
    MimeMessageHelper helper = null;
    try {
        helper = new MimeMessageHelper(mimeMessage, true);
        helper.setSubject("欢迎使用东源学院");
        helper.setFrom("posttimer@163.com");
        helper.setTo(email);
        //        helper.setCc("37xxxxx37@qq.com");
        //        helper.setBcc("14xxxxx098@qq.com");
        helper.setSentDate(new Date());
        // 这里引入的是Template的Context
        Context context = new Context();
        // 设置模板中的变量
        context.setVariable("email", email);
        context.setVariable("code",param.get("code"));
        // 第一个参数为模板的名称
        String process = templateEngine.process(templateName, context);
        // 第二个参数true表示这是一个html文本
        helper.setText(process,true);
        javaMailSender.send(mimeMessage);
        return true;
    } catch (MessagingException e) {
        e.printStackTrace();
        return false;
    }

}


//具体代码见：https://gitee.com/hubu8/edu_dongyuan/blob/master/service/service_msm/src/main/java/buzz/hubu/msmservice/service/impl/MsmServiceImpl.java
//此仓库为私有仓库，代码仅供参考
```



## 3.模板升级

thymeleaf模板进数据库

```java

@Override
public boolean sendEmailByTemplate(String funcNo, String email, Map<String, Object> param) {
    QueryWrapper<EmailTemplate> emailTemplateQueryWrapper = new QueryWrapper<>();
    emailTemplateQueryWrapper.eq("func_no", funcNo);
    EmailTemplate emailTemplate = emailTemplateMapper.selectOne(emailTemplateQueryWrapper);
    if (emailTemplate != null) {
        MimeMessage mimeMessage = javaMailSender.createMimeMessage();
        MimeMessageHelper helper = null;
        helper = new MimeMessageHelper(mimeMessage, null);

        try {
            helper.setSubject((String) param.get("subject"));
            helper.setFrom("posttimer@163.com");
            helper.setTo(email);
            helper.setSentDate(new Date());


            String template = emailTemplate.getEmailTemplate();
            //parameterList:'[{\"name\": \"邮件\", \"fields\": \"email\", \"initValue\": \"2650523664@qq.com\"}, {\"name\": \"验证码\", \"fields\": \"code\", \"initValue\": \"152368\"}]'
            //解析参数
            List<FieldsDTO> fieldsDTOS = JSON.parseArray(emailTemplate.getParameterList(), FieldsDTO.class);

            Map<String, Object> resultMap = new HashMap<>();

            fieldsDTOS.forEach(i -> {
                String fields = i.getFields();
                Object initValue = i.getInitValue();
                if (param.containsKey(fields)) {
                    initValue = param.get(fields);
                }
                resultMap.put(fields, initValue);
            });
            Context context = new Context();
            context.setVariables(resultMap);
            TemplateEngine engine = new TemplateEngine();
            String process = engine.process(template, context);
            helper.setText(process, true);
            javaMailSender.send(mimeMessage);
            return true;
        } catch (Exception e) {
            return false;
        }

    }
    return false;
}

// https://gitee.com/-/ide/project/hubu8/edu_dongyuan/edit/dev-v1.0.0/-/service/service_msm/src/main/java/buzz/hubu/msmservice/service/impl/MsmServiceImpl.java
```

## 4.其他实现思路

### Velocity模板引擎

### freemarker 模板引擎

