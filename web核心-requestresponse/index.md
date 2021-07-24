# request & response


<!--more-->

# 【web核心-请求响应对象-随堂笔记】

## 一.今日重点：

1. 请求对象-request （请求对象的api）
   - 请求参数的获取
   - 请求乱码的处理
   - 请求转发
2. 响应对象-response (响应对象的api)
   - 常见响应状态码
   - 服务器数据响应到浏览器
   - 响应乱码的处理
   - 请求重定向









## 二.前置案例：

#### 1.请求方式与servlet的方法对应关系：

![image-20201028103018918](/实训/req/image-20201028103018918.png)



#### 2.请求对象与响应对象的快速入门：

![image-20201028103055987](/实训/req/image-20201028103055987.png)





## 三.request请求对象

#### 1.请求路径：（理解）

```java
/**
 * 请求对象的各种信息获取
 * @author 黑马程序员
 * @Company http://www.itheima.com
 */
public class RequestDemo1 extends HttpServlet {

    public void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {

        //本机地址：服务器地址
        String localAddr = request.getLocalAddr();
        //本机名称：服务器名称
        String localName = request.getLocalName();
        //本机端口：服务器端口
        int localPort = request.getLocalPort();
        //来访者ip
        String remoteAddr = request.getRemoteAddr();
        //来访者主机
        String remoteHost = request.getRemoteHost();
        //来访者端口
        int remotePort = request.getRemotePort();
        //统一资源标识符
        String URI = request.getRequestURI();
        //统一资源定位符
        String URL = request.getRequestURL().toString();
        //获取查询字符串
        String queryString = request.getQueryString();
        //获取Servlet映射路径
        String servletPath = request.getServletPath();

        //输出内容
		System.out.println("getLocalAddr() is :"+localAddr);
		System.out.println("getLocalName() is :"+localName);
		System.out.println("getLocalPort() is :"+localPort);
		System.out.println("getRemoteAddr() is :"+remoteAddr);
		System.out.println("getRemoteHost() is :"+remoteHost);
		System.out.println("getRemotePort() is :"+remotePort);
		System.out.println("getRequestURI() is :"+URI);
		System.out.println("getRequestURL() is :"+URL);
        System.out.println("getQueryString() is :"+queryString);
        System.out.println("getServletPath() is :"+servletPath);
    }

    public void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        doGet(request, response);
    }
}	
```





##### 小结：

![image-20201028112941570](/实训/req/image-20201028112941570.png)







#### 2.请求参数的获取：（重要）



#### 1）获取请求参数

**getParameter()方法的示例代码**

```java
/**
 * 获取请求正文，一个名称对应一个值。								没有使用确认密码
 * @param request
 * @param response
 * @throws ServletException
 * @throws IOException
 */
private void test1(HttpServletRequest request, HttpServletResponse response)
    throws ServletException, IOException {
    //1.获取请求正文
    String username = request.getParameter("username");
    String password = request.getParameter("password");
    String gender = request.getParameter("gender");
    System.out.println(username+","+password+","+gender);
}
```

**getParameterValues()方法的示例代码**

```java
/**
 * 获取请求正文，一个名称可能对应多个值									使用了确认密码
 * @param request
 * @param response
 * @throws ServletException
 * @throws IOException
*/
private void test2(HttpServletRequest request, HttpServletResponse response)
    throws ServletException, IOException {
    //1.获取请求正文
    String username = request.getParameter("username");
    String[] password = request.getParameterValues("password");//当表单中有多个名称是一样时，得到是一个字符串数组
    String gender = request.getParameter("gender");
    System.out.println(username+","+Arrays.toString(password)+","+gender);
}
```

```html
<html>
<head>
	<title>login to request demo 4</title>
</head>
<body>
<form action="/day10_1122_requestresponse/RequestDemo4" method="post" enctype="multipart/form-data">
	用户名：<input type="text" name="username" /><br/>
	密码：<input type="password" name="password" /><br/>
	确认密码：<input type="password" name="password" /><br/>
	性别：<input type="radio" name="gender" value="1" checked>男
	<input type="radio" name="gender" value="0">女
	<br/>
	<input type="submit" value="注册" />
</form>
</body>
</html>
```

**getParameterNames()方法的示例代码**

```java
/**
 * 获取请求正文，一个名称一个值。但是先要获取正文名称的枚举（key的枚举）				没有使用确认密码
 * @param request
 * @param response
 * @throws ServletException
 * @throws IOException
*/
private void test3(HttpServletRequest request, HttpServletResponse response)
    throws ServletException, IOException {
    //1.获取请求正文名称的枚举
    Enumeration<String> names = request.getParameterNames();
    //2.遍历正文名称的枚举
    while(names.hasMoreElements()){
        String name = names.nextElement();
        String value = request.getParameter(name);
        System.out.println(name+":"+value);
    }
}
```





##### **小结：**

​	以上三个方法可以获取表单提交过来的请求参数。

​	参数的名称是一个字符串，参数的值可能是一个字符串，也可能是一个字符串数组。



