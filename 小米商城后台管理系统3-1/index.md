# 商城系统笔记




## 《小米商城后台管理系统》

## 一、需求分析

| 电商系统项目结构                          |
| ----------------------------------------- |
| ![1610244158701](/imgs/1610244158701.png) |

- 管理员登录

  > 管理员要使用正确的账号和密码进行登录之后才能使用这个管理系统

- 商品类别管理

  > 通过管理系统，能够动态完成添加商品类别、商品类别列表、修改类别及删除类别等操作

- 商品管理

  > 通过管理系统，可以动态完成商品发布、商品列表（分页）、修改商品信息、删除商品信息（下架）

- 用户管理

- 订单管理

- 售后服务

## 二、项目设计

#### 2.1 数据库设计

##### 2.1.1 分析数据实体

- 管理员

- 类别
- 商品

##### 2.1.2 提取实体数据项

> 根据甲方的业务需求及项目的功能需求分析每个数据实体的属性

- 管理员（管理员编号/工号，管理员账号，管理员密码，管理员姓名，管理员头像，管理员电话）
- 类别（类别ID，类别名称，类别描述）
- 商品（商品编号，商品名称，商品图片，商品价格，商品库存，商品说明，图文详情）、

##### 2.1.3 规范实体数据项

> 使用数据库设计范式检查数据项是否合理——数据库设计三范式

`第一范式：数据表中的字段不可再分`

`第二范式：不存在非关键字段对关键字段的部分函数依赖`

`第三范式：不存在非关键字段之间的传递依赖`

##### 2.1.4 绘制实体关系图（E-R）

> 通过E-R图例，分析实体和实体之间的关系（一对一、一对多、多对一、多对多）
>
> 在企业开发中通常是通过PD进行数据库建模

| 实体关系图                                |
| ----------------------------------------- |
| ![1610259879582](/imgs/1610259879582.png) |

##### 2.1.5 数据表设计（三线图）

> ![1610261848540](/imgs/1610261848540.png)

![1610260593733](/imgs/1610260593733.png)

![1610261108490](/imgs/1610261108490.png)

![1610262732828](/imgs/1610262732828.png)

##### 2.1.6 建库建表（SQL）

- 大家根据三线图自行完成

#### 2.2 业务流程设计

> 项目中业务实现的过程

##### 2.2.1 管理登录业务流程

| 管理员登录                                |
| ----------------------------------------- |
| ![1610442011156](/imgs/1610442011156.png) |

##### 2.2.2 类别管理业务流程

| 添加类别                                  |
| ----------------------------------------- |
| ![1610443063398](/imgs/1610443063398.png) |

| 类别列表                                  |
| ----------------------------------------- |
| ![1610499588468](/imgs/1610499588468.png) |

| 修改类别                                  |
| ----------------------------------------- |
| ![1610501150920](/imgs/1610501150920.png) |

| 删除类别                                  |
| ----------------------------------------- |
| ![1610501789493](/imgs/1610501789493.png) |

##### 2.2.3 商品管理业务流程

| 发布商品                                  |
| ----------------------------------------- |
| ![1610677841684](/imgs/1610677841684.png) |

| 商品列表 |
| -------- |
|          |

| 修改商品 |
| -------- |
|          |

| 删除商品 |
| -------- |
|          |

| 商品详情 |
| -------- |
|          |

#### 2.3 UI界面设计

##### 2.3.1 管理员登录

- 登录页面 login.html
- 主页面 index.html

##### 2.3.2 类别管理

- 类别添加页面 type-add.html
- 类别列表页面 type-list.html
- 类别修改页面 type-modify.html
- 提示页面 tips.html

##### 2.3.3 商品管理

暂略

## 三、编码—数据库编程、UI

#### 3.1 创建项目—web项目

##### 3.1.1 web项目创建

![1610443666213](/imgs/1610443666213.png)

![1610443710592](/imgs/1610443710592.png)

##### 3.1.2 IDEA启动tomcat

