# 用户创建和删除以及权限

## 用户创建和删除以及权限操作

### 一. 创建用户

#### 命令:

```sql
mysql> CREATE USER 'username'@'host' IDENTIFIED BY 'password';      
                        #这样的用户,不允许分发自己的权限
                        
mysql> CREATE USER 'username'@'host' IDENTIFIED BY 'password' 
            WITH GRANT OPTION;   
                        # 这个用户可以分发自己 已有的权限.
                        
mysql> CREATE USER 'username'@'host' IDENTIFIED BY 'password' 
            WITH max_queries_per_hour int1   max_connections int2 ;   
                        # 限制这个用户在每小时的查询次数不能超过 int1 
                        # 最大登陆并发连接不能超过 int2. 
```

#### 说明：

* username：你将创建的用户名
* host：指定该用户在哪个主机上可以登陆，如果是本地用户可用localhost，如果想让该用户可以**从任意远程主机登陆**，可以使用通配符`%`
* password：该用户的登陆密码，密码可以为空，如果为空则该用户可以不需要密码登陆服务器

#### 例子：

```sql
mysq> CREATE USER 'dog'@'localhost' IDENTIFIED BY '123456';
mysq> CREATE USER 'pig'@'192.168.1.101' IDENTIFIED BY '123456';
mysq> CREATE USER 'pig'@'192.168.1.%' IDENTIFIED BY '123456';      
                                # 可以在192.168.1 这个网段下的任意地址进行登陆
mysq> CREATE USER 'pig'@'%' IDENTIFIED  BY '';
mysq> CREATE USER 'pig'@'%';
```

### 二. 权限设置和授权:

#### 命令:

```sql
mysq> GRANT privileges ON databasename.tablename TO 'username'@'host';
```

#### 说明:

* privileges：用户的操作权限，如`SELECT`，`INSERT`，`UPDATE`等，如果要授予所有的权限则使用`ALL`
* databasename：数据库名
* tablename：表名，如果要授予该用户对所有数据库和表的相应操作权限则可用`*`表示，如`*.*  就表示他有所有库和表的操作权限`

#### 例子:

```sql
mysq> GRANT SELECT, INSERT ON test.user TO 'pig'@'%';
mysq> GRANT ALL ON *.* TO 'pig'@'%';
```

#### 注意:

如果不添加 `WITH GRANT OPTION` 关键字就不允许把别人给自己的权限再分发下去.

用以上命令授权的用户不能给其它用户授权，如果想让该用户可以授权，用以下命令:

```sql
mysq> GRANT privileges ON databasename.tablename TO 'username'@'host' WITH GRANT OPTION;
                # 这样的话这个用户 就有权限给他人分配 他已经有的权限了
```

### 三.设置与更改用户密码

#### 命令:

```sql
mysq> SET PASSWORD FOR 'username'@'host' = PASSWORD('newpassword');
```

如果是当前登陆用户用:

```sql
mysq> SET PASSWORD = PASSWORD("newpassword");
```

#### 例子:

```sql
mysq> SET PASSWORD FOR 'pig'@'%' = PASSWORD("123456");    #用户pig, 登陆地点所有, 密码修改为123456
```

### 四. 撤销用户权限

#### 命令:

```sql
mysq> REVOKE privilege ON databasename.tablename FROM 'username'@'host';
```

#### 说明:

privilege, databasename, tablename：同授权部分

#### 例子:

```sql
mysq> REVOKE SELECT ON *.* FROM 'pig'@'%';
```

#### 注意:

假如你在给用户`'pig'@'%'`授权的时候是这样的（或类似的）：`GRANT SELECT ON test.user TO 'pig'@'%'`，则在使用`REVOKE SELECT ON *.* FROM 'pig'@'%';`命令并不能撤销该用户对test数据库中user表的`SELECT` 操作。相反，如果授权使用的是`GRANT SELECT ON *.* TO 'pig'@'%';`则`REVOKE SELECT ON test.user FROM 'pig'@'%';`命令也不能撤销该用户对test数据库中user表的`Select`权限。

具体信息可以用命令`SHOW GRANTS FOR 'pig'@'%';` 查看。

### 五. 查看用户的权限

```sql
mysql> SHOW GRANTS FOR '用户名'@'host';  #host 需要严格匹配
```

### 六.对账号权限的资源设置

#### 账号资源的限制：

* max\_queries\_per\_hour 该参数设置一个用户在一小时内可以执行查询的次数（基本包含所有语句） 
* max\_updates\_per\_hour 该参数设置一个用户在一小时内可以执行修改的次数（仅包含修改数据库或表的语句）
* max\_connections\_per\_hour 该参数设置一个用户在一小时内可以连接MySQL的时间
* max\_user\_connections 该参数作用是设置所有用户在同一时间连接MySQL实例的最大连接数限制。但这个参数无法对每个用户区别对待。

#### 当某个用户的max\_user\_connections非0时，则忽略全局系统参数对应的配置，反之则使用全局参数。

#### 创建帐号时设定资源设置,  也就是该账号的最大查询次数,最多登陆并发连接个数

```sql
mysql> CREATE USER 'username'@'host' IDENTIFIED BY 'password' 
            WITH max_queries_per_hour int1   max_connections int2 ;   
                        # 限制这个用户在每小时的查询次数不能超过 int1 
                        # 最大登陆并发连接不能超过 int2. 
```

#### 修改已有账号权限的资源设置

```sql
mysq> ALTER user '用户名'@'host' IDENTIFIED BY  '密码'
            WITH max_queries_per_hour int1   max_connections int2 ;   
                        # 限制这个用户在每小时的查询次数不能超过 int1 
                        # 最大登陆并发连接不能超过 int2. 
```

#### 查看用户目前的资源设置

```sql
mysql> SELECT user.max_queries_per_hour,user.max_connections FROM user WHERE user = '用户名';
        #上面仅仅是查询一部分.  其他的还需要看上面的参数设定,一般都是 max_ 开头
```

### 七.删除用户

#### 命令:

```sql
mysq> DROP USER 'username'@'host';  #后面的 host 需要严格匹配才可以删除
```

