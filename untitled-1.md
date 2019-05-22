# SQL优化

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

## 索引优化































