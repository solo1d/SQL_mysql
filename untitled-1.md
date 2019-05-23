# MYSQL优化

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
* 调整 **thread\_cache\_size**  ,加快连接数据库的速度, mysql会缓存一定数量的客户端服务线程 以备重用, 通过参数  thread\_cache\_size  可控制 mysql 缓存客户端服务线程的数量.  ****
  * **\(如果需要千万级并发, 那么这值也不能少于几千 或几万 \)**
* 调整 **innodb\_lock\_wait\_timeout**    控制innodb 事务等待行锁的时间, 对于快速处理的sql语句.可以将行锁的等待超时 时间调小, 以避免事务长时间挂起,  对于后台运行的批处理操作, 可以将行锁等待超时  时间调大, 以避免发生大的回滚操作.
  * **\(如果需要千万级并发, 那么这个值保持在 30  \(单位是毫秒\), 但是 当我们进行服务器维护的时候, 这个值要设置的大一些 到 80 左右 \)**

## MYSQL应用优化

#### 主要是针对 应用程序访问 MYSQL 服务 的时候,一些优化和处理.

### 访问数据库采用连接池





### 采用缓存减少对MYSQL的访问





### 使用负载均衡



























