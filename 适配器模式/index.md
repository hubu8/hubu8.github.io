# 适配器模式


<!--more-->

## 模式动机

![image-20210501153039285](/desigh_images/image-20210501153039285.png)

- 现实生活：

不兼容：生活用电220V ßà 笔记电脑20V

引入 AC Adapter（交流电适配器）

- 软件开发：

存在不兼容的结构，例如方法名不一致

引入适配器模式

## 模式定义

- 适配器模式(Adapter Pattern) ：将一个接口转换成客户希望的另一个接口，适配器模式使接口不兼容的那些类可以一起工作。

- 适配器模式既可以作为类结构型模式，也可以作为对象结构型模式

- 定义中所提及的接口是指广义的接口，它可以表示一个方法或者方法的集合

  类适配模式

![image-20210501153210990](/desigh_images/image-20210501153210990.png)

对象适配模式：

![image-20210501163517652](/desigh_images/image-20210501163517652.png)

## 模式分析

1. 典型的类适配器示例代码：

   ```java
   public class Adapter extends Adaptee implements Target {public void request() {
   	super.specificRequest();
   }
   ```

2. 典型的对象适配器示例代码

   ```java
   public class Adapter extends Target {
   	private Adaptee adaptee;//维持一个对适配者对象的引用
       public Adapter(Adaptee adaptee){
   		this.adaptee=adaptee;
   	}
   	public void request() {
   		adaptee.specificRequest();//转发调用
       }
   }
   ```

## 优点

- 将目标类和适配者类解耦，通过引入一个适配器类来重用现有的适配者类，无须修改原有结构
- 增加了类的透明性和复用性，提高了适配者的复用性，同一个适配者类可以在多个不同的系统中复用
- 灵活性和扩展性非常好
- 类适配器模式：置换一些适配者的方法很方便
- 对象适配器模式：可以把多个不同的适配者适配到同一个目标，还可以适配一个适配者的子类

## 缺点

- 类适配器模式：

   (1) 一次最多只能适配一个适配者类，不能同时适配多个适配者

​		(2) 适配者类不能为最终类

​		(3) 目标抽象类只能为接口，不能为类

- 对象适配器模式：在适配器中置换适配者类的某些方法比较麻烦

## 应用场景

- 系统需要使用一些现有的类，而这些类的接口不符合系统的需要，甚至没有这些类的源代码

- 创建一个可以重复使用的类，用于和一些彼此之间没有太大关联的类，包括一些可能在将来引进的类一起工作



## 应用实例

4-2.现有一个接口DataOperation定义了排序方法sort(int[])和查找方法search(int[],int)，已知类QuickSort的quickSort(int[])方法实现了快速排序算法，类BinarySearch的binarySearch(int[],int)方法实现了二分查找算法。现使用适配器模式设计一个系统，在不修改源代码的情况下，将QuickSort和类BinarySearch的方法适配到接口DataOperation中，绘制类图并实现。

![image-20210501154813927](/desigh_images/image-20210501154813927.png)

代码：

```java
public interface DataOperation {
    public void sort(int data[]);//排序方法
    public void search(int[] list,int key);//查找方法

}
```

```java
public class QuickSort {
    //快速排序
    public void quickSort(int data[]) {
        java.util.Arrays.sort(data);	//直接调用排序方法
        print(data);
    }
    //数组输出

    public void print(int data[]) {
        for(int x=0;x<data.length;x++) {
            System.out.print(data[x]+"、");
        }
    }

}
```

```java
public class BinarySearch {
    // 二分查找
    public void binarySearch(int[] list,int key) {
        // 调用java.util.Arrays的binarySearch二分查找方法
        System.out.print(java.util.Arrays.binarySearch(list, key));
    }

}

```

```java
public class DataAdapter implements DataOperation {
    private QuickSort quicksort; // 定义适配者QuickSort的对象
    private BinarySearch binarysearch; // 定义适配者 BinarySearch的对象
    // 构造函数初始化适配者对象

    public DataAdapter() {
        quicksort = new QuickSort(); // 实例化QuickSort对象
        binarysearch = new BinarySearch(); // 实例化 BinarySearch对象
    }

    @Override
    public void sort(int data[]) {
        // 建立连接实现快速排序
        quicksort.quickSort(data);// 通过对象调用实现快速排序的功能

    }

    @Override
    public void search(int[] list, int key) {
        // 建立连接实现二分查找
        binarysearch.binarySearch(list, key);// 通过对象调用实现二分查找的功能

    }

}
```

```java
public class Client {

    public static void main(String[] args) {
        // TODO 自动生成的方法存根
        //定义一个数组
        int data[]=new int[] {2,4,1,5,3,8,6,7};
        DataOperation operation;	//定义接口对象
        operation=new DataAdapter();	//	实例化接口对象向上转型
        System.out.println("排序前的数组：");
        for(int i=0;i<data.length;i++) {
            System.out.print(data[i]+"、");
        }

        System.out.println("\n\n排序后的数组");
        operation.sort(data);//调用排序
        //查找数据4所在的下表
        System.out.println("\n\n输出数字4所在的索引:");
        operation.search(data,4);//调用查找

    }

}
```

结果：

![image-20210501155521888](/desigh_images/image-20210501155521888.png)
