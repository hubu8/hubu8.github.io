# Jenkins+Docker一键自动化部署SpringBoot项目


<!--more-->

本文章实现最简单全面的`Jenkins+docker+springboot` 一键自动部署项目，步骤齐全，少走坑路。

> 环境：centos7+git(gitee)

简述实现步骤：在docker安装jenkins，配置jenkins基本信息，利用Dockerfile和shell脚本实现项目自动拉取打包并运行。

## **一、安装docker**

docker安装社区版本CE

1.确保 yum 包更新到最新。

```
yum update
```

2.卸载旧版本(如果安装过旧版本的话)

```
yum remove docker  docker-common docker-selinux docker-engine
```

3.安装需要的软件包

```
yum install -y yum-utils device-mapper-persistent-data lvm2
```

4.设置yum源

```
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

5.安装docker

```
yum install docker-ce  #由于repo中默认只开启stable仓库，故这里安装的是最新稳定版17.12.0
yum install <自己的版本>  # 例如：sudo yum install docker-ce-17.12.0.ce
```

6.启动和开机启动

```
systemctl start docker
systemctl enable docker
```

7.验证安装是否成功

```
docker version
```

## **二、安装Jenkins**

Jenkins中文官网：

> https://www.jenkins.io/zh/

#### 1.安装Jenkins

docker 安装一切都是那么简单，注意检查8080是否已经占用！如果占用修改端口

```shell
docker run --name jenkins -u root --rm -d -p 8080:8080 -p 50000:50000 -v /var/jenkins_home:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock jenkinsci/blueocean
```

如果没改端口号的话

安装完成后访问地址-> `http://{部署Jenkins所在服务IP}:8080`

此处会有几分钟的等待时间。

#### 2.初始化Jenkins

##### 2.1 解锁Jenkins

- 进入Jenkins容器：`docker exec -it {Jenkins容器名} bash`
- 例如 `docker exec -it jenkins bash`
- 查看密码：`cat /var/lib/jenkins/secrets/initialAdminPassword`
- 复制密码到输入框里面

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/knmrNHnmCLFvhCbhGzOibqxc75RyoJ6cyyshvRoBvJ6QbJZRw9wic7gibylV1cJicxOJVWg0Hg0qFhXyT5ug0Pbn2g/640?wx_fmt=png&random=0.7236724850345728&wxfrom=5&wx_lazy=1&wx_co=1)

图片

##### 2.2 安装插件

选择第一个：安装推荐的插件

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/knmrNHnmCLFvhCbhGzOibqxc75RyoJ6cyMwucPYZb9ZmW27xZl3IpQicZBrWqCThp7LRphtnib2wsLdFYynHaGyPw/640?wx_fmt=png&random=0.7501449915729308&wxfrom=5&wx_lazy=1&wx_co=1)

图片

##### 2.3 创建管理员用户

此账户一定要记住哦

## **三、系统配置**

#### 1. 安装需要插件

进入【首页】–【系统管理】–【插件管理】–【可选插件】

搜索以下需要安装的插件，点击安装即可。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/knmrNHnmCLFvhCbhGzOibqxc75RyoJ6cyLaNAM4xbaWaBeNHPvGhm6Iu9ZmvgOiaIdOo11Q8PiacAhj15uA2123KA/640?wx_fmt=png&random=0.42899086065774017&wxfrom=5&wx_lazy=1&wx_co=1)

图片

- 安装`Maven Integration`
- 安装`Publish Over SSH`(如果不需要远程推送，不用安装)
- 如果使用Gitee 码云，安装插件Gitee（Git自带不用安装）

#### 2. 配置Maven

进入【首页】–【系统管理】–【全局配置】，拉到最下面maven–maven安装

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/knmrNHnmCLFvhCbhGzOibqxc75RyoJ6cyeQqIcfic8kVBoDHjoEsicYTdibGicicO1BvkHTib1QQV7bAd77VxCDZRKsAQ/640?wx_fmt=png&random=0.8667036553651208&wxfrom=5&wx_lazy=1&wx_co=1)

图片

## **四、创建任务**

#### 1. 新建任务

点击【新建任务】，输入任务名称，点击构建一个自由风格的软件项目

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/knmrNHnmCLFvhCbhGzOibqxc75RyoJ6cyxUKZsCZOoXUv7lpBxm9mqId7Iq3icoPjictqTTle4zjxjoD4RxNW7yKQ/640?wx_fmt=png&random=0.2006864959409247&wxfrom=5&wx_lazy=1&wx_co=1)

图片

#### 2. 源码管理

点击【源码管理】–【Git】，输入仓库地址，添加凭证，选择好凭证即可。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/knmrNHnmCLFvhCbhGzOibqxc75RyoJ6cyzdO3Iy629MrSsiavFlicfFsgmbWQlIAIIaGyIrYw5533BhMbR5kjuHiaQ/640?wx_fmt=png&random=0.7056154241642933&wxfrom=5&wx_lazy=1&wx_co=1)

图片

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/knmrNHnmCLFvhCbhGzOibqxc75RyoJ6cyKrRuvzhCrdKe6RsUMoIe5Iic9wCNSCtJ1OP6YJgdvCG54XFNkv86ARA/640?wx_fmt=png&random=0.09957022906412849&wxfrom=5&wx_lazy=1&wx_co=1)

图片

#### 3.构建触发器

