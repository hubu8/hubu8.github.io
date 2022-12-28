# Jasypt实现springboot参数加密


<!--more-->

对于大部分项目来说，为了运维方便和项目解耦，往往会将一些和代码无关的配置抽离出来，单独放在一个配置文件中进行维护。但这类配置文件中常常含有密码等比较敏感的信息。为了避免出现敏感信息泄露等问题，采用加密等手段来增强项目的安全性是比较有必要的。本篇文章将以jasypt这个应用较为广泛的第三方依赖来进行讲解，主要基于Springboot进行应用。

#### 一、基于Springboot项目使用jasypt进行加解密

##### （一）引入依赖

jasypt对应Springboot项目有着良好的支持，可以省略我们对工具类的封装。使用jasypt需要在项目中引入如下依赖：

```xml
<!-- spring-boot项目的依赖 -->
<dependency>
    <groupId>com.github.ulisesbocchio</groupId>
    <artifactId>jasypt-spring-boot-starter</artifactId>
    <version>3.0.3</version>
</dependency>
```

##### （二）配置jasypt响应的加密配置

对于加密秘钥的话，最好是不要写在配置文件中，不然别人根据你的配置就可以轻而易举地进行反推，敏感信息的加密程度就大大降低了。

```yaml
jasypt:
  encryptor:
    #默认加密算法:PBEWITHHMACSHA512ANDAES_256，sha512+AES算法，安全性更高，但是需要 Java JDK 1.9+
    #本服务使用jdk1.8，所以使用 PBEWithMD5AndDES md5+des算法
    #默认使用 com.ulisesbocchio.jasyptspringboot.encryptor.DefaultLazyEncryptor 进行加解密 ，PooledPBEStringEncryptor可以对其加密的内容进行解密
    algorithm: PBEWithMD5AndDES
    # 加密密钥，使用方式 spring.datasource.password=ENC(密文)，不要设置在配置文件中，建议使用环境变量或者启动参数: --jasypt.encryptor.password=123456
    #加密密钥可以放在yml配置文件里，也可以放在运行参数里
    password: 123456
    #设置密文前缀和后缀
    property:
      prefix: ENC(
      suffix: )
    iv-generator-classname: org.jasypt.iv.RandomIvGenerator
```

除了上面的配置项之外，还有如下配置项可选

| Key                                       | Required | Default Value                       |
| ----------------------------------------- | -------- | ----------------------------------- |
| jasypt.encryptor.password                 | True     | -                                   |
| jasypt.encryptor.algorithm                | False    | PBEWITHHMACSHA512ANDAES_256         |
| jasypt.encryptor.key-obtention-iterations | False    | 1000                                |
| jasypt.encryptor.pool-size                | False    | 1                                   |
| jasypt.encryptor.provider-name            | False    | SunJCE                              |
| jasypt.encryptor.provider-class-name      | False    | null                                |
| jasypt.encryptor.salt-generator-classname | False    | org.jasypt.salt.RandomSaltGenerator |
| jasypt.encryptor.iv-generator-classname   | False    | org.jasypt.iv.RandomIvGenerator     |
| jasypt.encryptor.string-output-type       | False    | base64                              |
| jasypt.encryptor.proxy-property-sources   | False    | false                               |
| jasypt.encryptor.skip-property-sources    | False    | empty list                          |

##### （三）在代码中进行测试

注意，在代码测试之前，需要保证springboot项目中有正常使用**`@SpringBootApplication`** 或者`@EnableAutoConfiguration`注解
 我们可以项目中使用`@Value`注解来直接获取解密后的配置值

###### 步骤零：加密工具类

```java
package com.jindong.dailytest.utils;

import org.jasypt.encryption.pbe.StandardPBEStringEncryptor;
import org.jasypt.encryption.pbe.config.EnvironmentPBEConfig;
import org.junit.Test;

public class JasyptTest {
    @Test
    public void testEncrypt() throws Exception {
        //加密工具
        StandardPBEStringEncryptor standardPBEStringEncryptor = new StandardPBEStringEncryptor();
        EnvironmentPBEConfig config = new EnvironmentPBEConfig();
		// 加密的算法，这个算法是默认的
        config.setAlgorithm("PBEWithMD5AndDES");     
        // 加密的密钥,要和配置文件一致或者项目启动参数
        config.setPassword("");            
        standardPBEStringEncryptor.setConfig(config);
        //需要加密的文本字符串
        String plainText = "202172";
        String encryptedText = standardPBEStringEncryptor.encrypt(plainText);
        System.out.println(encryptedText);
    }

    @Test
    public void testDe() throws Exception {
        //解密工具
        StandardPBEStringEncryptor standardPBEStringEncryptor = new StandardPBEStringEncryptor();
        EnvironmentPBEConfig config = new EnvironmentPBEConfig();
		//加密算法
        config.setAlgorithm("PBEWithMD5AndDES");
        //加密密钥
        config.setPassword("");
        standardPBEStringEncryptor.setConfig(config);
        //需要解密的字符串
        String encryptedText = "";
        String plainText = standardPBEStringEncryptor.decrypt(encryptedText);
        System.out.println(plainText);
    }
}
```

###### 步骤一：在配置文件中新增一个加密的配置

注意，需要在密文外面使用我们前面定义的前缀和后缀进行包裹，这样jasypt才会识别到这个配置属于需要加密的配置。我们上面配置的`jasypt.property.prefix`和`jasypt.property.suffix`分别是`ENC(`和`)` （也是默认的前后缀）。至于加密后的值是怎么来的，后文会介绍一个工具类。

```yml
jasypt:
  encryptor:
    algorithm: PBEWithMD5AndDES
    iv-generator-classname: org.jasypt.iv.NoIvGenerator
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://12.168.120.3:3307/hubu_test?serverTimezone=GMT%2B8&useSSL=true&serverTimezone=Asia/Shanghai
    username: root
    password: ENC(sbvop4M65v0xqcPpJFbADQ==)
```

这里配置文件里面没有加加密密钥，放在启动参数里面，防止代码泄露，反解密出文本

###### 步骤二：加入加密启动参数

在项目启动时加入加密启动参数-Djasypt.encryptor.password=test

![image-20221228202513257](/spring/image-20221228202513257.png)
