---
description: '表的分析 检查 优化,  分区,  优化MsqlServer ,  应用优化,  权限管理,   监控,  定时维护, 定时器,  备份和还原.'
---

# MYSQL优化

#### Mysql优化的原因:   性能低,  执行时间长,  等待时间长,  SQL语句欠佳\(连接查询\),  索引失效,  服务器参数设置不合理.

## status 是服务器状态变量

## variables 是服务器系统变量

## SQL语句优化

### 编写过程:  SELECT DINSTINCT ... FROM ... JOIN ... ON ... WHERE ... GROUP BY .... HAVING ... ORDER BY .... LIMIT ....

### 解析过程:  FROM ... ON ... JOIN  ... WHERE.. GROUP BY ... HAVING ... SELECT   DINSTINCT ... ORDER BY ... LIMIT ...

#### SQL语句优化, 就是在优化索引.

### SQL性能问题

* 分析SQL的执行计划    :   
  * mysql&gt;  explain + SQL 语句;    可以模拟SQL优化器执行SQL语句, 从而让开发人员知道自己编写的SQL状态
* Mysql查询优化器会干扰用户的优化.
  * 

## 索引优化

### 分类:

* 单值索引   单列, 一个表可以有多个单值索引
* 唯一索引   不能重复的列
* 复合索引   多个列构成的索引





### 索引带来的好处

* 提高查寻的效率 ,  \(降低IO使用率\)
* 降低CPU使用率
* 
### 索引的弊端

* 索引本身很大, 可以放在硬盘或者内存内. 通常是硬盘, 很占据空间
* 索引不是所有情况都适用: a.少量数据,   b .频繁更新的字段,     c.很少使用的字段
* 索引会降低增删改的效率

## B树

