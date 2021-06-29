# SQL笔记

## 数据库学习笔记

数据库对象:  表, 视图,  索引, 序列 ,  同义词, 存储过程,  存储函数,  触发器,  包, 包体, 数据库链路, 快照. 

### 关键顺序

#### 书写顺序\(执行顺序\):    SELECT\(7\)   聚合函数\(4\)  计算所有的表达式\(6\)  FROM\( 1\)     WHERE \(2\)      GROUP BY\(3\)       HAVING\(5\)     ORDER BY\(8\)     LIMIT \(9\)  

 SELECT DISTUBCT       删除查询结果中,重复的值,

### DLL : 数据库定义语言\( 管理数据结构的 \)  : 用来 定义表 有什么样的类型和数据.

#### 只要是对表进行操作\(增,删,改\), 一般都是 ALTER 

1. _**创建数据库**_
   1. `mysql>   CREATE DATABASE 数据库名  CHARSET UTF8;`
2. _**显示所有数据库**_
   1. `mysql>   SHOW  DATABASES ;`
3. _**显示建表语句**_
   1. `mysql>   SHOW CREATE TABLE 表;`
4. _**使用数据库**_
   1. `mysql>   USE 数据库名;`
5. _**创建表:  表: 存储数据的单位.**_
   1. 用户表: 用户的ID, 用户的年龄,  用户名
   2. `mysql>   CREATE TABLE user ( id int , age int , username varchar(10) not null default '')  ENGINE Innodb  CHARSET UTF8;`
6. _**显示 使用的数据库内 所有的表**_
   1. `mysql>   SHOW TABLES;`
7. _**查表的字段, 也就是表的结构.**_
   1. `mysql>   DESC 表名;`
8. _**给表加字段   , 一般表操作都是 ALTER ,   添加一个性别字段**_
   1. `mysql>   ALTER TABLE  表名   ADD  添加的新列的名字     新列属性;`
9. _**修改字段的类型**_
   1. `mysql > ALTER TABLE   表名   MODIFY  要修改的字段名    新字段类型;`
10. _**修改表的存储引擎**_
    1. `mysql>  ALTER TABLE  表名 ENGINE= Innodb;`
11. _**修改表名**_
    1. `mysql>   RENAME   TABLE  原表名  TO   新表名;`
12. _**修改表的字符集**_
    1. `mysql>   ALTER TABLE 表名  CHARACTER SET   字符集名称UTF8或者GBK之类的;`
13. _**修改库的字符集**_
    1. `mysql>   ALTER DATABASE 库名   DEFAULT  CHARACTER SET UTF8;`
14. _**修改列名**_
    1. `mysql>    ALTER  TABLE  表名  CHANGE 原始列名   新列名   新字段类型;`
15. _**查看建表细节, 也就是建表语句.**_
    1. `mysql>   SHOW CREATE TABLE 表名;`
16. _**删除字段**_
    1. `mysql>    ALTER TABLE   表名  DROP  字段名; `
17. _**删除表**_

    1. `mysql>   DROP TABLE  表名;`



### DML : 管理数据 ,  管理表内有什么样子的数据

* _**查询所有的数据**_
  * `mysql>  SELECT * FROM   表名;`
  * \#后面有个 /G 参数它会导致数据格式发生变化,可加可不加.  如果加了 则末尾不能出现 ;  分号
  * `mysql>  SELECT COUNT(*) INTO @b  FROM 表名;`
  * **#将查询结果输出到 @b 这个变量中去,而不是显示在屏幕上, 统计表中 所有列不为 NULL 的行数**
      * `mysql> SET @LOCAL.BB='';`
      * `mysql> SELECT @LOCAL.BB;`
* _**插入数据**_
  * `mysql>   INSERT INTO 表名   (字段1, 字段2 )  VALUES    (字段1的值, 字段2的值) , (  字段1的值,字段2的值);`
  * \#如果省略VALUES前面的字段, 则表示挨个字段进行插入,不能跳过.
* _**更新数据**_
  * `mysql>   UPDATE  表名  SET  列名 = 新列值  WHERE   列名=将被修改的列值;`
* _**删除记录**_
  * `mysql>  DELETE   FROM  表名   WHERE  列名=值;`    \# 数据可以恢复,但是自增长不会重置
  * `mysql>   TRUNCATE TABLE  表名;`      \#删除整张表, 然后重新建立一个一样结构的表, 自增长会重置.



### DQL :   数据查询语句  

* 查询所有数据 SELECT
  * 查询指定数据  WHERE
  * 条件查询  (根据条件查询)  WHERE
  * 条件查询用到的关键字  BETWEEN 1 AND 2,  IN , LIKE , OR , AND, NOT,  IS NULL, IS NOT NULL
