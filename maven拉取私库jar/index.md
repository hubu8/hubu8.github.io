# Maven拉取私库JAR


<!--more-->

​	springboot依赖导入的时候需要从公网仓库拉取JAR包，同时为了自己代码的安全性，自己也会封装一部分JAR包上传到自己的私库或者内网服务器上

	在项目中使用Maven管理jar包依赖，往往会出现以下状况：

1、国内访问maven默认远程中央镜像特别慢；

2、使用阿里的镜像替代远程中央镜像；

3、阿里云镜像中缺少部分jar包；

4、同时使用私有仓库和公有仓库；

针对以上情况，我们就需要让Maven支持多仓库配置。

单独仓库配置
当只配置一个仓库时，操作比较简单，直接在Maven的settings.xml文件中进行全局配置即可，以阿里云的镜像为例：

```xml
<mirrors>
    <mirror>
        <id>alimaven</id>
        <name>aliyun maven</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
        <mirrorOf>central</mirrorOf>
    </mirror>
</mirrors>
```


只用新增一个mirror配置即可。

要做到单一仓库，设置mirrorOf到*。

只用新增一个mirror配置即可。要做到单一仓库，设置mirrorOf到*。

**mirrorOf中配置的星号，表示匹配所有的artifacts，也就是everything使用这里的代理地址**。上面的mirrorOf配置了具体的名字，指的是repository的名字。

镜像配置说明：

1、id: 镜像的唯一标识；

2、name: 名称描述；

3、url: 地址；

4、mirrorOf: 指定镜像规则，什么情况下从镜像仓库拉取。

- *: 匹配所有，所有内容都从镜像拉取；

- external:*: 除了本地缓存的所有从镜像仓库拉取；

- repo,repo1: repo或者repo1，这里的repo指的仓库ID；

- *,!repo1: 除了repo1的所有仓库；

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.3.1.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>sol</groupId>
	<artifactId>edrms</artifactId>
	<version>5.12.1.1</version>
	<name>edrms</name>
	<description>edrms core</description>

	<properties>
		<java.version>1.8</java.version>
		<com.hank.core.version>3.3</com.hank.core.version>
		<com.hank.common.version>3.6</com.hank.common.version>
		<com.hank.i18n.version>3.0</com.hank.i18n.version>
		<com.hank.es.version>1.1</com.hank.es.version>
		<elasticsearch.version>6.7.1</elasticsearch.version>
		<spring4all.swagger.version>1.9.0.RELEASE</spring4all.swagger.version>
	</properties>

	<dependencies>


		<dependency>
			<groupId>com.hank</groupId>
			<artifactId>es-spring-boot-starter</artifactId>
			<version>${com.hank.es.version}</version>
		</dependency>

		<dependency>
			<groupId>com.hank</groupId>
			<artifactId>core-spring-boot-starter</artifactId>
			<version>${com.hank.core.version}</version>
		</dependency>

		<dependency>
			<groupId>com.hank</groupId>
			<artifactId>common</artifactId>
			<version>${com.hank.common.version}</version>
		</dependency>

		<dependency>
			<groupId>com.hank</groupId>
			<artifactId>i18n-spring-boot-starter</artifactId>
			<version>${com.hank.i18n.version}</version>
		</dependency>

		<!--mysql -->
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<version>8.0.13</version>
		</dependency>

		<dependency>
			<groupId>org.mybatis.generator</groupId>
			<artifactId>mybatis-generator-core</artifactId>
			<version>1.3.5</version>
		</dependency>


		<!-- https://mvnrepository.com/artifact/net.sf.json-lib/json-lib -->
		<dependency>
			<groupId>net.sf.json-lib</groupId>
			<artifactId>json-lib</artifactId>
			<classifier>jdk15</classifier>
			<version>2.4</version>
		</dependency>


	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
			<plugin>
				<groupId>org.mybatis.generator</groupId>
				<artifactId>mybatis-generator-maven-plugin</artifactId>
				<version>1.4.0</version>
				<configuration>
					<!--配置文件的位置-->
					<configurationFile>src/main/resources/generatorConfig.xml</configurationFile>
					<overwrite>true</overwrite>
					<verbose>true</verbose>
				</configuration>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-surefire-plugin</artifactId>
				<configuration>
					<skipTests>true</skipTests>
				</configuration>
			</plugin>
		</plugins>
	</build>

	<repositories>
		<repository>
			<id>public</id>
			<name>Public Repositories</name>
			<url>http://192.168.251.93:8081/nexus/content/groups/public/</url>
			<layout>default</layout>
			<releases>
				<enabled>true</enabled>
			</releases>
			<snapshots>
				<enabled>true</enabled>
			</snapshots>
		</repository>

		<repository>
			<id>releases</id>
			<name>Releases</name>
			<url>http://192.168.251.93:8081/nexus/content/repositories/releases/</url>
			<layout>default</layout>
			<releases>
				<enabled>true</enabled>
			</releases>
			<snapshots>
				<enabled>true</enabled>
			</snapshots>
		</repository>

		<repository>
			<id>snapshots</id>
			<name>Snapshots</name>
			<url>http://192.168.251.93:8081/nexus/content/repositories/snapshots/</url>
			<layout>default</layout>
			<releases>
				<enabled>true</enabled>
			</releases>
			<snapshots>
				<enabled>true</enabled>
			</snapshots>
		</repository>
	</repositories>

	<pluginRepositories>
		<pluginRepository>
			<id>public</id>
			<name>Public Repositories</name>
			<url>http://192.168.251.93:8081/nexus/content/groups/public/</url>
		</pluginRepository>
	</pluginRepositories>

</project>
```

install之后的结果是公网依赖都可以拉取，只有私网依赖找不到。

解决办法，修改maven的设置：

```xml
    <mirrors>
        <mirror>
            <id>nexus-aliyun</id>
            <mirrorOf>central</mirrorOf>
            <name>Nexus aliyun</name>
            <url>http://maven.aliyun.com/nexus/content/groups/public</url>
        </mirror>
    </mirrors>
```


