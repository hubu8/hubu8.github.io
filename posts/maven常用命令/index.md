# maven常用命令


<!--more-->

抽了点时间，整理了一些 maven 常用命令参数，以便参考；

### mvn 命令参数

**mvn -v**, --version 显示版本信息;

**mvn -V**, --show-version 显示版本信息后继续执行 Maven 其他目标;

**mvn -h**, --help 显示帮助信息;

**mvn -e**, --errors 控制 Maven 的日志级别, 产生执行错误相关消息;

**mvn -X**, --debug 控制 Maven 的日志级别, 产生执行调试信息;

**mvn -q**, --quiet 控制 Maven 的日志级别, 仅仅显示错误;

**mvn -P**xxx 激活 id 为 xxx 的 profile (如有多个，用逗号隔开);

**mvn -D**xxx=yyy 指定 java 全局属性;

**mvn -o** , --offline 运行 offline 模式, 不联网更新依赖;

**mvn -N**, --non-recursive 仅在当前项目模块执行命令, 不构建子模块;

**mvn -pl**, --module_name 在指定模块上执行命令;

**mvn -ff**, --fail-fast 遇到构建失败就直接退出;

**mvn -fn**, --fail-never 无论项目结果如何, 构建从不失败;

**mvn -fae**, --fail-at-end 仅影响构建结果, 允许不受影响的构建继续;

**mvn -C**, --strict-checksums 如果校验码不匹配的话, 构建失败;

**mvn -c**, --lax-checksums 如果校验码不匹配的话, 产生告警;

**mvn -U** 强制更新 snapshot 类型的插件或依赖库 (否则 maven 一天只会更新一次 snapshot 依赖);

**mvn -npu**, --no-plugin-updates 对任何相关的注册插件, 不进行最新检查 (使用该选项使 Maven 表现出稳定行为，该稳定行为基于本地仓库当前可用的所有插件版本);

**mvn -cpu**, --check-plugin-updates 对任何相关的注册插件, 强制进行最新检查 (即使项目 POM 里明确规定了 Maven 插件版本, 还是会强制更新);

**mvn -up**, --update-plugins [mvn -cpu] 的同义词;

**mvn -B**, --batch-mode 在非交互（批处理）模式下运行 (该模式下, 当 Mven 需要输入时, 它不会停下来接受用户的输入, 而是使用合理的默认值);

**mvn -f**, --file 强制使用备用的 POM 文件;

**mvn -s**, --settings 用户配置文件的备用路径;

**mvn -gs**, --global-settings 全局配置文件的备用路径;

**mvn -emp**, --encrypt-master-password 加密主安全密码, 存储到 Maven settings 文件里;

**mvn -ep**, --encrypt-password 加密服务器密码, 存储到 Maven settings 文件里;

**mvn -npr**, --no-plugin-registry 对插件版本不使用~/.m2/plugin-registry.xml(插件注册表) 里的配置;

### mvn 常用命令

1. 创建 Maven 的普通 java 项目： 
     mvn archetype:create 
     -DgroupId=packageName 
     -DartifactId=projectName 
2. 创建 Maven 的 Web 项目：  
     mvn archetype:create 
     -DgroupId=packageName  
     -DartifactId=webappName 
     -DarchetypeArtifactId=maven-archetype-webapp  
3. 编译源代码：mvn compile
4. 编译测试代码：mvn test-compile   
5. 运行测试：mvn test  
6. 产生 site：mvn site  
7. 打包：mvn package  
8. 在本地 Repository 中安装 jar：mvn install
9. 清除产生的项目：mvn clean  
10. 生成 eclipse 项目：mvn eclipse:eclipse  
11. 生成 idea 项目：mvn idea:idea  
12. 组合使用 goal 命令，如只打包不测试：mvn -Dtest package  
13. 编译测试的内容：mvn test-compile  
14. 只打 jar 包: mvn jar:jar 
15. 只测试而不编译，也不测试编译：mvn test -skipping compile -skipping test-compile
       (-skipping 的灵活运用，当然也可以用于其他组合命令) 
16. 清除 eclipse 的一些系统设置: mvn eclipse:clean 

**ps**

一般使用情况是这样，首先通过 cvs 或 svn 下载代码到本机，然后执行 mvn eclipse:eclipse 生成 ecllipse 项目文件，然后导入到 eclipse 就行了；修改代码后执行 mvn compile 或 mvn test 检验，也可以下载 eclipse 的 maven 插件。 

