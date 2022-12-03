# 分布式ID生成算法Snowflake的原理与应用


<!--more-->

## 分布式ID生成算法Snowflake的原理与应用

### UUID

UUID(universally unique identifier) 长度是128 bit，也就是由32个16进制数值组成。其中 M 表示 version，N 表示 Variants xxxxxxxx-xxxx-Mxxx-Nxxx-xxxxxxxxxxxx。例如：1e92d156-ffd6-4a38-9275-f66c31c31e13



UUID由以下几部分的组合：

1. 当前日期和时间，
2. 时钟序列。
3. 全局唯一的IEEE机器识别号，如果有网卡，从网卡MAC地址获得，没有网卡以其他方式获得。

优点：
1. 本地生成ID，不需要远程调用，时延低，适用于数据量小。



当数据量大的时候，UUID用作数据库主键会存在以下问题：

1. UUID比较长，占用空间大，会间接导致数据库性能下降。
2. 无序。会导致B+树索引在写的时候有过多的随机写操作、叶分裂等问题，导致数据库性能下降。
3. 信息安全。UUID可能包含了MAC地址信息。

### Snowflake

#### 概述

SnowFlake算法生成id的结果是一个64bit大小的整数，它的结构如下图：

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/2/26/1692952e7ee6f2a3~tplv-t2oaga2asx-zoom-in-crop-mark:4536:0:0:0.image)


1) 1位，不用。二进制中最高位为1的都是负数，但是我们生成的id一般都使用整数，所以这个最高位固定是0
2) 41位，用来记录时间戳（毫秒）。
3) 41位可以表示2^41−1个数字，如果只用来表示正整数（计算机中正数包含0），可以表示的数值范围是：0 至 2^41−1，减1是因为可表示的数值范围是从0开始算的，而不是1。
也就是说41位可以表示2^41−1个毫秒的值，转化成单位年则是(2^41−1)/(1000∗60∗60∗24∗365)=69年
4) 10位，用来记录工作机器id。
可以部署在2^10=1024个节点，包括5位datacenterId和5位workerId
5) 5位（bit）可以表示的最大正整数是2^5−1=31，即可以用0、1、2、3、....31这32个数字，来表示不同的datecenterId或workerId
6) 12位，序列号，用来记录同毫秒内产生的不同id。
12位（bit）可以表示的最大正整数是2^12−1=4095，即可以用0、1、2、3、....4094这4095个数字，来表示同一机器同一时间截（毫秒)内产生的4095个ID序号
由于在Java中64bit的整数是long类型，所以在Java中SnowFlake算法生成的id就是long来存储的。

#### 作用

SnowFlake可以保证：

所有生成的id按时间趋势递增

整个分布式系统内不会产生重复id（因为有datacenterId和workerId来做区分）

#### java实现