* 排序 (是对查询出来的结果进行排序, 也就是说在 SELECT(5) 后面 ,属于第6个,然后再排序) ORDER BY
* 分组  ( 在 WHERE (2)  后面,属于第3个 ) , 单列分组时 与 DISTINCT 关键字类似  GROUP BY
* 分页 ( 在ORDER BY (6)  后面,属于第7个 )  LIMIT

```mysql
#查询所有数据
mysql>  SELECT * FROM 表名;

	#查询指定数据
		mysql>   SELECT 列名1,列名2,列名4   FROM 表名  WHERE  条件;

	#条件查询  (根据条件查询)
		mysql   SELECT * FROM  表名  WHERE   条件;

		#条件查询用到的关键字
			BETWEEN 小 AND 大     # 在某范围内, 值1 和值2 之间, 也包括他俩.
    		  mysql> SELECT id,age FROM tablesname WHERE age BETWEEN 12 ADN 1; #范例
			IN  (集合)   
				  mysql> SELECT * FROM tablesname WHERE  age IN(10, 20, 30, 42); #范例
			运算符 
  	 		  mysql> SELECT * FROM tablesname WHERE  age >10; #范例
			LIKE  值%
		      mysql>  SELECT * FROM uu WHERE name LIKE '周%'; #范例
			AND
				 mysql>  SELECT * FROM uu WHERE id=3 AND age=15; #范例
			OR
				 mysql>  SELECT * FROM uu WHERE id=3 OR age=15; #范例
			NOT    
				 mysql>  SELECT * FROM uu WHERE  NOT  id=5;   #范例,输出 id不等于5的其他列
			IS NULL
			   mysql> SELECT * FROM uu WHERE name IS NULL;  #范例,输出name 为 NULL 的行
			IS NOT NULL
         mysql> select * from uu where name  IS  NOT NULL;  # 范例, 输出 name 不为 NULL 的其他行 


#排序 (是对查询出来的结果进行排序, 也就是说在 SELECT(5) 后面 ,属于第6个,然后再排序)
		mysql>  SELECT * FROM 表名  WHERE 条件   ORDER  BY  列    DESC/ASC;  大到小 / 小到大
		    mysql>  SELECT * FROM uu  WHERE id > 1 ORDER BY age DESC;  #范例

#分组  ( 在 WHERE (2)  后面,属于第3个 ) , 单列分组时 与 DISTINCT 关键字类似
   mysql> SELECT 列名   WHERE 条件  GROUP BY 分组的列名;
        mysql> SELECT age FROM uu GROUP BY age; #范例

#分页 ( 在ORDER BY (6)  后面,属于第7个 )
   mysql> SELECT 列名  WHERE  条件   LIMIT   从哪里开始 , 显示多少个  ; # 后面是两个int的值
       mysql> SELECT * FROM uu LIMIT 0,4;  #范例
```





### 聚合函数 : 

* _**COUNT \(列\)     统计不为 NULL的行数.**_  
  * mysql    SELECT COUNT\( name\)  AS a FROM stu;    \#会输出, name不为 NULL的 行数, 列名是 a
* _**MAX \(列\)      取这一列数值最大的.**_
  * mysql   SELECT MAX\(name\) AS a FROM stu;
* _**MIN\(列\)     取这一列数值最小的**_
  * mysql   SELECT MIN\(name\)   AS  a  FROM stu;
* _**SUM\(列\)    将这一列的所有数据相加,然后将结果返回**_
  * mysql    SELECT SUM\(Id\)  AS b  FROM stu;
* _**AVG\(列\)     将这一行的和取平均值  返回.**_
  * mysql   SELECT  AVG\(shop\_price\) AS  p   FROM stu;



### ==约束和索引以及主键 外键    \(建立和添加索引可以加快查询效率\)==

* _**primary key     主键  唯一而不为空,而且不能出现重复值!!!**_
  *   **创建时建立主键以及自动增长**
    * _mysql  CREATE TABLE  表名  \( Id int primary key  auto\_increment\) ENGINE Innodb CHARSET utf8;_    
  *  **添加主键, 将表内的Id列设为主键**
    * _mysql   ALTER TABLE   表名  ADD     primary key  \(Id\)  ;_     
  *  __**将表内的主键删除**
    * _删除主键之前需要删除,主键带的自增属性 \(auto\_increment\)_
      * _mysql  ALTER TABLE 表名 CHANGE   列名  新列名  没有自增的新属性;_ 
    * _mysql   ALTER  TABLE  表名  DROP  primary key;        \#因为主键唯一,所以 无名;_