![B&#x6811;](.gitbook/assets/ping-mu-kuai-zhao-20190524-18.06.33.png)

#### 红色块数据树用来对比的,而不是真正的数据,  

#### 对比过程:  一个数字对比根结点的两个数字\(也有可能超过两个\),  那么比数据A 小的就找P1指针, 比数据B大的就找P3指针, 介于两者之间的去找P2 指针, 往复循环.

#### B树分为 B+ 树和 B-  树,   一般指的是 B+树

#### B+  树就是左侧是较小的数字,左侧是较大数字, B- 树则相反.



## 慢查询

#### MySQL 记录下查询超过指定时间的语句,  我们将超过指定时间的SQL语句查询称为 "慢查询".

#### 慢查询的所有超过设置时间的 语句 都会记录在 以  -slow .log  结尾的日志文件中.

#### 慢查询日志功能是关闭的, 需要慢查询日志,首先要开启慢查询日志功能, 开启之后重启服务.

* 在mysql的配置文件中添加 如下内容,\( sudo vim /etc/mysql/mariadb.conf.d/50-server.cnf \)
  * log-slow-queries = /home/pi/mysql\_log/mysql-slow-query.log    \#你必须对这个目录拥有写权限
  * log-query-time = 5            \#设置 慢查询时间,超过这个5秒 设置,那么查询语句 就会被记录到日志内.
  * log-queries-not-using-indexs         
* 有的时候 mysql 配置文件中也会有如下内容, 他会帮助你设定好. \(配置文件名 50-server.cnf \)

```bash
#这是我配置文件的内容, 因为前面有#号,所以不生效, 你可以去掉#号设置并让他生效.来启用慢查询功能.

# Enable the slow query log to see queries with especially long duration
# slow-query-log=On              # 表示开启 慢查询 
#slow_query_log_file	= /var/log/mysql/mariadb-slow.log    # 日志名和位置
#long_query_time = 10                                        # 设定时间
#log_slow_rate_limit	= 1000                               # 日志题条数限制
#log_slow_verbosity	= query_plan                    
#log-queries-not-using-indexes
```

### 查询和设置慢查询

```sql
#查询慢查询的设定时间. (单位是秒,浮点数, 既然是浮点数,那么小数点后面的就是毫秒级别了) 0.1秒=100毫秒
# 名字是 long_query_time, 后面的 Value 值代表 查询时间超过该值的话就会被记录 .
mysql>  SHOW GLOBAL variables  like 'long_query_time';   #所有客户端的设定时间. 全局变量
mysql>  SHOW   variables  like 'long_query_time';    #单个客户端的设定时间. 会话变量
        # 如果一条语句的执行时间 超过设定的值, 那么这条语句 就会被称为 慢查询.


# 将慢查询语句的时间上限 修改为 5秒;  (GLOBAL代表全局)
mysql>  SET GLOBAL  long_query_time = 5;      #设置所有人, 全局变量
mysql>  SET  long_query_time = 5;       # 只设置目前使用的单个客户端的值, 会话变量


# 查询有多少条语句被慢查询记录了
mysql> show GLOBAL status  like 'slow_queries';   # 整个服务器, 被记录 慢查询语句的总个数.
```

### 启动慢查询 和 mysql 服务安全模式

```bash
#第一种方法, 直接设置全局变量,不需要重启服务器

"首先查询是否开启了安全模式,如果关闭 那么请打开它"
mysql> show GLOBAL variables like 'SQL_SAFE_UPDATES';  # OFF 关  ON 开

mysql>set global slow_query_log=ON;   # 开启慢查询模式

-------------------------------------------------
#第二种方法  , 首先需要关闭服务器,然后再添加参数后 重启
"以安全模式启动 mysql 服务, 才可以启动慢查询"
"首先停止MySQL服务"
sudo service mysqld stop
sudo service mysqld_safe stop
sudo service mysql stop 
sudo /etc/init.d/mysql  stop

"安全模式启动 mysql 服务"
sudo mysqld --safe-mode --slow-query-log

"启动后 bash 可能会卡死, 这个是正常现象"
```

### 找到  慢查询日志记录文件

```bash
#可以直接在mysql内输入命令来查找,他会返回 日志所在的位置.
 mysql> show variables like "%slow%";   
 

"找到 mysql 安装目录"
whereis  mysql ;

"来到配置文件目录"
cd /etc/mysql/mariadb.conf.d

"打开 50-server.cnf 这个服务器配置文件, 寻找到 datadir 这个选项, 例如下面"
# 假如我的配置文件中 是这么写的:   datadir = /var/lib/mysql

"随后进入上面搜索到的路径"
cd /var/lib/mysql

"该目录下会有一个结尾是 : xxxx-slow.log  文件, 里面存放的就是慢查询记录"
例如我的树莓派就是 raspberrypi-slow.log :   sudo vim  raspberrypi-slow.log
```

## 在使用MYSQL 的时候,一些常见的设置

```sql
# show status 语句
mysql>  show status  like 'uptime';         # 获取当前数据库运行时间.单位是 秒
mysql>  show status  like 'com_Select';     # 获取目前我登陆的客户端所执行的查询语句SELECT的个数.
    mysql> show GLOBAL status like 'com_Select';    #获取所有人执行SELECT的个数总和.
mysql>  show status  like 'connections';    # 获取 服务器 被登陆的次数.(被客户端连接的次数)
mysql>  show status  like 'slow_queries';   #  被记录的 慢查询 语句的个数.
    mysql> show GLOBAL status  like 'slow_queries'; # 整个服务器,被记录的 慢查询语句的总个数.

```

## 表的优化

#### 无论是在ANALYZE, CHECK 还是 OPTIMIZE 在执行期间将对表进行锁定,  因此请注意 这些操作要在数据库不繁忙的时候进行.

#### 在进行分析和检查表之前,  先获取以下表的信息,    \(返回值解释 放在  表规范和列规范 里面了.\)

```sql
# 获取表的信息
mysql> SHOW TABLE STATUS  like  '表名' \G   #如果不写表名, 则代表获取当前库下,所有的表信息.
```

* 定期分析表,      只针对某些引擎的表,
  * mysql&gt;  ANALYZE    TABLE  表名 ;     \#可以同时分析多个表.
* 定期检查表
  * mysql&gt;  CHECK  TABLE  表名 ;
  * CHECK TABLE 也可以检查视图 是否有错误,比如在视图定义中被引用的表已不存在.
* 定期优化表     
  * mysql&gt;  OPTIMIZE\]  TABLE 表名;
  * OPTIMIZE TABLE  只对 MyISAM  , BDB 和 INNODB表 起作用.
    * 对于MyISAM表, OPTIMIZE TABLE 按以下方式操作:
      * 如果表已经删除或分解了行, 则修复表.
      * 如果未对索引也进行分类, 则进行分类.
      * 如果表的统计数据没有更新\( 并且通过对索引进行分类不能实现修复\), 则进行更新.