| 启动                                      |
| ----------------------------------------- |
| ![1610523339517](/imgs/1610523339517.png) |

**如果在启动Tomcat时出现以下错误，请手动打开Tomcat/bin/tomcat8w.exe关闭之前运行的Tomcat**

![1610523020670](/imgs/1610523020670.png)

##### 3.1.3 修改项目的访问路径

> 在IDEA中启动Tomcat，会自动将web项目部署到Tomcat，但是web项目的访问路径默认与web名称时不一致的；我们可以设置项目访问路径

|                                           |
| ----------------------------------------- |
| ![1610523582882](/imgs/1610523582882.png) |
| ![1610523659284](/imgs/1610523659284.png) |

##### 3.1.4 设置项目的默认访问页面

> 当我们启动Tomcat之后，通过路径`http://ip:port/pro_name`后面没有指定访问的页面名称，会默认访问index.html,如果没有index.html就寻找index.jsp

| web/WEB-INF/web.xml                       |
| ----------------------------------------- |
| ![1610524292794](/imgs/1610524292794.png) |



#### 3.2 完成项目业务所需的JDBC操作

> 完成项目业务流程中所需的数据库操作

##### 3.2.1 向web项目中添加mysql数据库驱动文件

- 在web/WEB-INF目录下创建lib文件夹，将驱动文件拷贝粘贴到此文件夹，然后Add as Library

| 目录结构                                  |
| ----------------------------------------- |
| ![1610524617669](/imgs/1610524617669.png) |

##### 3.2.2 创建数据库连接工具类DBUtil

- 创建`cn.edu.hubu.xmsc.utils`包

- 在`cn.edu.hubu.xmsc.utils`包装创建`DBUtil`类

  | DBUtil                                    |
  | ----------------------------------------- |
  | ![1610525237922](/imgs/1610525237922.png) |
  | ![1610525261726](/imgs/1610525261726.png) |

##### 3.2.3 完成管理登录所需的数据库操作

- DTO类

  | Manager                                   |
  | ----------------------------------------- |
  | ![1610525517891](/imgs/1610525517891.png) |

- DAO类

  | ManagerDAO                                |
  | ----------------------------------------- |
  | ![1610526283255](/imgs/1610526283255.png) |

##### 3.2.4 完成类别管理的数据库操作

- DTO类

  | Type                                      |
  | ----------------------------------------- |
  | ![1610527450867](/imgs/1610527450867.png) |

- DAO类

  | TypeDAO                                   |
  | ----------------------------------------- |
  | ![1610528636872](/imgs/1610528636872.png) |

#### 3.3 设计界面并把界面整合到项目中

##### 3.3.1 添加页面到项目的web目录

> 将在HBuilder中设计完成的HTML文件及其关联的静态资源(css\js\img\fonts)拷贝到Javaweb项目的 `web目录`

##### 3.3.2 将HTML页面转换成JSP页面

> JSP（Java Server Page） 是一种基于web服务器运行的动态网页技术
>
> - 支持Java代码
>
> - 必须依赖于web服务器运行，不能使用浏览器直接打开
>
>   ![1610530081453](/imgs/1610530081453.png)
>
> -  兼容HTML，在JSP中可以向在HTML文件中一样写HTML/CSS和JS 

## 四、编码— 业务(流程)实现

> 按照设计阶段完成业务流程，完成项目功能的实现

#### 4.1 管理员登录功能

###### 4.1.1 创建LoginServlet

> Servlet类——指的是继承了javax.servlet.HttpServlet类、实现了HTTP协议、能够接收HTTP请求的类

| Servlet类的创建步骤                       |
| ----------------------------------------- |
| ![1610587012252](/imgs/1610587012252.png) |

###### 4.1.2 login.jsp提交数据到LoginServlet类

| login.jsp                                 |
| ----------------------------------------- |
| ![1610587524497](/imgs/1610587524497.png) |

