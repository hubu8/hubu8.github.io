# SpringBoot项目打包瘦身


<!--more-->

```xml
    <build>
        <finalName>ruoyi-admin</finalName>
        <plugins>
            <!-- 1、编译出不带 lib 文件夹的Jar包 -->
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <version>2.2.2.RELEASE</version>
                <configuration>
                    <!--表示编译版本配置有效-->
                    <fork>true</fork>
                    <!--引入第三方jar包时,不添加则引入的第三方jar不会被打入jar包中-->
                    <includeSystemScope>true</includeSystemScope>
                    <!--排除第三方jar文件-->
                    <includes>
                        <include>
                            <groupId>nothing</groupId>
                            <artifactId>nothing</artifactId>
                        </include>
                    </includes>
                </configuration>
                <executions>
                    <execution>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>

            <!-- 2、完成对Java代码的编译，可以指定项目源码的jdk版本，编译后的jdk版本，以及编码 -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <!-- 源代码使用的JDK版本 -->
                    <source>${java.version}</source>
                    <!-- 需要生成的目标class文件的编译版本 -->
                    <target>${java.version}</target>
                    <!-- 字符集编码 -->
                    <encoding>UTF-8</encoding>
                    <!-- 用来传递编译器自身不包含但是却支持的参数选项 -->
                    <compilerArguments>
                        <verbose/>
                        <!-- windwos环境（二选一） -->
                        <!--<bootclasspath>${java.home}/lib/rt.jar;${java.home}/lib/jce.jar</bootclasspath>-->
                        <bootclasspath>C:/Program Files/Java/jdk1.8.0_301/jre/lib/rt.jar;C:/Program Files/Java/jdk1.8.0_301/jre/lib/jce.jar</bootclasspath>
                        <!-- Linux环境（二选一） -->
                        <!--<bootclasspath>${java.home}/lib/rt.jar:${java.home}/lib/jce.jar</bootclasspath>-->
                    </compilerArguments>
                </configuration>
            </plugin>

            <!-- 3、将所有依赖的jar文件复制到target/lib目录 -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
                <version>2.8</version>
                <executions>
                    <execution>
                        <id>copy-dependencies</id>
                        <phase>prepare-package</phase>
                        <goals>
                            <goal>copy-dependencies</goal>
                        </goals>
                        <configuration>
                            <!--复制到哪个路径，${project.build.directory} 缺醒为 target，其他内置参数见下面解释-->
                            <outputDirectory>${project.build.directory}/lib</outputDirectory>
                            <overWriteReleases>false</overWriteReleases>
                            <overWriteSnapshots>false</overWriteSnapshots>
                            <overWriteIfNewer>true</overWriteIfNewer>
                        </configuration>
                    </execution>
                </executions>
            </plugin>

            <!-- 4、指定启动类，指定配置文件，将依赖打成外部jar包 -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <configuration>
                    <archive>
                        <manifest>
                            <!-- 是否要把第三方jar加入到类构建路径 -->
                            <addClasspath>true</addClasspath>
                            <!-- 外部依赖jar包的最终位置 -->
                            <classpathPrefix>lib/</classpathPrefix>
                            <!-- 项目启动类 -->
                            <mainClass>com.ruoyi.RuoYiApplication</mainClass>
                        </manifest>
                    </archive>
                    <!--资源文件不打进jar包中，做到配置跟项目分离的效果-->
                    <excludes>
                        <!-- 业务jar中过滤application.properties/yml文件，在jar包外控制 -->
                        <exclude>*.properties</exclude>
                        <exclude>*.xml</exclude>
                        <exclude>*.yml</exclude>
                    </excludes>
                </configuration>
            </plugin>

        </plugins>
    </build>
```

includeSystemScope：jar包分两种

- 一种是spring、mybatis等这种项目依赖的
- 一种是手动引入的第三方 jar 依赖

> 如果该参数不设置为 true 的话是不能被打包进来的

includes：排除项目中所有的 jar

> 我们需要将打包插件替换为 maven-jar-plugin，然后使用该插件拷贝依赖到 jar 到外面的 lib 目录

- maven-compiler-plugin：

完成对Java代码的编译，可以指定项目源码的jdk版本、编译后的jdk版本，以及编码，如果不写这个插件也是没问题的，不写会使用默认的 jdk 版本来处理，只是这样容易出现版本不匹配的问题，比如本地maven环境用的3.3.9版本，默认会使用jdk1.5进行编译，而项目中用的jdk1.8的编译环境，那就会导致打包时编译不通过。

- maven-dependency-plugin：

作用就是将所有依赖的jar文件复制到指定目录下

- maven-jar-plugin：

主要作用就是将maven工程打包成jar包。

- 打包时排除资源配置文件，
- 排除了配置文件那么项目启动是怎么读取呢？配置文件有这么一个默认的优先级

当前项目config目录下 > 当前项目根目录下 > 类路径config目录下 > 类路径根目录下
因此只需要

**打包命令：`mvn clean package`**



以上打包出来的工程jar包分为两部分，依赖包和主程序

```sh
target
	/lib
	ruoyi-admin.jar
	...
```

**但是上述打包分离出来了yml/properties/xml等配置文件，需要在jar的同级创建config目录放置配置文件**

```
xxxx
	/lib
	/config
	ruoyi-admin.jar
```


