# 字符乱码解决方案

## 字符乱码解决操作

服务器编码长度   &gt;=  连接器编码长度  &gt;=  客户端编码长度

UTF8 &gt;  GBK &gt;  ASCII  

```sql
/*---------------------------------------------------
在访问表内数据的时候出现乱码的解决命令,其实是因为终端和mysql 的字符集不同导致的
--设置连接字符编码, 就是修改服务器传送过来数据的编码, (看自己本机的编码再来设定)
*/----------------
# 客户端有GBK和UTF8,   连接器支持很多编码 默认是UTF8 , 数据库服务器 默认也是UTF8

mysql> SET CHARACTER_SET_CLIENT = GBK;   
        # 这步是告诉 服务器, 我客户端的发送数据的编码是 GBK 的

mysql> SET CHARACTER_SET_CONNECTION = UTF8;
        # 再告诉连接器, 将得到的数据编码转换成 UTF8 然后送给服务器. (服务器默认是UTF8)
        
mysql> SET  CHARACTER_SET_RESULTS = GBK;
        # 再告诉连接器, 返回数据的编码是 GBK 的.

-------
mysql> SET NAMES utf8;
        # 这句话是上面三句话的集合, 将客户端,连接器,连接器返回客户端, 这三个编码都改成UTF8
```



## 范例1 

### 服务器 GBK,  客户端 UTF8, 设置连接器

```sql
mysql> SET CHARACTER_SET_CLIENT = UTF8;      # 告诉连接器  客户端是发送的编码是 UTF8
mysql> SET CHARACTER_SET_CONNECTION = GBK;   # 告诉连接器  服务器是GBK
mysql> SET CHARACTER_SET_RESULTS = UTF8;     # 告诉连接器  返回给客户端的编码是 UTF8
```