###### 4.1.3 在LoginServlet类接收并处理请求

| LoginServlet                              |
| ----------------------------------------- |
| ![1610591013522](/imgs/1610591013522.png) |

###### 4.1.4 在login.jsp显示提示信息

| login.jsp                                 |
| ----------------------------------------- |
| ![1610591138468](/imgs/1610591138468.png) |

###### 4.1.5 使用session解决登录认证问题

> 登录认证问题：如果管理员没有，我们不允许浏览器访问处理login.jsp以外的任何页面
>
> ![1610592349346](/imgs/1610592349346.png)

| index.jsp                                 |
| ----------------------------------------- |
| ![1610592637201](/imgs/1610592637201.png) |

| LoginServlet                              |
| ----------------------------------------- |
| ![1610592811633](/imgs/1610592811633.png) |

###### 4.1.6 在index.jsp显示当前登录管理员的姓名和头像

> 当管理员登录成功之后，我们将管理员的信息已经存放到了session中

| index.jsp                                 |
| ----------------------------------------- |
| ![1610593336210](/imgs/1610593336210.png) |

#### 4.2 类别管理—添加类别

###### 4.2.1 点击添加类别跳转到type-add.jsp

| index.jsp                                 |
| ----------------------------------------- |
| ![1610594673710](/imgs/1610594673710.png) |

###### 4.2.2 创建TypeAddServlet

| TypeAddServlet                            |
| ----------------------------------------- |
| ![1610594884650](/imgs/1610594884650.png) |

###### 4.2.3 type-add.jsp提交类别信息到TypeAddServlet

| type-add.jsp                              |
| ----------------------------------------- |
| ![1610595016332](/imgs/1610595016332.png) |

###### 4.2.4 在TypeAddServlet中接收并保存类信息，并跳转到提示页面

| TypeAddServlet                            |
| ----------------------------------------- |
| ![1610595527075](/imgs/1610595527075.png) |

###### 4.2.5 在tips页面显示提示信息

> 因为操作成功和操作失败显示不同的信息，因此在jsp页面中需要实现流程控制—分支语句。
>
> 我们可以在JSP页面中使用JSTL实现流程控制（分支、循环）

**在JSP页面中使用JSTL标签的准备工作：**

- 下载`jstl.jar` 和 `standard.jar`并添加到项目

- 在需要使用JSTL标签库的JSP页面中引入标签

  ```jsp
  <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
  ```

| tips.jsp                                  |
| ----------------------------------------- |
| ![1610596793639](/imgs/1610596793639.png) |

###### 4.2.6 类别数据校验

| type-add.jsp                              |
| ----------------------------------------- |
| ![1610675320038](/imgs/1610675320038.png) |
| ![1610675401594](/imgs/1610675401594.png) |

#### 4.3 类别管理—类别列表

###### 4.3.1 创建TypeListServlet类

| TypeListServlet                           |
| ----------------------------------------- |
| ![1610604929345](/imgs/1610604929345.png) |

###### 4.3.2 在index.jsp点击`类别列表`跳转到TypeListServlet

| index.jsp                                 |
| ----------------------------------------- |
| ![1610605023082](/imgs/1610605023082.png) |

###### 4.3.3 在TypeListServlet中查询所有类别并传递到type-list.jsp

| TypeListServlet                           |
| ----------------------------------------- |
| ![1610605323655](/imgs/1610605323655.png) |

###### 4.3.4 在type-list.jsp中显示类别列表信息

> 因为TypeListServlet传递的是一个Type的集合，因此在type-list.jsp中需要通过JSTL的循环来遍历所有的类别

| type-list.jsp                             |
| ----------------------------------------- |
| ![1610606044153](/imgs/1610606044153.png) |

#### 4.4 类别管理—修改类别

###### 4.4.1 创建TypeQueryServlet

> 因为点击类别列表中的“修改”之后，需要跳转到TypeQueryServlet，因此要先完成TypeQueryServlet的创建

