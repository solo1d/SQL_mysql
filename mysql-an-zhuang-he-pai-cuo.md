---
description: 'Linux, 服务名是 mysqld'
---

# Mysql服务器 安装和排错

## 首先清除系统残留.

### 删除系统自带mysql：

     sudo apt-get autoremove --purge mysql-server

    sudo apt-get remove mysqyl-server

    sudo apt-get autoremove mysql-server

    sudo apt-get remove mysql-common //重要

### 清除残留数据

    dpkg -l \|grep ^rc\|awk '{print $2}' \|sudo xargs dpkg -P

### 安装Mysql软件

    sudo apt-get install mysql-server sudo apt-get install mysql-client 

      sudo apt-get nstall mysql-server python-mysqldb //安装python接口的mysql

### 安装完成后使用下面命令进行登陆和设定root密码\(第一次进入后会提示 Enter password: \) 

$ sudo mysql -u root -p

### 设置root密码

```sql
use mysql;
update user set plugin='mysql_native_password' where user='root';  --这行不要修改任何字符
UPDATE user SET password=PASSWORD('你自己的密码') WHERE user='root'; --这行把密码设定了
flush privileges; 
exit;
```

### 使用以下命令来进行mysql 的服务的重启,停止,运行.

      $ sudo /etc/init.d/mysql status/start/stop/restart

## 开启mysql远程访问

### 修改mysql配置，允许远程登录

    $ sudo vim /etc/mysql/mariadb.conf.d/50-server.cnf  //打开mysql配置文件

   _**在配置文件中寻找bind-address这行 ,然后注释掉,然后重启mysql服务程序**_

           $ sudo /etc/init.d/mysql restart

### 设置账号可以远程登录

        $ mysql -u root -p    \#再次登陆进入数据库

```sql
use mysql;
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'root账号密码' WITH GRANT OPTION;
flush privileges;
-- 上面的root帐号密码,是安装mysql设定的,而不是真正的Linux 用户root密码.
```

#### 设置完成后重启mysql服务程序

         $sudo /etc/init.d/mysql restart

#### 最好设置完成后重启以下服务器

        $sudo shutdown -r now 



### 安装结束, 借鉴参考以下内容[https://blog.csdn.net/u011270542/article/details/80023873](https://blog.csdn.net/u011270542/article/details/80023873)



