# Docker之mysql数据备份


<!--more-->

### 问题背景

在一个上线项目中，系统需要定时备份数据，如果系统部署方式为服务器本地环境部署或者数据库与系统应用在同一个环境内，可以通过程序调用mysqldump来备份程序，核心代码如下：

```java
/**
     * Cannot run program "mysqldump": CreateProcess error=2, 系统找不到指定的文件。
     * 确保代码运行环境和MySQL在一个环境，目前还未找到好的方法
     * @param username 用户名
     * @param password 密码
     * @param dbName 数据库名
     * @param backupDir 备份路径
     * @param sqlName 文件名
     */
    public void backUpDataBase(String username,String password,String dbName,String backupDir,String sqlName){
        String backupSql = StringUtils.format("mysqldump -u{} -p{} {} > {}/{}`date +%Y%m%d%H%M%S`.sql",
                username, password, dbName, backupDir, sqlName);
        logger.info(StringUtils.format("{} 待执行的shell命令:{}", DateUtils.getTime(),backupSql));
        File folder = new File(backupDir);
        if (!folder.exists() && !folder.isDirectory()) {
            folder.mkdirs();
            logger.info(StringUtils.format("{} 没有当前备份目录%s,已经自动创建",DateUtils.getTime()));
        }
        try {
            Runtime.getRuntime().exec(backupSql);
        } catch (IOException e) {
            logger.error(StringUtils.format("{} 备份脚本执行失败，请检查",DateUtils.getTime()));
            throw new RuntimeException(e);
        }

    }
```

但是现在普遍的部署方式为docker部署服务和底层组件，此时如果容器处在同一个网络，网络是通的，但是并不能互相免密登录（因为容器环境的不同，生态并没有完整linux完善），所以就需要一种定时备份数据的程序。

### 环境简介

![image-20230511174022829](/docker_image/image-20230511174022829.png)

主应用程序和MySQL容器部署在不同容器但是属于同一个网络，此时服务器系统没有mysql环境，所以也就没有mysqldump备份命令，此时数据库的备份方案只能在mysql容器上部署。

### 方案简介

1、MySQL容器挂载出一个目录作为数据备份输出目录，这样服务器系统可以访问；

2、主系统程序部署一个定时任务，通过定时任务把备份数据备份到其他地方（例如通过邮件发送给系统管理员）；

3、主系统程序需要访问到备份数据，那么就需要MySQL容器和主系统程序挂载在主机的同一个目录；

### 技术落地

#### 1、MySQL容器目录挂载

```yml
  ruoyi-mysql:
    container_name: ruoyi-mysql
    image: mysql:5.7
    build:
      context: ./mysql
    ports:
      - "3306:3306"
    volumes:
      - ./mysql/conf:/etc/mysql/conf.d
      - ./mysql/logs:/logs
      - ./mysql/data:/var/lib/mysql
      - ./mysql/backup:/opt/backup
    command: [
          'mysqld',
          '--innodb-buffer-pool-size=80M',
          '--character-set-server=utf8mb4',
          '--collation-server=utf8mb4_unicode_ci',
          '--default-time-zone=+8:00',
          '--lower-case-table-names=1'
        ]
```

#### 2、服务器备份脚本

