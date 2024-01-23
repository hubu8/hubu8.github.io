# javax.net.ssl.SSLHandshakeException: No appropriate protocol (protocol is disa(String)


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

jdk1.8版本导致SSL调用权限上有问题，新电脑装的jdk是jdk1.8.0_291，版本比较高。搜到的解决方法是：

**找到jdk 1.8安装目录，找到C:\Program Files\Java\jre里面的lib\security 下面有个java.security。找到对应的SSLv3，删除掉，重启项目就好了。（删掉SSLv3就是允许SSL调用）**

### 解决问题

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

### 衍生命令

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
