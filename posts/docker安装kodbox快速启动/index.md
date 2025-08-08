# Docker安装Kodbox快速启动


<!--more-->

官方文档：[Docker环境可道云网盘的安装示例 | Kodcloud Documentation](https://docs.kodcloud.com/setup/docker/)

#### 安装Docker环境

安装并启动docker服务, 新版本docker会自动安装`docker-compose-plugin`

```sh
 curl -fsSL https://get.docker.com | bash -s docker
 systemctl enable docker && systemctl start docker
```

docker compose 用法, 在通过`docker compose up`启动后, 可以使用`docker compose ls`查看配置文件位置

若`docker compose`命令不存在，可手动安装二进制文件`docker-compose`并在后续命令中替换。

```sh
curl -SL https://github.com/docker/compose/releases/download/v2.23.3/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```

#### http方式快速启动

首先创建一个目录作为项目目录，后面所有命令都在这个目录下执行

```sh
mkdir /kodbox && cd /kodbox
```

创建文件来设置数据库环境变量`vim db.env`

```sh
MYSQL_PASSWORD=
MYSQL_DATABASE=kodbox
MYSQL_USER=kodbox
```

创建docker-compose.yml 文件，在其中配置映射端口、持久化目录

```yml
version: '3.5'

services:
  db:
    image: mariadb:10.6
    restart: always
    command: --transaction-isolation=READ-COMMITTED --log-bin=binlog --binlog-format=ROW
    volumes:
      - "./db:/var/lib/mysql"       #./db是数据库持久化目录，可以修改
      # - "./etc/mysql/conf.d:/etc/mysql/conf.d"       #增加自定义mysql配置
    environment:
      - MYSQL_ROOT_PASSWORD=
      - MARIADB_AUTO_UPGRADE=1
      - MARIADB_DISABLE_UPGRADE_BACKUP=1
    env_file:
      - db.env
      
  app:
    image: kodcloud/kodbox
    restart: always
    ports:
      - 80:80                       #左边80是使用端口，可以修改
    volumes:
      - "./site:/var/www/html"      #./site是站点目录位置，可以修改
    environment:
      - MYSQL_HOST=db
      - REDIS_HOST=redis
    env_file:
      - db.env
    depends_on:
      - db
      - redis

  redis:
    image: redis:alpine
    restart: always
```

```sh
增加自定义mysql配置：
mkdir -p ./etc/mysql/conf.d && vim ./etc/mysql/conf.d/custom.cnf
```

进入项目目录，执行`docker compose up -d`启动命令，会自动拉取容器并运行

列出docker容器，可以看到3个容器正在运行

如果需要停止服务

```sh
docker compose down
```

安装要点

1、MYSQL_ROOT_PASSWORD需要自己填

2、安装过程中 app 容器第一次安装事件比较长，不要中途停止容器

3、MYSQL_PASSWORD这个是环境变量，不知道是不是必填，自己测试一下

