# 创建线程的三种方式


<!--more-->

https://blog.csdn.net/xiahuale/article/details/82113929?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522163452109116780271595133%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=163452109116780271595133&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-82113929.pc_search_all_es&utm_term=%E5%88%9B%E5%BB%BA%E7%BA%BF%E7%A8%8B%E7%9A%84%E5%87%A0%E7%A7%8D%E6%96%B9%E5%BC%8F&spm=1018.2226.3001.4187

```java
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.FutureTask;

public class TestThread {
    static class MyRunable implements Runnable{
        @Override
        public void run() {
            System.out.println("这是一个Runable线程，执行了run方法，没有返回值，不能抛出异常");
        }
    }
    static class MyCallable implements Callable<String>{

        @Override
        public String call() throws Exception {
            return "这是一个Callable线程，执行了call方法，有返回值，可以抛出异常。";
        }
    }
    static class MyThread extends Thread{
        public void run(){
            System.out.println("这是一个Thread类的子类，涉及运行机制问题，影响程序性能");
        }
    }
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        MyRunable myRunable = new MyRunable();
        Thread thread = new Thread(myRunable);
        thread.start();

        MyCallable myCallable = new MyCallable();
        FutureTask<String> stringFutureTask = new FutureTask<>(myCallable);
        Thread thread1 = new Thread(stringFutureTask);
        thread1.start();
        String s = stringFutureTask.get();
        System.out.println(s);

        MyThread myThread = new MyThread();
        myThread.start();
    }
}

```

运行结果

```shell
这是一个Runable线程，执行了run方法，没有返回值，不能抛出异常
这是一个Callable线程，执行了call方法，有返回值，可以抛出异常。
这是一个Thread类的子类，涉及运行机制问题，影响程序性能
```