```java
public class SnowFlake {

    /**
     * 起始的时间戳:这个时间戳自己随意获取，比如自己代码的时间戳
     */
    private final static long START_STMP = 1543903501000L;

    /**
     * 每一部分占用的位数
     */
    private final static long SEQUENCE_BIT = 12; //序列号占用的位数
    private final static long MACHINE_BIT = 5;  //机器标识占用的位数
    private final static long DATACENTER_BIT = 5;//数据中心占用的位数

    /**
     * 每一部分的最大值：先进行左移运算，再同-1进行异或运算；异或：相同位置相同结果为0，不同结果为1
     */
     /** 用位运算计算出最大支持的数据中心数量：31 */
    private final static long MAX_DATACENTER_NUM = -1L ^ (-1L << DATACENTER_BIT);
    
    /** 用位运算计算出最大支持的机器数量：31 */
    private final static long MAX_MACHINE_NUM = -1L ^ (-1L << MACHINE_BIT);
    
    /** 用位运算计算出12位能存储的最大正整数：4095 */
    private final static long MAX_SEQUENCE = -1L ^ (-1L << SEQUENCE_BIT);

    /**
     * 每一部分向左的位移
     */
     
     /** 机器标志较序列号的偏移量 */
    private final static long MACHINE_LEFT = SEQUENCE_BIT;
    
    /** 数据中心较机器标志的偏移量 */
    private final static long DATACENTER_LEFT = SEQUENCE_BIT + MACHINE_BIT;
    
    /** 时间戳较数据中心的偏移量 */
    private final static long TIMESTMP_LEFT = DATACENTER_LEFT + DATACENTER_BIT;

    private static long datacenterId;  //数据中心
    private static long machineId;    //机器标识
    private static long sequence = 0L; //序列号
    private static long lastStmp = -1L;//上一次时间戳

	 /** 此处无参构造私有，同时没有给出有参构造，在于避免以下两点问题：
	 	  1、私有化避免了通过new的方式进行调用，主要是解决了在for循环中通过new的方式调用产生的id不一定唯一问题问题，因为用于			 记录上一次时间戳的lastStmp永远无法得到比对；
	 	  2、没有给出有参构造在第一点的基础上考虑了一套分布式系统产生的唯一序列号应该是基于相同的参数
	  */
    private SnowFlake(){}

    /**
     * 产生下一个ID
     *
     * @return
     */
    public static synchronized long nextId() {
    	  /** 获取当前时间戳 */
        long currStmp = getNewstmp();
        
        /** 如果当前时间戳小于上次时间戳则抛出异常 */
        if (currStmp < lastStmp) {
            throw new RuntimeException("Clock moved backwards.  Refusing to generate id");
        }
		 /** 相同毫秒内 */
        if (currStmp == lastStmp) {
            //相同毫秒内，序列号自增
            sequence = (sequence + 1) & MAX_SEQUENCE;
            //同一毫秒的序列数已经达到最大
            if (sequence == 0L) {
            
            		/** 获取下一时间的时间戳并赋值给当前时间戳 */
                currStmp = getNextMill();
            }
        } else {
            //不同毫秒内，序列号置为0
            sequence = 0L;
        }
		 /** 当前时间戳存档记录，用于下次产生id时对比是否为相同时间戳 */
        lastStmp = currStmp;


        return (currStmp - START_STMP) << TIMESTMP_LEFT //时间戳部分
                | datacenterId << DATACENTER_LEFT      //数据中心部分
                | machineId << MACHINE_LEFT            //机器标识部分
                | sequence;                            //序列号部分
    }

    private static long getNextMill() {
        long mill = getNewstmp();
        while (mill <= lastStmp) {
            mill = getNewstmp();
        }
        return mill;
    }

    private static long getNewstmp() {
        return System.currentTimeMillis();
    }

}
```

#### 缺点

雪花算法强依赖机器时钟，如果因为不可抗力或者人为因素导致机器时间出现倒退，则可能导致生成重复的ID。



以下是几种解决时钟回拨的思路：

1. 回拨时间很短（<=3ms）：Sleep对应的时间差值
2. 回拨时间适中（>3ms <= 100ms）：本地缓存最近100ms内生成的所有最大ID值，当时间回拨处于该范围，通过本地缓存获取最大ID值并加一。

3. 回拨时间较长（> 100ms <=5s）：抛出异常，利用业务端的重试机制，负载到其它正常的ID生成服务实例。

4. 回拨时间很长（> 5s）：通过注册中心下线服务实例，并通知人工干预。


注：时间值为参考值，具体如何设置看实际情况

```java
public synchronized long nextId() {
    	//
        long timestamp = this.timeGen();
    	//检查时钟回拨
        timestamp = this.checkClockBackwards(timestamp);
    	//同时间戳，那么时间戳加1
        if (this.lastTimestamp == timestamp) {
            this.sequence = this.sequence + 1 & this.sequenceMask;
            if (this.sequence == 0) {
                timestamp = this.tilNextMillis(this.lastTimestamp);
            }
        } else if (this.sequence != 0) {
            this.sequence = 0;
        }

        this.lastTimestamp = timestamp;
    	//构造ID
        return timestamp - this.epoch << this.shiftTime | (long)(this.workerId << this.shiftWorker) | (long)this.sequence;
    }

    private long checkClockBackwards(long timestamp) {
        if (timestamp < this.lastTimestamp) {
            boolean fixedByWait = true;
            long offset = this.lastTimestamp - timestamp;
            //小于默认等待时间，那就一直等待，然后重新生成时间戳
            if (offset <= this.tolerantClockBackTimestamp && this.tolerantClockBackTimestamp > 0L) {
                try {
                    this.wait(this.tolerantClockBackTimestamp << 1);
                } catch (InterruptedException var7) {
                }

                timestamp = this.timeGen();
                //通过等待没有修复成功
                if (timestamp < this.lastTimestamp) {
                    fixedByWait = false;
                }
            }

            if (!fixedByWait) {
                //抛出异常
            }
        }

        return timestamp;
    }
```

