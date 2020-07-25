# API编程

## 点击进入 [官方参考手册](https://dev.mysql.com/doc/refman/8.0/en/c-api-functions.html) 进行更加详细的查询.

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

#### 这个操作分为以下几步

* mysql\_query\(\)     首先执行查找sql语句
* mysql\_store\_result\(\)       拿到返回的结果集
* mysql\_num\_fields\(\)       得到表内列的个数
* mysql\_fetch\_row\(\)        得到行数据\( 也就是表数据 \) 
* mysql\_fetch\_fields\(\)       获得表头, 也就是列名称
* 按照循环的方式 ,以 行数据为基础, 列个数为循环次数, 通过二维数组来获得 行数据.
* mysql\_free\_result\(\)    释放结果集 ,  结束

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


#获取列的个数, 也就是表个字段个数
unsigned int mysql_num_fields(MYSQL_RES *result)
    //参数: 将上面 mysql_store_result() 得到的 返回值传入进去就好了
    //返回的是表的字段个数, 也就是列的个数, 用来进行取数据时候的循环判断.

#获取表头, 就是每个列的列名,
MYSQL_FIELD *mysql_fetch_fields(MYSQL_RES *result)
    //参数: mysql_store_result()  返回的结构体 ,MYSQL_RES
    //返回值:  是一个结构体类型, 里面存放了表中列的名称和个数.
    

# 获取行数据
MYSQL_ROW mysql_fetch_row(MYSQL_RES *result)
    //参数:  mysql_store_result() 返回的结构体 MYSQL_RES
    //返回值: typedef char **MYSQL_ROW;  就是一个二级char指针,指向一个二维数组
    //       如果数据多,需要循环取出. 在栈上,不需要释放.
    // 因为返回值 MYSQL_ROW 指向的是二维数组, 所以直接使用 [] 来取数据.
    
    

    
#释放结果集操作  ---- 在下面
void mysql_free_result(MYSQL_RES *result);
    //就是释放 MYSQL_RES 结果集的.



----------------------------------------------------
# MYSQL_RES 结构体 ,保存的是结果集.
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
#MYSQL_FIELD 结构体    用来保存名称和数量
typedef struct MYSQL_FIELD {
  char *name;               /* Name of column */
  char *org_name;           /* Original column name, if an alias */
  char *table;              /* Table of column if column was a field */
  char *org_table;          /* Org table name, if table was an alias */
  char *db;                 /* Database for table */
  char *catalog;            /* Catalog for table */
  char *def;                /* Default value (set by mysql_list_fields) */
  unsigned long length;     /* Width of column (create length) */
  unsigned long max_length; /* Max width for selected set */
  unsigned int name_length;
  unsigned int org_name_length;
  unsigned int table_length;
  unsigned int org_table_length;
  unsigned int db_length;
  unsigned int catalog_length;
  unsigned int def_length;
  unsigned int flags;         /* Div flags */
  unsigned int decimals;      /* Number of decimals in field */
  unsigned int charsetnr;     /* Character set */
  enum enum_field_types type; /* Type of field. See mysql_com.h for types */
  void *extension;
} MYSQL_FIELD;

----------------------------------------------------
# MYSQL_ROW  二维数组指针. 保存所有的行数据
typedef char **MYSQL_ROW;                /* return data as array of strings */

----------------------------------------------------


```

### 释放结果集

```c
void mysql_free_result(MYSQL_RES *result);

mysql_free_result()释放分配给组由结果的内存 
    mysql_store_result()， mysql_use_result()， mysql_list_dbs()，等等。
完成结果集后，必须通过调用释放它使用的内存 mysql_free_result()。
```

### 返回上次 UPDATE, DELETE, INSERT 更改的行数,以及错误

```c
my_ulonglong mysql_affected_rows(MYSQL *mysql)
    // 参数: 已经连接成功 而且 也进行过查询的 mysql
    // 返回值: 大于零的整数表示受影响或检索的行数
    //        零表示没有为UPDATE 语句更新记录，没有与WHERE查询中的子句匹配的行或者尚未执行任何查询
    //        -1表示查询返回错误.
    // mysql_affected_rows()可以在用mysql_query() 执行语句后立即调用 mysql_real_query().
    // 它返回改变，删除或插入的最后一条语句的行数，如果它是一个UPDATE， DELETE或 INSERT.
    // 对于 SELECT陈述， mysql_affected_rows() 功能如mysql_num_rows()
    
    
const char* mysql_error(MYSQL* mysql)
    // 输出详细的错误信息.
```

### 设置字符集

```c
int mysql_set_character_set(MYSQL *mysql, const char *csname)
    //参数: 已经连接成功的mysql, 
    //      这个是字符集设置, 有很多选择,一般设置是 "UTF8" 或者 "GBK"
    //返回值: 成功返回零。如果发生错误，则为非零。
    // 此函数用于设置当前连接的默认字符集。该字符串csname 指定有效的字符集名称。
    //   连接排序规则成为字符集的默认排序规则。此函数的作用类似于SET NAMES语句,
    //  但也设置了值 mysql->charset，从而影响了所使用的字符集 mysql_real_escape_string().