> 掌握两种：
>
>  a: 单个获取：   request.getParameter("username");       
>
>  b: 批量获取：   request.getParameterMap();  // 获取所有提交的参数，封装到一个map集合中     ---> 推荐使用





#### 3.请求参数的封装：（掌握）

**第一种：最简单直接的封装方式**

```java
/**
 * 封装请求正文到User对象中									没有使用确认密码
 * @param request
 * @param response
 * @throws ServletException
 * @throws IOException
 */
private void test4(HttpServletRequest request, HttpServletResponse response)
    throws ServletException, IOException {
    //1.获取请求正文
    String username = request.getParameter("username");
    String password = request.getParameter("password");
    String gender = request.getParameter("gender");
    //2.创建一个User对象
    User user = new User();
    System.out.println("封装前："+user.toString());
    //3.把请求正文封装到user对象中
    user.setUsername(username);
    user.setPassword(password);
    user.setGender(gender);
    System.out.println("封装后："+user.toString());
}
```

此时，帮我们写好这段封装代码的是apache软件基金会，我们前面学习的tomcat也是它提供的。它里面有一个开源工具包集合commons，里面有很多开源工具类，今天我们就来讲解第一个：<font color='red'><b>commons-beanutils</b></font>。

**第二种：使用apache的commons-beanutils实现封装**

实现代码：

```java
/**
 * 终极方法：使用beanutils实现请求正文封装到javabean中				使用了确认密码
 * 要想使用beanutils，需要先导包
 * @param request
 * @param response
 * @throws ServletException
 * @throws IOException
 */
private void test8(HttpServletRequest request, HttpServletResponse response)
    throws ServletException, IOException {
    Users user = new Users();
    System.out.println("封装前："+user.toString());
    try{
        BeanUtils.populate(user, request.getParameterMap());//就这一句话
    }catch(Exception e){
        e.printStackTrace();
    }
    System.out.println("封装后："+user.toString());
}
```







##### 小结：

> 掌握两种：
>
> ​		a. 手动封装     （user.setXxx）
>
> ​		b. 工具类封装（BeanUtils.populate）  --- > 推荐使用



![image-20201028143019748](/实训/req/image-20201028143019748.png)









#### 4.请求乱码问题的解决：（掌握）

> 在tomcat7及以前，不管get/ post请求参数包含中文时，都是有乱码的， 但是从tomcat8开始，默认编码采用了utf-8，所以get请求就没有乱码了！
>
> 但是我们我们还是需要处理post请求的中文乱码！



##### 问题展示：

![image-20201028144606133](/实训/req/image-20201028144606133.png)



##### 解决方案：

> 因为post默认不是u8编码，所以需要在获取参数前，设置编码！
>
> ```java
> request.setCharacterEncoding("UTF-8");
> ```

![image-20201028145243276](/实训/req/image-20201028145243276.png)









#### 5.域对象 （理解）

> 【回顾域对象】：
>
> 什么是域对象？  --- 可以共享数据的对象！
>
> servletContext : 最大的域对象， 整个web项目就一个， 可以在整个项目范围内都可以存取数据。  --- ==共享范围： 整个项目的每个servlet每次请求都共享==

**域对象的存取值方法：**

```java
// 所有的域对象都有这三个方法，分别对应是 数据的存 、 取、 删除
.setAttribute();   // 设置  和 修改
.getAttribute();
.removeAttribute();
```



**request对象本身其实也是一个域对象，也可以用于在一次请求范围内存取数据！   -----  **==共享范围： 一次请求==

>什么叫一次请求：   从客户端发起的一个表单提交、一次页面刷新，都可以是一次请求 （如果再次访问，都是一个新的请求）；



##### 小结：

>**域对象目前学了有：**
>
>**servletContext** ： 只有一个；   --- ==共享范围： 整个项目==
>
>​					---eg:    servlet01 存， 其他servlet都能取到；
>
>**request**：  有无数个，每一次请求都是一个新的request；   -----  ==共享范围： 一次请求==
>
>​					----eg:    servlet01 存，只有 servlet01能取， 其他servlet都不能取到；  （请求转发例外）







#### 6.请求转发：forward （掌握）

> 一次请求的作用范围很小， 默认来说，一次请求只能让一个servlet执行，只能当前servlet获取到参数；
>
> 转发可以实现： 一次请求，让两个甚至多个servlet执行， 并且参数是可以共享的！

![image-20201028161235467](/实训/req/image-20201028161235467.png)

##### 如何使用：

```java
 // 实现请求转发：
req.getRequestDispatcher("/testRequest05").forward(req,resp);
```



##### **请求转发的特点：**

- 浏览器地址栏不变
- request域对象中的数据不丢失， 可以共享
- 如果在转发中有多个response进行响应，以最后一个被转发的response为准（后面的）



##### 请求转发执行的顺序：

![image-20201028160307721](/实训/req/image-20201028160307721.png)

<img src="/实训/req/image-20201028160415972.png" alt="image-20201028160415972" style="zoom:80%;" />









