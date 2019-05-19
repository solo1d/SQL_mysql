# 存储过程

## 存储过程

#### 存储过程是预编译, 相当于定义了一个函数, 调用就会运行.

#### 存储过程是作用在选定的库上的

### 优点

* 存储过程增强了SQL语言的功能和灵活性
* 允许标准组件是编程的.
* 存储过程能实现较快的执行速度
* 存储过程能减少网络流量
* 存储过程可被是为一种安全机制来充分利用

### 创建存储过程

```sql
#1. 首先选中一个数据库, 注意: 创建的存储过程, 只能在选中的这个库内执行!!!!
        mysql> use  库;

#2. 改变分隔符功能  ;    不让其成为执行结束的标记. 主要是为了后面的步骤.
        # 执行完毕后,分号结尾就无用了,  必须使用  $$  来进行语句结尾.( 或者使用 $$; )
                mysql>  delimiter 
                        $$;        # 独占一行 这样使用就需要分号, 否则不需要分号
                mysql>  SHOW TABLES 
                        $$;    #语句要这样写.

#3. 创建存储过程,关键词是 procedure, 随后的参数是自定义的过程名字和括号内的参数,就当函数看.
        # 参数关键字:  in 输入参数(不可返回),无论存储过程如何运算.原参数的本值不变,
           #         out 输出参数(可返回), 这个值是传出参数,只作为结果(return),无论什么,默认值都是NULL
           #         inout 输入输出参数(可返回), 可计算可返回.
                #参数使用:  关键字 变量名字  类型 
        mysql> CREATE PROCEDURE  
                hello_p(in p int,out d int ,inout c int )    #  名字是 hello_p,参数: in 输入参数,名字是p 类型int
                begin              #从这里开始向下到 end  ,当成函数体看.
                SELECT 'hello';     # 上面改变了分隔符, 所以这里不会直接执行,而是继续向下读取
                SELECT p,d,c;        # 除了这些, 还可以添加局部变量之类的内容;
                SET p = p+1;     # 让p 等于 p+1
                SELECT  COUNT(*) INTO d FROM README;  # 将查询的结果,输出到d参数里面去.而不会输出到屏幕上.
                SET c = 50;
                SELECT p,d,c;    # 再次输出
                end  
                $$;           # 两个 $$; 才是分隔符,

#4. 存错过程创建完成后, 需要恢复分隔符的功能,然后才可以执行刚刚创建的存储过程.
        mysql> delimiter ;        #执行完毕之后可能没有返回值.这是正常的.(报错就是你关键字打错了)

#5. 执行存储过程. 说白了就是一个函数调用.
        mysql> call hello_p();      # 调用函数名, 如果我括号内有参数,那么需要用括号带上参数.
        mysql> call hello_p(参数1,参数2,参数3);   #调用有参数的 存储过程
 

  # 根据上面的存储过程定义, 调用后会打印如下结果,(完全就是将两个语句进行封装嘛,跟函数封装没区别)
mysql>   SET  @t1 = 4,@t2 = 10 ,@t3 = 13;
mysql>   call p_hello(@t1,@t2.@t3);     
+-------+
| hello |
+-------+
| hello |
+-------+
1 row in set (0.01 sec)

+------+------+------+
| p    | d    | c    |
+------+------+------+
|    4 | NULL |   50 |
+------+------+------+
1 row in set (0.01 sec)

+------+------+------+
| p    | d    | c    |
+------+------+------+
|    5 |   34 |   50 |
+------+------+------+
1 row in set (0.02 sec)


# @t1 的值没有改变(in).  @t2 被覆盖式的改变了 原值被清空(out), @t3 也改变了 (inout)

mysql> SELECT @t1,@t2,@t3;
+------+------+------+
| @t1  | @t2  | @t3  |
+------+------+------+
|    4 |   34 |   50 |
+------+------+------+
1 row in set (0.01 sec)


```

### 恢复分隔符功能

```sql
mysql> delimiter ;       
```

### 执行存储过程

```sql
# 执行之前 ,需要恢复分隔符功能. 否则会失败
mysql> call  hello_p ;                  #无参数, 存储过程名是 hello_p
mysql> call  hello_p(参数1,参数2);       #有参数的调用
```

### 删除存储过程

```sql
MySQL中使用DROP PROCEDURE语句来删除存储过程.
mysql>   DROP PROCEDURE  p_hello;     #后面是存储过程名
```

### 存储过程和流程控制语句中的选择语句

```sql
第一种:
if 条件   then  条件为真执行这条语句  else  条件不为真执行这条语句   end if; #最后是结束标志
第二种:
if 条件1   then  条件为真执行这条语句  elseif  条件2  then  条件1不成立就判断条件2 为真执行这条语句
          else  前面条件1 2 都不为真则执行这条语句   end if;
第三种:
case 条件判断的变量   when 变量的取值1  then  前面为真则执行这条语句2   when 变量的取值2 
     then 前面为真则执行这条语句2  else 前面都不成立则执行这条语句  end;


范例:
mysql>  use test;
mysql> delimiter $$;
mysql> CREATE PROCEDURE p_showage( in age int)
       begin
         if  age >= 18  && age <60
              then  SELECT '成年人';     #在这里必须添加分号
         elseif age>=60 
              then SELECT '老年人';     #注意分号
         else
              SELECT  '未成年人';        #这里也必须添加分号
         end if;
      end
      $$;
 mysql> delimiter ;
 mysql> call p_showage(18);
```

#### case 语句分支: 可以出现在sql语句中, 也可以出现在 存储过程 内

```sql
第一种:
case 条件判断的变量 when 变量的取值1 then 1的执行语句  when 变量的取值2  then 2的执行语句
    else  以上条件都不为真时的执行语句   end case;

#例子1:普通语句
    mysql>  SELECT case age when 18 then '成年了'  when 12  then '未成年' else '不确定'
            end case   from  student;

#例子2: 存储过程 
    mysql>  use test;
    mysql> delimiter $$;
    mysql> CREATE PROCEDURE  
            p_date(in v_empno int)
            begin
            declare addS int;      # 定义一个局部变量addS, 类型是 int , 关键字 declare
            case v_empno
                when  1001
                    then  SET addS=1500;
                when  1002
                    then  SET addS=2500;
                when  1003
                    then  SET addS=3200;
                else
                    SET addS=1000;
                end case;
            UPDATE goods SET price = addS WHERE  id = v_empno;  
                    #上面的UPDATE 解释: goods是表名,有price和id两个列, 运行的结果是,
                        #              找到 id 等于 v_empno的行 ,并将 price设置为 addS的值
            end
            $$;
                

```

## 

## 还可以使用SET 设置一个局部变量来传入进去

库作用域;

```sql
mysql>  SET @变量名字= 初始值 ;       #这个名字随便设置, 初始值也可以是任意类型.
                            # 这个值可以用来直接调用,还可以用来当作存储过程参数
mysql>  call p_hello(@变量名字);
mysql>  SELECT @变量名字;        #这样可以直接打印这个变量的值.
```