## 优化MYSQL服务中的内存管理优化  以及 并发调整

### MyISAM 引擎 内存优化

MyISAM 引擎使用的是 key\_buffer 引擎索引块,  加速myisam 索引的读写速度. 对于myisam 表的数据块,mysql 没有特别的 缓存机制,  完**全依赖于操作系统的 IO 操作.**

* **key\_buffer\_size 设置**
  * _key\_buffer\_size 决定 myisam 索引块缓存区的大小._  直接影响 myisam表的存取效率. 对于一般 myisam 数据库, 建议将  四分之一  可用内存分配给 key\_buffer\_size;
    * **首先查看key\_buffer\_size  设置**
      * mysql&gt;  SHOW GLOBAL variables like 'key\_buffer\_size';   
        * 如果显示出来的数字很大 , 那么单位是字节,  默认值是167777216  \( 160 MB\)
    * **然后设置它占用的缓存空间. 有可能是命令,也有可能是配置文件.**
      * mysql&gt;   SET  GLOBAL  key\_buffer\_size = 262144000;  \#设置为 250MB
* **read\_buffer\_size 设置**
  * 如果需要经常顺序扫描  myisam 表, 可以通过增大 read\_buffer\_size 的值来改善性能. 但需要注意的是 read\_buffer\_size 是每个 session\(会话\) 独占的,  **如果默认值设置太大,  会造成内存浪费.**
    * **首先查看  read\_buffer\_size 设置,  有可能是命令,也有可能是配置文件.**
      * mysql&gt; SHOW variables like 'read\_buffer\_size';
        * 单位和上面的设置相同, 也是字节, 默认值是 131072 \(128KB\)
    * **修改他的设置**
      * mysql&gt;  SET read\_buffer\_size =  10007000 ;         \# 设置为500 KB
* **read\_rnd\_buffer\_size  设置**
  * 对于需要做排序的 myisam 表查询, 如 带有 order by  子句的sql ,适当增加 read\_rnd\_buffer\_size 的值, 可以改善此类 sql 的性能, **但需要注意的是, read\_rnd\_buffer\_size 是每个 session\(会话\) 独占的, 如果默认值设置太大,就会造成内存浪费.**
    * **首先查看  read\_rnd\_buffer\_size 设置是多少,  有可能是命令,也有可能是配置文件.**
      * mysql&gt; SHOW variables like 'read\_rnd\_buffer\_size'; 
        * 单位是字节,  默认是  262144 \(256KB\)
      * mysql&gt; SET read\_rnd\_buffer\_size = 10007000;    \#设置为500KB



### Innodb 引擎  内存优化

Innodb 用一块内存区做 IO 缓存池, 该缓存池不仅用来缓存 Innodb的索引块, 而且也用来缓存 Innodb的数据块.

* **innodb\_buffer\_size    设置**
  * 该变量决定了 Innodb 存储引擎  表数据和索引数据  的最大缓冲区大小.
    * mysql&gt;  SHOW variables like 'innodb\_buffer\_size';        \#查寻
    * mysql&gt;  SET innodb\_bufer\_size = 262144000;       \#设置为 250MB
