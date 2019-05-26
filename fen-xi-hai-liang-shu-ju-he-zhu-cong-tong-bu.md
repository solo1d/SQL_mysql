---
description: 感觉比慢查询好用
---

# 分析海量数据和主从同步

## 分析海量数据

### 模拟海量数据   使用存储过程/存储函数

```sql
# 通过存储函数 插入海量数据
# 准备工作 ,  创建两个表  ,创建两个存储函数 和一个存储过程  , 用来批量插入数据

mysql>  CREATE  database testdata;   
mysql>  use  testdata;

mysql> create table dept(                         #创建一个部门表
        dno int(5) PRIMARY KEY DEFAULT 0, 
        dname varchar(20) not null default '', 
        loc varchar(30) default '' 
        )engine innodb default charset utf8;
        
mysql>  CREATE  table  emp (                       #员工表
        eid int(5) PRIMARY key,
        ename varchar(20) not null default '',
        job varchar(20) not null default '',
        deptno  int (5) not null default 0
        )ENGINE Innodb default charset=utf8;
        
mysql>  SET global  log_bin_trust_function_creators=1;      #开启创建函数
mysql>  delimiter  $$;                              #创建存储函数之前,修改分号含义
mysql>  CREATE  function randstring(n int) returns varchar(255)    #创建随机数函数,需要一个int参数,返回字符串
        begin
           declare  all_str varchar(100) default 'qwertyuiopasdfghjklzxcvbnmQWERTYUIOPASDFGHJKLZXCVBNM';
           declare  return_str  varchar(255) default '';   # 上面是所有字符串,用来进行随机抽取的, 这个存放随机字符串的组合
           declare  i  int default 0;
           while i<n                # 循环, 当i等于传入的n参数时, 循环停止.
           do
               set return_str = concat( return_str, substring( all_str, FLOOR(1+rand()*52) ,1));
               set i = i+1;        # 上面拼接字符串,
           end while;
           return return_str;
        end
        $$;
        
mysql>  CREATE function ran_num()  returns int(5)     #产生随机整数的  存储函数
        begin
           declare i int default 0;
           set i = FLOOR(rand() *100);   #产生 0 到 99 中的随机数,  rand() 只返回0~0.99
           return i;
        end
        $$;

# 通过存储过程插入海量数据到 emp 表中.
#eid_start 员工编号从哪里累加,  data_times 累加次数, ( 100 ,  2000 ) 从100 员工编号开始,插入2000条数据.
mysql> CREATE procedure insert_emp(in eid_start int(10), in data_times int(10))
        begin
           declare i int default 0;
           set autocommit = 0;        #关闭自动提交
           repeat  
               INSERT INTO emp values(eid_start + i, randstring(5), 'other',ran_num());
               set i=i+1;
             until i = data_times        #这里语法上 没有分号
          end repeat;
           set autocommit = 1;        #开启自动提交
        end
        $$;

                        
# 通过存储过程插入海量数据到  dept表中, 参数设置和上面一样
mysql> CREATE procedure insert_dept (in dno_start int(10), in data_times int(10))
        begin
           declare i int default 0;
           set autocommit = 0;        #关闭自动提交
            repeat  
               INSERT INTO dept values(dno_start + i, randstring(6),randstring(8));
               set i=i+1;
             until i = data_times        #这里语法上 没有分号
          end repeat;
           set autocommit = 1;        #开启自动提交
        end
        $$;

mysql> delimiter ;                #恢复分号含义



********************************************************************
# 准备过程结束,  插入数据
mysql> set autocommit = 1;        #开启自动提交

mysql>  call insert_emp (1000,800000);      #从编号1000开始插入 八十万条数据
mysql>  call insert_dept (10,30);           #从编号 10 开始,插入30条数据



********************************************************************
# 分析海量数据   profiles : 会记录所有 profiling打开之后的所有 SQL 语句

# 首先分析是默认关闭的,  需要手动开启
mysql>  show variables like '%profiling%';        # 查询状态
mysql>  SET profiling  =  1;                      # 临时开启


# 显示记录的sql语句和执行时间(不精确)
mysql>  SELECT count(1) FROM emp;      # profiles 只会记录开启之后的查询语句,所以先查询一次
mysql>  show profiles;                 #显示记录的sql语句和执行时间(不精确)


# 精确分析,  sql 诊断, 通过上面 show profiles; 中id 返回值来进行分析
mysql> show profile all for query   上一步查询的Query_ID;    # all 表示查看全部
mysql> show profile cpu,block  io for query 上一步查询的Query_ID;     #查看 cpu ,block  的值

# 全局查询日志 : 记录开启之后的全部 sql 语句, (仅可用于开发调试,最终项目上线必须关闭), 默认关闭
mysql>  SHOW variables  like '%general_log%';    #查询目前查询日志功能是否开启
mysql>  SET GLOBAL general_log = 1;              #开启全局日志功能
mysql>  SET GLOBAL log_output = 'table';         #让记录输出到表中, 而不是文件中
                                                 #开启后,会记录所有的SQL,  记录到 mysql.general_log 表中.
        #让他输出到文件,而不是表中.
       mysql> SET GLOBAL general_log = 1;        #需要二次执行,让他开启
       mysql> SET GLOBAL general_log_file = '/var/lib/mysql/localhost-general.log';   #让记录输出到文件
       mysql> SET GLOBAL log_output = 'file';    #输出到文件
       
mysql> select * FROM mysql.general_log ;         #查询 记录的全局查询日志















```



