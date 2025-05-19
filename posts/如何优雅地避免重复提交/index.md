# 如何优雅地避免重复提交


<!--more-->

在一些新增的场景下，往往都会采取各种方式去防止重复提交。否则当新增逻辑执行较慢时，用户多次点击提交按钮，就会导致数据库中出现多条重复数据或者后端执行出错。

那么防止重复提交最简单的方式就是从前端出发，让前端设置按钮为只可以点击一次，但是前端是可以被跳过的。要想根治还得需要从后端入手。

本篇文章提供了一种通过自定义注解的方式来解决某一场景下某个实体只能被提交一次。

### 具体使用示例：

```java
/**
 * @ApiResubmit注解防止重复提交，
 *     prefix - 用来标识场景，不填默认为类名+方法名
 *     key - 限流key，用来辨别是否是一次重复的请求
 *     forbidSeconds - 请求禁止秒数
 *     message - 限流提示信息
 * 
 * 此处表示同一个学生id，在2秒内只能够提交一次作业
 * @param homeWorkCommitReq
 * @return
 */
@PostMapping("/commit/homeWork")
@ApiResubmit(prefix = "commitHomeWork", key = "#homeWorkCommitReq.studentId",forbidSeconds = 2, message = "正在提交中，请稍后再试")
@ApiOperation(value = "学生交作业接口")
public Result<HomeWorkCommitResp> homeWorkCommit(@RequestBody HomeWorkCommitReq homeWorkCommitReq) {
   return Result.success(homeWorkBizService.homeWorkCommit(recheckCommitReq));
}
```

### @ApiResubmit注解具体定义

```java
@Target({ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface ApiResubmit {

   /**
    * 限流前缀，用来区分不同的限流场景
    */
   String prefix() default "";

   /**
    * 限流key，用来辨别是否是一次重复的请求，支持SpEL，可以从方法的入参中获取
    */
   String key() default "";

   /**
    * 请求禁止秒数，即在多少秒内禁止重复请求
    */
   int forbidSeconds() default 3;

   /**
    * 限流提示信息
    */
   String message() default "请求过于频繁，请稍后再试";

}
```

### 注解切面逻辑：

```java
@Aspect
@Component
@Slf4j
public class ResubmitAspect {

   //默认的提示语
   private static final String DEFAULT_BLOCKING_MESSAGE = "提交的频率过快，请稍后再试";

   //切点
   @Pointcut("@annotation(ApiResubmit)")
   public void resubmitPointcut() {
   }

   @Around(value = "resubmitPointcut()")
   public Object checkResubmit(ProceedingJoinPoint joinPoint) throws Throwable {
      MethodSignature signature = (MethodSignature) joinPoint.getSignature();
      Method method = signature.getMethod();
      ApiResubmit annotation = AnnotationUtils.findAnnotation(method, ApiResubmit.class);
      if (annotation != null) {  //如果这个方法标注这个注解
         //以类名+方法名作为key的默认前缀
         String defaultPrefix = joinPoint.getSignature().getDeclaringTypeName() + "#" + method.getName();

         //获取此方法所传入的参数 map<参数名, 参数值>
         Map<String, Object> methodParam = getMethodParam(joinPoint);

         validate(annotation, defaultPrefix, methodParam);
      }
      return joinPoint.proceed();
   }

   private Map<String, Object> getMethodParam(ProceedingJoinPoint joinPoint) {
      Object[] args = joinPoint.getArgs();
      String[] parameterNames = ((MethodSignature) joinPoint.getSignature()).getParameterNames();
      Map<String, Object> paramMap = new HashMap<>(args.length);
      for (int i = 0; i < args.length; i++) {
         paramMap.put(parameterNames[i], args[i]);
      }
      return paramMap;
   }

   private void validate(ApiResubmit annotation, String defaultPrefix, Map<String, Object> methodParam) {
      try {
         String prefix = StringUtils.isBlank(annotation.prefix()) ? defaultPrefix : annotation.prefix();

         //去解析spel语句
         StandardEvaluationContext context = new StandardEvaluationContext(methodParam);
         context.addPropertyAccessor(new MapAccessor());
         String key = SpELUtils.parseExpression(annotation.key()).getValue(context, String.class);

         //去获取redis锁，锁持有时间为注解属性forbidSeconds
         boolean lock = RedisUtil.tryLock(prefix + key, 0, annotation.forbidSeconds() * 1000L);

         if (!lock) { //如果获取锁失败
            // 拿到注解属性提示语，抛出异常
            String message = StringUtils.isBlank(annotation.message()) ? DEFAULT_BLOCKING_MESSAGE : annotation.message();
            throw new BizException(2001, message);
         }
      } catch (BizException biz) {
         throw biz;
      } catch (Exception ex) {
         //如果不是重复提交的异常，即出现了其他的异常，正常请求，但需要打印error日志感知
         LOGGER.error("ApiResubmitAspectException: ", ex);
      }
   }

}
```

### 最后附上切面里使用的一个工具类RedisUtil的方法

```java
/**
* 这里采用的Redssion进行的加锁，
* 大家如果对redission底层不熟悉的可以看我的另一篇文章
* https://juejin.cn/post/7254444906210000955
* 里面具体讲解了Redission分布式锁的底层原理且分析了看门狗机制
*/
public static boolean tryLock(String redisKey, long waitMill, long leaseMill) {
    RLock rLock = redissonClient.getLock(redisKey);
    boolean locked = false;

    try {
        locked = rLock.tryLock(waitMill, leaseMill, TimeUnit.MILLISECONDS);
    } catch (Exception ex) {
        LOGGER.warn("redisson tryLock failed redisKey:{}, e", redisKey, ex);
    }
   
    return locked;
}
```


