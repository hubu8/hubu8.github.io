# Springboot事务代码实践


<!--more-->

### 无事务代码

```java
    @Override
    public int add(User user) {
        user.setId(idGenerator.nextId());

        int insert = userMapper.insert(user);
        int i = 1/0;
        int insert1 = numMapper.insert(user);

        return insert;
    }
```

一个事务有两个数据库操作来完成，但是当第一个操作完成之后，系统发生了异常，就会造成数据不一致。

如下，两表同步数据，一个表有数据，另一个表没有

![image-20221205185437096.png](/posts/Spring/Springboot事务代码实践/image-20221205185437096.png)

### 编程式事务管理

编程式事务管理使用TransactionTemplate或者直接使用底层的PlatformTransactionManager。对于编程式事务管理，spring推荐使用TransactionTemplate。

Service层实现类代码：

```java
	@Resource
    TransactionTemplate transactionTemplate;

    @Override
    public int add(User user) {
        final int[] num = {0};
        user.setId(idGenerator.nextId());
        transactionTemplate.execute(new TransactionCallbackWithoutResult() {
            @Override
            protected void doInTransactionWithoutResult(TransactionStatus status) {
                try {
                    int insert = userMapper.insert(user);
                    num[0] +=insert;
	//                    int i = 1/0;
                    int insert1 = numMapper.insert(user);
                    num[0] +=insert1;
                }catch (Exception e){
                    status.setRollbackOnly();
                }
            }
        });
        return num[0];
    }
```

另一种实现事务编程方法：

```java
import org.springframework.transaction.PlatformTransactionManager;	
	
	@Resource
    PlatformTransactionManager transactionManager;

    public int add(User user) {
        int num = 0;
        long l = idGenerator.nextId();
        user.setId(l);
        TransactionStatus status = transactionManager.getTransaction(new DefaultTransactionDefinition());
        try {
            int insert = userMapper.insert(user);
            //int i = 1/0;
            int insert1 = numMapper.insert(user);
            num = insert1;
            transactionManager.commit(status);
        }catch (Exception e){
            transactionManager.rollback(status);
        }
        return num;
    }
```

就编程式事务管理而言，Spring 更推荐使用 TransactionTemplate。

- TransactionTemplate可以隐式调用commit，不需要在代码中显式调用，只需要事务代码块出现异常时回滚即可
- TransactionManager需要显式调用commit和rollback操作；没有commit，数据库不会生成记录。

在编程式事务中，必须在每个业务操作中包含额外的事务管理代码，就导致代码看起来非常的臃肿，但对理解 Spring 的事务管理模型非常有帮助。

### 声明式事务管理

​	声明式事务将事务管理代码从业务方法中抽离了出来，以声明式的方式来实现事务管理，对于开发者来说，声明式事务显然比编程式事务更易用、更好用。
​	当然了，要想实现事务管理和业务代码的抽离，就必须得用到 Spring 当中的AOP，其本质是对方法前后进行拦截，然后在目标方法开始之前创建或者加入一个事务，执行完目标方法之后根据执行的情况提交或者回滚。
**声明式事务虽然优于编程式事务，但也有不足，声明式事务管理的粒度是方法级别，而编程式事务是可以精确到代码块级别的。**

|                   |                      |      |
| ----------------- | -------------------- | ---- |
| **REQUIRED**      | 有则加入，内外同步   |      |
| **REQUIRES_NEW**  | 内外分离             |      |
| **NESTED**        | 外影响内，内不影响外 |      |
| **SUPPORTS**      | 有事务就使用，无则非 |      |
| **MANDATORY**     | 有则加入，无则抛异常 |      |
| **NOT_SUPPORTED** | 非事务，挂起当前事务 |      |
| **NEVER**         | 在事务中抛异常       |      |

```java
@Transactional(propagation = Propagation.REQUIRED)
    public int add(User user) {
        int num = 0;
        long l = idGenerator.nextId();
        user.setId(l);
        int insert = userMapper.insert(user);
        int i = 1/0;
        int insert1 = numMapper.insert(user);
        num = insert1;
        return num;
    }


java.lang.ArithmeticException: / by zero
	at com.jindong.dailytest.service.impl.UserServiceImpl.add(UserServiceImpl.java:78) ~[classes/:na]
```

业务代码处在同一个事务中，系统抛出异常后，业务代码都不成功
