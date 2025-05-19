# javax.net.ssl.SSLHandshakeException: No appropriate protocol (protocol is disa(String)----发送邮件报错


<!--more-->

```sh
java.net.MalformedURLException
        at java.net.URL.<init>(URL.java:645)
        at java.net.URL.<init>(URL.java:508)
        at java.net.URL.<init>(URL.java:457)
        at com.ruoyi.common.email.util.EmailSendUtil.send(EmailSendUtil.java:74)
        at jdk.nashorn.internal.scripts.Script$4$\^eval\_.:program(<eval>:56)
        at jdk.nashorn.internal.runtime.ScriptFunctionData.invoke(ScriptFunctionData.java:637)
        at jdk.nashorn.internal.runtime.ScriptFunction.invoke(ScriptFunction.java:494)
        at jdk.nashorn.internal.runtime.ScriptRuntime.apply(ScriptRuntime.java:393)
        at jdk.nashorn.api.scripting.NashornScriptEngine.evalImpl(NashornScriptEngine.java:449)
        at jdk.nashorn.api.scripting.NashornScriptEngine.evalImpl(NashornScriptEngine.java:406)
        at jdk.nashorn.api.scripting.NashornScriptEngine.evalImpl(NashornScriptEngine.java:402)
        at jdk.nashorn.api.scripting.NashornScriptEngine.eval(NashornScriptEngine.java:155)
        at javax.script.AbstractScriptEngine.eval(AbstractScriptEngine.java:264)
        at com.ruoyi.quartz.util.NashornScriptInvokeUtil.invokeAllScript(NashornScriptInvokeUtil.java:17)
        at com.ruoyi.quartz.util.ScriptInvokeUtil.invokeMethod(ScriptInvokeUtil.java:28)
        at com.ruoyi.quartz.util.JobInvokeUtil.invokeMethod(JobInvokeUtil.java:44)
        at com.ruoyi.quartz.util.QuartzDisallowConcurrentExecution.doExecute(QuartzDisallowConcurrentExecution.java:23)
        at com.ruoyi.quartz.util.AbstractQuartzJob.execute(AbstractQuartzJob.java:52)
        at org.quartz.core.JobRunShell.run(JobRunShell.java:202)
        at org.quartz.simpl.SimpleThreadPool$WorkerThread.run(SimpleThreadPool.java:573)
Caused by: java.lang.NullPointerException
06:24:50.653 [quartzScheduler_Worker-5] ERROR c.r.q.u.AbstractQuartzJob - [execute,58] - 任务执行异常  - ：
cn.hutool.extra.mail.MailException: MessagingException: Could not connect to SMTP host: smtp.163.com, port: 465
        at cn.hutool.extra.mail.Mail.send(Mail.java:398)
        at cn.hutool.extra.mail.MailUtil.send(MailUtil.java:416)
        at cn.hutool.extra.mail.MailUtil.send(MailUtil.java:349)
        at cn.hutool.extra.mail.MailUtil.send(MailUtil.java:329)
        at cn.hutool.extra.mail.MailUtil.send(MailUtil.java:312)
        at com.ruoyi.common.email.util.EmailSendUtil.send(EmailSendUtil.java:84)
        at jdk.nashorn.internal.scripts.Script$4$\^eval\_.:program(<eval>:56)
        at jdk.nashorn.internal.runtime.ScriptFunctionData.invoke(ScriptFunctionData.java:637)
        at jdk.nashorn.internal.runtime.ScriptFunction.invoke(ScriptFunction.java:494)
        at jdk.nashorn.internal.runtime.ScriptRuntime.apply(ScriptRuntime.java:393)
        at jdk.nashorn.api.scripting.NashornScriptEngine.evalImpl(NashornScriptEngine.java:449)
        at jdk.nashorn.api.scripting.NashornScriptEngine.evalImpl(NashornScriptEngine.java:406)
        at jdk.nashorn.api.scripting.NashornScriptEngine.evalImpl(NashornScriptEngine.java:402)
        at jdk.nashorn.api.scripting.NashornScriptEngine.eval(NashornScriptEngine.java:155)
        at javax.script.AbstractScriptEngine.eval(AbstractScriptEngine.java:264)
        at com.ruoyi.quartz.util.NashornScriptInvokeUtil.invokeAllScript(NashornScriptInvokeUtil.java:17)
        at com.ruoyi.quartz.util.ScriptInvokeUtil.invokeMethod(ScriptInvokeUtil.java:28)
        at com.ruoyi.quartz.util.JobInvokeUtil.invokeMethod(JobInvokeUtil.java:44)
        at com.ruoyi.quartz.util.QuartzDisallowConcurrentExecution.doExecute(QuartzDisallowConcurrentExecution.java:23)
        at com.ruoyi.quartz.util.AbstractQuartzJob.execute(AbstractQuartzJob.java:52)
        at org.quartz.core.JobRunShell.run(JobRunShell.java:202)
        at org.quartz.simpl.SimpleThreadPool$WorkerThread.run(SimpleThreadPool.java:573)
Caused by: javax.mail.MessagingException: Could not connect to SMTP host: smtp.163.com, port: 465
        at com.sun.mail.smtp.SMTPTransport.openServer(SMTPTransport.java:1961)
        at com.sun.mail.smtp.SMTPTransport.protocolConnect(SMTPTransport.java:654)
        at javax.mail.Service.connect(Service.java:317)
        at javax.mail.Service.connect(Service.java:176)
        at javax.mail.Service.connect(Service.java:125)
        at javax.mail.Transport.send0(Transport.java:194)
        at javax.mail.Transport.send(Transport.java:124)
        at cn.hutool.extra.mail.Mail.doSend(Mail.java:412)
        at cn.hutool.extra.mail.Mail.send(Mail.java:390)
        ... 21 common frames omitted
Caused by: javax.net.ssl.SSLHandshakeException: No appropriate protocol (protocol is disabled or cipher suites are inappropriate)
        at sun.security.ssl.HandshakeContext.<init>(HandshakeContext.java:171)
        at sun.security.ssl.ClientHandshakeContext.<init>(ClientHandshakeContext.java:103)
        at sun.security.ssl.TransportContext.kickstart(TransportContext.java:220)
        at sun.security.ssl.SSLSocketImpl.startHandshake(SSLSocketImpl.java:428)
        at com.sun.mail.util.SocketFetcher.configureSSLSocket(SocketFetcher.java:549)
        at com.sun.mail.util.SocketFetcher.createSocket(SocketFetcher.java:354)
        at com.sun.mail.util.SocketFetcher.getSocket(SocketFetcher.java:211)
        at com.sun.mail.smtp.SMTPTransport.openServer(SMTPTransport.java:1927)
        ... 29 common frames omitted
06:24:50.655 [quartzScheduler_Worker-5] DEBUG c.r.q.m.S.insertJobLog - [debug,137] - ==>  Preparing: insert into sys_job_log( job_name, job_group, invoke_target, job_message, status, exception_info, create_time )values( ?, ?, ?, ?, ?, ?, sysdate() )
06:24:50.655 [quartzScheduler_Worker-5] DEBUG c.r.q.m.S.insertJobLog - [debug,137] - ==> Parameters: 定时任务_获取乔安云监控数据(String), DEFAULT(String), ryTask.ryNoParams(String), 定时任务_获取乔安云监控数据 总共耗时：10547毫秒(String), 1(String), cn.hutool.extra.mail.MailException: MessagingException: Could not connect to SMTP host: smtp.163.com, port: 465
        at cn.hutool.extra.mail.Mail.send(Mail.java:398)
        at cn.hutool.extra.mail.MailUtil.send(MailUtil.java:416)
        at cn.hutool.extra.mail.MailUtil.send(MailUtil.java:349)
        at cn.hutool.extra.mail.MailUtil.send(MailUtil.java:329)
        at cn.hutool.extra.mail.MailUtil.send(MailUtil.java:312)
        at com.ruoyi.common.email.util.EmailSendUtil.send(EmailSendUtil.java:84)
        at jdk.nashorn.internal.scripts.Script$4$\^eval\_.:program(<eval>:56)
        at jdk.nashorn.internal.runtime.ScriptFunctionData.invoke(ScriptFunctionData.java:637)
        at jdk.nashorn.internal.runtime.ScriptFunction.invoke(ScriptFunction.java:494)
        at jdk.nashorn.internal.runtime.ScriptRuntime.apply(ScriptRuntime.java:393)
        at jdk.nashorn.api.scripting.NashornScriptEngine.evalImpl(NashornScriptEngine.java:449)
        at jdk.nashorn.api.scripting.NashornScriptEngine.evalImpl(NashornScriptEngine.java:406)
        at jdk.nashorn.api.scripting.NashornScriptEngine.evalImpl(NashornScriptEngine.java:402)
        at jdk.nashorn.api.scripting.NashornScriptEngine.eval(NashornScriptEngine.java:155)
        at javax.script.AbstractScriptEngine.eval(AbstractScriptEngine.java:264)
        at com.ruoyi.quartz.util.NashornScriptInvokeUtil.invokeAllScript(NashornScriptInvokeUtil.java:17)
        at com.ruoyi.quartz.util.ScriptInvokeUtil.invokeMethod(ScriptInvokeUtil.java:28)
        at com.ruoyi.quartz.util.JobInvokeUtil.invokeMethod(JobInvokeUtil.java:44)
        at com.ruoyi.quartz.util.QuartzDisallowConcurrentExecution.doExecute(QuartzDisallowConcurrentExecution.java:23)
        at com.ruoyi.quartz.util.AbstractQuartzJob.execute(AbstractQuartzJob.java:52)
        at org.quartz.core.JobRunShell.run(JobRunShell.java:202)
        at org.quartz.simpl.SimpleThreadPool$WorkerThread.run(SimpleThreadPool.java:573)
Caused by: javax.mail.MessagingException: Could not connect to SMTP host: smtp.163.com, port: 465;
  nested exception is:
        javax.net.ssl.SSLHandshakeException: No appropriate protocol (protocol is disa(String)
```

