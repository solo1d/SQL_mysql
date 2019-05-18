# 全局变量,会话变量,局部变量

## 全局变量和会话变量

```sql
# 这两个变量主要记录客户端的设置的 会话变量.(差不多可以看成是环境变量)
mysql> SHOW session variables;          
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

## 局部变量

局部变量定义在存储过程内.  


```sql
#1. 依旧先选择一个库  和 改变分隔符
    mysql>  use 库;
    mysql> delimiter  $$;      #改变分隔符

#2. 创建一个存储过程
    mysql>  CREATE PROCEDURE
            p_vartest ()
            begin
            




```













