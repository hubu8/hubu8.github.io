# Spring Boot如何优雅提高接口数据安全性


<!--more-->

## 1.背景

最近我司业务上需要对接第三方各大银行平台，调用第三方接口和提供接口供第三方调用，这时候的对外open接口安全性就得重视了，再有就是之前我在知乎上发布一篇**《Spring Security实现后端接口权限验证》**的总结，有个兄弟提出一个问题：只做接口功能菜单权限检验还不够，还得做数据权限检验才行，举个例子：用户A有删除某条数据的接口权限，这个接口的参数是传记录id来删除的(ps：平时我们开发接口也是这么做的)，后端执行的逻辑就是通过登录信息通过用户认证，然后再判断接口菜单权限，紧接着就执行如下SQL逻辑：

```
delete from table where id=?
```

这里的id就是掉接口传递的参数，这时候假如用户B知道了怎么调接口，就根据id自增长的特性随意传id，就会删掉别人的数据，所以这是一个严重的问题，要解决这问题可以像上面说的一样加上数据权限，执行逻辑如下：

```
delete from table where id=? and user_id = userId
```

这样就避免数据被别人操作了，也就是加上了数据权限判断，但是却给业务逻辑增加了复杂性同时老接口业务逻辑难以适配，本质上来说web页面上看到的数据就是根据用户角色做过数据隔离的，可以这么理解你能看到哪些数据和你有那些功能菜单操作权限就差不多避免上面所说的情况了，但是保不准懂代码的人使用postman等工具恶意调接口而产生上面的情况，我们还是得正视这个问题，既然通过数据权限解决该问题不太友好，那么我们可以再思考下怎么避免这个问题？？？这个问题可以转换为怎么避免别人轻易就能调通接口，解决办法就是**不能在外网暴露接口信息，拒绝接口裸奔，从而有效提高接口安全性**，这也是今天我们这篇总结的核心主旨。当然这里强调一下我这里说的是**有效提高，不是绝对保证安全，做不到...**

> **项目推荐**：基于SpringBoot2.x、SpringCloud和SpringCloudAlibaba企业级系统架构底层框架封装，解决业务开发时常见的非功能性需求，防止重复造轮子，方便业务快速开发和企业技术栈框架统一管理。引入组件化的思想实现高内聚低耦合并且高度可配置化，做到可插拔。严格控制包依赖和统一版本管理，做到最少化依赖。注重代码规范和注释，非常适合个人学习和企业使用
>
> **Github地址**：https://github.com/plasticene/plasticene-boot-starter-parent
>
> **Gitee地址**：https://gitee.com/plasticene3/plasticene-boot-starter-parent

## 2.Spring Boot如何提高接口安全性

在Spring Boot项目中提高接口安全的核心所在：**加密和加签**，加固接口参数、验证复杂度。

**加密：**对参数进行加密传输，拒绝接口参数直接暴露，这样就可以有效做到防止别人轻易准确地获取到接口参数定义和传参格式要求了。

**加签：**对接口参数进行加签，可以有效防止接口参数被篡改和接口参数被重放恶刷。

### 2.1 加密

现今有许许多多的加密算法，这里就不对算法进行过度叙述，毕竟不是我们今天的主题，但是加密算法大体分为非对称加密和对称加密。

**非对称加密**

非对称加密算法是一种密钥的保密方法。非对称加密算法需要两个密钥：公开密钥（publickey:简称公钥）和私有密钥（privatekey:简称私钥）。公钥与私钥是一对，如果用公钥对数据进行加密，只有用对应的私钥才能解密。因为加密和解密使用的是两个不同的密钥，所以这种算法叫作非对称加密算法。

**对称加密**

加密秘钥和解密秘钥是一样，当你的密钥被别人知道后，就没有秘密可言了。

经过需求分析和科学借鉴我们采用了**非对称加密算法RSA和对称加密算法AES**来完成接口加密。至于这两种加密算法的原理与实现有兴趣自己去查资料，我这里就说一下选它们的原因：

**AES 是对称加密算法**，优点：加密速度快；缺点：如果秘钥丢失，就容易解密密文，安全性相对比较差

**RSA 是非对称加密算法** ， 优点：安全 ；缺点：加密速度慢

接口参数加解密的流程大致如图所示：

![图片](/java_images/java_640)

具体步骤如下：

