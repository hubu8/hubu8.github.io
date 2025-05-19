# IDEA如何创建专属JAR包


<!--more-->

## 什么是Jar

- 实现了特定功能，Java字节码文件的压缩包

- 本质上就是一个编译后的Java项目，打包城jar文件

  log4j.jar 、mysql-connection-8.01.jar等

## 自己创建jar包，让别人来用：

1. 创建Java项目
2. 创建实现了特定功能的类
3. 将项目生成为jar包
4. 将jar包发布出去，给别人使用

## 创建项目和验证

### 创建项目

创建项目名为dateUtil的project项目

![c9c1ba0366a040d0b738ea2433759c49.png](./images/c9c1ba0366a040d0b738ea2433759c49.png)

或者：

![40eaed6c1a2a484cac3e2b728497bcbe.png](./images/40eaed6c1a2a484cac3e2b728497bcbe.png)

![368e1ae398f34b42b8739ceb11fa27d2.png](./images/368e1ae398f34b42b8739ceb11fa27d2.png)

项目 dateUtil 创建完成

![e4f70edbb09f49edb36292007c5130fe.png](./images/e4f70edbb09f49edb36292007c5130fe.png)

### 创建包和类

![074f6a5b93f44cf28072bbc6078d1400.png](./images/074f6a5b93f44cf28072bbc6078d1400.png)

### 相应的代码

```java
package com.dongyuan.util;

import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;


/**日期转换工具类
 * @author: XYT
 * @create-date: 2022/7/26 15:12
 */
public class DateConvertUtil {
    /**   注释方法：/**+回车键 形参会被自动提取出来并加粗描黑。这就是为什么引用系统方法的时候，会有很多的说明
     * 将字符串转为日期，默认格式为yyyy-MM-dd
     * @param strDate 字符串日期
     * @return 返回转换的日期
     */
    public static Date getDate(String strDate){ //一个参数；对下方方法的一种封装;这种方式叫“语法糖”；
        return getDate(strDate,"yyyy-MM-dd"); //格式是默认的 getDate
    }

    /**
     * 将字符串转为日期类型
     * @param strDate 字符串日期
     * @param format 转换格式
     * @return 返回转换的日期，转换失败返回null
     */

    public static Date getDate(String strDate, String format){ //两个参数
        SimpleDateFormat sdf=new SimpleDateFormat(format); //格式需要自己指定（设置）

//        try {  //自动生成try-catch
//            return sdf.parse(strDate);
//        } catch (ParseException e) {
//            throw new RuntimeException(e);
//        }
//    }
        try {  //课堂代码try-catch
            return sdf.parse(strDate);  //注意：parse
        } catch (ParseException e) {
            e.printStackTrace(); //这里不一样
        }
        return null;
    }

    /**
     * 将日期转为字符串，固定格式为yyyy-MM-dd
     * @param date 日期
     * @return 返回转换的字符串
     */

    public static String getDate(Date date){
        return getDate(date,"yyyy-MM-dd"); //格式是默认的 getDate
    }

    /**
     * 将日期转为字符串
     * date 日期
     * format 格式字符串
     * 返回转换后的字符串
     */
    public static String getDate(Date date,String format){ //两个参数
        SimpleDateFormat sdf=new SimpleDateFormat(format); //格式需要自己指定（设置）
        return sdf.format(date);   //注意：format
    }

}
```

```java
package com.dongyuan.util;

/**
 * @author: XYT
 * @create-date: 2022/7/26 16:02
 */
public class StringUtils {
    /**
     * 判断字符串是否为空
     * @param str
     * @return
     */
    public static boolean isNullOrEmpty(String str){
        return str==null || str.isEmpty();  //注意 ==
    }
}
```

### 验证代码