* **innodb\_log\_buffer\_size  设置**
  * 决定了Innodb 重做日志缓存的大小,  对于可能产生大量更新记录的大事务. 增加 innodb\_log\_buffer\_size  的大小, 可以避免 Innodb 在事务提交前就执行不必要的日志写入磁盘操作.​
    * mysql&gt; SHOW variables like 'innodb\_log\_buffer\_size';      \#查询
    * mysql&gt; SET  innodb\_log\_bufer\_size = 10007000;   设置为 500KB



### 调整MYSQL 并发相关的参数

* 调整 **max\_connections**  提高并发连接, 这个就是同时连接到服务器的 连接数. 
  * **\( 如果需要千万级并发,那么这个值就一定要设置到千万\)**
    * mysql&gt;  SHOW variables like '**max\_connections**';     \#查寻
    * mysql&gt; SET  **max\_connections** =20000 ;     \#设置同时连接数是 20000
* 调整 **thread\_cache\_size**  ,加快连接数据库的速度, mysql会缓存一定数量的客户端服务线程 以备重用, 通过参数  thread\_cache\_size  可控制 mysql 缓存客户端服务线程的数量.  ****
  * **\(如果需要千万级并发, 那么这值也不能少于几千 或几万 \)**
    * mysql&gt;  SHOW variables like '**thread\_cache\_size**';        \#查寻
    * mysql&gt; SET  **thread\_cache\_size** = 200000;           \#设置缓存线程个数是 200000;
* 调整 **innodb\_lock\_wait\_timeout**    控制innodb 事务等待行锁的时间, 对于快速处理的sql语句.可以将行锁的等待超时 时间调小, 以避免事务长时间挂起,  对于后台运行的批处理操作, 可以将行锁等待超时  时间调大, 以避免发生大的回滚操作.
  * **\(如果需要千万级并发, 那么这个值保持在 30  \(单位是毫秒\), 但是 当我们进行服务器维护的时候, 这个值要设置的大一些 到 80 左右 \)**
    * mysql&gt;  SHOW variables like '**innodb\_lock\_wait\_timeout**';        \#查寻
    * mysql&gt; SET  **innodb\_lock\_wait\_timeout** =30;           \#设置为30毫秒

## MYSQL应用优化

#### 主要是针对 应用程序访问 MYSQL 服务 的时候,一些优化和处理.

### 访问数据库采用连接池

线程池内存放一些初始化好的线程,留给连接过来的客户端使用.

就算客户端中断了连接之后, 这个用户占用过的线程还是会回到线程池,  如果线程池已经满了,或者已经达到mysql设置的最大数量, 那么才会销毁这个线程.

可以增加用户在访问服务器的速度, 因为有线程直接来进行使用,而不是去创建,  这样很节省时间.

调整 **thread\_cache\_size**   的大小就可以达到增加线程池内 线程个数的目的.

* mysql&gt;  SHOW variables like '**thread\_cache\_size**';        \#查寻
* mysql&gt; SET  **thread\_cache\_size** = 200000;           \#设置缓存线程个数是 200000;



### 采用缓存机制  来减少对MYSQL的访问

**当表的结构和数据没有发生任何改变的时候,这个缓存才是有效的,  一旦出现任何更改和插入, 那么缓存直接失效.**

#### 建议将缓存空间设置为0   , 这样可以保证数据的实时有效性和安全性, 虽然这样会增加服务器的压力,但是可以保证数据安全.

* 避免对同一数据做重复检索
  * 避免查询同一张表的时候, 检索的列不同而造成的重复检索
* 使用查询缓存
  * 当多个客户端 连续发送两条完全相同的查询语句的时候, 那么服务器将会执行第一条sql语句,并且返回, 然后发现第二个sql语句和上句相同,  就会把上次查询的结果直接,而不会执行查询.\(缓存机制\)