1. 客户端(调用接口方)随机生成AES加解密的密钥**aes key**，这里的AES密钥每次调接口都需要随机生成，可以有效提高安全性。
2. 使用`aes key`对接口参数`requestBody`进行加密，`data=base64(AES(json参数))`
3. 通过RSA加密算法加密`aes key`，有效保证aes算法的密钥的可靠安全性  `key=base64(RSA(aes key))`
4. 经过上面的步骤，得到了加密后的业务参数及密钥，这时候就可以发送请求调用接口了
5. 服务端接收到请求之后，先通过RSA算法对key进行解密获取到`ase key`， 再通过`aes key`解密data得到真正json参数，最后映射到接口方法的参数对象上，供controller的业务方法逻辑使用。
6. 业务方法执行完成后，对响应参数进行加密，加密流程和上面的1、2、3一样
7. 客户端收到响应参数之后，和步骤5一样解密响应参数，就拿到了真正的数据结果了。

### 2.2 加签

签名验证也是当下提高接口安全性主要措施之一，核心就是客户端在调用接口时按照一定规则生成签名`sign`，服务端拿到签名`sign`之后进行验证操作，大致流程如下：

![图片](/java_images/java_641)

具体步骤：

1. 对请求参数对象bean转`sortMap`保证参数拼接的有序性，如果接口没有参数也没有关系，这里转成一个空的`sortMap`
2. 按照约定拼接生成字符串`content = sortMap + nonce + timestamp`
3. 使⽤`SHA1WithRSA`算法及私钥对`concent`进⾏签名`sign`
4. 服务端判断`timestamp`是否超过签名有效期和`nonce`是否重复使用
5. 服务端和步骤2一样规则生成字符串`content`
6. 使⽤`SHA1WithRSA`算法及公钥对`concent`和`sign`进行验签

## 3.优雅实现接口加密、加签

在实现这个需求时，考虑到全公司的多个团队开发使用的通用性和便捷性，所以我们对加密、加签操作进行了公共的抽取封装，同时通过一个注解`@ApiSecurity`来标识接口是否需要进行加密、加签操作，在业务侧极大程度地降低了开发使用成本，不用写冗余代码，做到了真正的优雅。

```
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE, ElementType.METHOD})
@Documented
public @interface ApiSecurity {

    @Alias("isSign")
    boolean value() default true;

    /**
     * 是否加签验证，默认开启
     * @return
     */
    @Alias("value")
    boolean isSign() default true;

    /**
     * 接口请求参数是否需要解密
     * @return
     */
    boolean decryptRequest() default false;

    /**
     * 接口响应参数是否需要加密
     * @return
     */
    boolean encryptResponse() default false;
}
```

这里注解属性可以看到签名验证默认是开启的，因为我们认为接口安全性加签是必须的，至于参数加解密可以视情况而定，通过属性配置开关，做到了极致的灵活性，这也是优雅呀。

使用案例：下面就是一个需要加密加签的接口

```
    @PostMapping("/security")
    @ApiSecurity(encryptResponse = true, decryptRequest = true)
    public User testApiSecurity(@RequestBody User user) {
        System.out.println(user);
        return user;
    }
```

可以看到我们在项目业务服务中只需要`@ApiSecurity`就可以了，就是这么简单，至于怎么实现的下面我们就来看看。

为了全公司对接口加密、加签功能实现统一和规范，我们将实现抽取，封装集成在公司自定义的`web starter`中，这样只要项目服务引入这个starter依赖就可以使用该功能了

首先我们对加密传输的参数bean进行规定封装如下：

```
@Data
public class ApiSecurityParam {

    /**
     * 应用id
     */
    private String appId;

    /**
     * RSA加密后的aes秘钥，需解密
     */
    private String key;

    /**
     * AES加密的json参数
     */
    private String data;

    /**
     * 签名
     */
    private String sign;

    /**
     * 时间戳
     */
    private String timestamp;

    /**
     * 请求唯一标识
     */
    private String nonce;

}
```

等于说加密、加签的参数格式，调用方需按照上面的对象传参，当然为了提高拓展性，签名的相关信息`sign、timestamp、nonce`可以放到请求的`header`里面，也能获取到。拿到`apiSecurityParam`我们就可以进行请求参数解密、验签了，需要通过判断是否使用了注解`@ApiSecuriy`来决定是否执行请求参数解密、验签逻辑，这就正好可以使用基于注解的切面实现啦，在说切面之前，先说说一次接口请求`requestBody`的输入流InputStream只能读取一次，就是说`request.getInputStream()`只能使用一次，原因如下：