```java
package com.dongyuan.test;

import com.aaa.util.DateConvertUtil;

import java.util.Date;

/**
 * @author: XYT
 * @create-date: 2022/7/26 16:08
 */
public class Test { //测试
    public static void main(String[] args) {
        //日期转为字符串
        System.out.println(DateConvertUtil.getDate(new Date()));
        //日期转为固定格式的字符串
        System.out.println(DateConvertUtil.getDate(new Date(),"yyyy-MM-dd HH:mm:ss"));
    }
}
```

### 验证结果

![5df36c09555f465a84f5560a2e1ffeb3.png](./images/5df36c09555f465a84f5560a2e1ffeb3.png)

## 打包和引用

选择 File 中的 Project Structure。

![aad87d54dbca4efdbb38b9e09b4134c1.png](./images/aad87d54dbca4efdbb38b9e09b4134c1.png)

### Project 左侧工具栏的功能





![c05db86570354633bd98c2e95ab4f0f3.png](./images/c05db86570354633bd98c2e95ab4f0f3.png)


介绍 Project Structure 的工具栏及具体功能：

![5ba7efa017d2451aa73cf8584204d1c0.png](./images/5ba7efa017d2451aa73cf8584204d1c0.png)



![e84cc125d3724056b93dbb69476acfc7.png](./images/e84cc125d3724056b93dbb69476acfc7.png)



![a70fe502524e4704b0f6303ace8e84a8.png](./images/a70fe502524e4704b0f6303ace8e84a8.png)



![ea5afbe74ea14b7886682b6ad226b24f.png](./images/ea5afbe74ea14b7886682b6ad226b24f.png)



![12df6479701b40cabd1f6893e759c175.png](./images/12df6479701b40cabd1f6893e759c175.png)

### 打 jar 包

![9460e68760fd4ca58e9dbc86857f1a3c.png](./images/9460e68760fd4ca58e9dbc86857f1a3c.png)



![0694cb0d72554b438b44752cc2c4a712.png](./images/0694cb0d72554b438b44752cc2c4a712.png)

![c245e894eb2444b59910adcbc072d8f3.png](./images/c245e894eb2444b59910adcbc072d8f3.png)



![345b038dc03843af8562a6f07dd7102f.png](./images/345b038dc03843af8562a6f07dd7102f.png)



![0accc90bbd734951881ff308882936a8.png](./images/0accc90bbd734951881ff308882936a8.png)



![486cecb92d384e36978be5a415e721b8.png](./images/486cecb92d384e36978be5a415e721b8.png)


复制生成的 jar 包到桌面，然后用解压工具打开：

![341dd8765180461881608260972898a0.png](./images/341dd8765180461881608260972898a0.png)



![135751604cd549f8a872dcd24f859158.png](./images/135751604cd549f8a872dcd24f859158.png)

### 导入生成的 jar 包

![289753be63354952a19e7ecb7d86c78c.png](./images/289753be63354952a19e7ecb7d86c78c.png)



![7ec3e778dc5c446cb79ef06a39d729b7.png](./images/7ec3e778dc5c446cb79ef06a39d729b7.png)



![eb4010cf18c6489b806c5d9d7fdaec8c.png](./images/eb4010cf18c6489b806c5d9d7fdaec8c.png)

### 验证 jar 包导入 / 应用的正确性

![86ed277925d8432792f4b4201dc488b2.png](./images/86ed277925d8432792f4b4201dc488b2.png)

## maven项目如何使用jar包

### 前言

maven作为包管理工具，好处不必多说

但是有些情况，比如需要引入第三方包，如快递鸟，支付宝，微信等jar包（当然有可能直接提供maven依赖）

如果直接下载到本地之后，怎么整合到自己的maven工程呢？

### 下面列举五种方式

1. **上传到maven中心仓库**

   https://oss.sonatype.org/

愿意折腾可也搞搞，可以参考 如何发布Jar包到Maven中央仓库（确实比较麻烦）

如这是我手动上传到maven中央仓库的jar包 https://mvnrepository.com/search?q=chendahai

优点：可以直接在pom.xml中引入，打包部署没有问题

缺点：上传复杂，麻烦（创建工单，生成秘钥等等

2. **搭建maven私服**