点击【构建触发器】–【构建】–【增加构建步骤】–【调用顶层Maven目标】–【填写配置】–【保存】

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/knmrNHnmCLFvhCbhGzOibqxc75RyoJ6cy2wofUQHqbcQHwtQOeib2RuDvbCeH7rfhPbUuDpRV6k3ZSOicFibhzs7Vg/640?wx_fmt=png&random=0.317058323396312&wxfrom=5&wx_lazy=1&wx_co=1)

图片

此处命令只是install，看是否能生成jar包

```
clean install -Dmaven.test.skip=true
```

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/knmrNHnmCLFvhCbhGzOibqxc75RyoJ6cyPoU6XbhmLmERPP6FwKHDkAhb2ibD3pLO1mbibYgSIN0qu2SOIvW9ft3A/640?wx_fmt=png&random=0.37942360753709004&wxfrom=5&wx_lazy=1&wx_co=1)

图片

#### 4. 保存

点击【保存】按钮即可

## **五、测试**

该功能测试是否能正常打包

#### 1. 构建

点击构建按钮

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/knmrNHnmCLFvhCbhGzOibqxc75RyoJ6cyCFytG38bLww3MnGCY4ISQ2ITic8Hia0IKtt3tn7TslPF7AyebeBJPtqg/640?wx_fmt=png&random=0.09550435108069477&wxfrom=5&wx_lazy=1&wx_co=1)

图片

#### 2.查看日志

点击正在构建的任务，或者点击任务名称，进入详情页面，查看控制台输出，看是否能成功打成jar包。

该处日志第一次可能下载依赖jar包失败，再次点击构建即可成功。![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/knmrNHnmCLFvhCbhGzOibqxc75RyoJ6cyCFytG38bLww3MnGCY4ISQ2ITic8Hia0IKtt3tn7TslPF7AyebeBJPtqg/640?wx_fmt=png&random=0.29831465950283786&wxfrom=5&wx_lazy=1&wx_co=1)

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/knmrNHnmCLFvhCbhGzOibqxc75RyoJ6cy5Fz6qyDTiaGgDg1qicb3xfu2ouFVIHcG7PibvUJpG8hjcXIibtpzWR2DEg/640?wx_fmt=png&random=0.027020731370377638&wxfrom=5&wx_lazy=1&wx_co=1)

图片

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/knmrNHnmCLFvhCbhGzOibqxc75RyoJ6cyRldXjpOewRsSTXjGSibAfPcVeI2srVPRzRcWtAWpKeKAsdDuUXmb8Hw/640?wx_fmt=png&random=0.60444221647061&wxfrom=5&wx_lazy=1&wx_co=1)

图片

#### 3. 查看项目位置

1. `cd /var/jenkins_home/workspace`
2. `ll` 即可查看是否存在

## **六、运行项目**

因为我们项目和jenkins在同一台服务器，所以我们用shell脚本运行项目，原理既是通过dockerfile 打包镜像，然后docker运行即可。

#### 1. Dockerfile

在springboot项目根目录新建一个名为Dockerfile的文件，注意没有后缀名，其内容如下:（大致就是使用jdk8，把jar包添加到docker然后运行prd配置文件。详细可以查看其他教程）

```
FROM jdk:8
VOLUME /tmp
ADD target/zx-order-0.0.1-SNAPSHOT.jar app.jar
EXPOSE 8888
ENTRYPOINT ["Bash","-DBash.security.egd=file:/dev/./urandom","-jar","/app.jar","--spring.profiles.active=prd"]
```

#### 2. 修改jenkins任务配置

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/knmrNHnmCLFvhCbhGzOibqxc75RyoJ6cyUQbZA4dtxoREHicOaMriceUfIaBMBg103vF8sM6JtoFkrxN5niaibkwEjA/640?wx_fmt=png&random=0.48763975710440555&wxfrom=5&wx_lazy=1&wx_co=1)

图片

配置如下：

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/knmrNHnmCLFvhCbhGzOibqxc75RyoJ6cy7l2piaPZ1zMmFiahjcwQzHrbOKX3UIOuia2veMS9DtkiaZc0B2344WrgLQ/640?wx_fmt=png&random=0.2645517507663635&wxfrom=5&wx_lazy=1&wx_co=1)

图片

- `-t`：指定新镜像名
- `.`：表示Dockfile在当前路径

```
cd /var/jenkins_home/workspace/zx-order-api
docker stop zx-order || true
docker rm zx-order || true
docker rmi zx-order || true
docker build -t zx-order .
docker run -d -p 8888:8888 --name zx-order zx-order:latest
```

备注：

1. 我上图用了`docker logs -f` 是为了方便看日志，真实不要用，因为会一直等待日志，构建任务会失败
2. 加`|| true` 是如果命令执行失败也会继续实行，为了防止第一次没有该镜像报错

#### 3. 保存

点击保存即可

#### 4. 构建

查看jenkins控制台输出，输出如下，证明成功！

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/knmrNHnmCLFvhCbhGzOibqxc75RyoJ6cy1GoXDD0TNOia3KNZeJ97FB5YBibFeFaMSdbY6xfy4RuKP9UPxlatmDdQ/640?wx_fmt=png&random=0.7296276906655683&wxfrom=5&wx_lazy=1&wx_co=1)

图片

#### 5. 验证

1. `docker ps` 查看是否有自己的容器
2. `docker logs` 自己的容器名 查看日志是否正确
3. 浏览器访问项目试一试
