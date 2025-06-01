# 一键安装docker和docker Compose


<!--more-->

1.复制下面命令至文件并保存

```sh
#!/bin/bash

sudo systemctl stop docker.socket

#删除原有docker
sudo yum remove docker docker-ce docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-selinux docker-engine-selinux docker-engine
echo "=======删除 docker服务 完成======="

#删除镜像、容器、配置文件等内容
sudo rm -rf /var/lib/docker
sudo rm -rf /etc/systemd/system/docker.service.d
sudo rm -rf /var/run/docker
echo "=======删除 docker文件夹 完成======="


#安装 docker
#sudo curl -sSL https://get.daocloud.io/docker | sh
sudo curl -sSL https://get.docker.com | sh
#启动 docker
systemctl start docker
docker -v
echo "=======安装 docker 完成======="
#设置 docker仓库为国内仓库
touch /etc/docker/daemon.json
cat > /etc/docker/daemon.json <<EOF
{
  "registry-mirrors": ["https://nrbewqda.mirror.aliyuncs.com","https://dmmxhzvq.mirror.aliyuncs.com","https://registry.docker-cn.com"]
}
EOF


#启动 docker
systemctl daemon-reload
systemctl restart docker


echo "=======开始安装 docker-compose 完成======="


#安装 docker-compose
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version

echo "=======安装 docker-compose 完成======="
```

2.给文件添加执行权限

```sh
chmod +x  install-docker.sh
```

3./bin/bash^M: 坏的解释器: 没有那个文件或目录

​	我们的脚本文件在windows下编辑过，windows下每一行的结尾是\n\r，而在linux下文件的结尾是\n，那么在windows下编辑过的文件在linux下打开看的时候每一行的结尾就会多出来一个字符\r,当在linux下查看时，\r会被替换为^M

我们使用这个命令来/r结束的字符换成空白

```sh
sed -i 's/\r$//' xxx.sh
```

