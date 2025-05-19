# 工厂方法模式


<!--more-->

## 模式动机

![image-20210416220937685.png](/posts/设计模式/工厂方法模式/image-20210416220937685.png)

## 模式定义

- 工厂方法模式(Factory Method Pattern)简称工厂模式，也叫虚拟构造器(Virtual Constructor)模式或者多态工厂(Polymorphic Factory)模式，它属于类创建型模式。

- 在工厂方法模式中，工厂父类负责定义创建产品对象的公共接口，而工厂子类则负责生成具体的产品对象，这样做的目的是将产品类的实例化操作延迟到工厂子类中完成，即通过工厂子类来确定究竟应该实例化哪一个具体产品类。

  ![image-20210416221137917.png](/posts/设计模式/工厂方法模式/image-20210416221137917.png)



## 模式分析

- 工厂方法模式是简单工厂模式的进一步抽象和推广
- 工厂方法模式保持了简单工厂模式的优点，并克服了它的缺点
- 核心的工厂类不再负责所有产品的创建，而是将具体创建工作交给其子类去完成
- 可以允许系统在不修改工厂角色的情况下引进新产品
- 增加具体产品-->增加具体工厂，符合“开闭原则”

## 代码示例

将原有的电视机工厂进行分割，为每种品牌的电视机提供一个子工厂，海尔工厂专门负责生产海尔电视机，海信工厂专门负责生产海信电视机，如果需要生产TCL电视机或创维电视机，只需要对应增加一个新的TCL工厂或创维工厂即可，原有的工厂无须做任何修改，使得整个系统具有更加的灵活性和可扩展性。

![image-20210416221516090.png](/posts/设计模式/工厂方法模式/image-20210416221516090.png)

抽象产品接口：

```java
public interface TV {
    public void play();
}
```

具体产品类：

```java
public class HairTV implements TV {
    @Override
    public void play() {
        System.out.println("海尔电视播放中、、、、、");
    }
}
```

```java
public class HisenseTV implements TV {
    @Override
    public void play() {
        System.out.println("海信电视播放中、、、");
    }
}
```

抽象工厂类：

```java
public interface TVFactory {
    public TV produceTV();
}
```

具体工厂类:

```java
public class HisenseTVFactory implements TVFactory {

    @Override
    public TV produceTV() {
        System.out.println("海信电视机工厂生产海信电视机。");
        return new HisenseTV();
    }
}
```

```java
public class HairTVFactory implements TVFactory {

    @Override
    public TV produceTV() {
        System.out.println("海尔电视机工厂生产海尔电视机。");
        return new HairTV();
    }
}
```

辅助工具类：

```java
public class XMLUtilTV {
    public static Object getType(){
        try{
            DocumentBuilderFactory documentBuilderFactory=DocumentBuilderFactory.newInstance();
            DocumentBuilder documentBuilder=documentBuilderFactory.newDocumentBuilder();
            Document document;
            document= documentBuilder.parse(new File("config.xml"));

            NodeList nodeList=document.getElementsByTagName("type");
            Node classNode=nodeList.item(0).getFirstChild();
            String string=classNode.getNodeValue().trim();
            System.out.println(string);
            Class c=Class.forName("io.hubu8.com."+string);
            Object obj=c.newInstance();
            return obj;

        }catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }
}
```

xml文件：

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>

<config>
    <type>HairTVFactory</type>
</config>
```

客户端：

```java
public class Client {
    public static void main(String args[]) {

        try {
            TV tv;
            TVFactory factory=(TVFactory) XMLUtilTV.getType();
            System.out.println(factory);
            tv=factory.produceTV();
            tv.play();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

```shell
HisenseTVFactory
io.hubu8.com.HisenseTVFactory@1540e19d
海信电视机工厂生产海信电视机。
海信电视播放中、、、
```

如果需要新增一种类型的电视，只需要新增一个具体产品类和一个具体工厂类：

```java
public class TCLTV implements TV {
    @Override
    public void play() {
        System.out.println("TCL电视正在播放中、、、");
    }
}
```

```java
public class TCLTVFactory implements TVFactory {
    @Override
    public TV produceTV() {
        System.out.println("TCL电视工厂类正在生产");
        return new TCLTV();
    }
}
```



## 优点

- 工厂方法用来创建客户所需要的产品，同时还向客户隐藏了哪种具体产品类将被实例化这一细节
- 能够让工厂自主确定创建何种产品对象，而如何创建这个对象的细节则完全封装在具体工厂内部
- 在系统中加入新产品时，完全符合开闭原则

## 缺点

- 系统中类的个数将成对增加，在一定程度上增加了系统的复杂度，会给系统带来一些额外的开销
- 增加了系统的抽象性和理解难度

## 应用场景

- 客户端不知道它所需要的对象的类（客户端不需要知道具体产品类的类名，只需要知道所对应的工厂即可，具体产品对象由具体工厂类创建）
- 抽象工厂类通过其子类来指定创建哪个对象



**闹眼子呢？**

```shell
java.lang.ClassNotFoundException: HisenseTVFactory
	at java.net.URLClassLoader.findClass(URLClassLoader.java:382)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:418)
	at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:355)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:351)
	at java.lang.Class.forName0(Native Method)
	at java.lang.Class.forName(Class.java:264)
	at io.hubu8.com.XMLUtilTV.getType(XMLUtilTV.java:24)
	at io.hubu8.com.Client.main(Client.java:8)
null
java.lang.NullPointerException
	at io.hubu8.com.Client.main(Client.java:11)

Process finished with exit code 0
```

需要在类名前面加上包名：

```java
Class c=Class.forName("io.hubu8.com."+string);
```


