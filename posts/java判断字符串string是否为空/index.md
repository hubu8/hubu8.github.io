# Java判断字符串String是否为空


<!--more-->

**思路：判断某字符串是否为空，为空的标准是str==null或str.length()==0**



### 一.空字符串与null区别

#### 1、类型

null表示的是一个对象的值，而并不是一个字符串。例如声明一个对象的引用，String a = null ;
""表示的是一个空字符串，也就是说它的长度为0。例如声明一个字符串String str = “” ;

#### 2、内存分配

String str = null ; 表示声明一个字符串对象的引用，但指向为null，也就是说还没有指向任何的内存空间；
String str = “”; 表示声明一个字符串类型的引用，其值为""空字符串，这个str引用指向的是空字符串的内存空间；
在java中变量和引用变量是存在栈中（stack），而对象（new产生的）都是存放在堆中（heap）：
就如下：

String str = new String("yuge") ;
=左边的是存放在栈中（stack），=右边是存放在堆中（heap）

#### 3、字符串对象与null的值不相等，且内存地址也不相等；

空字符串对象与null的值不相等，且内存地址也不相等；
new String()创建一个字符串对象的默认值为"" （String类型成员变量的初始值为null）

### 二.Java判断字符串常见方法

方法一: 最多人使用的一个方法, 直观, 方便, 但效率很低:

```java
if(str == null || str .equals(""));
```

方法二: 比较字符串长度, 效率高, 是我知道的最好一个方法:

```java
if(str == null || str .length() == 0);
```

方法三: Java SE 6.0 才开始提供的方法, 效率和方法二几乎相等, 但出于兼容性考虑, 推荐使用方法二.

```java
if(str == null || str .isEmpty());
```

方法四: 这是一种比较直观,简便的方法,而且效率也非常的高,与方法二、三的效率差不多:

```java
if (str == null || str == "");
```

注意:str == null 是有必要存在的.

如果 String 类型为null, 而去进行 equals(String) 或 length() 等操作会抛出java.lang.NullPointerException.

并且str ==null 的顺序必须出现在前面，不然同样会抛出java.lang.NullPointerException.

### 三、StringUtils的isBlank与isEmply

#### 1.public static boolean isEmpty(String str)

判断某字符串是否为空，为空的标准是 str==null 或 str.length()==0

#### 2.public static boolean isNotEmpty(String str)

判断某字符串是否非空，等于 !isEmpty(String str)

#### 3.public static boolean isBlank(String str)

判断某字符串是否为空或长度为0或由空白符(whitespace) 构成

#### 4.public static boolean isNotBlank(String str)

判断某字符串是否不为空且长度不为0且不由空白符(whitespace) 构成，等于 !isBlank(String str)