### 问题原因

```java
package com.ruoyi.common.email.util;

import cn.hutool.extra.mail.Mail;
import cn.hutool.extra.mail.MailAccount;
import cn.hutool.extra.mail.MailUtil;
import com.ruoyi.common.constant.CacheConstants;
import com.ruoyi.common.core.redis.RedisCache;
import com.ruoyi.common.email.EmailConfig;
import com.ruoyi.common.email.EmailVO;
import com.ruoyi.common.exception.GlobalException;
import com.ruoyi.common.exception.tool.EmailConfigException;
import com.ruoyi.common.exception.tool.EmailSendException;
import com.ruoyi.common.utils.EncryptUtils;
import com.ruoyi.common.utils.StringUtils;
import com.ruoyi.common.utils.spring.SpringUtils;
import com.sun.mail.util.MailSSLSocketFactory;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import javax.activation.DataSource;
import java.io.File;
import java.io.InputStream;
import java.net.URL;
import java.net.URLConnection;
import java.security.GeneralSecurityException;
import java.util.*;

public class EmailSendUtil {
    private static final Logger log = LoggerFactory.getLogger(EmailSendUtil.class);
    private static final String EMAIL_CONFIG_HOST = "email.config.host";
    private static final String EMAIL_CONFIG_PORT = "email.config.port";
    private static final String EMAIL_CONFIG_USER = "email.config.user";
    private static final String EMAIL_CONFIG_PASS = "email.config.pass";
    private static final String EMAIL_CONFIG_FROM_USER = "email.config.fromUser";

    /**
     * 获取邮件配置信息
     * @return
     */
    public static EmailConfig getEmailConfig(){
        EmailConfig emailConfig = new EmailConfig();
        emailConfig.setHost(getCacheValue(EMAIL_CONFIG_HOST));
        emailConfig.setPort(getCacheValue(EMAIL_CONFIG_PORT));
        emailConfig.setUser(getCacheValue(EMAIL_CONFIG_USER));
        emailConfig.setPass(getCacheValue(EMAIL_CONFIG_PASS));
        emailConfig.setFromUser(getCacheValue(EMAIL_CONFIG_FROM_USER));
        return emailConfig;
    }


    /**
     * 获取邮件账户信息
     * @return
     */
    public static MailAccount getMailAccount()
    {
        EmailConfig emailConfig = getEmailConfig();
        // 封装
        MailAccount account = new MailAccount();
        // 设置用户
        String user = emailConfig.getFromUser().split("@")[0];
        account.setHost(emailConfig.getHost());
        account.setPort(Integer.parseInt(emailConfig.getPort()));
        account.setAuth(true);
        account.setPass(emailConfig.getPass());
        //方案一：
        // SSL 握手失败可能与协议版本协商不稳定有关，需在代码中强制指定 TLSv1.2：
        account.setSslProtocols("TLSv1.2");
        // 方案二：
        // 补充 SSL SocketFactory 相关参数，避免因缺少证书信任导致握手失败：添加 SSL 安全套接字工厂配置
//        MailSSLSocketFactory sf = null;
//        try {
//            sf = new MailSSLSocketFactory();
//            sf.setTrustAllHosts(true);  // 信任所有主机（测试环境可临时启用）:ml-citation{ref="4" data="citationList"}
//            account.setCustomProperty("mail.smtp.ssl.socketFactory", sf);
//            account.setCustomProperty("mail.smtp.ssl.enable", "true");
//        } catch (GeneralSecurityException e) {
//            throw new RuntimeException(e);
//        }
        // ssl方式发送
        account.setSslEnable(true);
        // 使用STARTTLS安全连接
        account.setStarttlsEnable(true);
        account.setUser(emailConfig.getUser());
        account.setFrom(emailConfig.getFromUser());
        return account;
    }

    /**
     * 单个收件人
     * 发送邮件 ，不包含照片主体
     * @param to
     * @param subject
     * @param content
     * @param isHtml
     * @param files
     */
    public static void send(String to, String subject, String content, boolean isHtml, File... files){
        MailAccount account = getMailAccount();

        String send = MailUtil.send(account, to, subject, content, isHtml, files);
        log.info(String.format("邮件发送返回结果：%s",send));
    }

    /**
     * 多个收件人
     * @param tos
     * @param subject
     * @param content
     * @param isHtml
     * @param files
     */
    public static void sendTos(Collection<String> tos, String subject, String content, boolean isHtml, File... files){
        MailAccount account = getMailAccount();

        String send = MailUtil.send(account, tos, subject, content, isHtml, files);
        log.info(String.format("邮件发送返回结果：%s",send));
    }

    /**
     * 发送邮件， 可发送图片
     * @param to
     * @param subject
     * @param content
     * @param imageStrMap
     * @param isHtml
     * @param files
     */
    public static void send(String to, String subject, String content, Map<String,String> imageStrMap, boolean isHtml, File... files){
        MailAccount account = getMailAccount();

        Map<String, InputStream> imageMap = new HashMap<>();
        Set<String> imagesKeys = imageStrMap.keySet();
        for (String imagesKey : imagesKeys) {
            String imageurl = imageStrMap.get(imagesKey);
            try {
                if(StringUtils.isNotEmpty(imageurl)){
                    URL url = new URL(imageurl);
                    URLConnection conn = url.openConnection();
                    InputStream inputStream = conn.getInputStream();
                    // 从 inputStream 中读取文件流并进行处理
                    imageMap.put(imagesKey,inputStream);
                }
            } catch (Exception e) {
                log.info(String.format("图片Url获取数据，出错的是：%s:%s",imagesKey,imageurl));
                e.printStackTrace();
            }
        }

        String send = MailUtil.send(account, to, subject, content, imageMap, isHtml, files);
        log.info(String.format("邮件发送返回结果：%s",send));
    }

    /**
     * 发送多人， 可发送图片
     * @param tos
     * @param subject
     * @param content
     * @param imageStrMap
     * @param isHtml
     * @param files
     */
    public static void sendTos(Collection<String> tos, String subject, String content, Map<String,String> imageStrMap, boolean isHtml, File... files){
        MailAccount account = getMailAccount();

        Map<String, InputStream> imageMap = new HashMap<>();
        Set<String> imagesKeys = imageStrMap.keySet();
        for (String imagesKey : imagesKeys) {
            String imageurl = imageStrMap.get(imagesKey);
            try {
                if(StringUtils.isNotEmpty(imageurl)){
                    URL url = new URL(imageurl);
                    URLConnection conn = url.openConnection();
                    InputStream inputStream = conn.getInputStream();
                    // 从 inputStream 中读取文件流并进行处理
                    imageMap.put(imagesKey,inputStream);
                }
            } catch (Exception e) {
                log.info(String.format("图片Url获取数据，出错的是：%s:%s",imagesKey,imageurl));
                e.printStackTrace();
            }
        }

        String send = MailUtil.send(account, tos, subject, content, imageMap, isHtml, files);
        log.info(String.format("邮件发送返回结果：%s",send));
    }

    /**
     * 发送邮件
     * @param emailVo
     * @param emailConfig
     */
    public static void send(EmailVO emailVo, EmailConfig emailConfig) {
        if (emailConfig == null) {
            emailConfig = getEmailConfig();
        }
        if (emailConfig == null) {
            throw new EmailConfigException("Email参数设置为空，请检查参数设置");
        }

        MailAccount account = getMailAccount();
        String content = emailVo.getContent();
        // 发送
        try {
            int size = emailVo.getTos().size();
            Mail mail = Mail.create(account);
            mail.setTos(emailVo.getTos().toArray(new String[size]))
                    .setTitle(emailVo.getSubject())
                    .setContent(content)
                    .setHtml(true);
            List<DataSource> attachments = emailVo.getAttachments();
            if (attachments != null && attachments.size() > 0) {
                mail.setAttachments(attachments.toArray(new DataSource[]{}));
            }

            List<File> files = emailVo.getFiles();
            if (files != null && files.size() > 0) {
                mail.setFiles(files.toArray(new File[]{}));
            }

            mail
                    //关闭session
                    .setUseGlobalSession(false)
                    .send();
        } catch (Exception e) {
            throw new EmailSendException(e.getMessage());

        }
    }

    /**
     * 设置cache key
     *
     * @param configKey 参数键
     * @return 缓存键key
     */
    private static String getCacheKey(String configKey)
    {
        return CacheConstants.SYS_CONFIG_KEY + configKey;
    }

    /**
     * 获取缓存值
     * @param configKey
     * @return
     */
    private static String getCacheValue(String configKey){
        if(!configKey.startsWith(CacheConstants.SYS_CONFIG_KEY)){
            configKey = getCacheKey(configKey);
        }
        String value = SpringUtils.getBean(RedisCache.class).getCacheObject(configKey);
        if(StringUtils.isEmpty(value)){
            throw new EmailConfigException(String.format("未找到email相关参数，请检查%s参数设置并同步到redis缓存。",configKey));
        }
        return value;
    }
}
```



