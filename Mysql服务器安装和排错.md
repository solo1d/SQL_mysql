---
description: 'Linux, 服务名是 mysqld'
---

# Mysql服务器 安装和排错

真正在后台提供服务程序的进程是 mysqld

## 首先清除系统残留.

### 删除系统自带mysql：

     sudo apt-get autoremove --purge mysql-server
    
    sudo apt-get remove mysql-server
    
    sudo apt-get autoremove mysql-server
    
    sudo apt-get remove mysql-common //重要

### 清除残留数据

    dpkg -l \|grep ^rc\|awk '{print $2}' \|sudo xargs dpkg -P

### 安装Mysql软件

    sudo  sid-used apt install mysql-server  mysql-client   -y
    
      sudo apt-get nstall mysql-server python-mysqldb //安装python接口的mysql

### 安装完成后使用下面命令进行登陆和设定root密码\(第一次进入后会提示 Enter password: \) 

```shell
$ sudo mysql -u root
```



### 设置root密码

```sql
use mysql;
update user set host='%' where user ='root';  #更新域属性，'%'表示允许外部访问
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY "新密码";
flush privileges;
```

## 设置Mysql 数据库编码, 统一字符集

```sql
mysql> show global variables like '%char%';         #查看编码,然后按需求进行设置
```



### 使用以下命令来进行mysql 的服务的重启,停止,运行.

```bash
$ sudo systemctl restart  mysql.service 
$ sudo systemctl start  mysql.service 
$ sudo systemctl stop  mysql.service 
```

## Mysql配置文件

/etc/mysql/mariadb.conf.d/50-server.cnf



## 开启mysql远程访问

### 修改mysql配置，允许远程登录

```bash
# 打开mysql配置文件
$sudo vim mysql.conf.d/mysqld.cnf  

# 在配置文件中寻找 下面这两行 ,然后注释掉
bind-address           = 127.0.0.1
mysqlx-bind-address    = 127.0.0.1

#重启mysql服务程序
$ sudo systemctl restart mysql
```

### 设置账号可以远程登录

```bash
$ mysql -u root -p    \#再次登陆进入数据库
```

```sql
use mysql;
update user set host='%' where user ='root';  #更新域属性，'%'表示允许外部访问
FLUSH PRIVILEGES;
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%'WITH GRANT OPTION;
flush privileges;
exit;
#然后进行下面的步骤,完成后就可以使用其他客户端直接连接了
```

#### 设置完成后重启mysql服务程序

```bash
$sudo systemctl restart  mysql.service 
```

#### 最好设置完成后重启以下服务器

```bash
$sudo shutdown -r now 
```