| TypeQueryServlet                          |
| ----------------------------------------- |
| ![1610606520243](/imgs/1610606520243.png) |

###### 4.4.2 点击type-list.jsp页面的`修改`将类别ID传递到TypeQueryServlet

| type-list.jsp                             |
| ----------------------------------------- |
| ![1610607061493](/imgs/1610607061493.png) |

###### 4.4.3 在TypeQueryServlet接收类别ID、查询并传递到type-modify.jsp

| TypeQueryServlet                          |
| ----------------------------------------- |
| ![1610607434391](/imgs/1610607434391.png) |

###### 4.4.4 在type-modify.jsp显示类别的原始信息

| type-modify.jsp                           |
| ----------------------------------------- |
| ![1610607955058](/imgs/1610607955058.png) |

###### 4.4.5 创建TypeUpdateServlet

###### 4.4.6 在type-modify.jsp完成修改之后提交到TypeUpdateServlet

| type-modify.jsp                           |
| ----------------------------------------- |
| ![1610608556742](/imgs/1610608556742.png) |

###### 4.4.7 在TypeUpdateServlet接收类别信息并执行修改

| TypeUpdateServlet                         |
| ----------------------------------------- |
| ![1610673044403](/imgs/1610673044403.png) |

#### 4.5 类别管理—删除类别

###### 4.5.1 创建TypeDeleteServlet类

###### 4.5.2 在type-list.jsp页面点击删除跳转到TypeDeleteServlet

| type-list.jsp                             |
| ----------------------------------------- |
| ![1610672684600](/imgs/1610672684600.png) |

###### 4.5.3 在TypeDeleteServlet中接收类别ID并执行删除

| TypeDeleteServlet                         |
| ----------------------------------------- |
| ![1610673567623](/imgs/1610673567623.png) |

###### 4.5.4 当点击“删除”之后弹出弹窗确认

| type-list.jsp                             |
| ----------------------------------------- |
| ![1610673640358](/imgs/1610673640358.png) |

#### 4.6 商品管理—发布商品

![1610677868219](/imgs/1610677868219.png)

###### 4.6.1 设计goods-add.jsp

步骤略

###### 4.6.2 完成数据库操作

- 创建DTO类

  | Goods                                     |
  | ----------------------------------------- |
  | ![1610679631539](/imgs/1610679631539.png) |

- 创建DAO类

  | GoodsDAO                                  |
  | ----------------------------------------- |
  | ![1610680057416](/imgs/1610680057416.png) |

###### 4.6.3 创建GoodsListTypeServlet类

###### 4.6.4 在index.jsp页面点击发布商品跳转到

| index.jsp                                 |
| ----------------------------------------- |
| ![1610681185646](/imgs/1610681185646.png) |

###### 4.6.5. 在GoodsListTypeServlet类中查询类别列表传递到goods-add.jsp

| GoodsListTypeServlet                      |
| ----------------------------------------- |
| ![1610681358595](/imgs/1610681358595.png) |

###### 4.6.6 在goods-add.jsp中通过JSTL显示类别信息到下拉菜单选项

| goods-add.jsp                             |
| ----------------------------------------- |
| ![1610681638022](/imgs/1610681638022.png) |

###### 4.6.7 创建GoodsAddServlet类

###### 4.6.8 在goods-add.jsp点击“立即提交”提交到GoodsAddServlet类

> 在goods-add.jsp表单中包含了文件，如果提交的form表单中包含文件，需要注意以下几点：
>
> - form的提交方式必须为 post
> - form表单中的数据默认会压缩传输，如果包了文件就必须设置 `enctype="multipart/form-data"`声明表达中的数据非压缩传输

| goods-add.jsp                             |
| ----------------------------------------- |
| ![1610682415839](/imgs/1610682415839.png) |

###### 4.6.9 在GoodsAddServlet类中接收商品信息

