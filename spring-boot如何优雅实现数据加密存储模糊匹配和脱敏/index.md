# Spring Boot如何优雅实现数据加密存储、模糊匹配和脱敏


<!--more-->

## 1.概述

近来我们都在围绕着使用`Spring Boot`开发业务系统时如何保证数据安全性这个主题展开总结，当下大部分的`B/S架构`的系统也都是基于`Spring Boot + SpringMVC三层架构`开发的，之前我们总结了在接口层面如何加固数据安全性：[Spring Boot如何优雅提高接口数据安全性](https://link.juejin.cn?target=https%3A%2F%2Fmp.weixin.qq.com%2Fs%3F__biz%3DMzg5MDY1NzI0MQ%3D%3D%26mid%3D2247486045%26idx%3D1%26sn%3D1ed52c5433908d483045916a142a12d2%26chksm%3Dcfd80aebf8af83fd9c001bdb99ee5427b50e5c2f79b009a6073abe48a0670d378b18dae376b2%26token%3D1312864910%26lang%3Dzh_CN%23rd)，可以认为是在`SpringMVC`的三层架构中的`controller层(逻辑控制层)`对接口数据进行安全处理操作，更直接点说就是在接口请求参数传入进行逻辑处理或者响应参数输出到页面展示之前进行数据处理的，所以只是在`SpringMVC`三层架构中的一层中进行安全加固，还不是很稳固，接下来今天我们就再来讲讲在`SpringMVC`三层架构另一层中如何进行数据安全加固，在进入今天主题之前先来看看什么是`SpringMVC`架构？

**什么是SpringMVC三层架构？**

SpringMVC的工程结构一般来说分为三层，自下而上是Modle层（模型，数据访问层）、Cotroller层（控制，逻辑控制层）、View层（视图，页面显示层），其中Modle层分为两层：dao层、service层，MVC架构分层的主要作用是解耦。采用分层架构的好处，普遍接受的是系统分层有利于系统的维护，系统的扩展。就是增强系统的可维护性和可扩展性。对于Spring这样的框架，（View\Web）表示层调用控制层（Controller），控制层调用业务层（Service），业务层调用数据访问层（Dao） 可以这么说，现在90%以上的业务系统都是基于该三层架构模式开发的，这种架构模式也有人说是设计模式中一种，可见其重要性不言而喻，所以我们需重视。

我们也都知道在日常开发系统过程中，数据安全是非常重要的。特别是在当今互联网时代，个人隐私安全极其重要，一旦个人用户数据遭到攻击泄露，将会造成灾难级的事故问题。所有之前我们基于接口层进行数据安全处理是远远不够的，今天我们就来谈谈如何Model层(数据访问层)怎样做到优雅数据加密存储、模糊匹配及其脱敏展示，本文的主题：**数据加密存储、模糊匹配和脱敏展示**。

银行系统对数据安全性的要求在业务系统中是首屈一指的，所以今天我们就以常见的个人银行账户数据：密码、手机号、详细地址、银行卡号等信息字段为例，进行主题的宣讲与浅析。

## 2.数据加密存储

我们之前总结的是在接口层进行数据加解密传输，也强调过这种方式保证不了数据的绝对安全，只是有效提高接口数据安全性，抬高数据被抓取的门槛而已。所以接下来我们就来讲述一下如何在数据的源头存储层保障其安全。我们都知道一些核心私密字段，比如说密码，手机号等在数据库层存储就不能明文存储，必须加密存储保证即使数据库泄露了也不会轻易曝光数据。

### 2.1 优雅实现数据库字段加解密原理

Mybatis-plus提供企业高级特性就有支持[数据加密解密](https://link.juejin.cn?target=https%3A%2F%2Fbaomidou.com%2Fpages%2F1864e1%2F%23%E5%AD%97%E6%AE%B5%E5%8A%A0%E5%AF%86%E8%A7%A3%E5%AF%86)，不过是收费的。。。但是我们可以细细探究其原理进行功能的自我实现。

其实在我们上面推荐的[快速开发框架](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fplasticene%2Fplasticene-boot-starter-parent)中就已经优雅整合了数据加解密功能了，[EncryptTypeHandler](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fplasticene%2Fplasticene-boot-starter-parent%2Fblob%2Fmain%2Fplasticene-boot-starter-mybatis%2Fsrc%2Fmain%2Fjava%2Fcom%2Fplasticene%2Fboot%2Fmybatis%2Fcore%2Fhandlers%2FEncryptTypeHandler.java)：实现数据库的字段加密与解密。

默认提供了基于base64加密算法[Base64EncryptService](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fplasticene%2Fplasticene-boot-starter-parent%2Fblob%2Fmain%2Fplasticene-boot-starter-mybatis%2Fsrc%2Fmain%2Fjava%2Fcom%2Fplasticene%2Fboot%2Fmybatis%2Fcore%2Fencrypt%2FBase64EncryptService.java)和AES加密算法[AESEncryptService](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fplasticene%2Fplasticene-boot-starter-parent%2Fblob%2Fmain%2Fplasticene-boot-starter-mybatis%2Fsrc%2Fmain%2Fjava%2Fcom%2Fplasticene%2Fboot%2Fmybatis%2Fcore%2Fencrypt%2FAESEncryptService.java)，当然业务侧也可以自定义加密算法，这需要实现接口[EncryptService](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fplasticene%2Fplasticene-boot-starter-parent%2Fblob%2Fmain%2Fplasticene-boot-starter-mybatis%2Fsrc%2Fmain%2Fjava%2Fcom%2Fplasticene%2Fboot%2Fmybatis%2Fcore%2Fencrypt%2FEncryptService.java)，并把实现类注入到容器中即可。加密功能核心逻辑

```java
@Bean
@ConditionalOnMissingBean(EncryptService.class)
public EncryptService encryptService() {
  Algorithm algorithm = encryptProperties.getAlgorithm();
  EncryptService encryptService;
  switch (algorithm) {
    case BASE64:
      encryptService =  new Base64EncryptService();
      break;
    case AES:
      encryptService = new AESEncryptService();
      break;
    default:
      encryptService =  null;
  }
  return encryptService;
}
```

接下来就可以基于加密算法，扩展mybatis的`typeHandler`对实体字段数据进行加密解密了：**EncryptTypeHandler**

```java
public class EncryptTypeHandler<T> extends BaseTypeHandler<T> {

    @Resource
    private EncryptService encryptService;

    @Override
    public void setNonNullParameter(PreparedStatement ps, int i, Object parameter, JdbcType jdbcType) throws SQLException {
        ps.setString(i, encryptService.encrypt((String)parameter));
    }
    @Override
    public T getNullableResult(ResultSet rs, String columnName) throws SQLException {
        String columnValue = rs.getString(columnName);
        return StrUtil.isBlank(columnValue) ? (T)columnValue : (T)encryptService.decrypt(columnValue);
    }

    @Override
    public T getNullableResult(ResultSet rs, int columnIndex) throws SQLException {
        String columnValue = rs.getString(columnIndex);
        return StrUtil.isBlank(columnValue) ? (T)columnValue : (T)encryptService.decrypt(columnValue);
    }

    @Override
    public T getNullableResult(CallableStatement cs, int columnIndex) throws SQLException {
        String columnValue = cs.getString(columnIndex);
        return StrUtil.isBlank(columnValue) ? (T)columnValue : (T)encryptService.decrypt(columnValue);
    }
}

```

### 2.2 加密与解密示例

首先创建一张`user`表：

```java
CREATE TABLE `user` (
  `id` bigint(20) NOT NULL,
  `name` varchar(255) DEFAULT NULL COMMENT '姓名',
  `phone` varchar(255) DEFAULT NULL COMMENT '手机号',
  `id_card` varchar(255) DEFAULT NULL COMMENT '身份证号',
  `bank_card` varchar(255) DEFAULT NULL COMMENT '银行卡号',
  `address` varchar(255) DEFAULT NULL COMMENT '住址',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

这时候我们正常插入一条数据：

```java
@Test
public void test() {
    User user = new User();
    user.setName("shepherd");
    user.setMobile("17812345678");
    user.setIdCard("213238199601182111");
    user.setBankCard("3222022046741500");
    user.setAddress("杭州市余杭区未来科技城");
    userDAO.insert(user);
}
```

数据库存储查询结果如下：

| id                  | name     | **mobile**  | id_card            | bank_card        | address                |
| ------------------- | -------- | ----------- | ------------------ | ---------------- | ---------------------- |
| 1567402046481436673 | shepherd | 17812345678 | 213238199601182111 | 3222022046741500 | 杭州市余杭区未来科技城 |

这就是我们平时不加密存储查询的结果，这里id是通过分布式id算法自动生成的哈。

接下来我们来看看实现对数据的加密，只需要在配置文件配置使用哪一种加密算法和在实体类的字段属性加上注解`@TableField(typeHandler = EncryptTypeHandler.class)`即可。

这里我们使用aes加密算法：

```yaml
ptc:
  encrypt:
    algorithm: aes
```

实体类：

```java
@Data
@TableName(autoResultMap = true)
public class User {

    private Long id;
    private String name;

    @TableField(typeHandler = EncryptTypeHandler.class)
    private String mobile;
    @TableField(typeHandler = EncryptTypeHandler.class)
    private String idCard;
    @TableField(typeHandler = EncryptTypeHandler.class)
    private String bankCard;
    @TableField(typeHandler = EncryptTypeHandler.class)
    private String address;
}
```

再次插入数据，数据库存储查询结果如下：

| id                  | name     | **mobile**               | id_card                                      | bank_card                                    | address                                                      |
| ------------------- | -------- | ------------------------ | -------------------------------------------- | -------------------------------------------- | ------------------------------------------------------------ |
| 1567402046481436673 | shepherd | 9MgWngwLcd/vbYYYpG9pGQ== | 97vlZQahK+y548ofQbXlW9JUwuzuj3xCkNF/is1KLa4= | 2oQv5+y4+rVyN23IzudtOz+Zd7Aj1Bv2toBzmnwTXxo= | 0Wj7qqLl6jWkBu+TcxuwGYcdIjv+zIJHDM7d1dU/c8D2jc2wLp+zVvpSwBKWjX44 |

然后我们可以测试对这条数据进行查询：

```java
@Test
public void get() {
    User user = userDAO.selectById(1567405175268642818l);
    System.out.println(user);
}
```

结果如下：

```json
User(id=1567405175268642818, name=shepherd, mobile=17812345678, idCard=213238199601182111, bankCard=3222022046741500, address=杭州市余杭区未来科技城)
```

基于以上完美展示了数据加密存储和解密查询。

### 2.3 数据加密后怎么进行模糊匹配

密码、手机号、详细地址、银行卡号这些信息对加解密的要求也不一样，比如说密码我们需要加密存储，一般使用的都是不可逆的慢hash算法，慢hash算法可以避免暴力破解（典型的用时间换安全性）。

在检索时我们既不需要解密也不需要模糊查找，直接使用密文完全匹配，但是手机号就不能这样做，因为手机号我们要查看原信息，并且对手机号还需要支持模糊查找，因此我们今天就针对可逆加解密的数据支持模糊查询来看看有哪些实现方式。

我们接下来看看常规的做法，也是最广泛使用的方法，此类方法及满足的数据安全性，又对查询友好。

- **在数据库实现加密算法函数，在模糊查询的时候使用`decode(key) like '%partial%`**

  在数据库中实现与程序一致的加解密算法，修改模糊查询条件，使用数据库加解密函数先解密再模糊查找，这样做的优点是实现成本低，开发使用成本低，只需要将以往的模糊查找稍微修改一下就可以实现，但是缺点也很明显，这样做无法利用数据库的索引来优化查询，甚至有一些数据库可能无法保证与程序实现一致的加解密算法，但是对于常规的加解密算法都可以保证与应用程序一致。如果对查询性能要求不是特别高、对数据安全性要求一般，可以使用常见的加解密算法比如说AES、DES之类的也是一个不错的选择。

- **对密文数据进行分词组合，将分词组合的结果集分别进行加密，然后存储到扩展列，查询时通过`key like '%partial%'`** [先对字符进行固定长度的分组，将一个字段拆分为多个，比如说根据4位英文字符（半角），2个中文字符（全角）为一个检索条件，举个例子

  > `shepherd`使用4个字符为一组的加密方式，第一组shep ，第二组heph ，第三组ephe ，第四组pher … 依次类推。

  如果需要检索所有包含检索条件4个字符的数据比如：pher ，加密字符后通过 `key like “%partial%”` 查库。

  分词加密实现

```java
    public static String splitValueEncrypt(String value, int splitLength) {
        //检查参数是否合法
        if (StringUtils.isBlank(value) && splitLength <= 0) {
            return null;
        }
        String encryptValue = "";

        //获取整个字符串可以被切割成字符子串的个数
        int n = (value.length() - splitLength + 1);

        //分词（规则：分词长度根据【splitLength】且每次分割的开始跟结束下标加一）
        for (int i = 0; i < n; i++) {
            String splitValue = value.substring(i, splitLength++);
            encryptValue += encrypt(splitValue);
        }

        return encryptValue;
    }

    /**
     * 获取加密值
     *
     * @param value 加密值
     * @return
     */
    private static String encrypt(String value) {
        // 这里进行加密
        return  null;
    }
```

基于上面分词加密保存到扩展列，同时要求对原字段的正删改查对需要对其相应的扩展列适配，还要注意由于分词之后导致扩展列的长度可能是原字段几倍甚至几十倍，所以务必在开发之前选择和合适分词长度和加密算法，一旦加密开始之后，再更改成本就较高了。像如果手机号我们只支持后8位搜索、身份证号只支持后4位搜索，这样我们就可以通过原字段截取后面位数直接加密存储到扩展列，不需要再分词。

## 3.数据脱敏

实际的业务开发过程中，我们经常需要对用户的隐私数据进行脱敏处理。所谓脱敏处理其实就是将数据进行混淆隐藏，例如用户手机信息展示`178****5939`，以免泄露个人隐私信息。

### 3.1实现思路

思路比较简单：**在接口返回数据之前按要求对数据进行脱敏加工之后再返回前端。**

一开始打算用@ControllerAdvice去实现，但发现需要自己去反射类获取注解，当返回对象比较复杂，需要递归去反射，性能一下子就会降低，于是换种思路，我想到平时使用的@JsonFormat，跟我现在的场景很类似，通过自定义注解跟字段解析器，对字段进行自定义解析。

**脱敏字段类型枚举**

```java
public enum MaskEnum {
    /**
     * 中文名
     */
    CHINESE_NAME,
    /**
     * 身份证号
     */
    ID_CARD,
    /**
     * 座机号
     */
    FIXED_PHONE,
    /**
     * 手机号
     */
    MOBILE_PHONE,
    /**
     * 地址
     */
    ADDRESS,
    /**
     * 电子邮件
     */
    EMAIL,
    /**
     * 银行卡
     */
    BANK_CARD
}
```

**脱敏注解类**：用在脱敏字段之上

```java
@Retention(RetentionPolicy.RUNTIME)
@JacksonAnnotationsInside
@JsonSerialize(using = MaskSerialize.class)
public @interface FieldMask {

    /**
     * 脱敏类型
     * @return
     */
    MaskEnum value();
}
```

**脱敏序列化类**

```java
public class MaskSerialize extends JsonSerializer<String> implements ContextualSerializer {

    /**
     * 脱敏类型
     */
    private MaskEnum type;


    @Override
    public void serialize(String s, JsonGenerator jsonGenerator, SerializerProvider serializerProvider) throws IOException {
        switch (this.type) {
            case CHINESE_NAME:
            {
                jsonGenerator.writeString(MaskUtils.chineseName(s));
                break;
            }
            case ID_CARD:
            {
                jsonGenerator.writeString(MaskUtils.idCardNum(s));
                break;
            }
            case FIXED_PHONE:
            {
                jsonGenerator.writeString(MaskUtils.fixedPhone(s));
                break;
            }
            case MOBILE_PHONE:
            {
                jsonGenerator.writeString(MaskUtils.mobilePhone(s));
                break;
            }
            case ADDRESS:
            {
                jsonGenerator.writeString(MaskUtils.address(s, 4));
                break;
            }
            case EMAIL:
            {
                jsonGenerator.writeString(MaskUtils.email(s));
                break;
            }
            case BANK_CARD:
            {
                jsonGenerator.writeString(MaskUtils.bankCard(s));
                break;
            }
        }
    }

    @Override
    public JsonSerializer <?> createContextual(SerializerProvider serializerProvider, BeanProperty beanProperty) throws JsonMappingException {
        // 为空直接跳过
        if (beanProperty == null) {
            return serializerProvider.findNullValueSerializer(beanProperty);
        }
        // 非String类直接跳过
        if (Objects.equals(beanProperty.getType().getRawClass(), String.class)) {
            FieldMask fieldMask = beanProperty.getAnnotation(FieldMask.class);
            if (fieldMask == null) {
                fieldMask = beanProperty.getContextAnnotation(FieldMask.class);
            }
            if (fieldMask != null) {
                // 如果能得到注解，就将注解的 value 传入 MaskSerialize
                return new MaskSerialize(fieldMask.value());
            }
        }
        return serializerProvider.findValueSerializer(beanProperty.getType(), beanProperty);
    }

    public MaskSerialize() {}

    public MaskSerialize(final MaskEnum type) {
        this.type = type;
    }
}
```

### 3.2使用示例

在发送短信记录的接口上对手机号进行脱敏：

```java
@FieldMask(MaskEnum.MOBILE_PHONE)
private String mobile;
```

调用接口返回数据如下：

```json
{
  "code": 200,
  "msg": "OK",
  "data": {
    "list": [
      {
        "id": 1565599123774607362,
        "signId": 8389008488923136,
        "templateId": 8445337328943104,
        "templateType": 1,
        "content": "可爱的${name}，博客文章已于${submitTime}上传更新，请抽空浏览。",
        "channelType": 0,
        "mobile": "178****5939",
        "sendStatus": 0,
        "receiveStatus": 0
      }
    ],
    "total": 19,
    "pages": 19
  }
}
```

## 4.总结

基于上面内容我们总结如何在数据存储层进行数据安全加固来达到系统的更安全性，可以这么说没有最安全的系统只有更安全的系统。所以我们在开发历程中都会穷极一生去加固系统安全性能。当然了，加强系统安全性的方式还有很多种，我们最近只是围绕基于`Spring Boot`和`SpringMVC`框架中有效优雅地实现数据安全性，感兴趣的小伙伴可以自行了解其他加固方式。（近来复阳了，状态不是很好，效率不高所以有点拖拉更新啦）



作者：Shepherd
链接：https://juejin.cn/post/7238617870267580477
来源：稀土掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
