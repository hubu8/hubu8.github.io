# 桥接模式


<!--more-->

## 模式动机

![image-20210511224812621](/desigh_images/image-20210511224812621.png)

**蜡笔：**颜色和型号两个不同的变化维度（即两个不同的变化原因）耦合在一起，无论是对颜色进行扩展还是对型号进行扩展都势必会影响另一个维度

**毛笔：**颜色和型号实现了分离，增加新的颜色或者型号对另一方没有任何影响

![image-20210511224844453](/desigh_images/image-20210511224844453.png)

## 模式定义

- 桥接模式(Bridge Pattern)：将抽象部分与它的实现部分分离，使它们都可以独立地变化。
- 对象结构型模式，又称为柄体(Handle and Body)模式或接口(Interface)模式
- 用抽象关联取代了传统的多层继承
- 将类之间的静态继承关系转换为动态的对象组合关系

## 模式结构

![image-20210511224916353](/desigh_images/image-20210511224916353.png)

## 模式分析

![image-20210511224941157](/desigh_images/image-20210511224941157.png)

模拟毛笔：实例说明

•现需要提供大中小3种型号的画笔，能够绘制5种不同颜色，如果使用蜡笔，我们需要准备3*5=15支蜡笔，也就是说必须准备15个具体的蜡笔类。而如果使用毛笔的话，只需要3种型号的毛笔，外加5个颜料盒，用3+5=8个类就可以实现15支蜡笔的功能。本实例使用桥接模式来模拟毛笔的使用过程。

![image-20210511225010570](/desigh_images/image-20210511225010570.png)

### 代码

```java
package com.bridge;

public interface Color {
	public void paint(String penType);
}

```

```java
package com.bridge;

public class Blue implements Color {

	@Override
	public void paint(String penType) {
		// TODO Auto-generated method stub
		System.out.println("使用"+penType+"涂蓝色");
	}

}
```

```java
package com.bridge;

public abstract class Pen {
	protected Color color;

	public void setColor(Color color) {
		this.color = color;
	}
	
	public abstract void draw();
}

```

```java
package com.bridge;

public class SmallPen extends Pen {

	@Override
	public void draw() {
		// TODO Auto-generated method stub
		System.out.println("准备用小号毛笔画画：");
		color.paint("小号毛笔");
	}

}

```

```java
package com.bridge;

import java.io.File;

import javax.xml.parsers.*;

import org.w3c.dom.*;

public class XMLUtil {
	public static Object getBean(String args)
	{
		try
		{
			//创建DOM文档对象
			DocumentBuilderFactory dFactory=DocumentBuilderFactory.newInstance();
			DocumentBuilder builder=dFactory.newDocumentBuilder();
			Document doc=builder.parse(new File("src/com/bridge/config.xml"));
			
			//获取包含类名的文本节点
			NodeList nl=doc.getElementsByTagName("className");
			Node classNode=null;	//nl.item(0).getFirstChild();
			String cName=null;	//classNode.getNodeValue();

			if(args.equals("color"))
			{
				classNode=nl.item(0).getFirstChild();
			}
			else if(args.equals("pen"))
			{
				classNode=nl.item(1).getFirstChild();
			}
			cName=classNode.getNodeValue();
			
			//通过类名生成实例对象并返回
			Class c=Class.forName(cName);
			Object obj=c.newInstance();
			return obj;
		}
		catch(Exception ex)
		{
			ex.printStackTrace();
			return null;
		}
	}
}

```

```xml
<?xml version="1.0"?>
<config>
	<className>com.bridge.Green</className>
	<className>com.bridge.BigPen</className>
</config>
```

```java
package com.bridge;

public class Client {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		Color color=(Color)XMLUtil.getBean("color");
		Pen pen=(Pen)XMLUtil.getBean("pen");
		pen.setColor(color);
		pen.draw();
	}

}

```



## 优点

- 分离抽象接口及其实现部分
- 可以取代多层继承方案，极大地减少了子类的个数
- 提高了系统的可扩展性，在两个变化维度中任意扩展一个维度，不需要修改原有系统，符合开闭原则

## 缺点

- 会增加系统的理解与设计难度，由于关联关系建立在抽象层，要求开发者一开始就要针对抽象层进行设计与编程
- 正确识别出系统中两个独立变化的维度并不是一件容易的事情

## 应用场景

- 需要在抽象化和具体化之间增加更多的灵活性，避免在两个层次之间建立静态的继承关系
- 抽象部分和实现部分可以以继承的方式独立扩展而互不影响
- 一个类存在两个（或多个）独立变化的维度，且这两个（或多个）维度都需要独立地进行扩展
- 不希望使用继承或因为多层继承导致系统类的个数急剧增加的系统
