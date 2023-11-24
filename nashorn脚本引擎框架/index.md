# Nashorn脚本引擎框架


<!--more-->

### Nashorn探索背景

​	通过合理的设计，将应用程序中所有动态变量都配置化，可以最大程度上让程序变得灵活。而灵活的配置，既可以减少开发成本，又能提高交付的效率。
提到业务规则的可配置化技术，自然是首推规则引擎了，像drools，easy rule 都是比较优秀的开源规则引擎，他们可以通过特定的语法，将if esle等判断逻辑从代码中独立出来，甚至可以热更新规则，非常的成熟好用。
​	只不过，有些时候，我们可能并不需要规则引擎的所有完整的功能，也不想引入一堆依赖的包，我们只是想要做一些简单的逻辑配置工作，这个时候，Nashorn脚本引擎就可以纳入考虑的范围了。

### Nashorn简介

​	Nashorn 最初是在 JDK 8 中引入的，用于取代 Rhino 脚本引擎。当其发布时，Nashorn 是 ECMAScript-262 5.1 的完整实现，增强了 Java 和 JavaScript 的兼容性。
​	借助 Nashorn，开发人员可以从 JavaScript 调用 Java 代码，也可以从 Java 代码调用 JavaScript 函数。Nashorn 可以作为 Java 应用程序的嵌入式解释器，提供使用 Nashorn 命令行工具 jjs 从命令行运行 JavaScript 的能力。当在 Java 中对 JavaScript 代码求值时，Nashorn 实现了javax.script API。

### Nashorn语法一览：

​	Nashorn使用的都是原生的java和js语法，并没有什么上手门槛，需要注意的是，js语法并不支持ES6。下面我们简单的看一下如何使用它。

#### hello js in JVM

​	Nashorn是JDK中自带的包，并不需要引入额外的依赖，只不过要jdk1.8的版本才支持。它通过在JVM上以原生方式运行动态JavaScript代码的方式来扩展java的功能。
首先，我们来感受一下在jvm运行js的骚操作：

```java
 ScriptEngine scriptEngine = new ScriptEngineManager().getEngineByName("nashorn");
 scriptEngine.eval("print('hello js in jvm')");
```

​	这里可以直接写js代码，也可以读取特定的文件中的js代码，也就是说，这里的js脚本，我们是可以在前端灵活配置的，这样就达到了我们随心所欲配置规则的目的了。

#### 在JVM在中调用js函数

Nashorn支持调用js的函数，我们先在脚本中定义函数，再到JVM

```js
var sayHi = function(name) {
    print('hello js,my name is  ' + name);
    return "result from js";
};
```

调用方法：

```java
ScriptEngine engine = new ScriptEngineManager().getEngineByName("nashorn");
engine.eval(new FileReader("script.js"));
Invocable invocable = (Invocable) engine;
Object result = invocable.invokeFunction("sayHi", "JAVA");
System.out.println(result);

//此处为调用控制台返回值：
//hello js,my name is  JAVA
//result from js
```

#### 在js中调用java

反过来，也可以在js中调用java的方法也很简单：
先定义静态方法

```java
   static String sayHiByJava(String name) {
        System.out.format("hello java,my name is  %s", name);
        return "result from java";
    }
```


注意，调用时，需要先使用Java.type引入java class，类似于java中import

```java
var MyJavaClass = Java.type('com.lx.soil.demos.leetcode.Solution');
var result = MyJavaClass.sayHiByJava('js');
print(result);

//此处为调用控制台返回值：
//hello java,my name is  js
//result from java
```

### 使用Nashorn的几种安全机制：

既然是可以支持动态传入脚本来运行js代码，而且可以调用java中的类，那么安全问题就是我们所必须要注意的了，在安心使用Nashorn前，下面两个点是必须要解决的：

首先是防止任意代码执行的漏洞
防止代码有逻辑错误死循环导致占用大量资源
好在，现在有两个比较靠谱的方案来解决这些问题：

#### 使用ClassFilter限制js引擎可以访问的类

1.在jdk1.8中引入了ClassFilter来限制可以访问的类，只要限制了可以访问的类，那么代码任意执行的漏洞就可以解决了，但是这个还不完美，因为还有第二个问题没解决。

```java
import javax.script.ScriptEngine;
import jdk.nashorn.api.scripting.ClassFilter;
import jdk.nashorn.api.scripting.NashornScriptEngineFactory;

public class MyClassFilterTest {

  class MyCF implements ClassFilter {
    @Override
    public boolean exposeToScripts(String s) {
      if (s.compareTo("java.io.File") == 0) return false;
      return true;
    }
  }

  public void testClassFilter() {

    final String script =
      "print(java.lang.System.getProperty(\"java.home\"));" +
      "print(\"Create file variable\");" +
      "var File = Java.type(\"java.io.File\");";

    NashornScriptEngineFactory factory = new NashornScriptEngineFactory();

    ScriptEngine engine = factory.getScriptEngine(
      new MyClassFilterTest.MyCF());
    try {
      engine.eval(script);
    } catch (Exception e) {
      System.out.println("Exception caught: " + e.toString());
    }
  }

  public static void main(String[] args) {
    MyClassFilterTest myApp = new MyClassFilterTest();
    myApp.testClassFilter();
  }
}

```


执行完则会报错：

```
C:\Java\jre8
Create file variable
Exception caught: java.lang.RuntimeException: java.lang.ClassNotFoundException:
java.io.File
```

#### Nashorn沙箱

事实上，Nashorn本身就考虑了安全问题，实现了一套沙箱机制(SandBox),用法如下：

```java
NashornSandbox sandbox = NashornSandboxes.create(); 
sandbox.allow(File.class);
sandbox.eval("var File = Java.type('java.io.File'); File;")
```


限制nashorn引擎的资源使用也有考虑：

```
NashornSandbox sandbox = NashornSandboxes.create();
sandbox.setMaxCPUTime(100);
sandbox.setMaxMemory(50*1024);
sandbox.allowNoBraces(false);
sandbox.setMaxPreparedStatements(30); // because preparing scripts for execution is expensive
sandbox.setExecutor(Executors.newSingleThreadExecutor());
sandbox.eval("var o={}, i=0; while (true) {o[i++]='abc';};");
```

### Nashorn沙箱的实现原理浅析：

java应用程序本身就是自带沙箱的，只不过一般都没有被启用，要启用沙箱，在启动的命令行要加如下参数：

```bash
java -Djava.security.manager <other args>
```

沙箱启动后，安全管理器会使用两个默认的策略文件来确定沙箱启动参数。当然也可以通过命令指定：

```bash
java -Djava.security.policy=<URL>
```

如果要求启动时只遵循一个策略文件，那么启动参数要加个等号，如下：

```bash
java -Djava.security.policy==<URL>
```

关于java的安全沙箱，这里有一篇比较完整的介绍文章，有兴趣的同学可以看看。
java安全沙箱

写在最后
本来Nashorn是有计划支持ES6的，但是由于对ES6的支持太过困难，导致oracle放弃了这一想法（手动表示遗憾。。。），甚至打算弃用Nashorn，所以最后在我的项目中并没有使用Nashorn引擎，而是选用了抽象的方式去迂回实现我所需要的功能，但是，Nashorn确实给我提供了一种思路，一种能让我在终极可配置化的道路上走的更远的思路，一种能让我离"write once， run forevre"的终极目标更近一小步的思路。

