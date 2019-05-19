# 函数

## 所有函数\(面试会考 时间日期函数, 控制流函数,字符串函数,转换函数\)

### 数学函数

* _**abs \(X\)**_ ****   返回 x 的绝对值
* _**bin \(x\)      返回x 的二进制 \(oct返回 八进制 , hex返回十六进制\)**_
* _**ceileing\(x\)    返回大于x的的最小整数值, 如果数字有小数部分,那么直接进位, 3.3 -&gt; 4**_
* exp\(x\)       返回值e \(自然对数的底\) 的x次方
* _**floor\(x\)**_    返回小于x的最大整数值, 并且删除后面的小数.
* greatest\(x1,x2 ..... xn\)    返回集合中的最大值
* least\(x1,x2, ...... xn\)     返回集合中最小的值
* in\(x\)      返回x的自然对数
* _**log\(x,y\)**_   返回x的以y为低的对数
* _**mod\(x,y\)     返回x/y 的模 \(余数\)**_
* pi\(\)  返回pi的值
* _**rand\(\)**_  返回0 到1 内的随机值, 可以通过提供一个参数\(种子\) 使rand\(\) 函数随机生存一个指定的值
* _**round\(x,y\)   返回参数x的四舍五入的有 y 位小数的值, 可以把 y 设置为负数,来舍入整数位.**_
* sign\(x\)   返回代表数字x的符号的值
* sqrt\(x\)  返回一个数的平方根
* _**truncate\(x,y\)    返回数字x截断为y位 小数的结果**_

### 聚合函数 \(常用于 GROUP BY 从句的 SELECT 查询中\) 也称 统计函数

* **AVG\(col**\)  返回指定列的平均值
* **COUNT\(col\)**   返回指定列中非NULL值的个数
* **MIN\(col\)**   返回指定的列的最小值
* **MAX\(col\)**   返回指定的列的最大值
* **SUM\(col\)**   返回指定列的所有值之和
* _**GROUP\_CONCAT\(col, str\)**_   将col的结果打印在一起,并且使用字符str进行分隔, 默认是 逗号,
* **DISTINCT\(列名称   \)**  关键词 DISTINCT 用于返回唯一不同的值,他会忽略掉指定列中相同的值.

### 字符串函数