jdk1.8版本导致SSL调用权限上有问题，新电脑装的jdk是jdk1.8.0_291，版本比较高。搜到的解决方法是：

**找到jdk 1.8安装目录，找到C:\Program Files\Java\jre里面的lib\security 下面有个java.security。找到对应的SSLv3，删除掉，重启项目就好了。（删掉SSLv3就是允许SSL调用）**

### 解决问题- 从JDK入手

我找到Java安装目录下D:\Java\jdk1.8.0_291\jre\lib\security中的java.security文件，将对应的SSLv3删掉了，但运行还是出错。最后发现SSLv3后面有两个和它后缀一样的算法，将它们一起删掉后重启项目，成功解决问题。

```sh
# Example:
#   jdk.tls.disabledAlgorithms=MD5, SSLv3, DSA, RSA keySize < 2048
jdk.tls.disabledAlgorithms=SSLv3, TLSv1, TLSv1.1,RC4, DES, MD5withRSA, \
    DH keySize < 1024, EC keySize < 224, 3DES_EDE_CBC, anon, NULL, \
    include jdk.disabled.namedCurves

# Legacy algorithms for Secure Socket Layer/Transport Layer Security (SSL/TLS)
# processing in JSSE implementation.
```

删除后的文件如下图所示。

```sh
# Example:
#   jdk.tls.disabledAlgorithms=MD5, SSLv3, DSA, RSA keySize < 2048
jdk.tls.disabledAlgorithms=RC4, DES, MD5withRSA, \
    DH keySize < 1024, EC keySize < 224, 3DES_EDE_CBC, anon, NULL, \
    include jdk.disabled.namedCurves

# Legacy algorithms for Secure Socket Layer/Transport Layer Security (SSL/TLS)
# processing in JSSE implementation.
```