* _**unique   key     唯一索引,  约束列  不会出现重复值**_
  * **创建时 建立唯一索引以及自动增长**
    * _mysql   CREATE TABLE  表名 \( Id int unique key auto\_increment\) ENGINE lnnodb CHARSET UTF8;_   
  * **添加唯一索引, 索引是 em, 索引内容是 id.** 
    * _mysql   ALTER TABLE 表名  ADD   UNIQUE  KEY  em\(id\) ;_  
  *  **删除唯一索引中的  em 索引. \(两种方法\)**
    * _mysql  ALTER TABLE 表名  DROP  INDEX  em ;_
    * _mysql  DROP  INDEX  索引的名称     ON  表名;_
* _**auto\_increment     修饰列类型使用, 自增类型**_
  * _mysql  CERATE  TABLE  表名  \(Id int  auto\_increment\) ENGINE Innodb CHARSET UTF8;_
* _**not null      修饰列类型使用,  不为空**_
  * mysql   _CERATE  TABLE  表名  \(Id int  not null default 0\) ENGINE Innodb CHARSET UTF8;_
* _**default      修饰列类型使用,  给一个默认初始化值**_
  * mysql   _CERATE  TABLE  表名  \(Id int  not null default 0\) ENGINE Innodb CHARSET UTF8;_
* _**check\(\)     限制设定的列的取值范围,  代表他只能设置为 后面的值的属性范围内.\(MYSQl下无效\)**_
  * _mysql _  
* _**显示表内已经存在索引**_
  * mysql   SHOW  INDEX FROM 表名;   \# 可以添加 /G 参数来更具体化的显示\(不适合程序\)
* _**外键, 想在一个表添加外键,那么必须有另外一个主表,而且只能引用主表的主键.\(两个表的 外键和主键的 属性相同\) , 把外键看成引用就行了**_
  * 设置外键
    * mysql   CREATE TABLE  表名 \(  id int  ,  sid int  REFERENCES 主表名\(主键列\)\); 
  * **删除 主表的时候, 需要先删除 所有的外键表.否则会造成 主表无法删除.**



### 多表联查

* _**99查询, 使用逗号连接**_
  * `mysql>  SELECT student.name, teacher.name  FROM student,teacher  WHERE sid=id;`
* _**左连接和左连接, LEFT JOIN ,   RIGHT JOIN**_
  * `mysql>   SELECT student.name , teacher.name  FROM student  LEFT  JOIN teacher  ON id=sid;`
  * `mysql>  SELECT student.name, teacher.name  FROM  teacher RIGHT JOIN student ON sid=id;`
* _**内连接  INNER JOIN**_ 
  * `mysql>  SELECT student.name,teachar.name FROM student INNER JOIN teachar ON sid=id;`
* _**合并  UNION      ,  两个表的取出的数据类型以及个数必须相同.   默认合并相同行,  ALL 参数则不合并**_
  * `mysql>   SELECT name,id   FROM student     UNION    SELECT name,sid FROM teacher ;`
  * `mysql>   SELECT name,id   FROM student    UNION ALL   SELECT name,sid FROM teacher;` 



### 设置一个临时值

* `mysql>   SET  @b=10;`
* `mysql>  SELECT   SUM(price)   INTO @b FROM goods;`
* `mysql>  SELECT @b;`  #输出临时值的内容



### 序列

#### 内部原理其实就是一个数据结构, 有两个int的值来标识当前下标和下一个节点,默认第一个Int指向空,第二个是第一个数据.

#### 创建序列 : mysql 内没有序列, 需要使用自增类型来进行别样实现.



### 视图

**创建和删除视图 : 一般用在隔离数据访问.**

```sql
# 视图是创建一个已有的表的一个投影,本身并没有数据, 他会随着主表的变化而变化, 
# 而且有时候视图也可以影响表的变化当视图和主表内容和字段完全相同时, 增删改查就可以影响到主表了.
# 视图的列和主表可以不同,可以添加和修改 以及运算. 视图本身没有数据, 它依赖于主表.
# 假设主表是  goods.   列 ID,name
mysql>  CREATE VIEW 视图表名
        AS 
        SELECT ID,name,ID*2 AS sid 
        FROM goods;

# 删除视图
mysql> DROP VIEW 视图表名;
```

### 同义词

#### 作用是  简化查询, 隔离访问. 跟设置权限差别不大

#### 因为mysql没用同义词,  需要使用   设置用户对某张表的权限来实现同义词

