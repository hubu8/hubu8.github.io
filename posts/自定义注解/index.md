# 自定义注解


<!--more-->

## 1、简介

注解是一种能被添加到java源代码中的元数据，方法、类、参数和包都可以用注解来修饰。注解可以看作是一种特殊的标记，可以用在方法、类、参数和包上，程序在编译或者运行时可以检测到这些标记而进行一些特殊的处理。

## 2、元注解

元注解的作用就是负责注解其他注解。Java5.0定义了4个标准的meta-annotation类型，它们被用来提供对其它 annotation类型作说明。Java5.0定义的元注解：

```java
@Target,
@Retention,
@Documented,
@Inherited
```


下面我们看一下每个元注解的作用和相应分参数的使用说明。

### 2.1、@Target

表明该注解可以应用的java元素类型：

| Target类型                  | 描述                                                         |
| --------------------------- | ------------------------------------------------------------ |
| ElementType.TYPE            | 应用于类、接口（包括注解类型）、枚举                         |
| ElementType.FIELD           | 应用于属性（包括枚举中的常量）                               |
| ElementType.METHOD          | 应用于方法                                                   |
| ElementType.PARAMETER       | 应用于方法的形参                                             |
| ElementType.CONSTRUCTOR     | 应用于构造函数                                               |
| ElementType.LOCAL_VARIABLE  | 应用于局部变量                                               |
| ElementType.ANNOTATION_TYPE | 应用于注解类型                                               |
| ElementType.PACKAGE         | 应用于包                                                     |
| ElementType.TYPE_PARAMETER  | 应用于类型变量                                               |
| ElementType.TYPE_USE        | 应用于任何使用类型的语句中（例如声明语句、泛型和强制转换语句中的类型） |

### 2.2、@Retention

表明该注解的生命周期

| 生命周期类型            | 描述                                             |
| ----------------------- | ------------------------------------------------ |
| RetentionPolicy.SOURCE  | 编译时被丢弃，不包含在类文件中                   |
| RetentionPolicy.CLASS   | JVM加载时被丢弃，包含在类文件中，默认值          |
| RetentionPolicy.RUNTIME | 由JVM 加载，包含在类文件中，在运行时可以被获取到 |

### 2.3、@Document

表明该注解标记的元素可以被Javadoc 或类似的工具文档化。

### 2.4、@Inherited

表明使用了@Inherited注解的注解，所标记的类的子类也会拥有这个注解。

## 3、自定义注解

修饰符： 访问修饰符必须为public,不写默认为pubic；
关键字： 关键字为@interface；
注解名称： 注解名称为自定义注解的名称，使用时还会用到；
注解内容： 注解中内容，对注解的描述。

### 3.1 自定义注解

```java
@Documented
@Inherited
@Target({ ElementType.FIELD, ElementType.METHOD ,ElementType.TYPE})  //可以在字段、枚举的常量、方法
@Retention(RetentionPolicy.RUNTIME)
public @interface Init {
     String value() default "";
}
```

### 3.2 数据模型使用注解

```java
public class User {
    private String name;
    private String age;
    public String getName() {
        return name;
    }

    @Init("louis")
    public User setName(String name) {
        this.name = name;
        return this;
    }

    public String getAge() {
        return age;
    }

    @Init("22")
    public User setAge(String age) {
        this.age = age;
        return this;
    }
}
```

### 3.3 定义一个“注解解析器”

```java
public class userFactory {
   public static User create() {
        User user = new User();
        // 获取User类中所有的方法（getDeclaredMethods也行）
        Method[] methods = User.class.getMethods();
        try
        {
            for (Method method : methods)
            {
                // 如果一个注解指定注解类型是存在于此元素上此方法返回true，否则返回false
                //参数 -- 对应于注解类型的Class对象
                if (method.isAnnotationPresent(Init.class))
                {
                    //此方法返回该元素的注解在此元素的指定注释类型（如果存在），否则返回null
                    Init init = method.getAnnotation(Init.class);
                    // 如果Method代表了一个方法 那么调用它的invoke就相当于执行了它代表的这个方法,在这里就是给set方法赋值
                    method.invoke(user, init.value());
                }
            }
        }
        catch (Exception e)
        {
            e.printStackTrace();
            return null;
        }
        return user;
   }
}
```

### 3.4 运行的代码

   ```java
public static void main(String[] args) {
        User user = userFactory.create();
        user.setAge("30");
        System.out.println(user.getName());
        System.out.println(user.getAge());
    }
   ```

### 3.5 结果