因为流对应的是数据，数据放在内存中，有的是部分放在内存中。read 一次标记一次当前位置（mark position），第二次read就从标记位置继续读（从内存中copy）数据。 所以这就是为什么读了一次第二次是空了。 怎么让它不为空呢？只要inputstream 中的pos 变成0就可以重写读取当前内存中的数据。javaAPI中有一个方法public void reset() 这个方法就是可以重置pos为起始位置，但是不是所有的IO读取流都可以调用该方法！ServletInputStream是不能调用reset方法，这就导致了只能调用一次getInputStream()。

而我们需要先读取出`requestBody`进行解密，然后拿到解密之前的参数映射到真正的接口方法参数对象里，所以必须解决这个问题。

解决方法就是原始的`HttpServletRequest`的InputStream只能读取一下，那么我们就重新自定义封装一个`HttpServletRequest`可以实现多次读取。

```
public class RequestBodyWrapper extends HttpServletRequestWrapper {

    //用于将流保存下来
    private String body;

    public RequestBodyWrapper(HttpServletRequest request) throws IOException {
        super(request);
        body = new String(StreamUtils.copyToByteArray(request.getInputStream()), StandardCharsets.UTF_8);
    }

    /**
     * 重写getInputStream， 从body中获取请求参数
     * @return
     * @throws IOException
     */
    @Override
    public ServletInputStream getInputStream() throws IOException {
        final ByteArrayInputStream byteArrayInputStream = new ByteArrayInputStream(body.getBytes("UTF-8"));
        ServletInputStream servletInputStream = new ServletInputStream() {
            @Override
            public boolean isFinished() {
                return false;
            }

            @Override
            public boolean isReady() {
                return false;
            }

            @Override
            public void setReadListener(ReadListener readListener) {

            }

            @Override
            public int read() throws IOException {
                return byteArrayInputStream.read();
            }
        };
        return servletInputStream;
    }

    @Override
    public BufferedReader getReader() throws IOException {
        return new BufferedReader(new InputStreamReader(getInputStream()));
    }

    public String getBody() {
        return this.body;
    }

    public void setBody(String body) {
        this.body = body;
    }
}
```

然后通过一个过滤器filter把自定义封装的`RqequestBodyWapper`传递下去：

```
@Slf4j
public class BodyTransferFilter implements Filter {
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        RequestBodyWrapper requestBodyWrapper = null;
        try {
            HttpServletRequest req = (HttpServletRequest)request;
            requestBodyWrapper = new RequestBodyWrapper(req);

        }catch (Exception e){
            log.warn("requestBodyWrapper Error:", e);
        }
        chain.doFilter((Objects.isNull(requestBodyWrapper) ? request : requestBodyWrapper), response);
    }
}
```

接下来就可以来看看切面了：这里是解析请求参数和验签和逻辑所在：