```

### 预处理

#### 主要是为了提高效率

#### 如果程序只进行很少量的sql语句,那么就不值得用预处理.

* mysql\_stmt\_init\(\)         预处理初始化
* mysql\_stmt\_prepare\(\)       sql语句预处理
* mysql\_stmt\_bindparam\(\)    绑定变量
  * 赋值
* mysql\_stmt\_exectue\(\)   预处理sql执行
  * 回到绑定变量的赋值.(循环重复赋值这一步)

很少用到,多多参考[官方文档](https://dev.mysql.com/doc/refman/8.0/en/c-api-prepared-statement-function-overview.html)

## 程序范例

### 简单的查询和插入范例

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

void show_result(MYSQL_RES* result){   // 显示结果集的封装函数.
        //打印表头,就是列名称
        unsigned int num_fields;     // 记录列的个数
        unsigned int i;     // for循环字段个数的变量.
        MYSQL_FIELD *fields;    //保存每个列的名称的一个结构体.

        num_fields = mysql_num_fields(result);  // 取字段个数, 就是列的个数
        fields = mysql_fetch_fields(result);    // 取到每个列的名称.
        for(i = 0; i < num_fields; i++)
        {
           printf("%s\t",fields[i].name);       //循环打印列名称.
        }
        printf("\n-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+\n");
            //表头和行数据的分隔符


        //打印行数据
        MYSQL_ROW row;              //存放取出的结果集       

        while ((row = mysql_fetch_row(result)))   //循环取一行,有多少行就循环多少次.
        {   
            //需要打印结果集
            for(i = 0; i < num_fields; i++) // 挨行 取出数据, 有多少列就循环多少次.
            {  //每循环一次, 就打印一行
                printf("%s\t",row[i] ? row[i] : "NULL");
            }
            printf("\n");
        }
}



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

    //设置字符集    
    if (!mysql_set_character_set(mysql1, "utf8"))   // 0成功, 非0 失败.
    {
        printf("New client character set: %s\n",
                    mysql_character_set_name(mysql1));
    }
    else{
        printf("设置字符集出错, UTF8");
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
    strcpy(rSql,"SELECT * FROM student");   // 查询
    if(mysql_query(mysql1,rSql) != 0){
        printf("mysql_query err  \n");
        exit(1);
    }
    
    //取回结果集
    MYSQL_RES* result= mysql_store_result(mysql1);   // 取结果
    MYSQL_ROW  row;             // 这个值在栈上,不用释放.  其内部是个指针 
    
    //打印结果集
    if(result != NULL){
        show_result(result);   //打印结果集
        mysql_free_result(result);   //释放 result结果集
    }   
    mysql_close(mysql1);   //关闭连接
    return 0;
}    
```

#### -+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-

### 简单的客户端实现范例

* 初始化
* 连接
* 设置数据字符集
* 循环等待 sql 输入
  * 执行 sql
    * 如果有结果集, 打印结果集
* 关闭

设计一个退出指令 quit

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


void show_result(MYSQL_RES* result,MYSQL* mysql){   // 显示结果集
        //打印表头,就是列名称
        unsigned int num_fields;     // 记录列的个数
        unsigned int i;     // for循环字段个数的变量.
        MYSQL_FIELD *fields;    //保存每个列的名称的一个结构体.

        num_fields = mysql_num_fields(result);  // 取字段个数, 就是列的个数
        fields = mysql_fetch_fields(result);    // 取到每个列的名称.
        for(i = 0; i < num_fields; i++)
        {
           printf("%s\t",fields[i].name);       //循环打印列名称. 也就是表头
        }
        printf("\n-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+\n");
            //表头和行数据的分隔符

        //打印行数据
        MYSQL_ROW row;              //存放取出的结果集       
        while ((row = mysql_fetch_row(result)))   //循环取一行,有多少行就循环多少次.
        {   
            //需要打印结果集
            for(i = 0; i < num_fields; i++) // 挨行 取出数据, 有多少列就循环多少次.
            {
                printf("%s\t",row[i] ? row[i] : "NULL");
            }
            printf("\n");
        }
        printf("\n-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+\n");
        printf("%lld rows in set \n", mysql_affected_rows(mysql));
}


int main(int argc, const char * argv[]) {
    
    //初始化mysql对象
    MYSQL* mysql1 = mysql_init(NULL);
    if(mysql1 == NULL){
        printf("init error \n");
        exit(1);
    }
    
    //进行连接,如果成功则mysql 不为null
     mysql1 = mysql_real_connect(mysql1, _HOST_, _USER_, _PASSWD_, _DBNAME_, 0, NULL, 0 );
    if(mysql1 == NULL){
        printf("real_connect error \n");
        exit(1);
    }

    printf("连接成功\n");

    char rSql [1024] = {0};  //用来存放 sql 语句

    while(1){
        write(STDOUT_FILENO, "yoursql>", strlen("yoursql>")); //输出一个标志 到终端
        memset(rSql, 0x00, sizeof(rSql));
        read(STDIN_FILENO, rSql, sizeof(rSql));   //读取用户输出的sql语句,read有阻塞性

        if( strncmp(rSql, "quit", 4) == 0){   // 如果rSql和quit相等则返回0, 代表退出数据库
            printf("\nbey!\n");
            break;
        }

         //sql语句执行函数, 执行sql语句.
        if( 0 != mysql_query(mysql1,rSql)){
            printf("语句出错\n");
            continue;       // 为了健壮性, 语句出错则返回开头.
        }

        //取回结果集
        MYSQL_RES* result= mysql_store_result(mysql1);   //取结果
        MYSQL_ROW  row;             // 这个值在栈上,不用释放.  其内部是个指针 
    
        //打印结果集
        if(result != NULL){
            show_result(result,mysql1);   //打印结果集
            mysql_free_result(result);   //释放 result结果集
        }
        else{
                //打印了受影响的行数.
            long long temp = mysql_affected_rows(mysql1);
            if(temp >= 0 )      // 用来判断是失败了 还是成功了,
                printf("Query OK,%lld row affected \n", temp);
            else
                printf("ERROR  code (%lld)  \n", temp);
        }
    }

    //关闭连接
    mysql_close(mysql1);
    return 0;
}
```









