# 字符乱码解决方案

## 字符乱码解决操作

```sql
/*---------------------------------------------------
在访问表内数据的时候出现乱码的解决命令,其实是因为终端和mysql 的字符集不同导致的
--设置连接字符编码, 就是修改服务器传送过来数据的编码, (看自己本机的编码再来设定)
*/----------------
mysql> SET NAMES utf8;
```