```
@Aspect
@Slf4j
@Order(value = OrderConstant.AOP_API_DECRYPT)
public class ApiSecurityAspect {
    @Resource
    private ApiSecurityProperties apiSecurityProperties;
    @Resource
    private StringRedisTemplate stringRedisTemplate;

    private static final String NONCE_KEY = "x-nonce-";

    @Pointcut("execution(* com.plasticene..controller..*(..)) && " +
            "(@annotation(com.plasticene.boot.web.core.anno.ApiSecurity) ||" +
            " @target(com.plasticene.boot.web.core.anno.ApiSecurity))")
    public void securityPointcut(){}

    @Around("securityPointcut()")
    public Object aroundApiSecurity(ProceedingJoinPoint joinPoint) throws Throwable {
        //=======AOP解密切面通知=======
        ApiSecurity apiSecurity = getApiSecurity(joinPoint);
        boolean isSign = apiSecurity.isSign();
        boolean decryptRequest = apiSecurity.decryptRequest();
        // 获取request加密传递的参数
        HttpServletRequest request = getRequest();
        // 只能针对post接口的请求参数requestBody进行统一加解密和加签，这是规定
        if (!Objects.equals("POST", request.getMethod())) {
            throw new BizException("只能POST接口才能加密加签操作");
        }
        // 获取controller接口方法定义的参数
        Object[] args = joinPoint.getArgs();
        Object[] newArgs = args;
        ApiSecurityParam apiSecurityParam = new ApiSecurityParam();
        // 请求参数解密
        if (decryptRequest) {
            // 不支持多个请求，因为解密请求参数之后会json字符串，再根据请求参数的类型映射过去，如果有多个参数就不知道映射关系了
            if (args.length > 1) {
                throw new BizException("加密接口方法只支持一个参数，请修改");
            }
            // args.length=0没有请求参数，就说明没必要解密，因为接口压根不接收参数，即使使用者无脑开启的该接口的参数加密，这里不做任何逻辑即可
            if (args.length == 1) {
                RequestBodyWrapper requestBodyWrapper;
                if (request instanceof RequestBodyWrapper) {
                    requestBodyWrapper = (RequestBodyWrapper) request;
                } else {
                    requestBodyWrapper = new RequestBodyWrapper(request);
                }
                String body = requestBodyWrapper.getBody();
                apiSecurityParam = JSONObject.parseObject(body, ApiSecurityParam.class);
                // 通过RSA私钥解密获取到aes秘钥
                String aesKey = RSAUtil.decryptByPrivateKey(apiSecurityParam.getKey(), apiSecurityProperties.getRsaPrivateKey());
                // 通过aes秘钥解密data参数数据
                String data = AESUtil.decrypt(apiSecurityParam.getData(), aesKey);
                //获取接口入参的类
                Class<?> c = args[0].getClass();
                //将获取解密后的真实参数，封装到接口入参的类中
                Object o = JSONObject.parseObject(data, c);
                newArgs = new Object[]{o};
            }
        }
        // 验签
        if (isSign) {
            verifySign(request, newArgs.length == 0 ? null : newArgs[0], apiSecurityParam);
        }
        return joinPoint.proceed(newArgs);
    }

    void verifySign(HttpServletRequest request, Object o, ApiSecurityParam apiSecurityParam) {
        // 如果请求参数是加密传输的，那就先从ApiSecurityParam获取签名和时间戳等等。
        // 如果请求参数不是加密传输的，那么ApiSecurityParam的字段取值都为null，这时候在请求的header里面获取参数信息
        String sign = apiSecurityParam.getSign();
        if (StringUtils.isBlank(sign)) {
            sign = request.getHeader("X-Sign");
        }
        if (StringUtils.isBlank(sign)) {
            throw new BizException("签名不能为空");
        }

        String nonce = apiSecurityParam.getNonce();
        if (StringUtils.isBlank(nonce)) {
            nonce = request.getHeader("X-Nonce");
        }
        if (StringUtils.isBlank(nonce)) {
            throw new BizException("唯一标识不能为空");
        }

        String timestamp = apiSecurityParam.getTimestamp();
        Long t;
        if (StringUtils.isBlank(timestamp)) {
            timestamp = request.getHeader("X-Timestamp");
        }
        if (StringUtils.isBlank(timestamp)) {
            throw new BizException("时间戳不能为空");
        } else {
            try {
                t = Long.valueOf(timestamp);
            } catch (Exception e) {
                throw new BizException("非法的时间戳");
            }
        }

        // 判断timestamp时间戳与当前时间是否超过签名有效时长（过期时间根据业务情况进行配置）,如果超过了就提示签名过期
        long now = System.currentTimeMillis() / 1000;
        if (now - t > apiSecurityProperties.getValidTime()) {
            throw new BizException("签名已过期");
        }

        // 判断nonce
        boolean nonceExists = stringRedisTemplate.hasKey(NONCE_KEY + nonce);
        if (nonceExists) {
            //请求重复
            throw new BizException("唯一标识nonce已存在");
        }

        // 验签
        SortedMap sortedMap = SignUtil.beanToMap(o);
        String content = SignUtil.getContent(sortedMap, nonce, timestamp);
        boolean flag = RSAUtil.verifySignByPublicKey(content, sign, apiSecurityProperties.getRsaPublicKey());
        if (!flag) {
            throw new BizException("签名验证不通过");
        }

        stringRedisTemplate.opsForValue().set(NONCE_KEY+ nonce, "1", apiSecurityProperties.getValidTime(),
                TimeUnit.SECONDS);
    }


    private HttpServletRequest getRequest() {
        ServletRequestAttributes requestAttributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
        HttpServletRequest request = requestAttributes.getRequest();
        return request;
    }


    private ApiSecurity getApiSecurity(JoinPoint joinPoint) {
        MethodSignature methodSignature = (MethodSignature) joinPoint.getSignature();
        Method method = methodSignature.getMethod();
        ApiSecurity apiSecurity = method.getAnnotation(ApiSecurity.class);
        if (Objects.isNull(apiSecurity)) {
            apiSecurity = method.getDeclaringClass().getAnnotation(ApiSecurity.class);
        }
        return apiSecurity;
    }
}
```

