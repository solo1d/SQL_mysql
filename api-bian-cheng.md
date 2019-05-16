# API编程

## 首先要做的就是登入和退出mysql

#### 句柄: 句柄是一种特殊的智能指针 。当一个应用程序要引用其他系统（如数据库、操作系统）所管理的内存块或对象时，就要使用句柄。

```c
#初始化
MYSQL *mysql_init(MYSQL *mysql) 
   // 参数:  给NULL  或者其他的,无所谓的值
   // 返回值:  成功返回 MYSQL* 指针 ,失败返回NULL
   // 主要用来给MYSQL指针初始化.

#连接到数据库
MYSQL *mysql_real_connect(MYSQL *mysql, const char *host, const char *user,
        const char *passwd, const char *db, unsigned int port, const char *unix_socket,
        unsigned long client_flag)   
   // 参数: mysql  是mysql_init()初始化得来的,
   //      host 是服务器 IP , user 是数据库用户名,
   //      passwd  密码,  db 需要登陆的数据库名称,  port 服务器端口(默认给0,就是3306) ,
   //      unix_socket 本地套接字(默认给NULL),  
   //      client_flag 客户端标志(一般填0)
   //返回值: 如果连接成功返回一个MYSQL 连接句柄, 失败返回NULL,  
   //       对于成功的连接,返回值与第一个参数的值相同.
        
#关闭连接
void mysql_close(MYSQL *mysql)
   // 参数 : 就是musql_init() 和 mysql_real_connect() 两个值的返回值. 
   //        也就是说这个函数应该调用两次,来分别释放两个值.
   //它会关闭句柄和连接
```