> - 因为表单提交数据采用`enctype="multipart/form-data"`提交，所以在Servlet中按照原来的方式接收数据就接收不到了，必须在GoodsAddServlet类前添加`@MultipartConfig`解析非压缩方式提交的数据
>
> - 上传的文件需要保存在特定的目录——在项目的web目录下创建名为files的文件夹用于保存图片（如果files文件夹是空的，项目部署时不会自动创建此目录，因此建议在files目录下默认放一个文件）

| GoodsAddServlet                           |
| ----------------------------------------- |
| ![1610683448456](/imgs/1610683448456.png) |
| ![1610694609894](/imgs/1610694609894.png) |
| ![1610694643764](/imgs/1610694643764.png) |

#### 4.7 商品管理—商品列表

![1610697617245](/imgs/1610697617245.png)

###### 4.7.1 设计goods-list.jsp

###### 4.7.2 完成此功能的数据库操作

| GoodsDAO                                  |
| ----------------------------------------- |
| ![1610698900572](/imgs/1610698900572.png) |
| ![1610699013847](/imgs/1610699013847.png) |

###### 4.7.3  创建GoodsListServlet类

###### 4.7.4 在index.jsp中点击`商品列表`跳转到GoodsListServlet类

> 默认显示第一页商品信息，传递默认页码为1

| index.jsp                                 |
| ----------------------------------------- |
| ![1610700106378](/imgs/1610700106378.png) |

###### 4.7.5 在GoodsListServlet类中接收页码、查询数据

| GoodsListServlet                          |
| ----------------------------------------- |
| ![1610700741984](/imgs/1610700741984.png) |

###### 4.7.6 在goods-list.jsp中通过JSTL显示商品信息、分页信息

| goods-list.jsp     显示商品数据           |
| ----------------------------------------- |
| ![1610701545034](/imgs/1610701545034.png) |

| goods-list.jsp   显示分页数据             |
| ----------------------------------------- |
| ![1610702700144](/imgs/1610702700144.png) |

#### 4.8 商品管理—搜索—按类别搜索

![1610758976368](/imgs/1610758976368.png)

###### 4.8.1 完成按类别查询商品的数据库操作

| GoodsDAO                                  |
| ----------------------------------------- |
| ![1610759807858](/imgs/1610759807858.png) |

###### 4.8.2 在GoodsListServlet中查询所有类别，并传递到goods-list.jsp

| **GoodsListServlet**                      |
| ----------------------------------------- |
| ![1610760088589](/imgs/1610760088589.png) |

###### 4.8.3 在商品列表页面显示类别列表

| goods-list.jsp                            |
| ----------------------------------------- |
| ![1610760363222](/imgs/1610760363222.png) |

###### 4.8.4 创建GoodsListByTypeServlet类

###### 4.8.5 点击商品类别页面的类别跳转到GoodsListByTypeServlet类

| goods-list.jsp                            |
| ----------------------------------------- |
| ![1610760672833](/imgs/1610760672833.png) |

###### 4.8.6 在GoodsListByTypeServlet中接收类别ID和页码进行查询

| GoodsListByTypeServlet                    |
| ----------------------------------------- |
| ![1610761241234](/imgs/1610761241234.png) |

###### 4.8.9 按类别查询商品分页问题

| GoodsListServlet                          |
| ----------------------------------------- |
| ![1610763033370](/imgs/1610763033370.png) |

| GoodsListByTypeServlet                    |
| ----------------------------------------- |
| ![1610763056810](/imgs/1610763056810.png) |

| goods-list.jsp                            |
| ----------------------------------------- |
| ![1610763127797](/imgs/1610763127797.png) |
| ![1610763174044](/imgs/1610763174044.png) |



## 五、项目部署（Linux）

> 项目开发完成之后，将项目部署到生产环境正式的上线运营

#### 5.1 Linux介绍

###### 5.1.1 优点