改造后

```java
public class SnowFlake {

    /**
     * 起始的时间戳:这个时间戳自己随意获取，比如自己代码的时间戳
     */
    private final static long START_STMP = 1543903501000L;

    /**
     * 每一部分占用的位数
     */
    private final static long SEQUENCE_BIT = 12; //序列号占用的位数
    private final static long MACHINE_BIT = 5;  //机器标识占用的位数
    private final static long DATACENTER_BIT = 5;//数据中心占用的位数

    /**
     * 每一部分的最大值：先进行左移运算，再同-1进行异或运算；异或：相同位置相同结果为0，不同结果为1
     */
    /** 用位运算计算出最大支持的数据中心数量：31 */
    private final static long MAX_DATACENTER_NUM = -1L ^ (-1L << DATACENTER_BIT);

    /** 用位运算计算出最大支持的机器数量：31 */
    private final static long MAX_MACHINE_NUM = -1L ^ (-1L << MACHINE_BIT);

    /** 用位运算计算出12位能存储的最大正整数：4095 */
    private final static long MAX_SEQUENCE = -1L ^ (-1L << SEQUENCE_BIT);

    /**
     * 每一部分向左的位移
     */

    /** 机器标志较序列号的偏移量 */
    private final static long MACHINE_LEFT = SEQUENCE_BIT;

    /** 数据中心较机器标志的偏移量 */
    private final static long DATACENTER_LEFT = SEQUENCE_BIT + MACHINE_BIT;

    /** 时间戳较数据中心的偏移量 */
    private final static long TIMESTMP_LEFT = DATACENTER_LEFT + DATACENTER_BIT;

    private static long datacenterId;  //数据中心
    private static long machineId;    //机器标识
    private static long sequence = 0L; //序列号
    private static long lastStmp = -1L;//上一次时间戳

    private static long tolerantClockBackTimestamp = 5000;

    /** 此处无参构造私有，同时没有给出有参构造，在于避免以下两点问题：
     1、私有化避免了通过new的方式进行调用，主要是解决了在for循环中通过new的方式调用产生的id不一定唯一问题问题，因为用于			 记录上一次时间戳的lastStmp永远无法得到比对；
     2、没有给出有参构造在第一点的基础上考虑了一套分布式系统产生的唯一序列号应该是基于相同的参数
     */
    public SnowFlake(){}

    /**
     * 产生下一个ID
     *
     * @return
     */
    public synchronized long nextId() {
        /** 获取当前时间戳 */
        long timestamp = getNewstmp();
        timestamp = checkClockBackwards(timestamp);
        /** 如果当前时间戳小于上次时间戳则抛出异常 */
        if (timestamp < lastStmp) {
            throw new RuntimeException("Clock moved backwards.  Refusing to generate id");
        }
        /** 相同毫秒内 */
        if (timestamp == lastStmp) {
            //相同毫秒内，序列号自增
            sequence = (sequence + 1) & MAX_SEQUENCE;
            //同一毫秒的序列数已经达到最大
            if (sequence == 0L) {

                /** 获取下一时间的时间戳并赋值给当前时间戳 */
                timestamp = getNextMill();
            }
        } else {
            //不同毫秒内，序列号置为0
            sequence = 0L;
        }
        /** 当前时间戳存档记录，用于下次产生id时对比是否为相同时间戳 */
        lastStmp = timestamp;


        return (timestamp - START_STMP) << TIMESTMP_LEFT //时间戳部分
                | datacenterId << DATACENTER_LEFT      //数据中心部分
                | machineId << MACHINE_LEFT            //机器标识部分
                | sequence;                            //序列号部分
    }

    private long getNextMill() {
        long mill = getNewstmp();
        while (mill <= lastStmp) {
            mill = getNewstmp();
        }
        return mill;
    }

    private long getNewstmp() {
        return System.currentTimeMillis();
    }
    private long checkClockBackwards(long timestamp) {
        if (timestamp < lastStmp) {
            boolean fixedByWait = true;
            long offset = lastStmp - timestamp;
            //小于默认等待时间，那就一直等待，然后重新生成时间戳
            if (offset <= tolerantClockBackTimestamp && tolerantClockBackTimestamp > 0L) {
                try {
                    this.wait(tolerantClockBackTimestamp << 1);
                } catch (InterruptedException var7) {
                }

                timestamp = this.getNewstmp();
                //通过等待没有修复成功
                if (timestamp < lastStmp) {
                    fixedByWait = false;
                }
            }

            if (!fixedByWait) {
                //抛出异常
            }
        }

        return timestamp;
    }
}

```

