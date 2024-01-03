# Docker之ShowDoc安装


<!--more-->

ShowDoc官网：https://www.showdoc.com.cn/help

#### 安装

```sh
# 原版官方镜像安装命令(中国大陆用户不建议直接使用原版镜像，可以用后面的加速镜像)
# 如果你打算安装ARM版本的docker镜像，请将 latest 标签改为 arm-latest
docker pull star7th/showdoc:latest 

# 中国大陆镜像安装命令（安装后记得执行docker tag命令以进行重命名）
docker pull registry.cn-shenzhen.aliyuncs.com/star7th/showdoc
docker tag registry.cn-shenzhen.aliyuncs.com/star7th/showdoc:latest star7th/showdoc:latest 

##后续命令无论使用官方镜像还是加速镜像都需要执行

#新建存放showdoc数据的目录
mkdir -p /showdoc_data/html
chmod  -R 777 /showdoc_data
# 如果你是想把数据挂载到其他目录，比如说/data1，那么，可以在/data1目录下新建一个showdoc_data/目录，
# 然后在根目录的新建一个软链接/showdoc_data到/data1/showdoc_data
# 这样既能保持跟官方教程推荐的路径一致，又能达到自定义存储的目的.

#启动showdoc容器
docker run -d --name showdoc --user=root --privileged=true -p 4999:80 \
-v /showdoc_data/html:/var/www/html/ star7th/showdoc
```

根据以上命令操作的话，往后showdoc的数据都会存放在 /showdoc_data/html 目录下。
你可以打开 [http://localhost:4999](http://localhost:4999/) 来访问showdoc (localhost可改为你的服务器域名或者IP)。账户密码是showdoc/123456，登录后你便可以看到右上方的管理后台入口。建议登录后修改密码。

#### 升级

docker升级

```sh
# 从原版官方库更新镜像。(中国大陆用户不建议直接使用原版镜像，可以用后面的加速镜像)
docker pull star7th/showdoc 

# 中国大陆镜像更新命令（更新后记得执行docker tag命令以进行重命名）
docker pull registry.cn-shenzhen.aliyuncs.com/star7th/showdoc
docker tag registry.cn-shenzhen.aliyuncs.com/star7th/showdoc:latest star7th/showdoc:latest  

##后续命令无论使用官方镜像还是加速镜像都需要执行


rm -rf  /showdoc_data/html_bak
#备份。如果可以的话，命令中的html_bak还可以加上日期后缀，以便保留不同日期的多个备份
mv /showdoc_data/html  /showdoc_data/html_bak

# 新建准备存放新版代码的目录
mkdir -p /showdoc_data/html
chmod  -R 777 /showdoc_data/html

# 删除旧容器
docker stop showdoc && docker rm showdoc 

#启动showdoc容器
docker run -d --name showdoc --user=root --privileged=true -p 4999:80 \
-v /showdoc_data/html:/var/www/html/ star7th/showdoc

#执行新代码安装。默认安装中文版。如果想安装英文版，将下面参数中的zh改为en
curl http://localhost:4999/install/non_interactive.php?lang=zh

#转移旧数据库
\cp  -f  /showdoc_data/html_bak/Sqlite/showdoc.db.php /showdoc_data/html/Sqlite/showdoc.db.php

#转移旧附件数据
\cp -r -f /showdoc_data/html_bak/Public/Uploads/. /showdoc_data/html/Public/Uploads

# 重新给权限
chmod  -R 777 /showdoc_data/html

#如果中途出错，请重命名原来的/showdoc_data/html_bak文件为/showdoc_data/html ，然后重启容器便可恢复。
```

#### 其他命令

```sh
docker stop showdoc # 停止容器
docker restart showdoc #重启showdoc容器
docker rm showdoc #删除showdoc容器
docker rmi star7th/showdoc #删除showdoc镜像
docker stop $(docker ps -a -q) ;docker rm $(docker ps -a -q) ; #停止并删除所有容器。危险命令，不懂勿用。
docker rmi -f `docker images | grep showdoc| awk '{print $3}'` # 删除所有名字含有showdoc关键字的镜像
```


