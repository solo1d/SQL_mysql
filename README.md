---
description: 目录
---

# SQL笔记

## 数据库学习笔记

### DLL : 数据库定义语言\( 管理数据结构的 \)  : 用来 定义表 有什么样的类型和数据.

#### 只要是对表进行操作\(增,删,改\), 一般都是 ALTER 

1. _**创建数据库**_
   1. mysql&gt;  CREATE DATABASE 数据库名  CHARSET UTF8;
2. _**显示所有数据库**_
   1. mysql&gt;  SHOW  DATABASES ;
3. _**使用数据库**_
   1. mysql&gt;  USE 数据库名;
4. _**创建表:  表: 存储数据的单位.**_
   1. 用户表: 用户的ID, 用户的年龄,  用户名
   2. mysql&gt;  CREATE TABLE user \( id int , age int , username varchar\(10\) not null default ''\);
5. _**显示 使用的数据库内 所有的表**_
   1. mysql&gt;  SHOW TABLES;
6. _**查表的字段, 也就是表的结构.**_
   1. mysql&gt;  DESC 表名;
7. _**给表加字段   , 一般表操作都是 ALTER ,   添加一个性别字段**_
   1. mysql&gt;  ALTER TABLE  表名   ADD  添加的新列的名字     新列属性;
8. _**修改字段的类型**_
   1. mysql&gt;    ALTER TABLE   表名   MODIFY  要修改的字段名    新字段类型;
9. _**修改表的存储引擎**_
   1. mysql&gt; ALTER TABLE  表名 ENGINE= Innodb;
10. _**修改表名**_
    1. mysql&gt;  RENAME   TABLE  原表名  TO   新表名;
11. _**修改表的字符集**_
    1. mysql&gt;  ALTER TABLE 表名  CHARACTER SET   字符集名称UTF8或者GBK之类的;
12. _**修改列名**_
    1. mysql&gt;   ALTER  TABLE  表名  CHANGE 原始列名   新列名   新字段类型;
13. _**查看建表细节, 也就是建表语句.**_
    1. mysql&gt;  SHOW CREATE TABLE 表名;
14. _**删除字段**_
    1. mysql&gt;   ALTER TABLE   表名  DROP  字段名; 
15. _**删除表**_

    1. mysql&gt;  DROP TABLE  表名;

### DML : 管理数据 ,  管理表内有什么样子的数据

* _**查询所有的数据**_
  * mysql&gt;  SELECT \* FROM   表名;     
  * \#后面有个 /G 参数它会导致数据格式发生变化,可加可不加.  如果加了 则末尾不能出现 ;  分号
* _**插入数据**_
  * mysql&gt;  INSERT INTO 表名   \(字段1, 字段2 \)  VALUES    \(字段1的值, 字段2的值\) , \(  字段1的值,字段2的值\);
  * \#如果省略VALUES前面的字段, 则表示挨个字段进行插入,不能跳过.
* _**更新数据**_
  * mysql&gt;  UPDATE  表名  SET  列名 = 新列值  WHERE   列名=将被修改的列值;
* _**删除记录**_
  * mysql&gt; DELETE   FROM  表名   WHERE  列名=值; 



### DQL :   数据查询语句  

* _**查询所有数据**_
  * mysql&gt;  SELECT \* FROM 表名;
* _**查询指定数据**_
  * mysql&gt;  SELECT 列名1,列名2,列名4   FROM 表名  WHERE  条件;
* _**条件查询  \(根据条件查询\)**_
  * mysql&gt;  SELECT \* FROM  表名  WHERE   条件;
    * BETWEEN 小 AND 大    /   IN  \(集合\)   /  运算符  / LIKE  值% \_  / AND   /   OR   /   NOT    /  IS NULL   /    IS NOT NULL     
* _**排序 \(是对查询出来的结果进行排序, 也就是说 先查询,然后再排序\)**_
  * mysql&gt;  SELECT \* FROM 表名  WHERE 条件        ORDER  BY  列    DESC/ASC ;    大到小/小到大
* _**分组  \(**_

### 聚合函数 : 

* _**COUNT \(列\)     统计不为 NULL的行数.**_  
  * mysql&gt;   SELECT COUNT\( name\)  AS a FROM stu;    \#会输出, name不为 NULL的 行数, 列名是 a
* _**MAX \(列\)      取这一列数值最大的.**_
  * mysql&gt;  
* _**MIN\(列\)     取这一列数值最小的**_
  * mysql&gt; 
* _**SUM\(列\)    将这一列的所有数据相加,然后将结果返回**_
  * mysql&gt;  
* _**AVG\(列\)     将这一行的和取平均值  返回.**_
  * mysql&gt;  





















