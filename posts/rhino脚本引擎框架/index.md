# Rhino脚本引擎框架


<!--more-->

### 一:Rhino简介

Rhino是一个完全以Java编写的JavaScript引擎，目前由Mozilla基金会所管理。Rhino项目开始于1997年，当时，Netscape计划开发Java版本的Netscape Navigator，虽然该计划最终被放弃，但是Rhino引擎被遗留了下来。1998年4月，Mozilla基金会宣布开放源代码.引擎的命名源自于欧莱礼所出版的《JavaScript: The Definitive Guide》一书的封面动物.Rhino作为JDK脚本引擎对JavaScript支持部分包含到甲骨文JDK 1.6中，但将被Nashorn继承取代包含到OpenJDK 1.8中.

### 二:Rhino资料

下载地址:
https://developer.mozilla.org/en-US/docs/Mozilla/Projects/Rhino/Download_Rhino

官网文档资料:
https://developer.mozilla.org/en-US/docs/Mozilla/Projects/Rhino
http://mozilla.github.io/rhino/javadoc/index.html

版本选用的版本: rhino1.7.6

JavaScript 引擎概念:

“JavaScript 引擎”通常被称作一种 虚拟机。“虚拟机”是指软件驱动的给定的计算机系统的模拟器。有很多类型的虚拟机，它们根据自己在多大程度上精确地模拟或代替真实的物理机器来分类。例如，“系统虚拟机”提供了一个可以运行操作系统的完整仿真平台。Mac 用户很熟悉的 Parallels 就是一个允许你在 Mac 上运行 Windows系统虚拟机。另一方面，“进程虚拟机”不具备全部的功能，能运行一个程序或者进程。Wine 是一个允许你在 Linux 机器上运行 Windows 应用的进程虚拟机，但是并不在 Linux 中提供完整的 Windows 操作系统。JavaScript 虚拟机是一种进程虚拟机，专门设计来解释和执行的 JavaScript 代码。


### 三:主要解决的问题

本文档主要介绍的Rhino的基本使,重点在介绍JavasScrip在Java中的嵌入开发时Java和JavaScript之间的相互调用.

### 四:Rhino的基本使用

#### **1.引入 Rhino JavaScript 工具包**

由于Rhino作为JDK脚本引擎对JavaScript支持部分包含到甲骨文JDK 1.6中,所以在JDK1.6及以上版本不需要单独的引入Jar包.
JDK1.6以下版本需要引入以下jar包:   **js.jar**

#### **2.构造 JavaScript 的运行环境**

Context对象中储存了脚本执行的全局环境信息.
Context cx = Context.enter()

#### **3.初始化标准对象**

scope 变量类似于构造了一个全局对象，而在整个运行过程中 JavaScript 的变量都会作为该对象的一部分.
Scriptable scope = cx.initStandardObjects();

#### **4.执行 JavaScript 脚本**

执行脚本字符串 string，当脚本中需要使用其它变量时，会在作用域 scope 中寻找所需要的变量，如果发生异常的话则会报告 error 错误信息和所在的行号 num，正确执行结束后会返回一个 Object 对象的运行结果。如果脚本字符串中没有需要执行的表达式则会返回undefined.
Object result = cx.evaluateString(scope, string, error, num, null);
静态方法Context.toString(Object result)返回该对象在JavaScript中的值.

```java
public final Object evaluateString(Scriptable scope, String source,String sourceName, int lineno, Object securityDomain)

参数解释:
Scope : 运行的作用域,运行js时会在作用域 scope 中寻找所需要的变量.
Source: 待运行的JS脚本字符串.
SourceName: 定义源码的名称,出错的时候方便定位,可以为null.
Lineno: 发生错误时显示的行号.
securityDomain: 一个指定安全性的任意对象,如果不关心线程安全是,可以为null值.
```

#### **5.结束上下文**

结束上下文是每次使用 Rhino 结束后必须要做的工作，它将移除上下文和当前的线程并做垃圾回收。在每次进入上下文后都应该调用静态方法Context.exit()退出，因为在使用过程中可能会产生异常，所以通常在调用 Context.enter() 进入上下文之后，将退出操作放入对应的 finally 块中.
Context.exit();

#### **6.在Java代码中加载外部JS文件**

在Java代码中加载外部JS文件,有两种方式,Context实例提供两个方法:

方式一:
```java
public static void testLoadJSFile() {
    Context context = Context.enter();
    LineNumberReader reader = null;
    try {
        Scriptable scope = context.initStandardObjects();
        String filename = System.getProperty("user.dir") + File.separator + "testLoadJs.js";

        reader = new LineNumberReader(new FileReader(filename));
        String temp = null;
        StringBuffer sb = new StringBuffer();
        while ((temp = reader.readLine()) != null) {
            sb.append(temp).append("\n");
        }
        context.evaluateString(scope, sb.toString(), null, 1, null);
        Object result = context.evaluateString(scope, "countNumber(10010);", "mysource", 1, null);
        System.out.println(Context.toString(result));
    } catch (Exception e) {
        e.printStackTrace();
    } finally {
        if (reader != null) {
            try {
                reader.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        Context.exit();
    }
}

```

方式二:

```java
public static void testLoadJSFile2() {
    Context context = Context.enter();
    FileReader fileReader = null;
    try {
        Scriptable scope = context.initStandardObjects();
        String filename = System.getProperty("user.dir") + File.separator + "testLoadJs.js";
        fileReader = new FileReader(filename);
        context.evaluateReader(scope, fileReader, null, 1, null);
        Object result = context.evaluateString(scope, "countNumber(10010);", "mysource", 1, null);
        System.out.println(Context.toString(result));
    } catch (Exception e) {
        e.printStackTrace();
    } finally {
        if (fileReader != null) {
            try {
                fileReader.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        Context.exit();
    }
}

```

### 五:Java 和 JavaScript 对象相互调用

#### 1.在JavaScrip中调用Java对象

**A.在JS调用Java对象(在JS代码中声明Java对象)**

```java
 public static void testCallJavaInScript() {
     Context context = Context.enter();
     Scriptable scope = context.initStandardObjects();
     try {
         context.evaluateString(scope, "java.lang.System.out.println('你好Yves!');", null, 1, null);
     } catch (Exception e) {
         e.printStackTrace();
     } finally {
         Context.exit();
     }
 }

```

**B.在JS调用Java对象(在java代码中声明Java对象)**

```java
public static void testCallJavaInScript2() {
    Context context = Context.enter();
    Scriptable scope = context.initStandardObjects();

    // 1.将java对象放置JS的作用域中
    Student students = new Student();
    students.setAge(23);
    students.setName("Yves");
    Object jsObj = Context.javaToJS(students, scope);
    ScriptableObject.putProperty(scope, "student", jsObj);

    // 2.在JS中调用放置在JS作用域中的Java对象.
    try {
        Object result1 = context.evaluateString(scope, "student.showInfo()", null, 1, null);
        Object result2 = context.evaluateString(scope, "student.showInfo('study')", null, 1, null);
        System.out.println(Context.toString(result1));
        System.out.println(Context.toString(result2));
    } catch (Exception e) {
        e.printStackTrace();
    } finally {
        Context.exit();
    }
}

```

#### 2.在Java中调用JavaScript脚本

在 Java 中若要取得 JavaScript 脚本的运行结果非常的简单.

```
evaluateReader(Scriptable scope, Reader in,String sourceName, int lineno,Object securityDomain);
evaluateString(Scriptable scope, String source,String sourceName, int lineno,Object securityDomain);
```

这两个方法返回的结果都是JavaScript 脚本运行后的结果,如果没有运行的脚本,则返回的是undefined;
有时候在项目开发中我们或许常常会希望获得一些额外的信息，比如运行过程中的一些临时信息，无法作为最终结果返回时下面就是获取运行时的一些临时信息的方式:
A.在 Java 中调用 JavaScript 脚本中的变量

```java
public static void testCallScriptInJava() {
    Context context = Context.enter();
    try {
        Scriptable scope = context.initStandardObjects();
        String jsStr = "var name = 'Yves';";
        context.evaluateString(scope, jsStr, null, 1, null);
        Object jsObject = scope.get("name", scope);
        if (jsObject == Scriptable.NOT_FOUND) {
            System.out.println("name is not defined.");
        } else {
            System.out.println("name is " + Context.toString(jsObject));
        }
    } catch (Exception e) {
    } finally {
        Context.exit();
    }
}

```

**B.在Java中调用Script中的函数**

```java
 public static void testCallScriptInJava2() {
     Context context = Context.enter();
     try {
         Scriptable scope = context.initStandardObjects();
         String jsStr = "function showHello(name) {return 'hello ' + name +'!';}";
         context.evaluateString(scope, jsStr, null, 1, null);
         Object functionObject = scope.get("showHello", scope);
         if (!(functionObject instanceof Function)) {
             System.out.println("showHello is undefined or not a function.");
         } else {
             Object args[] = { "Yves" };
             Function test = (Function) functionObject;
             Object result = test.call(context, scope, scope, args);
             System.out.println(Context.toString(result));
         }
     } catch (Exception e) {
     } finally {
         Context.exit();
     }
 }
```

