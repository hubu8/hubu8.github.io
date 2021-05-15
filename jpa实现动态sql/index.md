# JPA实现动态SQL


<!--more-->

## JPA 基本查询

Spring Data JPA提供的一个查询规范，查询语句关键字，简单的SQL可根据方法命名来即可，省略了写sql语句

|   关键字    |   方法命名   |    sql where字句   |
| ---- | ---- | ---- |
|And            |         findByNameAndPwd        |            where name= ? and pwd =?|
|Or                |      findByNameOrSex            |         where name= ? or sex=?|
|Is,Equals         |      findById,findByIdEquals      |       where id= ?|
|Between       |          findByIdBetween         |            where id between ? and ?|
|LessThan        |        findByIdLessThan           |         where id < ?|
|LessThanEquals  |      findByIdLessThanEquals     |        where id <= ?|
|GreaterThan         |    findByIdGreaterThan           |      where id > ?|
|GreaterThanEquals      | findByIdGreaterThanEquals    |       where id > = ?|
|After            |       findByIdAfter            |           where id > ?|
|Before               |   findByIdBefore         |             where id < ?|
|IsNull              |    findByNameIsNull            |        where name is null|
|isNotNull,NotNull    |   findByNameNotNull           |        where name is not null|
|Like             |       findByNameLike         |             where name like ?|
|NotLike           |      findByNameNotLike         |          where name not like ?|
|StartingWith   |         findByNameStartingWith       |       where name like ‘?%’|
|EndingWith   |           findByNameEndingWith    |            where name like ‘%?’|
|Containing      |        findByNameContaining     |           where name like ‘%?%’|
|OrderBy      |           findByIdOrderByXDesc     |           where id=? order by x desc|
|Not           |          findByNameNot              |         where name <> ?|
|In             |         findByIdIn(Collection<?> c)     |    where id in (?)|
|NotIn       |            findByIdNotIn(Collection<?> c)  |    where id not in (?)|
|True          |          findByAaaTue              |          where aaa = true|
|False          |         findByAaaFalse           |           where aaa = false|
|IgnoreCase    |          findByNameIgnoreCase          |      where UPPER(name)=UPPER(?)|

## JPA 多条件查询（参数为空判断）语句

工作中遇到一个多条件查询的需求，需要根据名字，性别，年龄以及序号查询数据，名字需要模糊查询，参数有可能为空。

```java
@Query(value = "select * from people where if(?1 !='',name like concat('%',?1,'%'),1=1) and if(?2 !='',sex=?2,1=1)"+
" and if(IFNULL(?3,'') !='',age=?3,1=1) and if(IFNULL(?4,'') !='',num=?4,1=1) ",nativeQuery = true)
List<People> find(String name,String sex,Integer age,Integer num);
```

同理：

```Java
@Query(value = "select * from post_timer where if(IFNULL(?1,'') !='',is_send=?1,1=1) and if(IFNULL(?2,'') !='',typa=?2,1=1) and if(IFNULL(?3,'') !='',status=?3,1=1) and if(?4 !='',content like concat('%',?4,'%'),1=1) "+
            "order by create_time DESC limit ?5,?6",nativeQuery = true)
    List<PostTimer> findlike(Integer isSend,Integer typa,Integer status,String likeString,Integer page,Integer limit);

    @Query(value = "select count(*) from post_timer where if(IFNULL(?1,'') !='',is_send=?1,1=1) and if(IFNULL(?2,'') !='',typa=?2,1=1) and if(IFNULL(?3,'') !='',status=?3,1=1) and if(?4 !='',content like concat('%',?4,'%'),1=1) ",nativeQuery = true)
    Integer countlike(Integer isSend,Integer typa,Integer status,String likeString);
```

PS：

1. **nativeQuery = true** 的含义是使用原生SQL，即注解中的SQL语句会生效，false的话就不会生效。
2. SQL语句中 ?1、?2、?3、?4 的意思是代表方法中的第几个参数。
3. SQL中模糊查询的写法为 like concat('%', ?1, '%')
4. **if(?1 !='',name like concat('%',?1,'%'),1=1)** 代表传入的参数name如果不为""（Spring类型空是""而不是null）将参数传入name，如果为空时显示1=1 代表参数为真，对查询结果不产生作用。IF 的语法满足mysql的基本语法，IF(expr1,expr2,expr3)， 如果 expr1 为真(expr1 <> 0 以及 expr1 <> NULL)，那么 IF() 返回 expr2，否则返回expr3
5. **if(IFNULL(?3,'') !='',age=?3,1=1)** 表示如果传入的年龄是null，则替换成空字符串，然后判断是否为空，不为空则将参数传入age，否则忽略不对查询结果产生影响。IFNULL 是mysql中的一个函数，这个函数一般用来替换 NULL 值的。IFNULL(value1,value2)，判断value1是否为null，如果为null则用value2替换。
6. **参数定义时，定义数值，应使用Integer，**如果用int定义，当入参为NULL时，程序会报空指针错误。原因是JAVA 中 int 是值类型，非对象，不可以设置为 NULL，integer 是对象类型，可以设置为NULL