```sql
# 首先给用户对某个表的 特定的权限

mysql> GRANT 权限SELECT,INSERT ON 表 to 用户;

# 使用GRANT 关键字来设置对表的权限,  INSERT,SELECT,UPDATE 
# 后面跟着是表名 和  需要被设置的用户.
```

### 事务

```sql
#开启事务
mysql> start transaction;

#事务完成,提交, 结束事务, 将事务操作应用在表上.
mysql> commit;

#事务失败,回滚
mysql> rollback;
```

### 流程控制语句 之 选择语句

#### if 语句分支:    一般用在 存储过程内

```sql
第一种:
if 条件   then  条件为真执行这条语句  else  条件不为真执行这条语句   end if; #最后是结束标志
第二种:
if 条件1   then  条件为真执行这条语句  elseif  条件2  then  条件1不成立就判断条件2 为真执行这条语句
          else  前面条件1 2 都不为真则执行这条语句   end if;                

# 一般用在 存储过程 内, 但是实际应用会有些变化
mysql>  use test;
mysql> delimiter $$;
mysql> CREATE PROCEDURE 
        p_showage( in age int)
       begin
         if  age >= 18  && age <60
              then  SELECT '成年人';     #在这里必须添加分号
         elseif age>=60 
              then SELECT '老年人';     #注意分号
         else
              SELECT  '未成年人';        #这里也必须添加分号
         end if;
      end
      $$;
 mysql> delimiter ;
 mysql> call p_showage(18);




### 查看创建的自定义存储过程
mysql> SHOW CREATE PROCEDURE  存储过程名;

### 删除创建的自定义存储过程
mysql> DROP PROCEDURE 存储过程名;
```

#### case 语句分支: 可以出现在sql语句中, 也可以出现在 存储过程 内

```sql
第一种:
case 条件判断的变量 when 变量的取值1 then 1的执行语句  when 变量的取值2  then 2的执行语句
    else  以上条件都不为真时的执行语句   end case;

#例子1:普通语句
    mysql>  SELECT case age when 18 then '成年了'  when 12  then '未成年' else '不确定'
            end case   from  student;

#例子2: 存储过程 
    mysql>  use test;
    mysql> delimiter $$;
    mysql> CREATE PROCEDURE  
            p_date(in v_empno int)
            begin
            declare addS int;      # 定义一个局部变量addS, 类型是 int , 关键字 declare
            case v_empno
                when  1001
                    then  SET addS=1500;
                when  1002
                    then  SET addS=2500;
                when  1003
                    then  SET addS=3200;
                else
                    SET addS=1000;
                end case;
            UPDATE goods SET price = addS WHERE  id = v_empno;
                    #上面的UPDATE 解释: goods是表名,有price和id两个列, 运行的结果是,
                        #             找到 id 等于 v_empno的行 ,并将 price设置为 addS的值
            end
            $$;
```

ifnull\(st1,st2\)  控制语句

```sql
# ifnul(st1,st2)    如果st1是 空值 null, 就返回st2,  如果不是空值,就返回 st1

mysql> SELECT ifnull(null,'非空');
    #输出 : '非空'
mysql> SELECT ifnull('123','456');
    #输出:  '123'
mysql> SELECT ifnul('123',null);
    #输出:  '123'
```



## mysql 用户创建和权限

### 创建用户

```sql
mysql> CREATE USER 'username'@'host' IDENTIFIED BY 'password';

# username：你将创建的用户名
# host：指定该用户在哪个主机上可以登陆，如果是本地用户可用localhost，
#    如果想让该用户可以从任意远程主机登陆，可以使用通配符%
# password：该用户的登陆密码，密码可以为空，如果为空则该用户可以不需要密码登陆服务器
# 例子：

mysql> CREATE USER 'dog'@'localhost' IDENTIFIED BY '123456';
mysql> CREATE USER 'pig'@'192.168.1.101_' IDENTIFIED BY '123456';
mysql> CREATE USER 'pig'@'%' IDENTIFIED BY '123456';
```

### 权限

```sql
mysql> GRANT privileges ON databasename.tablename TO 'username'@'host';
# privileges：用户的操作权限，如SELECT，INSERT，UPDATE等，如果要授予所的权限则使用ALL
# databasename：数据库名
# tablename：表名，如果要授予该用户对所有数据库和表的相应操作权限则可用*表示，如*.*
# 例子
mysql> GRANT SELECT, INSERT ON test.user TO 'pig'@'%';
mysql> GRANT ALL ON *.* TO 'pig'@'%';

# 注意:
# 也就是说, 自己的权限是从其他用户授权来的, 而我的权限是我不能在分发给别人的.
# 用以上命令授权的用户不能给其它用户授权，如果想让该用户可以授权，用以下命令:

mysql> GRANT privileges ON databasename.tablename TO 
        'username'@'host' WITH GRANT OPTION;
```

