# ThreadLocal多值应用


<!--more-->

### ThreadLocal特点

- ThreadLocal是一个线程内部的变量，只在本线程中使用，隔离其他线程

- ThreadLocal内部维护了一个ThreadLocalMap

- Thread内部引用了ThreadLocalMap

- ThreadLocalMap可以保存键值对，但是一个ThreadLocal只能保存一个值，并且各个线程数据互不干扰

- ThreadLocalMap存储时的key永远为当前的ThreadLocal

- ThreadLocalMap存储时的key是弱引用的
  **ThreadLocalMap**
  每个ThreadLocal只能存储一个数据，如果需要存储多个值的话，可以定义多个ThreadLocal。ThreadLocal在内部维护了一个ThreadLocalMap用来存储这些值。
  ThreadLocalMap并没有去实现Map接口，它定义了一个Entry数组，每个Entry以<key,value>的形式来保存值，其中key为当前ThreadLocal本身，value为要保存的值。

### 应用

```java
public class UploadContext {
    private boolean isCompress;
    private String suffix;

    private static final ThreadLocal<UploadContext> current;
    static {
        current = new ThreadLocal<>();
    }

    public boolean isCompress() {
        return isCompress;
    }

    public void setCompress(boolean compress) {
        isCompress = compress;
    }

    public String getSuffix() {
        return suffix;
    }

    public void setSuffix(String suffix) {
        this.suffix = suffix;
    }

    public static UploadContext getOrCreate(){
        UploadContext uploadContext = current.get();
        if(uploadContext == null){
            uploadContext = new UploadContext();
            current.set(uploadContext);
        }
        return uploadContext;
    }
}
```

### 如何使用

```java
String suffix = UploadContext.getOrCreate().getSuffix();
```

### 代码解释

一个上下文变量类，里面包含上下文业务需要的参数；

静态成员变量（所有对象共享一个本地线程变量）为ThreadLocal，getOrCreate返回的是上下文变量对象（本地线程变量值），变量对象包含参数的get与set方法，