> 用于生产环境的服务器主机通常会选择Linux系统
>
> - windows的优点是有比较人性化的可视化操作界面，这个可视化界面会占用大量的系统资源；但是生产环境中服务器主机主要用于web容器（tomcat）的执行，为了最大限度的使用硬件资源，所以我们通常会选择系统开销更小的Linux
> - Linux系统是开源
> - Linux性能比windows更稳定（宕机概率较低）
> - Linux防火墙组件性能高、保证系统安全

###### 5.1.2 版本

- RedHat（效率高、可靠性好）
- Ubuntu
- CentOS（基于RedHat源码编译而成，完全开源） 6.x   7.x 

###### 5.1.3 如何获取Linux

- 安装虚拟机，在虚拟机中安装CentOS
- 购买硬件主机、安装（成本高、商用）
- 购买云主机

#### 5.2 购买云主机

###### 5.2.1 云主机运营商

- 阿里云（推荐）
  - https://developer.aliyun.com/adc/student/#ecscolleges-collocation-stu
  - https://dashi.aliyun.com/site/cloud/student
- 腾讯云

#### 5.3 管理云主机

###### 5.3.1 登录到云主机

- 登录到阿里云的控制台（使用购买云主机时的账号和密码）

  - https://account.aliyun.com/

- 进入到ECS服务器管理界面

  ![1610766109066](/imgs/1610766109066.png)

- 点击购买的实例进入到实例的配置页面

  ![1610766189956](/imgs/1610766189956.png)

###### 5.3.2 云主机系统管理

- 常规配置

  ![1610766335536](/imgs/1610766335536.png)

###### 5.3.3 修改云主机密码

- 设置root用户的登录密码

  ![1610766513247](/imgs/1610766513247.png)

- 密码重置完成之后，重启服务器

  ![1610766572092](/imgs/1610766572092.png)

###### 5.3.4 安全组设置

> 阿里云主机为了保证系统的安全，提供了访问限制（端口配置）

- 进入到安全组配置页面

  ![1610766840295](/imgs/1610766840295.png)

- 放行 3306端口

  ![1610767007831](/imgs/1610767007831.png)

- 放行8080端口

  ![1610767094433](/imgs/1610767094433.png)

#### 5.4 使用远程工具连接云主机

> 云主机是远程的主机，需要使用工具进行远程连接，然后通过远程工具进行云主机的操作

###### 5.4.1 xftp

> xftp是一个远程的文件传输工具，可以将本机上的文件上传到云主机

- 从群文件下载并安装（傻瓜式安装）

- 创建连接

  ![1610767640965](/imgs/1610767640965.png)

- 将Tomcat和JDK的安装包 上传到 Linux

  ![1610767811153](/imgs/1610767811153.png)

###### 5.4.2 xshell

> xshell是一个远程终端，可以通过指令操作远程云主机

- 从群文件下载并安装（傻瓜式）

- 如果xftp已经与云主机建立连接，xshell其实是可以不用建立连接的，建议在xshell创建连接

  ![1610768173210](/imgs/1610768173210.png)

  

#### 5.5 在Linux系统中安装JDK并配置环境变量

###### 5.5.1 将JDK的安装包上传到Linux系统的usr/local目录

- 使用xftp上传文件

###### 5.5.2 解压JDK的压缩包

- xshell连接到云主机

```shell
[root@theo ~]# cd /usr/local

[root@theo local]# ls
aegis  apache-tomcat-8.5.46.tar.gz  bin  etc  games  include  jdk-8u221-linux-x64.tar.gz  lib  lib64  libexec  sbin  share  src

[root@theo local]# tar -zxvf jdk-8u221-linux-x64.tar.gz
```

- 查看解压后的目录结构

