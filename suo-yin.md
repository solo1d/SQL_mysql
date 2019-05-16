---
description: '多列索引, 冗余索引'
---

# 索引和约束以及主键

## 索引的作用与类型

#### 索引是数据的目录,能快速定位行数据的位置

#### 索引提高了查询速度,但是降低了增删改的速度, 因为操作之后也需要更新索引.  并非加的越多越好

#### 一般在查询比较频繁的列上, 而且在区分度高的列上加效果更好.

#### 在查询语句前面 添加语句  explain  就可以得到 这句查询是否使用到了索引, 以及使用的是哪个索引.

### 语法

```sql
# 索引必须在所有列声明完成之后再用逗号 添加到列名 声明后面去.
key            普通索引;  纯粹提高查询速度,      属性标示为 Key = MUL 
unique key     唯一索引;  加快查询速度,约束数据,不允许插入相同的值(唯一的是数据). 属性标示为 Key = UNI  
primary key    主键索引;  整张表只能存在一个,     属性标志为 Key = PRI
fulltext       全文索引;  中文下几乎无效,要分词+索引,一般用第三方解决方案, 如sphinx 

# 修饰:
auto_increment  自增
not null        不为空
default         默认值



# 范例1 : 普通建立索引和主键
mysql>  CREATE TABLE t1(
        name char(10),
        id  int,
        email char(20),          # 列声明结束
        key name(name),          # 声明 name 为普通索引, name 是索引名
        unique key email(email), # 声明email 为 唯一索引, 并且约束这个列的数据.
        primary key (id)         # 声明 id 为 这个表的 主键,
        )ENGINE Innodb CHARSET utf8;

#范例2: 限制索引长度
mysql>  CREATE TABLE t2(
        name char(10),
        email char(20),
        unique key email(email(10))    #只使用email的前10个字符来建立索引. 与email本身长度无关
        )ENGINE Innodb CHARSET utf8;    
        
# 范例3: 多列索引
mysql> CREATE TABLE t3(
        xing char(2),
        ming char(10),
        key xm(xing,ming)    # 多列索引,
        )ENGINE Innodb CHARSET utf8;
        
# 范例4: 冗余索引
mysql> CREATE TABLE t4(
        xing char(2),
        ming char(10),
        key xm(xing,ming),
        key m(ming)        # ming 存在两个索引中.
        )ENGINE Innodb CHARSET utf8;
        
```

### 索引长度: 建索引时, 可以只索引列的前一部分的内容, 比如 邮箱的前10个字符.\(因为邮箱后缀一般都是 @xx.com \)  如: key email\(email\(10\)\);

### 多列索引:  就是把2列或多列的值,看成一个成体,然后建立索引.

### 冗余索引:  就是在某个列上, 可能存在多个索引.

### 左前缀, 如果想使用索引,那么必须遵守左前缀 ,

#### 左前缀: 知道索引的开头部分, 不知道后部分, 那么这是左前缀,  否则不是.\(abcd 知道 ab  就是左前缀\)

## 索引操作

```sql
# 查看一张表的索引
mysql> SHOW INDEX FROM 表名 \G

# 显示某张表的建表语句
mysql> SHOW CREATE TABLE 表名;

# 删除某张表的索引
mysql> ALTER TABLE 表名 DROP INDEX 索引名;  #如果建表时索引是 key na(me),那么这里就写na
mysql> DROP INDEX  索引名  ON 表名;        # 这个也可以做到删除.

# 添加索引
mysql> ALTER TABLE 表名  ADD  INDEX  普通索引  索引名(需要索引的列); # 普通索引 
mysql> ALTER TABLE 表名  ADD  UNIQUE KEY  索引名(需要索引的列);     # 唯一索引
mysql> ALTER TABLE 表名  ADD  PRIMARY KEY(需要主键的列);           # 主键
    # ALTER TABLE t3 ADD INDEX key na(me);
    # ALTER TABLE t3 ADD UNIQUE key na(me,id);
    # ALTER TABLE t3 ADD  primary key (me) ;    # 主键不是 INDEX,所以不需要添加

#添加主键
mysql> ALTER TABLE 表名 ADD PRIMARY key(列名);     # 这就是添加主键的格式
    # ALTER TABLE tableName ADD PRIMARY key(me);

#删除主键
mysql>  ALTER TABLE 表名  DROP PRIMARY key;     #因为主键没有名字,所以不用写
        # 如果主键有 自增属性   那么需要先删除这个自增属性.
    
# 删除列的自增属性 (删除主键前 需要删除自增,并且重新给定属性)
mysql> ALTER TABLE 表名  CHANGE  原列名   新列名  新属性;   #两个列名可以相同
```

## 表的约束\(5种\)

* 检查 
  * check\(\)
* 非空  
  * not null
* 唯一 
  *  unique  key 索引名\(列\)
* 主健\(非空 加 唯一\)    
  * primary key 
* 外键   
  * REFERENCES 表名\(列\)

### 约束  check 

```sql
# 关键字 check 设定必须要在列属性的最后
mysql> CREATE TABLE 表 (
        ID int PRIMARY KEY AUTO_INCREMENT,
        sex  char(1) NOT NULL CHECK ( price IN('男','女')) 
        )ENGINE INNODB DEFAULT CHARSET UTF8;
#  check 关键字后面跟的是对应这一列的 的值的限制约束, 可以进行 集合和各种运算限制
# 目前mysql 下 无效.
```

### 外键必须引用头表的主键,  如果不是主键,则没有资格当外键.

#### 设置外键:外键关键字必须在列属性的最后!!!

```sql
# 关键字 在列后面,  REFERENCES  外键关联的表( 关联表的列名)
mysql> CREATE TABLE 表  (
        Id  int PRIMARY KEY AUTO_INCREMENT,   #主键
        name   varchar(10) NOT NULL default '', 
        inst   int  NOT NULL REFERENCES class(sid)  #外键,sid是class表的主键
        ) ENGINE INNODB DEFAULT CHARSET UTF8;       # 注意顺序
```

### 外键删除方式

#### 删除 主表的时候, 需要先删除 所有的外键表.否则会造成 主表无法删除.

* casecade  级联删除, 主表记录删除的时候,子表数据跟着删除
* set null   设置为null  ,  主表删除的时候,  字表 外键 字段设为null
* 默认方式,  主表删除的时候, 如果字表引用了该字段的数据, 不能删除.
