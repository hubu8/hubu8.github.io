# Stanford CoreNLP 词云


<!--more-->

 Stanford Nlp是一个比较牛叉的自然语言处理工具，其很多模型都是基于深度学习方法进行训练得到的，准确率比起原来的很多工具有了很大程度的提高。近年来很多开源项目也用到了其中的一些方法。

   最近重拾这个工具做点语义分析的工作，但是发现中文资料比较少，入门比较困难，所以整理一下自己的使用方法，希望对有需要的童鞋能够有点帮助。

   本文主要是讲如何在Java工程中调用Stanford NLP的API。

## 一、环境准备（导入依赖）

### 1、pom原始导入

```xml
    <properties>
        <corenlp.version>4.0.0</corenlp.version>
    </properties>
 
    <dependencies>
        <dependency>
            <groupId>edu.stanford.nlp</groupId>
            <artifactId>stanford-corenlp</artifactId>
            <version>${corenlp.version}</version>
        </dependency>
 
        <dependency>
            <groupId>edu.stanford.nlp</groupId>
            <artifactId>stanford-corenlp</artifactId>
            <version>${corenlp.version}</version>
            <classifier>models</classifier>
        </dependency>
 
        <dependency>
            <groupId>edu.stanford.nlp</groupId>
            <artifactId>stanford-corenlp</artifactId>
            <version>${corenlp.version}</version>
            <classifier>models-chinese</classifier>
        </dependency>
    </dependencies>
```

如果上述方法pom不能导入可以选择第二种方法，把jar包下载到本地，然后本地导入

### 2、本地导入