* 缓存参数的配置
  * 打开缓存开关, 让缓存生效
    * mysql&gt;  SHOW GLOBAL variables like 'query\_cache\_type';           \#显示缓存配置, on 开 off关
    * mysql&gt;  SET GLOBAL  query\_cache\_type = ON;        \#打开缓存
  * 设置缓存空间大小,  
    * mysql&gt; SHOW  GLOBAL variables  like 'query\_cache\_size';
    * mysql&gt; SET GLOBAL query\_cache\_size =  0;     \#  必须是 1024 的整数倍  \(单位是字节\)
  * 分配内存块时的最小单位大小
    * mysql&gt;  SHOW GLOBAL  variables like 'query\_cache\_min\_res\_unit';      \#显示大小
    * mysql&gt;   SET   GLOBAL  query\_cache\_min\_res\_unit =  4096;    \#必须是 1024的整数倍 \(单位是字节\) 
  * mysql 能够缓存的最大结果的 数量, 如果超出,则增加 qcache\_not\_cached的值,并删除查询结果.
    * mysql&gt;  SHOW GLOBAL variables like 'query\_cache\_limit';       \#显示数量
    * mysql&gt;   SET   GLOBAL  query\_cache\_limit =  4096;    \#必须是 1024的整数倍 \(单位是字节\) 
  * 如果这个表被锁住, 是否仍然从缓存中返回数据,  默认是oFF,  表示仍然可以返回.
    * mysql&gt;   SHOW GLOBAL  variables like 'query\_cache\_wlock\_invalidate';   
    * mysql&gt;   SET   GLOBAL  query\_cache\_wlock\_invalidate = ON;



### 使用负载均衡

#### 一个主MYSQL 服务器\(master\)   与多个从属 MYSQL 服务器\(Slave\)  建立复制\(replication\)  连接.

主服务器与从服务器实现一定程度上的数据同步,  多个从属服务器存储相同的数据副本,  实现数据冗余, 提供容错功能,  部署开发应用系统时, 对数据库操作代码进行优化.  将写操作\( 如 UPDATE, INSERT\) 定向到 主服务器, 把大量的查询操作 \(SELECT \) 定位到从属服务器,  实现集群的负载均衡功能.

#### 如果主服务器发生故障, 从属服务器将转换角色 成为主服务器,  应使系统为终端用户提供不间断的网络服务;  当主服务器恢复启动后,  将目前临时的主服务器 将其转换为从属服务器,  存储数据库副本,  继续对终端用户提供数据查询检索服务.



## MYSQL监控

监控主要用来检查MYSQL服务器是否运行正常, 是否出现错误. 可以当作一种排查错误的手段.

常见的MYSQL监控方式有三种

* 自己编写程序或者脚本控制
* 采用商业解决方案监控
* 开源软件解决方案监控

### 自己编写程序或者脚本控制 监控

#### 脚本命令:

```bash
脚本控制, 一般都是 bash 命令
# 监控 mysql 是否提供正常的服务;  命令:
bash $ mysqladmin -u 用户名 -p 密码 -h localhost ping 
            # 一般监控都是使用 mysql的  root账户来进行
            # -h 表示返回给哪个谁(IP地址), localhost 表示返回本地
            #执行成功后会返回   mysqld is alive     这段表示运行正常.
    
    
# 获取mysql 当前的几个状态值
bash $ mysqladmin  -u root -p 密码  -h localhost status 
        #一般返回如下状态值
           # Uptime: 55560       /* 表示mysql 的运行时间 (秒) */
           # Threads: 2          /* 活动线程数（客户端）,一个客户端一个线程,每连接一个客户端,就增加一个线程*/
           # Questions: 276      /* 自服务器启动以来客户端 执行查询语句的个数 */
           # Slow queries: 0     /* 慢查询的次数总计 */
           # Opens: 99           /* 服务器打开过的 数据库中表 的总次数 */
           # Flush tables: 1     /* 服务器 执行过的 流溢命令的数量  */
           # Open tables: 93     /* 当前打开的表的数量 */
           # Queries per second avg: 0.004    /* 查询语句执行的平均时间 */


#获取数据库当前的连接信息 , 就是当前有 哪些个用户连接到了数据库. 并且列出详细信息.
bash $ mysqladmin  -u root -p 密码  -h localhost processlist


#获取当前 数据库 连接 数
bash $ mysql -uroot -p 密码 -BNe "SELECT host,COUNT(host) FROM processlist group by host;"  information_schema
          # 一般返回如下信息,  比如现在有2 个用户连接到了数据库
            # 192.168.1.104:54040	1       # 表示这个IP地址有一个用户在连接,端口是54040
            # localhost	1                   # 这是本地刚刚执行命令出现的,可以忽略,因为没端口号


#获取当前 休眠 的客户端 (也就是有一段时间没有执行过sql 语句的客户端)
bash $ mysql -uroot -p 密码 -BNe "SELECT host,COUNT(host) FROM processlist WHERE command='Sleep' group by host;"  information_schema


#检查,修复,分析, 优化 MysqlServer 中相关的表, (命令很消耗时间)
bash $ mysqlcheck -uroot -p 密码  --all-databases
            #会返回 " 库名.表名   OK " 的字样
            # 如果出现其他字样, 就代表 自动修复 出现了问题 ,需要手动修复.
           
```



### Mysql 客户端语句监控

```sql
# 查询  是否建立了太多的临时表,  过多的临时表会占用很大的内存空间, 服务器有可能会将它挪到硬盘上.
mysql> SHOW status like 'Created_tmp%';
        #会返回如下信息
+-------------------------+-------+
| Variable_name           | Value |
+-------------------------+-------+
| Created_tmp_disk_tables | 0     |
| Created_tmp_files       | 6     |
| Created_tmp_tables      | 7     |        #这个表示已经有7个临时表
+-------------------------+-------+



# 锁状态: 锁定状态包括表所和行锁 两种. 可以通过系统状态变量(status) 来获得锁定总次数. 锁定造成其他
#        线程等待的次数,以及锁定等待时间信息.
mysql>  SHOW status  like '%lock%';
          #会返回如下部分信息, 截取出来较为重要的内容
+---------------------------------------------+-------+
| Variable_name                               | Value |
+---------------------------------------------+-------+
| Innodb_row_lock_waits                       | 0     |   #Innodb引擎, 因为行锁,而阻塞的 语句个数
+---------------------------------------------+-------+


# Innodb_log_waits  状态变量直接反映出 Innodb Log Buffer 空间不足造成的等待次数
mysql> SHOW status like 'Innodb_log_waits';
           #返回值的信息
+------------------+-------+
| Variable_name    | Value |
+------------------+-------+
| Innodb_log_waits | 0     |    # 为0则代表正常, 非0 则表示内存空间不足 造成的 sql语句阻塞.
+------------------+-------+


```



## Mysql 定时维护  \(定时器\)

#### Mysql 的定时器 : 指的是在某个时间段去执行同样的代码, 比如闹钟, 每到指定的时间闹铃就会响.

#### Mysql 定时器是从 5.1 版本开始才支持 event \(定时器\) 的.

```sql
# 查看mysql版本 的三种方法
mysql> SHOW variables like '%version%';
mysql> SELECT version();
mysql> \s 
```

### 查看当前定时器是否处于开启状态 \(默认关闭\)

```sql
mysql>  SHOW variables like 'event_scheduler';
```

### 开启定时器功能

```sql
# 开启定时器功能
mysql>  SET GLOBAL event_scheduler = 1;
```

### 定时器语法