#### 精度问题

![image-20221129164454927](/java_images/image-20221129164454927.png)

值得注意的是，雪花算法在JavaScript中有一个坑。后端在返回ID的时候，需要使用String类型代替Long类型，否则会产生预想不到的错误。

这是因为。在JavaScript中，存在两种数字。Number和BigInt。最常用的，就是number。

最大的Number，叫做`Number.MAX_SAFE_INTEGER`，它的值为：

- 2^53-1 或者
- +/- 9,007,199,254,740,991

众所周知，Java中的Long，是64位的。Js中的这个安全Integer，完全达不到Java中定义的长度。

这就是万恶的`IEEE_754规范`，它在Long长度大于17位时会出现精度丢失的问题。

##### 数据库

```sql
CREATE TABLE user
(
	id BIGINT(20) NOT NULL COMMENT '主键ID',
        #其他字段省略
);
```

##### 实体

```java
@Data
public class User {
    private Long id;
//其他成员变量省略
```

```js
{
id：1297873308628307970,
//其他属性省略
}
```

**后端重新接收回来的id变成了：12978733086283000000，不再是1297873308628307970**

##### 哪里能导致精度丢失？

- 服务端都是Long类型的id，不可能丢失
- 前端是什么类型，JSON字符串转js对象，接收Long类型的是number

Number精度是16位（雪花ID是19位的），So：JS的Number数据类型导致的精度丢失

##### 解决

1. id字段由Long类型改成String类型（表、实体、service、mapper都需要改），且**String做ID查询性能会下降**
2. **后端的ID(Long) ==> Jackson(Long转String) ==> 前端使用String类型的ID,前端使用js string精度就不会丢失了**

最终：**前端用String类型的雪花ID保持精度，后端及数据库继续使用Long(BigINT)类型不影响数据库查询执行效率。**

在Spring Boot应用中，使用Jackson进行JSON序列化的时候怎么将Long类型ID转成String响应给前端。方案如下：

###### 1、设置全局序列化配置

```java
@Configuration
public class JacksonConfig {

  @Bean
  @Primary
  @ConditionalOnMissingBean(ObjectMapper.class)
  public ObjectMapper jacksonObjectMapper(Jackson2ObjectMapperBuilder builder)
  {
    ObjectMapper objectMapper = builder.createXmlMapper(false).build();

    // 全局配置序列化返回 JSON 处理
    SimpleModule simpleModule = new SimpleModule();
    //JSON Long ==> String
    simpleModule.addSerializer(Long.class, ToStringSerializer.instance);
    objectMapper.registerModule(simpleModule);
    return objectMapper;
  }

}
```

![image-20221129165000574](/java_images/image-20221129165000574.png)

###### 2、属性字段加注解

```java
@JsonSerialize(using = LongToStringSerializer.class)
```

```java
/**
 * 将long值转化为字符串
 */
public class LongToStringSerializer extends JsonSerializer<Long> {

    @Override
    public void serialize(Long value, JsonGenerator jsonGenerator, SerializerProvider serializerProvider) throws IOException {
        jsonGenerator.writeString(value == null ? "" : String.valueOf(value));
    }


}
```

![image-20221129170406111](/java_images/image-20221129170406111.png)

数据实体转换规则（不太准确，以实际测试为准）：

- 如果实体属性第一个首字母大写，那就会增加一个首字母小写的属性存储Long类型数据；
- 如果属性全部大写，增加属性转全部小写；
- 全是小写，不在家属性字段，原字段直接传String

### UidGenerator

