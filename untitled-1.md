---
description: '表的分析 检查 优化,  分区,  优化MsqlServer ,  应用优化,  权限管理,   监控,  定时维护,   备份和还原.'
---

# MYSQL优化

## status 是服务器状态变量

## variables 是服务器系统变量

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
# 查看mysql版本
mysql> SHOW variables like '%version%';
```