```shell
#!/bin/bash
# -v ./mysql/backup:/opt/backup
# 此文件同放在与backup同级
dockerMysqlName=ruoyi-mysql
mysqlUser=root
mysqlPassword=password
mysqlHost=localhost
mysqlPort=3306
dateBackup=$(date +%Y-%m-%d_%H_%M_%S)
dir=./backup/${dateBackup}
# 宿主机新建目录，通过挂载会自动添加到容器 这里是每次的log文件
if [ ! -d ${dir} ];
then
		mkdir ${dir}
		echo "*****************> 开始备份 ${dateBackup} 数据<*****************" >> ${dir}/backup.log
		echo "创建文件夹 ${dir} 成功" >> ${dir}/backup.log
else
  echo "-----------------> 开始备份 ${dateBackup} 数据<-----------------" >> ${dir}/backup.log
		echo "创建文件夹 ${dir} 失败，文件夹已存在" >> ${dir}/backup.log
fi
# 需要备份的数据库名
dbNames=(ry-cloud ry-dev ry-config)
for dbName in ${dbNames[@]}
do
		echo "-----------------> 备份 ${dbName} 数据库 start<-----------------" >> ${dir}/backup.log
      docker exec -i ${dockerMysqlName} sh -c "mkdir -p /opt/backup/${dateBackup}/table"
      docker exec -i ${dockerMysqlName} sh -c "mkdir -p /opt/backup/${dateBackup}/data"

		echo "    1、    ---------> 备份 ${dbName} 数据库 表结构 <---------        " >> ${dir}/backup.log
      docker exec -i ${dockerMysqlName} sh -c "mysqldump --opt -u${mysqlUser} -p${mysqlPassword} -h${mysqlHost} -P${mysqlPort} ${dbName} 1>> /opt/backup/${dateBackup}/table/${dbName}-dbschema.sql 2>> /opt/backup/${dateBackup}/table/error.log"

    echo "    2、    ---------> 备份 ${dbName} 数据库 数据 <---------        " >> ${dir}/backup.log
      docker exec -i ${dockerMysqlName} sh -c "mysqldump -t -u${mysqlUser} -p${mysqlPassword} -h${mysqlHost} -P${mysqlPort} ${dbName} 1>> /opt/backup/${dateBackup}/data/${dbName}-dbdata.sql 2>> /opt/backup/${dateBackup}/data/error.log"
    echo "-----------------> 备份 ${dbName} 数据库 end<-----------------" >> ${dir}/backup.log
done
echo "-----------------> 压缩今日备份数据目录：${dir} <-----------------" >> ${dir}/backup.log
tar -czvf ${dir}.tar.gz ${dir}

echo "----------------> 删除过期文件 <---------------------------" >> ${dir}/backup.log
# 判断文件夹数量是否大于7，防止程序意外停止，删除所有备份
dirCount=`ls -l ./backup/|grep "^d"|wc -l`
if [ ${dirCount} -gt 7 ]
then
	# 删除超过七天的带"_"的目录
	find ./backup/ -type d -mtime +6 -name "*_*" -exec rm -rf {} \;
	echo -e "删除过期文件成功" >> ${dir}/backup.log
else
	echo "删除过期文件失败，文件数量小于 7 " >> ${dir}/backup.log
fi

echo -e "*****************> 完成备份 ${dateBackup} 数据<*****************\n\n" >> ${dir}/backup.log
cat ${dir}/backup.log>>./backup/backup.log
```

同时可以通过定时任务实现定时执行

#### 3、文件异地备份

最后通过系统程序挂载到文件共享目录，通过系统定时任务发送邮件；这部分代码参考hutools的Email功能

```java
@Override
    public void send(EmailVO emailVo, EmailConfig emailConfig){
        if(emailConfig.getId() == null){
            throw new EmailConfigException("请先配置，再操作");
        }
        // 封装
        MailAccount account = new MailAccount();
        // 设置用户
        String user = emailConfig.getFromUser().split("@")[0];
        account.setHost(emailConfig.getHost());
        account.setPort(Integer.parseInt(emailConfig.getPort()));
        account.setAuth(true);
        try {
            // 对称解密
            account.setPass(EncryptUtils.desDecrypt(emailConfig.getPass()));
        } catch (Exception e) {
            throw new CheckedException(e.getMessage());
        }
        // ssl方式发送
        account.setSslEnable(true);
        // 使用STARTTLS安全连接
        account.setStarttlsEnable(true);
        account.setUser(user);
        account.setFrom(emailConfig.getFromUser());
        String content = emailVo.getContent();
        // 发送
        try {
            int size = emailVo.getTos().size();
            Mail mail = Mail.create(account);
            mail.setTos(emailVo.getTos().toArray(new String[size]))
                .setTitle(emailVo.getSubject())
                .setContent(content)
                .setHtml(true);
            List<DataSource> attachments = emailVo.getAttachments();
            if(attachments != null && attachments.size() > 0){
                mail.setAttachments(attachments.toArray(new DataSource[]{}));
            }

            List<File> files = emailVo.getFiles();
            if(files != null && files.size() > 0){
                mail.setFiles(files.toArray(new File[]{}));
            }

            mail
                //关闭session
                .setUseGlobalSession(false)
                .send();
        }catch (Exception e){
            throw new EmailSendException(e.getMessage());
        }
    }
```

#### 4、文件目录结构

![image-20230511195957921](/docker_image/image-20230511195957921.png)
