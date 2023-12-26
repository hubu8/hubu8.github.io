# Specified key was too long; max key length is 3072 bytes


<!--more-->

![image-20231226145606320](/mysql/image-20231226145606320.png)

Specified key was too long; max key length is 3072 bytes

```mysql
show variables like 'innodb_page_size';
```

![image-20231226150434031](/mysql/image-20231226150434031.png)

16384 = 16KB * 1024

#### 果断翻译了一下mysql8的官方文档，得出结论：

- 需要修改长度的字段做了索引；

- innodb引擎的每个索引列长度限制为767字节（bytes），所有组成索引列的长度和不能大于3072字节。**为什么限制为3072 ？**

- - 这个和InnoDB的页大小有关。 我们知道InnoDB一个page的默认大小是16k。**由于是B+树组织，要求叶子节点上一个page至少要包含两条记录**所以一个记录最多不能超过8k。又由于InnoDB的聚簇索引结构，一个二级索引要包含主键索引，因此每个单个索引不能超过 4 k（极端情况，聚集索引和某个二级索引都达到这个限制）。由于需要预留和辅助空间，扣掉后不能超过 3500 ，取个“整数”就是(1024*3)。由此推算将页大小改为8K，4K的时候索引长度限制也应该相应减小。
  - **问题所在的表的字符集是 utf8mb4 时，一个字符将占用 4 个字节。这意味着索引前缀最大长度为 3072 字节时，只能容纳 3072 / 4 = 768 个字符。因此只要将上面建表语句索引字段的前缀长度设为768或者修改索引字段，让其小于3072字节。**
  - 索引前缀长度还和 InnoDB 的 page size 有关。“innodb_page_size”选项默认是 16KB 的时候，最长索引前缀长度是 3072 字节，如果是 8KB 的时候，最长索引前缀长度是 1536 字节；4KB 的时候，最长索引前缀长度是 768 字节。
  - 总结：读取数据的整个流程中，最浪费时间的是磁盘寻道，查找数据所属扇区位置的过程（机械运动）。又因为内存最小的存储单位是页，这样最快的存储方式，1个内存页的数据，正好在1个扇区内，一次性查询完成。所以限制了3072字节为1个内存页。

综上可知：

**当Mysql数据库引擎为innodb时，page_size大小为16KB（16384）、utf8mb4字符集时，建立索引的字段长度不能超过 768个字符，超过就报错**



#### 如何计算索引长度

所有的索引字段，如果没有设置 Not Null，则需要加一个字节(这也是我们为什么建议建表时不要有 Null 字段的原因之一)。

对于定长字段，int 类型占四个字节、date 占三个字节、char(n) 占 N 个字符。

对于变成字段 varchar(n)，则是 N 个字符 + 两个字节。

不同的字符集，一个字符占用的字节数不同。latin1编码的，一个字符占用一个字节，gbk编码的，一个字符占用两个字节，utf8编码的，一个字符占用三个字节。

索引长度 char()、varchar() 索引长度的计算公式：

```
Character Set：utf8mb4=4,utf8=3,gbk=2,latin1=1) * 列长度 + 1(允许 Null) + 2(变长列)
```

基于以上原则，我们建张表来验证下：

```mysql
CREATE TABLE `tb_item` (  
    `id` bigint NOT NULL COMMENT '书籍id，同时也是书籍编号',  
    `title` varchar(100) NOT NULL COMMENT '书籍名称',  
    `sell_point` varchar(500) DEFAULT NULL COMMENT '书籍卖点',  
    `price` bigint NOT NULL COMMENT '书籍价格，单位为：分',  
    `num` int NOT NULL COMMENT '库存数量',  
    `barcode` varchar(30) DEFAULT NULL COMMENT '书籍条形码',  
    `image` varchar(500) DEFAULT NULL COMMENT '书籍图片',  
    `cid` bigint NOT NULL COMMENT '所属类目，叶子类目',  
    `status` tinyint NOT NULL DEFAULT '1' COMMENT '书籍状态，1-正常，2-下架，3-删除',  
    `created` datetime NOT NULL COMMENT '创建时间',  
    `updated` datetime NOT NULL COMMENT '更新时间',  
    `upload_id` bigint DEFAULT NULL,  
    PRIMARY KEY (`id`),  
    KEY `cid` (`cid`),  
    KEY `status` (`status`),  
    KEY `updated` (`updated`),  
    KEY `tb_item_title_price_num` (`title`,`price`,`num`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='书籍表';
```

在这张表上我们建立了联合索引 `tb_item_title_price_num` ，由三个字段组成，分别是变长字段 varchar，定长字段 price 以及 num。

我们先来执行如下这条语句：

`explain select * from tb_item where title = '编译原理' and price = 45 and num = 23232`;

我们看到 key 是 `tb_item_title_price_num`，同时索引长度 key_len 为 314 ，证明是使用到了联合索引 `tb_item_title_price_num` 的三个完整字段的。

![img](/mysql/d000baa1cd11728bfc3b1f8d529008c4c2fd2c86.png)

这个 314 的具体计算方式为：

字符集我们建表时用的为 utf8 编码，所以 title 字段的索引长度是 3 * 100 + 0 + 2 = 302，price 字段的索引长度是 8，num 字段的索引长度是 4。

因此 tb_item_title_price_num 索引总共长度是 302 + 8 + 4 = 314。

为了验证我们的计算方式，接下来我们再执行如下语句，使得索引部分失效。

```
explain select * from tb_item where title = '编译原理' and num = 23232
```

这条语句因为我们跳过了 price 字段，所以联合索引中只会有 title 字段生效，剩余部分都会失效，如果我们计算方式没有问题的话，那么此时执行计划中的 key_len 应该为 302。

![img](/mysql/c9fcc3cec3fdfc03754156704e534c9ea5c226b0.png)

果然，我们看到 key_len 已经变成了 302，这意味着索引部分失效了，只有 title 字段索引起了作用，同时 Extra 为 `Using index condition`，说明使用了索引，但是需要回表查询数据。



#### 简单总结一下，在 MySQL 中，索引长度不仅取决于我们建表时设置的字段长度，还和具体的字符集编码以及字段是否允许为 Null 等多个条件相关，字段长度只能作为索引长度的预估项，而不是准确值。

MySQL各版本中对索引大小限制（InnoDB引擎）
1.联合索引
MySQL 3.23：最大联合索引长度为1000字节。
MySQL 4.0.x/4.1.x/5.0.x：最大联合索引长度为1000字节。
MySQL 5.1.x：最大联合索引长度为3072字节（InnoDB存储引擎），或1000字节（MyISAM存储引擎）。
MySQL 5.5.x/5.6.x/5.7.x：最大联合索引长度为3072字节（InnoDB存储引擎），或1000字节（MyISAM存储引擎）。
MySQL 8.0.x：最大联合索引长度为3072字节（InnoDB和MyISAM存储引擎）。
2.单列索引
MySQL 5.6 及之前版本： InnoDB 存储引擎中单列索引的大小限制为767字节。这个限制是由于InnoDB存储引擎默认使用UTF-8字符集，而UTF-8编码中一个字符可能占用3个字节的存储空间，所以InnoDB将限制单列索引不能超过255个字符（因为255*3=765）。
MySQL 5.7：InnoDB 存储引擎中单列索引的大小限制已经被扩展到3072字节。这个改变主要是为了适应更长的JSON列索引。
MySQL 8.0：InnoDB 存储引擎中单列索引的大小限制依然是3072字节。