## 四.response响应对象：



#### 1.常见状态码：（记住）

<img src="/实训/req/image-20201028163203249.png" alt="image-20201028163203249" style="zoom:80%;" />









#### 2.响应数据到浏览器：（重要）

##### 响应文本数据

**如何使用：**

```java
 // 响应一句话到浏览器：
response.getWriter().write("你好 ~~ hello ~~ 123");
```



**如何避免中文乱码问题：**

```java
 //解决响应中文乱码
 response.setContentType("text/html; charset=utf-8");
```



![image-20201028165211930](/实训/req/image-20201028165211930.png)





#### 3.设置响应消息头定时刷新

```java
/**
 * 设置响应消息头：
 * 通过定时刷新演示添加消息头
 * @author 黑马程序员
 * @Company http://www.itheima.com
 *
 */
public class ResponseDemo5 extends HttpServlet {

    public void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        String str = "用户名和密码不匹配，2秒后转向登录页面...";
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        out.write(str);
        //定时刷新，其实就是设置一个响应消息头
        response.setHeader("Refresh", "2;URL=/login.html");//Refresh设置的时间单位是秒，如果刷新到其他地址，需要在时间后面拼接上地址
    }

    public void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        doGet(request, response);
    }

}
```

![ResponseDemo5](/实训/req/ResponseDemo5.png)





##### 小结：

> 定时刷新，后期可以结合js一起完成
>
> ```
> *       resp.setContentType: 告诉客户端浏览器，以何种方式解析内容；
> *
> *       resp.setHeader: 设置响应头，告诉浏览器应该做什么
> *
> *       resp.setHeader("Refresh","10;URL=/index.html");  //告诉浏览器，在10秒后，自动请求URL指定的资源
> ```









#### 4.重定向：redirect（重要）

> **思考：**
>
> ​		重定向后， 请求次数有几次？
>
> ​		重定向后，携带的参数是否可以共享？
>
> ​        重定向后，浏览器的地址栏是否有变化？

浏览器端-》servlet1-》servlet2

##### 如何使用：

```java
 // 重定向 到项目内部 testRedirect02
 response.sendRedirect("testRedirect02");

 // 演示跨服务器: 重定向到百度
response.sendRedirect("https://www.baidu.com/");
```



##### 重定向执行顺序：

![image-20201028190727467](/实训/req/image-20201028190727467.png)





##### 重定向和转发的区别：（面试常问）

- 请求次数
- 参数是否共享
- 地址栏是否变化
- 是否可以跨服务器







#### 5.响应和消息头组合应用-文件下载

首先，在工程的web目录下新建一个目录uploads，并且拷贝一张图片到目录中，如下图所示：

![文件下载的图片](/实训/req/文件下载的图片.png)

文件下载的Servlet代码如下：

```java
/**
 * 文件下载
 */
public class ResponseDemo8 extends HttpServlet {

    public void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        /*
         * 文件下载的思路：
         * 		1.获取文件路径
         * 		2.把文件读到字节输入流中
         * 		3.告知浏览器，以下载的方式打开（告知浏览器下载文件的MIME类型）
         * 		4.使用响应对象的字节输出流输出到浏览器上
         */
        //1.获取文件路径（绝对路径）
        ServletContext context = this.getServletContext();
        String filePath = context.getRealPath("/uploads/6.jpg");//通过文件的虚拟路径，获取文件的绝对路径
        //2.通过文件路径构建一个字节输入流
        InputStream in  = new FileInputStream(filePath);
        //3.设置响应消息头
        response.setHeader("Content-Type", "application/octet-stream");//注意下载的时候，设置响应正文的MIME类型，用application/octet-stream
        response.setHeader("Content-Disposition", "attachment;filename=1.jpg");//告知浏览器以下载的方式打开
        //4.使用响应对象的字节输出流输出
        OutputStream out = response.getOutputStream();
        int len = 0;
        byte[] by = new byte[1024];
        while((len = in.read(by)) != -1){
            out.write(by, 0, len);
        }
        in.close();
    }

    public void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        doGet(request, response);
    }

}
```

![ResponseDemo8](/实训/req/ResponseDemo8.png)







##### 小结：

> 在做下载时，如果时浏览器识别的类型，会默认打开，需要指定响应头 attachment，标识为以附件形式下载；
>
> 如果是浏览不支持打开的类型，默认其实就是下载！
>
> 所以，以后做下载： 
>
> 1.使用响应输出字节流；
>
> 2.直接超链接指定资源，也可以完成下载；

```html
<h3>文件下载:</h3>
<a href="/download/Mojito%20-%20周杰伦.mp4">Mojito高清MV下载</a>  <br>  
<a href="/download/最新阿凡达3D.zip"> 高清无码最新阿凡达3D</a>

```











# 今日任务安排：

1. 练习今天标注的重点代码
2. 理解今天标了重要和掌握的内容
3. 预习后面的内容：

![image-20201028192622769](/实训/req/image-20201028192622769.png)