#### 衍生命令

docker中Java安装目录为: **/usr/local/openjdk-8**

复制容器中的java.security文件到宿主机

```sh
docker cp e61b6e572d86:/usr/local/openjdk-8/lib/security/java.security ./java.security
```

使用宿主机的vi命令修改文件

```sh
vi java.security
```

复制宿主机到容器中的java.security文件

```sh
docker cp ./java.security e61b6e572d86:/usr/local/openjdk-8/lib/security/java.security
```

重启容器

```sh
docker restart e61b6e572d86
```

#### 从容器入手

修改容器的java.security文件, 目前还未验证

```sh
RUN sed -i 's/jdk.tls.disabledAlgorithms=SSLv3, TLSv1, TLSv1.1/jdk.tls.disabledAlgorithms=SSLv3/g' /opt/java/openjdk/lib/security/java.security
# 核心也是修改java.security文件中的jdk.tls.disabledAlgorithms配置，删除掉TLSv1&TLSv1.1
```

### 解决问题-从代码入手

之前是错误实例，修改jdk是不明智的操作

#### 方案一

```java
/方案一：
        // SSL 握手失败可能与协议版本协商不稳定有关，需在代码中强制指定 TLSv1.2：
        account.setSslProtocols("TLSv1.2");
        // 方案二：
        // 补充 SSL SocketFactory 相关参数，避免因缺少证书信任导致握手失败：添加 SSL 安全套接字工厂配置
//        MailSSLSocketFactory sf = null;
//        try {
//            sf = new MailSSLSocketFactory();
//            sf.setTrustAllHosts(true);  // 信任所有主机（测试环境可临时启用）:ml-citation{ref="4" data="citationList"}
//            account.setCustomProperty("mail.smtp.ssl.socketFactory", sf);
//            account.setCustomProperty("mail.smtp.ssl.enable", "true");
//        } catch (GeneralSecurityException e) {
//            throw new RuntimeException(e);
//        }

```

