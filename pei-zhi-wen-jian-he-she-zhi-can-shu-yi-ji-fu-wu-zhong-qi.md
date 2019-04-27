# 配置文件和设置参数以及服务重启

## 启动/重启/停止 命令

$ sudo  /etc/init.d/mysql     start/restart/stop

## 配置文件目录

/etc/mysql/mariadb.conf.d/50-server.cnf       \#\# 这个是总体配置文件,可以开启 '严格模式'

```bash
# 开启严格模式 ,  首先定位到  [mysqld]   这一行 , 然后修改成如下样子.

[mysqld]
sql_mode="STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION"
# 上面这行代表开启了严格模式, 如果删除这行就代表关闭了严格模式
```

### 目前所使用的版本

mysql  Ver 15.1 Distrib 10.1.37-MariaDB, for debian-linux-gnueabihf \(armv8l\) using readline 5.2