* _**ascii \(char\)**_   返回字符的 ASCII 码值
* bit\_length \(str\)  返回字符串的比特长度
* _**concat \(s1,s2,.....sn\)   将s1,s2 .... sn  字符串.  连接成一个新的字符串.并且返回结果.**_
* _**concat\_ws \(sep,s1,s2 .... sn\)   将s1s2,.....sn 连接成字符串, 并用 sep字符间隔**_
* _**char\_length\(str\)    返回字符串 str 中有几个字符, \(个数\),    等价于  strlen\(\)    C函数.**_
* find\_in\_set \(str,list\)   分析逗号分隔符的list列表,  如果发现 str, 就返回 str 在 list 中的位置
* _**insert \(str,x,y,instr\) 将字符串 str 从第x 位置开始, y个字符长的淄川替换成字符串instr,返回结果**_
* _**instr \( str1 , str2 \);   判断str2 是否在 str1 中存在, 如果存在,返回位置, 如不存在,则返回0**_
* initcap\(str\)       首字母大写
* lcase \(str\) 或 lower \(str\)   返回将字符串str中所有字符改变为小写的返回结果
* _**left\(str, x\)   返回字符串 str 中最左边的 x 个字符**_
* _**length \(str\)   返回字符串 str 中的字节长度\(字节\).  等价于  sizeof\(\)  C宏**_
* ltrim\(str\)    从字符串str 中切掉开头的空格
* _**lower\(str\)        返回将字符串变成小写的结果**_
* _**lpad \(str, len , char\);     返回 len 总长度的字符串, 如果 str 长度不够, 则在左侧 使用char 字符填充 到足够多的个数.**_
* _**position \(substr   IN   str\)     返回字串substr 在字符串 str 中第一次出现的位置. 需要 IN 来连接. 从1 开始数.**_
* quote\(str\)  用反斜杠转义 str中的单引号
* repeat\(str, x\)   返回字符串 str 重复 x 次的结果
* _**replace\(str1 ,   str2 \)  ;   在str1 中寻找 str2 相同的值. 然后删除str2那部分, 相当于: replace\('as',s',  '' \);**_
* _**replace\(str1, str2 , str3\) ;    在str1 中寻找与 str2相同的串, 然后将str2相同的部分替换为 str3 .**_
* _**reverse \(str\)   反转str 字符串的内容,然后输出,  例子:   'abcde'  ---&gt;   'edcba**_
* _**right\(str, x\)    返回字符串最右边的x 个字符**_
* rtrim\( std\)      返回字符串 str 尾部的空格
* _**rpad\(srt, len ,char\);     返回 len 总长度的字符串, 如果 str 长度不够, 则在右侧 使用char 字符填充 到足够多的个数.**_
* _**strcmp \( s1,s2\)    比较字符串 s1 和 s2, 相等返回0, 不相等则返回1**_
* _**trim\(str\)   去除字符串首部和尾部的所有空格**_
* _**ucase\(str\)  或  upper\(str\)   返回将字符串 str 中所有字符转变为大写后的结果**_
* _**substr\(str, pos, len\) ;  截取字符串,以及设置位置和截取长度,然后输出; \# substr\('asd',1\) 表示选择全部,  subsrt\('asd',1,2\) 选择两个,从第一个开始算 'as'.  substr\('asdf',-2\)  从尾部开始 选择两个 ‘df‘  ;**_

### 日期和时间函数  \(重点\)

* _**curdate\(\)  或 current\_date\(\)   返回当前的日期 data 格式,   2019-05-14**_
* _**curtime\(\) 或  current\_time\(\)   返回当前的时间  time 格式  21:21:11**_
* _**date\_add\( date, interval int keyword\)  返回日期 date 加上间隔时间 int 的结果, \(int 必须按照关键字进行格式化\) 如 :**_   SELECT DATE\_ADD\(current\_date\(\),INTERVAL 1 YEAR\);   \#当前日期,加上一年.
* _**date\_format\( date , fmt\)  依照指定的 fmt 格式  ,格式化日期 date值**_
* _**date\_sub \( date, interval int ketwork\)  返回日期date  减去间隔时间  int 的结果 , \( int 必须按照关键字进行格式化\)  如 :**_  SELECT DATE\_SUB\(current\_date\(\),INTERVAL 1 YEAR\);   \#当前日期,减去一年.
* _**dayofweek\(date\)   返回date所代表的一星期中的第几天\( 1~7\), 周日是第一天 ,date格式\('2019-03-03'\)**_
* _**dayofmonth\(date\)   返回date 是一个月的第几天\(  1 ~31\)**_
* _**dayofyear \(date\)   返回date  是一年的第几天 \(1 ~ 366\)**_
* _**dayname\(date\)    返回 date的星期名,  如:  SELECT dayname\(current\_date\);**_
* _**datediff\(date1, date2\)   返回两个日期的差值\(包括所有和时间有关的类型: 日期,时间,分钟,小时,纳秒....\), 可以用来判断;    datediff\(da1,da2\) =1; 查找两个日期差值为1的;**_
* _**from\_unixtime\( fs, fmt\)   根据指定的 fmt格式, 格式化unix时间戳  ts**_
* _**hour\(time\)    返回 time的小时值 \(0~23\)**_
* _**minute\(time\)   返回time的分钟值 \( 0~ 59\)**_
* _**month\( date\)    返回 date  的月份值 \(1~12\)**_
* _**monthname\(date\)    返回date 的月份名, 如: SELECT monthname \(current\_date\);**_
* _**now\(\)    返回当前的日期和时间,  格式是datetime,    ' 2019-05-09 21:21:11 '**_
* quarter\(  date\)    返回date 在一年中的季度  \(1~4\)  , 如: SELECT quarter\( current\_date\);
* _**sysdata\(\)      返回当前日期和时间.**_
* _**week \(date\)    返回日期 date 为一年中第几周\( 0 ~53\)**_
* _**year \(date\)    返回日期date 的年份 \(1000~ 9999\)**_
* **一些示例:**
  * _**获取当前系统时间:**_    
    * mysql&gt;  SELECT FROM UNIXTIME\( UNIX\_TIMESTAMP\(\)\);
    * mysql&gt;  SELECT EXTRACT\(year\_month  FROM current\_date\);
    * mysql&gt;  SELECT EXTRACT\( day\_second FROM  current\_date\);
    * mysql&gt;  SELECT EXTRACT\( hour\_minute  FROM   current\_date\);
  * _**返回两个日期值之间的差值\( 月数\) :**_
    * mysql&gt;  SELECT datediff\(200302, 199802\);
  * _**在mysql 中计算年龄:**_
    * mysql&gt;  SELECT DATE\_FORMAT\( FROM\_DAYS\(TO\_DAYS\(NOW\(\)\) - TO\_DAYS\(birthday\)\), '%y'\) +0 AS age FROM employee;
      * 这样. 如果  birthday  参数是 未来年月日的话, 结算结果为 0 
  * _**下面的sql 语句计算员工的绝对年龄 , 即当 birthday 是未来的日期时, 将得负值:**_
    * SELECT DATE\_FORMAT\(NOW\(\), '%y' \) - DATE\_FORMAT\( birthday \)\), '%y' \) - \(DATE\_FORMAT\(NOW\(\), '00-%m-%d'\) &lt; DATE\_FORMAT\(birthday, '00-%m-%d'\)\) AS age FROM employee;