```shell
[root@theo local]# ls
aegis  apache-tomcat-8.5.46.tar.gz  bin  etc  games  include  jdk1.8.0_221  jdk-8u221-linux-x64.tar.gz  lib  lib64  libexec  sbin  share  src

[root@theo local]# cd jdk1.8.0_221/
[root@theo jdk1.8.0_221]# ls
bin  COPYRIGHT  include  javafx-src.zip  jre  lib  LICENSE  man  README.html  release  src.zip  THIRDPARTYLICENSEREADME-JAVAFX.txt  THIRDPARTYLICENSEREADME.txt

[root@theo jdk1.8.0_221]# cd bin
[root@theo bin]# ls
appletviewer  jar        javadoc         javapackager  jconsole  jhat   jmc         jsadebugd  jvisualvm     pack200     rmiregistry  tnameserv  xjc
ControlPanel  jarsigner  javafxpackager  java-rmi.cgi  jcontrol  jinfo  jmc.ini     jstack     keytool       policytool  schemagen    unpack200
extcheck      java       javah           javaws        jdb       jjs    jps         jstat      native2ascii  rmic        serialver    wsgen
idlj          javac      javap           jcmd          jdeps     jmap   jrunscript  jstatd     orbd          rmid        servertool   wsimport
```

###### 5.5.3 配置JDK环境变量

- 进入到/etc目录，找到profile文件

```shell
[root@theo bin]# cd /etc
[root@theo etc]# ls
```

![1610769015678](/imgs/1610769015678.png)

- 编辑这个profile文件，在文件中添加JDK环境变量

> [root@theo etc]# vim profile  可以打开文件编辑器，必须输入字母i进入到编辑状态,在最后面添加如下四项配置：![1610769616142](/imgs/1610769616142.png)

```
export JAVA_HOME=/usr/local/jdk1.8.0_221
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib
export PATH=$JAVA_HOME/bin:$PATH
```

> 编辑完成之后，点击键盘“Esc”按键退出编辑状态，输入`:wq`保存并退出编辑器

###### 5.5.4 重新加载配置文件

```shell
[root@theo etc]# source /etc/profile

[root@theo etc]# java -version
java version "1.8.0_221"
Java(TM) SE Runtime Environment (build 1.8.0_221-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.221-b11, mixed mode)
```



#### 5.6 在Linux系统中安装Tomcat

###### 5.6.1 从群文件按下载Tomcat安装包并上传到Linux的/usr/local目录

###### 5.6.2 解压tomcat

> xshell连接到linux，在xshell中输入以下指令：

```shell
[root@theo etc]# cd /usr/local
[root@theo local]# tar -zxvf apache-tomcat-8.5.46.tar.gz 
```

###### 5.6.3 启动Tomcat

```shell
[root@theo local]# cd apache-tomcat-8.5.46/
[root@theo apache-tomcat-8.5.46]# cd bin
[root@theo bin]# ./startup.sh 
Using CATALINA_BASE:   /usr/local/apache-tomcat-8.5.46
Using CATALINA_HOME:   /usr/local/apache-tomcat-8.5.46
Using CATALINA_TMPDIR: /usr/local/apache-tomcat-8.5.46/temp
Using JRE_HOME:        /usr/local/jdk1.8.0_221/jre
Using CLASSPATH:       /usr/local/apache-tomcat-8.5.46/bin/bootstrap.jar:/usr/local/apache-tomcat-8.5.46/bin/tomcat-juli.jar
Tomcat started.
```

###### 5.6.4 关闭linux防火墙

```properties
# 查看防火墙状态
[root@theo bin]# systemctl status firewalld
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; disabled; vendor preset: enabled)
   Active: inactive (dead)
     Docs: man:firewalld(1)
     
# 关闭防火墙
[root@theo bin]# systemctl stop firewalld

# 启动防火墙
[root@theo bin]# systemctl start firewalld
```

###### 5.6.5 远程访问linux中的tomcat

| http://47.96.11.185:8080/                 |
| ----------------------------------------- |
| ![1610777918268](/imgs/1610777918268.png) |

###### 5.6.6 停止Tomcat

