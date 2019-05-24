# 定时器

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



