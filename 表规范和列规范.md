---
description: '建表, 建列, 添加列, 删除列, 修改列'
---

# 表规范 和 列规范

## 建表的过程就是一个 声明字段 \(表头\)的过程

### 存储同样的数据, 不同的列类型,所占据的空间和效率是不一样的--- 这就是建表前的 列类型 的意义. 

#### 在开发中, 某些信息优化往往是  _把频繁用到的信息,优先考虑效率 存储到一张表中._

#### 不常用的信息和比较占据空间的信息, _优先考虑空间占用, 存储到辅表中._

## 建表语法

```sql
# 建表语法: 就是一个声明列的过程

mysql> CREATE TABLE 表名(
        列1的声明 列类型 列1参数,
        列2的声明 列类型 列2参数,
        列3的声明 列类型 列3参数,
        ....
        列n的声明 列类型 列n参数
        )ENGINE  myisam/innodb/bdb   CHARSET  utf8/gbk/latin1..... ;

# 一般都会设置一个主键 ,所以 需要在设置主键的 列参数内 添加如下参数, 主键表识是 PRI
        # primary key
# 设置自动增长类型,也是在 列参数 内添加.
        #  auto_increment
# 最后一行参数是 : 
#        ENGINE 后面跟的是设置引擎
#        CHARSET  后面是字符集
#

#-----------------例子----------------------
mysql> CREATE TABLE member(
    -> id int unsigned auto_increment primary key,
    -> username char(20) not null default '',
    -> gender char(1) not null default '',
    -> weight tinyint unsigned not null default 0,
    -> birth date not null default '0000-00-00',
    -> salary decimal(8,2) not null default 0.00,
    -> lastlogin int unsigned not null default 0
    -> )ENGINE myisam CHARSET utf8;
```

## 添加列的语法

```sql
# 一张表创建完毕有了N列, 之后还有可能要增加或删除或修改列.
# 默认会加在最后的末尾.  AFTER 参数指定某列后插入,  FIRST 参数指定在表头插入.

mysql> ALTER TABLE 表名 ADD 列名称 列类型 列参数;   [加的列在表的最后]
    #例子: ALTER TABLE m1 ADD birth  date not null default '1900-01-01';

mysql> ALTER TABLE 表名 ADD 列名称 列类型 列参数 AFTER 某列; [把新列指定加在某列后面]
    #例子: ALTER TABLE m1 ADD gender char(1) not null default '' after username;

mysql> ALTER TABLE 表名 ADD 列名称 列类型 列参数 AFTER FIRST; [把新添加行放在整个表的最前面]
    #例子: ALTER TABLE m1 ADD pid int not null default 0 FIRST;
```

## 删除列的语法 \(尽量不要删除,可以使用标志位\)

```sql
# 删除表中存在的列, 而且对应列的行数据也会被删除,

mysql> ALTER TABLE 表名 DROP 列名;   [删除表中存在的某列]
    #例子: ALTER TABLE m1 DROP pid;
```

## 修改列类型的语法 \(谨慎修改\)

```sql
# 修改已经存在的列, 对应列的数据可能会丢失, 如果修改类型完全不同,那么会报错 (扩充容量除外).

mysql> ALTER TABLE 表名 MODIFY 列名 新类型 新参数;  [修改原有的列类型,名字不会发生变化]
    #例子:  ALTER TABLE m1 MODIFY gender char(4) not null default '';
```

## 修改列名及列类型 \(一定要非常谨慎\)

```sql
# 修改已经存在的列的名字及其类型, 如果修改类型完全不同的话,会报错.(非常容易丢失数据), 
# 如果修改的是主键列, 那么主键属性不会丢失, 但是自增长 属性会丢失,(在没有设定的情况下).

mysql> ALTER TABLE 表名 CHANGE 旧列名 新列名 新类型 新参数;    [修改列名,以及列参数]
    #例子: ALTER TABLE m1 CHANGE id uid int unsigned;
```

### 获取表的相关信息

```sql
# 获取表的信息
mysql> SHOW TABLE STATUS  like  '表名' \G   #如果不写表名, 则代表获取当前库下,所有的表信息.

# 表的 返回值大概是这样的:  
*************************** 1. row ***************************
           Name: Customers               #表名
         Engine: InnoDB                  #表的存储引擎
        Version: 10                      #版本
     Row_format: Compact                 #行格式, 对于MyISAM引擎,可能是 Dynamic动态, Fixed固定,compressed可变
           Rows: 5                       #表中的行数,对于非事务性表,这个值是精确的,对于事务性引擎(Innodb),这个值通常是估算的.
 Avg_row_length: 3276                    #平均每行包括的字节数
    Data_length: 16384                   #整个表的数据量  (单位: 字节)
Max_data_length: 0                       #表可容纳最大数据量
   Index_length: 0                       #索引占用磁盘的空间大小
      Data_free: 0                       #对于MyISAM引擎,表示已分配,但现在未使用的空间,并且包含了已被删除行的空间.
 Auto_increment: NULL                    #下一个自动增长类型的值
    Create_time: 2019-05-17 17:56:09     #表的创建时间.
    Update_time: NULL                    #最新更新时间
     Check_time: NULL                    #使用 CHECK TABLE 或 MYISAMCHK 工具检查表的最近时间
      Collation: utf8mb4_bin             #表的默认字符集和字符排序规则,utf8mb4_bin来进行(对大小写敏感)
       Checksum: NULL                    #实时校验和值, 对整个表的内容计算时的校验和
 Create_options:                         #创建表时的其他所有选项.
        Comment:                         #创建表时使用的注释和额外信息,
                                         #   如果是MyISAM引擎,包含注视
                                         #   如果是Innodb引擎,则表示表的剩余空间
                                         #   如果是一个视图, 则包含 VIEW 字样.
        

# 视图  的返回值信息:
*************************** 10. row ***************************
           Name: stats                  #这是一个视图名
         Engine: NULL
        Version: NULL
     Row_format: NULL
           Rows: NULL
 Avg_row_length: NULL
    Data_length: NULL
Max_data_length: NULL
   Index_length: NULL
      Data_free: NULL
 Auto_increment: NULL
    Create_time: NULL
    Update_time: NULL
     Check_time: NULL
      Collation: NULL
       Checksum: NULL
 Create_options: NULL
        Comment: View 'test.stats' references invalid table(s) or column(s) or 
                 function(s) or definer/invoker of view lack rights to use them 
        
```