```sql
#显示目前存在的定时器 (固定语法,可能需要root权限)
mysql> SELECT  * FROM  mysql.event \G

# 首先删除要创建可能会与自己将要定义的定时器名字相同的定时器.
mysql> DROP  event if exists 定时器名;


#先创建一个存储过程  来设置定时器要做的事情.
mysql delimiter $$;                #修改分号的属性
mysql> CREATE procedure 存储过程名()
        begin
        INSERT INTO  表(列)  VALUES (值);   #我想通过存储过程来插入某些值.
        end
        $$;
mysq> delimiter ;        #恢复分号属性


#创建定时器 (虽然创建了,但是默认是不开启的)
mysql> CREATE event 定时器名              #下面每段代码都有意义, 可以合理删除和添加
        ON schedule every 1 second       #设置定时器每 1 秒钟就执行一次, minute分钟,hour小时,day天,month月,quarter季度,year年
        STARTS '2012-09-24 00:00:00'     #设置从 2012年9月24号0点 开始执行. 如果这行不写,那么就需要手动执行
        ON completion preserve disable   #当定时器被停止,那么这个定时器也不会直接消失
        do call 存储过程名();              #do后面是 定时器要做的事情, call 调用存储过程.
                                         #do后面还可以是 函数,sql语句, 等等
        
        
#开启定时器, 一旦执行,那么定时器就会开始执行了.
mysql> ALTER event 定时器名  
        ON completion preserve enable;    # 开启定时器

#关闭定时器
mysql> ALTER event 定时器名  
        ON completion preserve disable;;    # 开启定时器      
        

#删除定时器
mysql> DROP event if exists 定时器名;
```



## 备份还原

### 三种备份方式

* 通过使用 bash\# mysqldump 命令备份
  * 命令会将数据库中的数据备份成一个文本文件. 表的结构和表中的数据存储在生成的文本文件中.
* 直接复制整个数据库目录
  * 
* 使用 mysqlhotcopy 工具快速备份 \(主要针对 Linux\)

#### mysqldump 命令备份方式

**原理:**  它会先查出需要备份的表的结构, 在再文本文件中生成一个 CREATE 语句, 然后将表中的所有记录转换成一条 INSERT 语句. 然后通过这些语句,就能够创建表并插入数据. \(说白了就是变成了一个脚本\)

```bash
"备份 指定库下的 某些指定的表"
bash # mysqldump -u 用户名  -p  dbname table1 table2 ...... tableN > name.sql   

"备份一个数据库, 不加表名就行"
bash # mysqldump -u 用户名  -p  dbname  > name.sql   

"备份多个数据库"
bash # mysqldump -u 用户名  -p  --databaes dbname1 dbname2 .... dbnameN > name.sql

"备份所有的数据库"
bash # mysqldump -u 用户名  -p  --all-databases > name.sql

        #首先需要 用户 有对表和数据库有相应的 读写权限才可以执行该命令
        # 解释:  dbname        需要备份的库 的名称
        #        --databaes   这个参数表示后面跟的都是数据库名称
        #        --all-databases    表示选择所有的数据库
        #        table1       该库下需要备份的的表名,表于表之间不需要逗号
        #        >            指向的是备份到哪个文件,也可以写路径
        #        name.sql     指的是备份到name.sql 这个文件内.
        
#执行该命令后, 只会提示你输入密码, 除此之外没有任何提示, 一旦出现任何提示,就代表出现了问题,会造成备份失败

# 如果使用该命令生成的备份去别的库进行还原的话,需要注意, 绝对不能出现和备份中同名的 表和库,
#     否则会直接被删除!!!
#  还原的时候, 会进行锁表操作.   还原结束后才会解锁
```

### 还原

```bash
"还原某一个单独的库, 但是需要保证这库存在,而且和备份文件的库名相同"
bash $ mysql -u root -p bank < name.sql
                "bank是一个库名,它存在数据库中,也存在备份文件中"
                
"还原整个库,将库内所有的内容全部还原到 备份文件 备份的日期"
bash $ mysql -u root -p < name.sql

"还原某个数据库中的某张固定的表"
"还是去仔细去备份中寻找被删除表的 建表语句和insert语句, 然后拷贝出来做成脚本来进行恢复 较为方便"

```



