手动在后台管理页面上传，之后pom.xml引入

上传方式查看 maven私服nexus上传第三方jar包以及下载

优点：下载速度快，maven聚合项目更适用，内部适用deploy即可，打包部署没有问题

缺点：相比第一种还要麻烦些，还占用本地服务器资源

3. **传统方式java SE的套路**

新建lib包，之后导入（eclipse直接build path）（idea麻烦些：File→project structure→libraries→+jar包）

优点：简单，不用做额外的配置

缺点：协作开发，多人都需做同样的操作，沟通也是一回事

4. **pom文件scope的system属性**

  优点：配置后之后直接引入，maven reImport即可

缺点：没啥缺点，就是配置者得费些时间

案例
要引入如下dateUtil.jar

操作
自定义目录，这里在project的根目录新建lib文件夹，将jar放进去

pom.xml中引入

```xml
    <dependency>
      <groupId>dingding</groupId>
      <artifactId>dingding</artifactId>
      <version>2.8</version>
      <scope>system</scope>
      <systemPath>${project.basedir}/lib/dateUtil.jar</systemPath>
    </dependency>
```

![image-20220806180341428.png](./images/image-20220806180341428.png)

注意：
groupId：自定义
artifactId：自定义
version：自定义
scope：必须是system
systemPath：jar包的路径（idea编写的时候会有提示的）
通过上述方式，在开发环境没有什么问题，不会存在什么包找不到等情况

但是，maven project部署一般打包发布，所以打包是需要额外配置的（上述的第一二中方式不需要做额外处理）

处理打包
打包的时候需要做如下配置，需要通过resource标签引入，位置build→resources→resource

 ```xml
  <build>
    <resources>
     <resource>
       <directory>lib</directory>
       <targetPath>/BOOT-INF/lib/</targetPath>
       <includes>
         <include>**/*.jar</include>
       </includes>
     </resource>
    </resources>
  </build>
 ```



directory：指定lib文件夹的位置，由于是相对工程根目录，所以直接写上lib即可
targetPath：打包到的文件夹位置，写上BOOT-INF/lib即可，或者是WEB-INF/lib。【斜杠（/）加不加都行，如果是mac的话写./】
includes：一般都是以jar结尾，就写**/*.jar
较为完整的pom.xml

```xml
        <dependency>
            <groupId>dingding</groupId>
            <artifactId>dingding</artifactId>
            <version>2.8</version>
            <scope>system</scope>
            <systemPath>${project.basedir}/lib/taobao-sdk-java-auto_1479188381469-20190628.jar</systemPath>
        </dependency>

    </dependencies>
    <build>
        <resources>
            <resource>
                <directory>lib</directory>
                <targetPath>/BOOT-INF/lib/</targetPath>
                <includes>
                    <include>**/*.jar</include>
                </includes>
            </resource>
        </resources>

```

ok，mvn package即可。

5. **本地安装Jar包**

在本地maven仓库安装本地jar包
    maven install可以把指定的文件安装到本地maven仓库（使用maven指令前需要安装apache maven）。有三种install方式：
（1）mvn install:install-file -Dfile=<path-to-file> -DgroupId=<group-id> -DartifactId=<artifact-id> -Dversion=<version> -Dpackaging=<packaging>
指定jar包、groupid、artifactId和version，maven会自动生成相应的pom.xml文件。

（2）mvn install:install-file -Dfile=<path-to-file> -DpomFile=<path-to-pomfile>
如果jar包是用maven打包生成的，可以直接指定jar包和pom.xml文件。

（3）mvn install:install-file -Dfile=<path-to-file>
如果jar包是用maven打包生成的，maven 2.5版本会自动根据jar包生成pom.xml文件。

安装成功后，在.m2/repository文件夹里可以看到jar包和pom.xml文件。

添加依赖

```xml
<dependency>
      <groupId>dateUtil</groupId>
      <artifactId>dateUtil</artifactId>
      <version>1.0.0</version>
</dependency>
```