[下载地址](https://stanfordnlp.github.io/CoreNLP/index.html)主要下载两个文件

![image-20210509081926890](/java_images/image-20210509081926890.png)

![image-20210509081945599](/java_images/image-20210509081945599.png)

在resources目录下新建lib包，将一下三个文件导入：

![image-20210509082333232](/java_images/image-20210509082333232.png)

pom文件导入本地Jar

```xml
<dependency>
            <groupId>edu.stanford.nlp</groupId>
            <artifactId>stanford-corenlp</artifactId>
            <version>4.2.1</version>
            <scope>system</scope>
            <systemPath>${project.basedir}/src/main/resources/lib/stanford-corenlp-4.2.1.jar</systemPath>
        </dependency>
        <dependency>
            <groupId>edu.stanford.nlp</groupId>
            <artifactId>stanford-corenlp</artifactId>
            <version>4.2.1</version>
            <classifier>models-chinese</classifier>
            <scope>system</scope>
            <systemPath>${project.basedir}/src/main/resources/lib/stanford-corenlp-4.2.1-models-chinese.jar</systemPath>
        </dependency>
        <dependency>
            <groupId>org.ejml</groupId>
            <artifactId>ejml</artifactId>
            <version>0.39</version>
<!--            <classifier>models</classifier>-->
            <scope>system</scope>
            <systemPath>${project.basedir}/src/main/resources/lib/ejml-core-0.39.jar</systemPath>
        </dependency>
        <dependency>
            <groupId>edu.stanford.nlp</groupId>
            <artifactId>stanford-corenlp</artifactId>
            <version>4.2.1</version>
            <classifier>models</classifier>
            <scope>system</scope>
            <systemPath>${project.basedir}/src/main/resources/lib/stanford-corenlp-4.2.1-models.jar</systemPath>
        </dependency>
```

## 二、分词

```java
package com.jd.posttimer.Util.SegmentationUtil;

import edu.stanford.nlp.ling.CoreAnnotations;
import edu.stanford.nlp.ling.CoreLabel;
import edu.stanford.nlp.pipeline.Annotation;
import edu.stanford.nlp.pipeline.StanfordCoreNLP;
import edu.stanford.nlp.util.CoreMap;
import edu.stanford.nlp.util.StringUtils;

import java.util.ArrayList;
import java.util.List;
import java.util.Properties;

/**
 *  * Created by dd on 2017/6/8.
 *  * 斯坦福NLP 包，中文分词和英文分词
 *  
 */
public class Segmentation {

    public List segInCh(String text) {
//载入properties 文件
//        StanfordCoreNLP pipline = new StanfordCoreNLP("StanfordCoreNLP-chinese.properties");

        //1.2 自定义功能 （1）
//        Properties properties = new Properties();
//        properties.put("annotators", "tokenize, ssplit, pos, lemma, ner, parse, dcoref");
//        properties.setProperty("annotators", "tokenize, ssplit, pos, lemma, ner, parse, dcoref");
//        properties.setProperty("coref.algorithm", "neural");
//        properties.setProperty("add-modules ","java.se.ee");
//        properties.setProperty("ner.useSUTime", "false");
//        StanfordCoreNLP pipline = new StanfordCoreNLP(properties);

//        自定义功能(2) 自己在项目中建一个properties 文件，然后在文件中设置模型属性，可以参考1中的配置文件
        String[] args = new String[]{"-props", "classpath:/properies/StanfordCoreNLP-chinese.properties"};
        Properties properties = StringUtils.argsToProperties(args);
        StanfordCoreNLP pipline = new StanfordCoreNLP(properties);

        //自定义功能(3)

//        StanfordCoreNLP pipline = new StanfordCoreNLP(PropertiesUtils.asProperties(
//                "annotators","tokenize,ssplit",
//                "ssplit.isOneSentence", "true",
//                "tokenize.language", "zh",
//                "segment.model", "edu/stanford/nlp/models/segmenter/chinese/ctb.gz",
//                "segment.sighanCorporaDict", "edu/stanford/nlp/models/segmenter/chinese",
//                "segment.serDictionary", "edu/stanford/nlp/models/segmenter/chinese/dict-chris6.ser.gz",
//                "segment.sighanPostProcessing", "true"
//        ));

        //创建一个解析器，传入的是需要解析的文本
        Annotation annotation = new Annotation(text);

        //解析
        pipline.annotate(annotation);

        //根据标点符号，进行句子的切分，每一个句子被转化为一个CoreMap的数据结构，保存了句子的信息()
        List<CoreMap> sentences = annotation.get(CoreAnnotations.SentencesAnnotation.class);

        //从CoreMap 中取出CoreLabel List ,打印
        System.out.println("word\tpos\tlemma\tner");
        List<String> list=new ArrayList();
        for (CoreMap sentence : sentences) {
            for (CoreLabel token : sentence.get(CoreAnnotations.TokensAnnotation.class)) {
//                "tokenize, ssplit, pos, lemma, ner, parse, dcoref",
                String word = token.get(CoreAnnotations.TextAnnotation.class);
//                String pos = token.get(CoreAnnotations.PartOfSpeechAnnotation.class);
//                String ne = token.get(CoreAnnotations.NamedEntityTagAnnotation.class);
//                String lemma = token.get(CoreAnnotations.LemmaAnnotation.class);
//                System.out.println(word+"\t"+pos+"\t"+lemma+"\t"+ne);
                list.add(word);
                System.out.println(word);
            }
        }
        return list;
    }
}
```

代码中一共定义了3中配置模型属性的方法，第一种、第三种需要自己写，容易出错，第二种jar包中有默认的配置文件，只需要做部分修改就可以用，但是jar包里的文件只读，所以需要复制出来

![image-20210509083109304](/java_images/image-20210509083109304.png)

```shell
2021-05-09 08:31:41.066  INFO 12596 --- [nio-9981-exec-1] e.stanford.nlp.pipeline.StanfordCoreNLP  : Adding annotator coref
2021-05-09 08:31:41.107 ERROR 12596 --- [nio-9981-exec-1] e.stanford.nlp.pipeline.CorefAnnotator   : Error creating CorefAnnotator...terminating pipeline construction!
2021-05-09 08:31:41.108 ERROR 12596 --- [nio-9981-exec-1] e.stanford.nlp.pipeline.CorefAnnotator   : java.lang.RuntimeException: Error initializing coref system
  edu.stanford.nlp.coref.CorefSystem.<init>(CorefSystem.java:44)
  ...
  Caused by: class edu.stanford.nlp.io.RuntimeIOException: java.io.IOException: Unable to open "edu/stanford/nlp/models/dcoref/demonyms.txt" as class path, filename or URL
  ...
```

默认配置文件的bug,模型配置了coref（同义词分辨）但是没有找到，想到还有一个modles没有用到，导入进来

![image-20210509083842970](/java_images/image-20210509083842970.png)



pS：该代码的思想是将text字符串交给Stanford CoreNLP处理，StanfordCoreNLP的各个组件（annotator）按“tokenize（分词）, ssplit（断句）, pos（词性标注）, lemma（词元化）, ner（命名实体识别）, parse（语法分析）, dcoref（同义词分辨）”顺序进行处理。

### 1、工具类调用service

工具类为了调用service方法：

```java
package com.jd.posttimer.Util;

import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.BeansException;
import org.springframework.beans.factory.DisposableBean;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;


@Slf4j
public class BeanUtil implements ApplicationContextAware, DisposableBean {

    private static ApplicationContext applicationContext = null;

    /**
     * 从静态变量applicationContext中取得Bean, 自动转型为所赋值对象的类型.
     */
    public static <T> T getBean(Class<T> requiredType) {
        if(applicationContext==null){
            throw new IllegalStateException("applicaitonContext属性未注入, 请在SpringBoot启动类中注册BeanUtil.");
        }
        return applicationContext.getBean(requiredType);
    }



    @Override
    public void destroy() {
        applicationContext = null;
    }

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        if (BeanUtil.applicationContext != null) {
            log.warn("BeanUtil中的ApplicationContext被覆盖, 原有ApplicationContext为:" + BeanUtil.applicationContext);
        }
        BeanUtil.applicationContext = applicationContext;
    }
}

```

主类注入

```java
@Bean
    public BeanUtil beanUtil() {
        return new BeanUtil();
    }
```

调用

```java
	private static LiuMessageService service;
    private static RedisUtils redisUtils;

    @Override
    public void run(){
        service= BeanUtil.getBean(LiuMessageService.class);
        redisUtils=BeanUtil.getBean(RedisUtils.class);
        getWordCloud();
    }
```



### 2、调用分词生成词云

词云依赖：

```xml
<dependency>
            <groupId>com.kennycason</groupId>
            <artifactId>kumo-core</artifactId>
            <version>1.13</version>
        </dependency>
        <dependency>
            <groupId>com.kennycason</groupId>
            <artifactId>kumo-tokenizers</artifactId>
            <version>1.12</version>
        </dependency>
```

生成词云，保存到redis,不等待结果，返回的是上一次的结果

```java
@GetMapping("wordscloud")
    public  String getWordCloud() throws IOException {
        WordsCloud wordsCloud=new WordsCloud();
        wordsCloud.start();
        System.out.println("wordscloud");
        return redisUtils.get("words_cloud");
    }
```

WordsCloud词云类

```java
package com.jd.posttimer.Util.wordscloud;

import com.jd.posttimer.Util.BeanUtil;
import com.jd.posttimer.Util.Redis.RedisUtils;
import com.jd.posttimer.Util.SegmentationUtil.Segmentation;
import com.jd.posttimer.entity.LiuMessage;
import com.jd.posttimer.service.PostEmailSer.LiuMessageService;
import com.kennycason.kumo.CollisionMode;
import com.kennycason.kumo.WordCloud;
import com.kennycason.kumo.WordFrequency;
import com.kennycason.kumo.bg.CircleBackground;
import com.kennycason.kumo.font.KumoFont;
import com.kennycason.kumo.font.scale.SqrtFontScalar;
import com.kennycason.kumo.nlp.FrequencyAnalyzer;
import com.kennycason.kumo.nlp.tokenizers.ChineseWordTokenizer;
import com.kennycason.kumo.palette.LinearGradientColorPalette;
import org.apache.tomcat.util.codec.binary.Base64;

import java.awt.*;
import java.io.ByteArrayOutputStream;
import java.io.OutputStream;
import java.util.ArrayList;
import java.util.List;
import java.util.Random;

//@Component
public class WordsCloud extends Thread{

//    private LiuMessageService liuMessageService;
    private static LiuMessageService service;
    private static RedisUtils redisUtils;

    @Override
    public void run(){
        service= BeanUtil.getBean(LiuMessageService.class);
        redisUtils=BeanUtil.getBean(RedisUtils.class);
        getWordCloud();
    }


    public void getWordCloud(){
        FrequencyAnalyzer frequencyAnalyzer = new FrequencyAnalyzer();
        frequencyAnalyzer.setWordFrequenciesToReturn(600);
        frequencyAnalyzer.setMinWordLength(2);

        // 引入中文解析器
        frequencyAnalyzer.setWordTokenizer(new ChineseWordTokenizer());
        List<LiuMessage> messages = service.findAllByStatus(1);
        System.out.println(messages.toString());
        String words="";
        for (LiuMessage liu:messages) {
            words+=liu.getMessage();
        }
        Segmentation segmentation=new Segmentation();
        List<String> list = segmentation.segInCh(words);

        System.out.println("list:"+list.toString());
        //加入分词并随机生成权重，每次生成得图片都不一样,图片基数大可以这么玩
        List<WordFrequency> wordFrequencyList=new ArrayList<>();
        for ( String liu : list){
            wordFrequencyList.add(new WordFrequency(liu,new Random().nextInt(list.size())));
        }
//        原文链接：https://blog.csdn.net/b644ROfP20z37485O35M/article/details/97583505
//        final List<WordFrequency> wordFrequencyList = frequencyAnalyzer.load(words);

        // 设置图片分辨率
        Dimension dimension = new Dimension(500, 500);
        // 此处的设置采用内置常量即可，生成词云对象
        WordCloud wordCloud = new WordCloud(dimension, CollisionMode.PIXEL_PERFECT);
        java.awt.Font font = new java.awt.Font("STSong-Light", 2, 18);
        wordCloud.setKumoFont(new KumoFont(font));
        wordCloud.setPadding(2);
//        wordCloud.setColorPalette(new ColorPalette(new Color(237, 25, 65),
//                new Color(242, 101, 34), new Color(132, 85, 56),
//                new Color(138, 93, 25),new Color(127, 117, 34),
//                new Color(92, 122, 41),new Color(29, 149, 63),
//                new Color(0, 125, 101),new Color(101, 194, 148)));
        wordCloud.setColorPalette(new LinearGradientColorPalette(Color.RED, Color.BLUE, Color.GREEN, 30, 30));
        wordCloud.setBackground(new CircleBackground(200));
        wordCloud.setFontScalar(new SqrtFontScalar(10, 40));
        wordCloud.setBackgroundColor(new Color(255, 255, 255));
        // 生成词云
        wordCloud.build(wordFrequencyList);
        OutputStream output = new ByteArrayOutputStream();
        wordCloud.writeToStream("png", output);
        byte[] outputByte = ((ByteArrayOutputStream)output).toByteArray();

        redisUtils.getAndSet("words_cloud", Base64.encodeBase64String(outputByte));
    }
}
```

什么是异步调用

对象controller中的方法调用对象"wordscloud"的方法，程序并不需要等待对象a的方法返回结果值，直接继续往下走。

为什么不同步？

由于模型的构建复杂性，生成一个模型并返回结果需要40+秒，如果模型复杂度增加，还会增加响应时间，影响客户端体验。

## 三、前端结果显示

后端传的数据是base64编码后的字符串，怎么显示在前端？

```javascript
			uni.request({
				url: config.baseUrl + '/liu/wordscloud',
				method: 'GET',
				success: res => {
					console.log(typeof(res.data))
					this.imageHerf='data:image/jpeg;base64,'+res.data
				},
				fail: (res) => {
					console.log("词云失败结果")
					console.log(res)
				}
			})
```

```html
<view>
			<van-image class="code text-right" width="100%" height="250px" fit="cover" mode=""
				:src="imageHerf.replace(/[\r\n]/g,'')" />
		</view>
```

![image-20210509095316236](/java_images/image-20210509095316236.png)