### 加密函数

* aes\_encrypt \( str, key\)   返回用密钥key 对字符串 str 利用高级加密标准算法加密后的结果, 调用ase\_encrypt 的结果是一个二进制的字符串,  以blob类型存储.
* aes\_decrypt\( str, key \)  返回用密钥key对字符串 str 利用高级加密算法解密后的结果.
* decode\( str, key\)     使用 key 作为密钥解密加密字符串 str
* encrypt\(str, salt\)   使用unixcrypt\(\) 函数,用关键字 salt\( 一个可以唯一确定口令的字符串, 就像要是一样\)  加密字符串str.
* encode\( str,key \)     使用key 作为密钥加密字符串str,   调用encode\(\) 的结果是一个二进制字符串, 它以blob类型存储
* _**md5 \(str\)    计算字符串str的 md5 校验和**_
* password\(str\)   返回字符串str的加密版本,  这个加密过程是不可逆的, 和unix密码加密过程使用不同的算法
* sha\(str\)    计算字符串str的安全散列算法\(sha\) 校验和
* 示例:
  * mysql&gt;  SELECT  ENCRYPT \('root' , 'salt'\);
  * mysql&gt;  SELECT  ENCODE\( 'xufeng' , 'key' \) ;
  * mysql&gt;  SELECT  DECODE\(ENCODE\('xufeng', 'key' \) ,'key\);   \# 加解密放在一起
  * mysql&gt;  SELECT  AES\_ENCRYPT\( 'root' , 'key'\);
  * mysql&gt;  SELECT  AES\_DECRYPT\( aes\_encrypt\( 'root', 'key' \), 'key' \);
  * mysql&gt;  SELECT  MD5 \('123456'\);
  * mysql&gt;  SELECT  SHA \('123456'\);

### 控制流函数

mysql 有4个函数是用来进行条件操作的, 这些函数可以实现sql 的条件逻辑, 允许开发者将一些应用程序业务逻辑转换到数据库后台.

#### mysql 控制流函数:

```sql
case when[test1] then [result1] .... when[testn] then [resultn] else [default] end 
# 如果testn 是真, 则返回resultn ,否则返回 defualt,  无逗号
# case 值  when 某种可能 then 返回值 when 另一种可能值 then 返回值 else 默认值  end 结束
    例如:  mysql> SELECT sname,case gender when 1 then '男' when 0 then '女' else '保密'
                  end  FROM test15;         # gender 这列的值会被替换成 男 女 保密. 其中一个

case [test] when[val1] then [result] ... else [default] end 
# 如果 test 和 valn 相等, 则返回 resultn ,某则返回 default , 无逗号

if(test,t,f)      # 如果 test 是真, 返回t,  否则返回 f.
    例如: mysql>  SELECT sname,if(gender=0,'优先','等待') as vip  FROM test15;

ifnull(arg1, arg2)   #如果arg1不是空, 返回arg1 , 否则返回 arg2

nullif( arg1, arg2)   #如果 arg1 = arg2 返回NULL,  否则返回arg1

#这些函数的第一个是ifnull() ,它有两个参数, 并且对第一个参数进行判断. 如果第一个参数不是NULL
#    函数就会向调用者返回第一个参数;  如果是 NULL , 将返回第二个参数
    如: SELECT nullif(1,2), ifnull(null,10),ifnull(4*null, 'false');

# nullif() 函数将会检验提供的两个参数是否相等, 如果相等, 返回 NULL, 如果不等,
#     就返回第一个参数
    如: SELECT nullif( 1,1), nullif('a', 'b'),nullif(2+3, 4+1);
    
# 和许多脚本语言提供的if() 函数一样, mysql的if() 函数也可以建立一个简单的条件测试,
#    这个函数有三个参数, 第一个是要被判断的表达式, 如果表达式为真, if() 将会返回第二个参数
#     如果为假, if() 将会返回第三个参数.
    如: SELECT if(1<10,2,3) , if(56>100, 'true' , 'false');

#if()  这个函数在只有两个可能结果时才适合使用.然而在现实世界中,我们可能发现在条件测试中会需要
# 多个分支.在这种情况下,mysql 提供了 case函数,他和 php及 perl 语言的 switch-case 条件例程一样
# case 函数格式有些复杂,通常如下所示:
    case [expression to be evaluated]
        when [val 1] then [result 1]
        when [val 2] then [result 2]
        when [val 3] then [result 3]
        .......
        when [val n] then [result n]
    else [default  result]
    end
# 这里, 第一个参数是要被判断的值或表达式, 接下来是一系列的 when-then块, 每一块的第一个参数指定要比较
#  的值, 如果为真, 就返回结果. 所有的 when-then 块将以 else 块结束,当end 结束了所有外部的case 块
#   时,如果前面的每一个块都不匹配就会返回 else块指定的默认结果,  如果没有指定 else块, 而且所有的
#      when-then 比较都不为真, mysql 会返回 NULL
# case函数还有另一种句法, 有时使用起来非常方便, 如下: 
    case 
        when [conditional test 1 ] then [result 1]
        when [conditional test 2 ] then [result 2]
    else  [default result]
    end
# 这种条件下, 返回的结果取决于相应的条件测试是否为真.  
# 示例:
mysql>  SELECT case 'green' 
        when 'red'  then 'stop'
        when 'green'  then 'go'  end;
mysql> SELECT case 9 when 1 then 'a' when 2 then 'b' else 'n/a' end;
mysql> SELECT case when (2+2)=4 then 'ok' when(2+2)<>4 then 'not ok' end asstatus;
mysql> SELECT name,if((isactive ] 1), '已激活','未激活') AS result fromuserlogininfo;
mysql> SELECT fname,lname,(math+sci+lit) AS total,
        case  when (math+sci+lit) < 50 then 'd' 
            when (math+sci+lit) between 50 and 150 then 'c'
            when (math+sci+lit) between 151 and 259 then 'd'
        else 'a' end 
        AS grade FROM marks;
mysql> SELECT if(encrypt('sue','ts')=upass, 'allow','deny') AS loginresultfrom users 
        WHERE uname = 'sue';     # 一个登陆验证
        
        
        
#循环语句 while
  while 条件 do
     内容
 end while;  #结束标志

mysql> use test;
mysql> delimiter $$;
mysql> CREATE PROCEDURE  p_func()    # 这个版本 存储过程只是实现了简单循环语句
        begin
        declare i int default 1;
        declare addresult  int default 0;
        while i<100 do
            SET addresult  = addresult + i;
            SET i = i + 1;        
        end while;
        SELECT addresult;
        end;
        $$;
        
mysql> CREATE 
```

### 格式化函数

* _**date\_format\(date,fmt\)    依照字符串 fmt格式化日期 date值**_
* _**format\(x,y\)    把x格式化为以逗号隔开的数字序列, y是结果的小数位数**_
* _**inet\_auon\(ip\)    返回ip地址的数字表示**_
* _**inet\_ntoa\(num\)    返回数字所代表的ip地址**_
* _**time\_format\(time ,fmt\)    依照字符串 fmt 格式化时间time 值.**_
* _**format\(number\)    它可以把大的数值格式化为以逗号间隔的易读的序列. 例: 100,203**_
* _**示例:**_
  * _**mysql&gt; SELECT format \(34234 .342332 ,3 \):**_
  * _**mysql&gt; SELECT date\_format\(now\(\), '%w, %d,%m,%y,%r' \);    \# w星期,d天,m月,y年,r时间**_
  * _**mysql&gt; SELECT date\_format\(now\(\), '%y-%m-%d'\);**_
  * _**mysql&gt; SELECT date\_format\(19990330, '%y-%m-%d'\);**_
  * _**mysql&gt; SELECT date\_format\(now\(\) , '%h:%i %p'\);**_
  * _**mysql&gt; SELECT inet\_aton\('192.168.1.2'\);**_
  * _**mysql&gt; SELECT inet\_ntoa\( 175790383\);**_

### 类型转换函数

```sql
# 为了进行数据类型转化, mysql 提供了 cast() 函数, 它可以把一个值转化为指定的数据类型,
# 类型有: binary , char  , date , time , datetime,  signed , unsigned 
#          cast( 被转换的值 AS  转换的类型) 
# 示例:
mysql> SELECT cast(now() AS signed integer ) , curdate() +0; 
mysql> SELECT 'f'=binary 'f', 'f'=cast('f' AS binary);

```

### 系统信息函数

* _**database\(\)    返回当前数据库名**_
* benchmark\(count,expr\)   将表达式 expr重复允许 count次
* connection\_id\(\)    返回当前客户的连接id
* found\_rows\(\)    返回最后一个SELECT 查询进行的检索的总行数
* _**user\(\)  或  system\_user\(\)   返回当前登陆用户名, 可以用来判断自己的身份**_
* _**version\(\)    返回mysql 服务器的版本,出于兼容性的考虑和功能的支持,需要查看版本信息.**_
* 示例: 
  * mysql&gt;  SELECT database\(\), version\(\), user\(\); 
  * mysql&gt; SELECT benchmark\( 9999999, log\(rand\(\) \* pi\(\) \)\);  \# 该例中, mysql 计算log\(rand\(\) \*pi\(\)\) 表达式 999999次

