### 设置与更改用户密码

```sql
# 使用root来修改其他用户的密码
mysql>   ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY "新密码";

#如果是当前登陆用户用:
mysql>  ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY "新密码";

#例子
mysql>  ALTER USER 'pi'@'' IDENTIFIED WITH mysql_native_password BY "123456";

#用户pig, 登陆地点所有, 密码修改为123456
```

### 撤销用户权限

```sql
mysql> REVOKE privilege ON databasename.tablename FROM 'username'@'host';
# privilege, databasename, tablename：同授权部分

#例子
mysql> REVOKE SELECT ON *.* FROM 'pig'@'%';

#注意:
#假如你在给用户'pig'@'%'授权的时候是这样的（或类似的）：
#    mysql> GRANT SELECT ON test.user TO 'pig'@'%';
#   则在使用REVOKE SELECT ON *.* FROM 'pig'@'%';
#   命令并不能撤销该用户对test数据库中user表的SELECT 操作。
#   相反，如果授权使用的是GRANT SELECT ON *.* TO 'pig'@'%';
#   则REVOKE SELECT ON test.user FROM 'pig'@'%';
#   命令也不能撤销该用户对test数据库中user表的Select权限。
# 具体信息可以用命令SHOW GRANTS FOR 'pig'@'%'; 查看。
```

### 删除用户

```sql
mysql> DROP USER 'username'@'host';

# '用户名' @ '登录方式'
```



## 会话变量和全局变量

```sql
# session 代表会话,   global 代表全局

# 这两个变量主要记录客户端的设置的 会话变量.(差不多可以看成是环境变量)
mysql> SHOW session variables  \G    #这样更好显示        
    # 客户端可以运行这个命令,来获取600行以上的 '局部会话变量'
mysql> SHOW GLOBAL  variables;
    # 运行这个命令, 来获取 '全局会话变量'

mysql> SHOW session variables  like '_查找内容%';
mysql> SHOW GLOBAL  variables  like '_查找内容%';
    # 两者都可以支持 模糊查找 

mysql> SELECT @@GLOBAL.autocommit; 
        #这个是查看全局变量中的 autocommit 这个选项的值是多少,  0表示off, 1表示on
    

mysql> SET  变量 = '需要设置的值';     # 这样来临时修改变量,只影响这一个客户端的会话
mysql> SET GLOBAL 变量 = '需要设置的值';  # 这样来修改全局变量, 所用登陆的用户都会修改,针对数据库

示例:  
mysql> SET autoconnit = 'off';    
    #自动保存设置, 手动修改为关闭.  这个是临时设置,也就是局部变量,下次登陆还是会恢复成原本的默认值.
mysql> SET GLOBAL autoconnit = 'off';
    #自动保存设置, 手动修改为关闭.  这个是全局设置,所有的客户端都会被修改成这个设置.

```

## 存储过程 

```sql
#1. 首先选中一个数据库, 注意: 创建的存储过程, 只能在选中的这个库内执行!!!!
        mysql> use  库;

#2. 改变分隔符功能  ;    不让其成为执行结束的标记. 主要是为了后面的步骤.
        # 执行完毕后,分号结尾就无用了,  必须使用  $$  来进行语句结尾.( 或者使用 $$; )
                mysql>  delimiter $$;
                mysql>  SHOW TABLES $$    #语句也就变成了这样.

#3. 创建存储过程,关键词是 procedure, 随后的参数是自定义的过程名字和括号内的参数,就当函数看.
        mysql> CREATE PROCEDURE  
                hello_p()        #  比如我自定义的名字是 hello_p,把这个看成函数名字
                begin              #从这里开始向下到 end  ,当成函数体看.
                SELECT 'hello';     # 上面改变了分隔符, 所以这里不会直接执行,而是继续向下读取
                SELECT 'world';     
                end  $$           # 两个$ 才是分隔符,  (没准还会加上 ;  分隔符)

#4. 存错过程创建完成后, 需要恢复分隔符的功能,然后才可以执行刚刚创建的存储过程.
        mysql> delimiter ;        #执行完毕之后可能没有返回值.这是正常的.(报错就是你关键字打错了)

#5. 执行存储过程. 说白了就是一个函数调用.
        mysql> call hello_p;      # 调用函数名, 如果我括号内有参数,那么需要用括号带上参数.
        mysql> call hello_p(参数1,参数2);   #调用有参数的 存储过程 
```