这代码没什么好讲的了，就按照上面的加密、加签流程图逻辑实现的，而且注释也很清楚，可以自己慢慢消化，这里面涉及的工具类如`RSAUtil、AESUtil、SignUtil`等，碍于文章代码篇幅，我就这里就在一一展示，我会在文章后面放上全部代码的项目github地址以供下载的。

上面的切面只完成了接口参数的解密和验签，至于对响应参数的加密返回我们放到了`ResponseBodyAdvice`中实现。

```
@RestControllerAdvice
@Slf4j
public class ResponseResultBodyAdvice implements ResponseBodyAdvice<Object> {
    @Resource
    private ObjectMapper objectMapper;
    @Resource
    private ApiSecurityProperties apiSecurityProperties;


    /**
     * 判断类或者方法是否使用了 @ResponseResultBody
     */
    @Override
    public boolean supports(MethodParameter returnType, Class<? extends HttpMessageConverter<?>> converterType) {
        return AnnotatedElementUtils.hasAnnotation(returnType.getContainingClass(), ResponseResultBody.class)
                || returnType.hasMethodAnnotation(ResponseResultBody.class)
                || AnnotatedElementUtils.hasAnnotation(returnType.getContainingClass(), ApiSecurity.class)
                || returnType.hasMethodAnnotation(ApiSecurity.class);
    }

    /**
     * 当类或者方法使用了 @ResponseResultBody 就会调用这个方法
     * 如果返回类型是string，那么springmvc是直接返回的，此时需要手动转化为json
     * 因为当body都为null时，下面的非加密下的if判断参数类型的条件都不满足，如果接口返回类似为String，
     * 会报错com.shepherd.fast.global.ResponseVO cannot be cast to java.lang.String
     */
    @SneakyThrows
    @Override
    public Object beforeBodyWrite(Object body, MethodParameter returnType, MediaType selectedContentType, Class<? extends HttpMessageConverter<?>> selectedConverterType, ServerHttpRequest request, ServerHttpResponse response) {
        Method method = returnType.getMethod();
        Class<?> returnClass = method.getReturnType();
        Boolean enable = apiSecurityProperties.getEnable();
        ApiSecurity apiSecurity = method.getAnnotation(ApiSecurity.class);
        if (Objects.isNull(apiSecurity)) {
            apiSecurity = method.getDeclaringClass().getAnnotation(ApiSecurity.class);
        }
        if (enable && Objects.nonNull(apiSecurity) && apiSecurity.encryptResponse() && Objects.nonNull(body)) {
            // 只需要加密返回data数据内容
            if (body instanceof ResponseVO) {
                body = ((ResponseVO) body).getData();
            }
            JSONObject jsonObject = encryptResponse(body);
            body = jsonObject;
        } else {
            if (body instanceof String || Objects.equals(returnClass, String.class)) {
                String value = objectMapper.writeValueAsString(ResponseVO.success(body));
                return value;
            }
            // 防止重复包裹的问题出现
            if (body instanceof ResponseVO) {
                return body;
            }
        }
        return ResponseVO.success(body);
    }

    JSONObject encryptResponse(Object result) {
        String aseKey = AESUtil.generateAESKey();
        String content = JSONObject.toJSONString(result);
        String data = AESUtil.encrypt(content, aseKey);
        String key = RSAUtil.encryptByPublicKey(aseKey, apiSecurityProperties.getRsaPublicKey());
        JSONObject jsonObject = new JSONObject();
        jsonObject.put("key", key);
        jsonObject.put("data", data);
        return jsonObject;
    }

}
```

这里就是对接口返回参数格式进行统一`ResponseVO`，同时判断是否需要进行返回参数加密执行相应逻辑即可。

## 4.总结

至此，对于Spring Boot如何提高接口安全性的思路与实现已讲完，同时我们也尽量进行了抽取封装，做到了极致的优雅实现。当然这里还是要再次强调一下以上的思路实现是不能绝对保证接口安全性的，只能做到”防君子不妨小人“，可以这么说假如不做加密加签这些保护措施，黑客破解接口就会不费吹灰之力.

最后奉上全部代码地址：https://github.com/plasticene/plasticene-boot-starter-parent/tree/main/plasticene-boot-starter-web



作者：Shepherd

链接：https://mp.weixin.qq.com/s?__biz=Mzg5MDY1NzI0MQ==&mid=2247486045&idx=1&sn=1ed52c5433908d483045916a142a12d2&chksm=cfd80aebf8af83fd9c001bdb99ee5427b50e5c2f79b009a6073abe48a0670d378b18dae376b2&token=1312864910&lang=zh_CN#rd

来源： Shepherd进阶笔记(微信公众号)

著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