UidGenerator是百度开源的Java语言实现，基于Snowflake算法的唯一ID生成器。
它是[分布式](https://so.csdn.net/so/search?q=分布式&spm=1001.2101.3001.7020)的，并克服了雪花算法的并发限制。
[单个实例的QPS能超过6000000。](https://blog.csdn.net/qq_29917503/article/details/128039707)
需要的环境：JDK8+，MySQL（用于分配WorkerId）

但是百度对这些组成部分稍微调整了一下：

![在这里插入图片描述](/java_images/20200910221235690.jpeg)

由上图可知，UidGenerator的时间部分只有28位，这就意味着UidGenerator默认只能承受8.5年（2^28-1/86400/365）
也可以根据你业务的需求，UidGenerator可以适当调整delta seconds、worker node id和sequence占用位数。

官方没有官方的jar包，所以使用需要自己打包，加入maven依赖

#### 打包

先去[仓库拉取代码](https://github.com/baidu/uid-generator)

拖进IDEA打包（需要先点击禁止按钮，跳过打包测试，否则会报错，因为这个包需要连接数据库创建对象）

![image-20221202160251439](/common_images/image-20221202160251439.png)

#### 加入依赖

```shell
mvn install:install-file -Dfile=E:\work\jar\uid-generator-1.0.0-SNAPSHOT.jar -DgroupId=com.baidu.fsg -DartifactId=uid-generator -Dversion=1.0.0-SNAPSHOT -Dpackaging=jar
```

**-Dfile=** 后边填写你的jar包具体位置
**-DgroupId=** 后边对应pom中*groupId*中的名称
**-DartifactId=** 后边对应pom中artifactId中名称
**-Dversion=** 后边对应pom中version中名称

pom文件：

```xml
<name>UID-Generator</name>
<description>An unique id generator</description>
<groupId>com.baidu.fsg</groupId>
<artifactId>uid-generator</artifactId>
<version>1.0.0-SNAPSHOT</version>
```

![image-20221202161144279](/common_images/image-20221202161144279.png)

也可以在全局安装jar包

pom加入依赖

```
<dependency>
    <groupId>com.baidu.fsg</groupId>
    <artifactId>uid-generator</artifactId>
    <version>1.0.0-SNAPSHOT</version>
</dependency>
```



#### 执行WORKER_NODE表脚本

```sql
DROP TABLE IF EXISTS WORKER_NODE;
CREATE TABLE WORKER_NODE
(
ID BIGINT NOT NULL AUTO_INCREMENT COMMENT 'auto increment id',
HOST_NAME VARCHAR(64) NOT NULL COMMENT 'host name',
PORT VARCHAR(64) NOT NULL COMMENT 'port',
TYPE INT NOT NULL COMMENT 'node type: CONTAINER(1), ACTUAL(2), FAKE(3)',
LAUNCH_DATE DATE NOT NULL COMMENT 'launch date',
MODIFIED TIMESTAMP NOT NULL COMMENT 'modified time',
CREATED TIMESTAMP NOT NULL COMMENT 'created time',
PRIMARY KEY(ID)
)
 COMMENT='DB WorkerID Assigner for UID Generator',ENGINE = INNODB;
```



#### maven中加入依赖

```xml
<dependency>
    <groupId>com.github.wujun234</groupId>
    <artifactId>uid-generator-spring-boot-starter</artifactId>
    <version>${uid-generator.version}</version>
</dependency>

```

#### 配置文件application.yml里引入自定义配置

```yml
# UidGenerator
# 初始时间, 默认:"2019-02-20"
uid:
  epochStr: 2020-05-08
  # 时间位, 默认:30
  timeBits: 41
  # 机器位, 默认:16
  workerBits: 10
  # 序列号, 默认:7
  seqBits: 12
  # 是否容忍时钟回拨, 默认:true
  enableBackward: true
  # RingBuffer size扩容参数, 可提高UID生成的吞吐量, 默认:3
  CachedUidGenerator:
    boostPower: 3
  # 指定何时向RingBuffer中填充UID, 取值为百分比(0, 100), 默认为50
    paddingFactor: 50
```

#### IdGenerator.java工具类

这边我们使用`CachedUidGenerato`

```java
@Component
public class IdGenerator {
    @Autowired
    private CachedUidGenerator cachedUidGenerator;

    /**
     * 获取uid
     *
     * @return
     */
    public long nextId() {
        return cachedUidGenerator.getUID();
    }

    /**
     * 格式化传入的uid，方便查看其实际含义
     *
     * @param uid
     * @return
     */
    public String parse(long uid) {
        return cachedUidGenerator.parseUID(uid);
    }
}
```