```properties
# 在Linux系统中安装lsof指令（安装一次之后就不用重复安装了）
[root@theo bin]# yum install -y lsof

# 通过lsof 指令查看8080的进程号
[root@theo bin]# lsof -i:8080
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
java    2749 root   53u  IPv4  37961      0t0  TCP *:webcache (LISTEN)

# 通过kill指令停止指定进程
[root@theo bin]# kill -9 2749
```



#### 5.7 在Linux系统中安装MySQL

###### 5.7.1 安装MySQL

> 在线安装  http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm

```properties
# 下载rpm安装包
[root@theo bin]# cd /usr/local
[root@theo local]# wget http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm

# 安装rpm
[root@theo local]# rpm -ivh mysql57-community-release-el7-10.noarch.rpm

# 通过yum指令安装mysql服务 （下载安装mysql）
[root@theo local]# yum -y install mysql-community-server

# 将mysql加入到开机启动项 （mysql安装之后，在linux中的服务名为 mysqld）
[root@theo local]# systemctl enable mysqld

# 启动MySQL
[root@theo local]# systemctl start mysqld

# 检查3306端口是否被mysql占用
[root@theo local]# lsof -i:3306
COMMAND  PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
mysqld  3192 mysql   17u  IPv6  42484      0t0  TCP *:mysql (LISTEN)

# 停止MySQL
[root@theo local]# systemctl stop mysqld
```

###### 5.7.2 MySQL的基本使用

> 在Linux中安装MySQL并没有提示设置root用户的密码，安装包中设置了一个默认随机密码；但是这个默认随机密码只能用来初始登录，不能用来进行数据库操作（默认密码必须改）

```properties
# 1.查询初始密码
[root@theo local]# cat /var/log/mysqld.log | grep password
2021-01-16T06:41:27.753855Z 1 [Note] A temporary password is generated for root@localhost: gpKBe-Oqj6SD

# 2.登录mysql
[root@theo log]# mysql -uroot -p
Enter password: （粘贴复制的初始密码--回车）

# 3.修改初始密码
mysql> set global validate_password_policy=LOW;
Query OK, 0 rows affected (0.00 sec)

mysql> set password=password('admin123');
Query OK, 0 rows affected, 1 warning (0.00 sec)

# 4.mysql的root账号默认不允许远程连接——授权远程连接
GRANT ALL PRIVILEGES on *.* to 'root'@'%' IDENTIFIED by 'admin123' WITH GRANT OPTION;
# 授权之后刷新权限
flush PRIVILEGES;
```

#### 5.8 项目部署

###### 5.8.1 数据库迁移

> 在linux中的mysql中创建数据库、添加准备数据  （可以复制）

###### 5.8.2 将web项目部署到linux的tomcat中

> 最直观的方法如下（省略打包的过程）：

![1610781176254](/imgs/1610781176254.png)

- 启动Tomcat

```shell
[root@theo log]# cd /usr/local/apache-tomcat-8.5.46/bin
[root@theo bin]# ./startup.sh 
Using CATALINA_BASE:   /usr/local/apache-tomcat-8.5.46
Using CATALINA_HOME:   /usr/local/apache-tomcat-8.5.46
Using CATALINA_TMPDIR: /usr/local/apache-tomcat-8.5.46/temp
Using JRE_HOME:        /usr/local/jdk1.8.0_221/jre
Using CLASSPATH:       /usr/local/apache-tomcat-8.5.46/bin/bootstrap.jar:/usr/local/apache-tomcat-8.5.46/bin/tomcat-juli.jar
Tomcat started.
```





## 六、项目开发任务（必须完成）

#### 1.随堂项目功能需要完成

- 管理员登录
- 类别管理
  - 添加类别（JS数据校验）
  - 类别列表
  - 删除类别（JS提示）
  - 修改类别

- 商品管理
  - 发布商品
  - 商品列表+分页

#### 2.扩展功能（自行完成）

- 管理员登录的表单校验
- 类别管理
  - 修改类别（JS数据校验）
- 商品管理
  - 发布商品（JS数据校验）
  - 删除商品
  - 修改商品
  - 商品详情