**mvn -version/-v** 显示版本信息 

**mvn archetype:generate**    创建 mvn 项目 

**mvn archetype:create -DgroupId=com.oreilly -DartifactId=my-app**  创建 mvn 项目 

**mvn package**      生成 target 目录，编译、测试代码，生成测试报告，生成 jar/war 文件 

**mvn jetty:run**      运行项目于 jetty 上, 

**mvn compile**          编译 

**mvn test**          编译并测试 

**mvn clean**          清空生成的文件 

**mvn site**          生成项目相关信息的网站 

**mvn -Dwtpversion=1.0 eclipse:eclipse**    生成 Wtp 插件的 Web 项目 

**mvn -Dwtpversion=1.0 eclipse:clean**    清除 Eclipse 项目的配置信息 (Web 项目) 

**mvn eclipse:eclipse**  将项目转化为 Eclipse 项目 

在应用程序用使用多个存储库

```xml
<repositories>        
	<repository>              
	<id>Ibiblio</id>              
	<name>Ibiblio</name>              
	<url>http://www.ibiblio.org/maven/</url>        
</repository>        
<repository>              
	<id>PlanetMirror</id>              
	<name>Planet Mirror</name>              
	<url>http://public.planetmirror.com/pub/maven/</url>        
</repository>  </repositories>
```

命令：

```shell
mvn deploy:deploy-file -DgroupId=com -DartifactId=client -Dversion=0.1.0-Dpackaging=jar 
-Dfile=d:\client-0.1.0.jar-DrepositoryId=maven-repository-inner -Durl=ftp://xxxxxxx/opt/maven/repository/ 
```

发布第三方 Jar 到本地库中：

```shell
mvn install:install-file -DgroupId=com -DartifactId=client -Dversion=0.1.0-Dpackaging=jar 
-Dfile=d:\client-0.1.0.jar -DdownloadSources=true  -DdownloadJavadocs=true 
```

**mvn -e**      显示详细错误 信息. 

**mvn validate**     验证工程是否正确，所有需要的资源是否可用。 

**mvn test-compile**   编译项目测试代码。

**mvn integration-test**   在集成测试可以运行的环境中处理和发布包。 

**mvn verify**     运行任何检查，验证包是否有效且达到质量标准。

**mvn generate-sources**   产生应用需要的任何额外的源代码，如 xdoclet。 

### mvn 常用命令 2

**mvn -v** 显示版本 

**mvn help:describe -Dplugin=help** 使用 help 插件的 describe 目标来输出 Maven Help 插件的信息。


**mvn help:describe -Dplugin=help -Dfull** 使用 Help 插件输出完整的带有参数的目标列 

**mvn help:describe -Dplugin=compiler -Dmojo=compile -Dfull** 获取单个目标的信息, 设置 mojo 参数和 plugin 参数。此命令列出了 Compiler 插件的 compile 目标的所有信息 

**mvn help:describe -Dplugin=exec -Dfull** 列出所有 Maven Exec 插件可用的目标 

**mvn help:effective-pom** 看这个 “有效的 (effective)”POM，它暴露了 Maven 的默认设置 

**mvn archetype:create -DgroupId=org.sonatype.mavenbook.ch03 -DartifactId=simple -DpackageName=org.sonatype.mavenbook** 创建 Maven 的普通 java 项目，在命令行使用 Maven Archetype 插件 

**mvn exec:java -Dexec.mainClass=org.sonatype.mavenbook.weather.Main Exec** 插件让我们能够在不往 classpath 载入适当的依赖的情况下，运行这个程序 

**mvn dependency:resolve** 打印出已解决依赖的列表 

**mvn dependency:tree** 打印整个依赖树 

**mvn install -X** 想要查看完整的依赖踪迹，包含那些因为冲突或者其它原因而被拒绝引入的构件，打开 Maven 的调试标记运行 

**mvn install -Dmaven.test.skip=true** 给任何目标添加 maven.test.skip 属性就能跳过测试 

**mvn install assembly:assembly** 构建装配 Maven Assembly 插件是一个用来创建你应用程序特有分发包的插件 

**mvn jetty:run** 调用 Jetty 插件的 Run 目标在 Jetty Servlet 容器中启动 web 应用 

**mvn compile** 编译你的项目 

**mvn clean install** 删除再编译 

**mvn hibernate3:hbm2ddl** 使用 Hibernate3 插件构造数据库
