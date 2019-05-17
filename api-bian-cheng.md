# API编程

## 点击进入 [官方参考手册](https://dev.mysql.com/doc/refman/8.0/en/c-api-functions.html)

## 需要的头文件和库,以及编译指令

需要的头文件有 mysql.h   ,静态库有 libmysqlclient.a   \(去服务器上面拿取\), 将这两个文件和原文件放在一起

编译指令: gcc 源代码.c   -i 头文件绝对路径   -L动/静 态库绝对路径   -l动/静态库名字

例子:    gcc  my.c  -i /Users/ns/mysql\_c   -L/Users/ns/mysql\_c    -l mysqlclient 

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

### 执行语句

```c
# 执行SQL语句函数 : 因为只能返回0 和非0, 所以适合插入操作 
int mysql_query(MYSQL *mysql, const char *stmt_str)
   //参数:  mysql 连接句柄,
   //      stmt_str   SQL语句, 不能包含 分号 ; 和 \G 参数,而且不可以出现二进制数据的语句, 
   //                只能执行一条sql语句 (服务器如果设置了多条执行,那么可以用分号 ; 连接多个sql语句)
   //返回值:  查询成功返回0, 失败或错误返回非0值.
```

### 查询操作

#### 这个操作分为三步,  执行查询语句,取得结果集, 处理结果集

```c
#实现查询之前,需要先执行执行语句  mysql_query() ,得到查询的结果集  
    mysql_query(mysql, 查询语句  SELECT .... );   # 确定它的返回值是0 .是正确的
    
#下面是拿到结果集操作
MYSQL_RES *mysql_store_result(MYSQL *mysql)
    //参数: 以及和数据库建立连接的句柄.
    //返回值:  MYSQL_RES 结构指针, 这个函数主要是为了拿到这个结果集, 失败返回NULL
    // 如果返回值不是NULL, 那么就可以调用 mysql_num_rows() 来找出结果集中的行数
    //  还可以调用 mysql_fetch_row()
    // 当结果集使用完成后,必须使用 mysql_free_result() 释放MYSQL_RES 结果集.

# 获取行
MYSQL_ROW mysql_fetch_row(MYSQL_RES *result)
    //参数:  mysql_store_result() 返回的结构体 MYSQL_RES
    //返回值: typedef char **MYSQL_ROW;  就是一个二级char指针,指向一个二级数组.
    //       如果数据多,需要循环取出. 在栈上,不需要释放.

#释放结果集操作  ---- 在下面


----------------------------------------------------
# MYSQL_RES 结构体
typedef struct MYSQL_RES {
  my_ulonglong row_count;    /* 查询结果的行的 个数*/
  MYSQL_FIELD *fields;       /* */
  struct MYSQL_DATA *data;   /* */
  MYSQL_ROWS *data_cursor;   /* */
  unsigned long *lengths;    /* 当前行的列长度 */
  MYSQL *handle;             /* 对于无缓冲的读取 */
  const struct MYSQL_METHODS *methods;    /* */
  MYSQL_ROW row;             /* 没有缓冲区的读取 */
  MYSQL_ROW current_row;     /* 缓冲区到当前行 */
  struct MEM_ROOT *field_alloc;  /* */
  unsigned int field_count, current_field;   /* */
  bool eof;              /*  */  
  bool unbuffered_fetch_cancelled;    /* */
  enum enum_resultset_metadata metadata;     /* */
  void *extension;      /* */
} MYSQL_RES;

----------------------------------------------------


```

### 释放结果集

```c
void mysql_free_result(MYSQL_RES *result);

mysql_free_result()释放分配给组由结果的内存 
    mysql_store_result()， mysql_use_result()， mysql_list_dbs()，等等。
完成结果集后，必须通过调用释放它使用的内存 mysql_free_result()。
```



### 程序范例

```c
#include <stdio.h>
#include <unistd.h>
#include "mysql.h"
#include <stdlib.h>
#include <string.h>

#define _HOST_ "192.168.1.110"      // 数据库ip地址    (免去了小端转大端的过程)
#define _USER_  "root"              // 数据库登陆用户
#define _PASSWD_  "123456"          // 密码
#define _DBNAME_  "bilibili"        // 登陆的库

int main(int argc, const char * argv[]) {
    
//初始化mysql对象
    MYSQL* mysql1 = mysql_init(NULL);
    if(mysql1 == NULL){
        printf("init error \n");
        exit(1);
    }
    
//进行连接,如果成功则mysql 不为null
     mysql1 = mysql_real_connect(mysql1, _HOST_, _USER_, _PASSWD_, _DBNAME_,0,NULL,0);
    if(mysql1 == NULL){
        printf("real_connect error \n");
        exit(1);
    }

    printf("连接成功\n");
    
    char rSql [256] = {0};  //用来存放 sql 语句
        
    
//sql语句执行函数, 执行了一次插入数据sql语句. 
    strcpy(rSql,"INSERT INTO student (id,name,email,sex,sal,deptno) VALUES (3,'asd',
            'mm@mm.com','男',10020,'3')");    // 将sql拷贝到 rSql数组内.
    if( 0 != mysql_query(mysql1,rSql)){    //执行sql语句,判断返回值, 0表示成功, 非0失败.
        printf("mysql_query err\n");
        exit(1);
    }

//查询语句并取回结果集
    strcpy(rSql,"SELECT * FROM student");   // 查询语句拷贝到rSql
    if(mysql_query(mysql1,rSql) != 0){     // 执行sql语句
        printf("mysql_query err  \n");
        exit(1);
    }
    MYSQL_RES* result= mysql_store_result(mysql1);   //取结果, 值使用完成后需要释放
    MYSQL_ROW  row;         // 这个值在栈上,不用释放.其内部是个二级指针,指向一个二级数组
    int i = 0;            // 表中列的个数
    if(result != NULL){    // 非NULL 就代表取到了数据.
        //打印结果集
        while( (row =  mysql_fetch_row(result)) != NULL){   //循环取一行,有多少行就循环多少次.
            for(i=0; i<7 ;i++){                  // 这个7 代表是表有 7列. 就是7个字段
                printf("%s\t",row[i]);    // 打印数据, 但是排版很乱
            }
            printf("\n");        //换行来区分行
        }
        mysql_free_result(result);   //释放 result结果集, 如果没有取到也就不用释放了,就是个NULL
    }
    

    
    mysql_close(mysql1);   //关闭连接
    return 0;
}    
```