#### 方案二

1.换个不用ssl端口的邮件服务器，相应调整代码：

```java
MailAccount account = new MailAccount();

account.setSslEnable(false);

account.setStarttlsEnable(false);
```


如果比较着急，并且对邮件安全要求不高，可以这样，也能解决。

#### 方案三

手动指定为TLSv1.2协议
百度发现，可能是本地jar包与服务器jar包版本不一致，才一个成功一个失败；默认用TLSv1.1协议，但是新jar包认为这个协议不安全，就会报错无法链接邮件服务器。

代码如下：

```java
MailAccount account = new MailAccount();

// ssl启用
account.setSslEnable(true);
// STARTTLS启用
account.setStarttlsEnable(true);

try {
      // 解决连接smtp服务失败问题，升级了hutool版本才有这个方法
      MailSSLSocketFactory sf = new MailSSLSocketFactory("TLSv1.2");
      //信任所有host
      sf.setTrustAllHosts(true);
      //自定义设置
      account.setCustomProperty("mail.smtp.ssl.socketFactory", sf);
      account.setCustomProperty("mail.smtp.ssl.protocols", "TLSv1.2");
} catch (GeneralSecurityException e) {
      throw new RuntimeException(e);
}
```

注意，如果是老版本hutool的jar包，有可能没有setCustomProperty方法，需要换成新版本hutool的jar包才有（已测试5.3.4版本没有，5.7.19有）：

        <dependency>
            <groupId>cn.hutool</groupId>
            <artifactId>hutool-all</artifactId>
            <version>5.7.19</version>
        </dependency>

