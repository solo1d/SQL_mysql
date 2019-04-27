# 数值类型 和 列类型

## 列类型的存储范围与占据的字节关系

### 存储同样的数据, 不同的列类型,所占据的空间和效率是不一样的--- 这就是建表前的 列类型 的意义. 

## MYSQL 三大列类型

### **数值型**

* **整形 \( 有个设定的统一规则zerofill\(M\) ,表示0填充,而且附带属性 unsigned \)**
  * **Tinyint**        占一个字节, 存储范围 -128~127,  0-255,  \( 默认规则是 -128 到 127 \)
    * 添加 unsigned 规则后是  0-255
  * **Smallint**      占两个字节, 存储范围 -32768~32767   
    * 添加 unsigned 规则后是 0-65535
  * **Mediumint**  占三个字节,存储范围 -8388608 ~ 8388607
    * 添加 unsigned 规则后是 0-16777215
  * **Int**                   占四个字节, 存储范围  -2147483648~ 2147483647
    * 添加 unsigned 规则后是 0-4294967295
  * **Bigint**             占八个字节,存储范围 -9223372036854775808 ~ 9223372036854775807
    * 添加unsigned 规则后是 0-18446744073709551615
  * 声明实例: _mysql&gt; ALTER TABLE class ADD snum smallint\(5\) zerofill not null default 0;_
* **小数 \(浮点型, 定点型\)**
  * **浮点: Float\(M,D\),       定点:  decimal\(M,D\)**
    * 浮点:   M 叫做 "精度" --&gt;代表 '总位数',    而 D 是"标度" , --&gt; 代表小数位数,\(小数点右边的长度\)
      * float\(6 , 2\)   --&gt; 存储范围是   +9999.99 到  -9999.99  \(默认有符号\)
      * float\(6, 2\) unsigned   ---&gt; 无符号,  +9999.99  到  0
      * 如果 M &lt;= 24  那么就占用 4字节, 否则占用 8字节.
    * **定点:  decimal , 定点是把整数部分和小数部分 分开存储, 比float精确.**
      * 用多少就分多少, 不用太过担心容量, 是一种变长类型.但是精度高.
    * mysql&gt;  CREATE TABLE class \( nd decimal unsigned not null default 0.00, nf float unsigned not null default 0.00\) ENGINE myisam CHARSET utf8;

### **字符串 \(占多少大的空间是按照字符编码确定的, utf8 占3字节 \)**

* **注意: char\(M\) , varchar\(M\) 限制的是** _**字符**_ **,而不是**_**字节.**_
  * **char**      _**定长字符串    : char\(6\)  --&gt;固定长度6, 在尾部用空格补充.  搜索快,但是会浪费空间. char类型最大长度是  0&lt;= char &lt;= 255 之间.**_
    * _mysql&gt; CREATE TABLE testChar\( ch char\(6\) not null default '' \)ENGINE myisam charset utf8;_
  * **varchar**       _变长字符串_: varchar\(10\)  --&gt;占用 \(1或2\)+10 字节长度,字符串前面有表示字符串长度的标示, 一个字节或两个字节, 用来索引**.  这个类型存储在另外的位置,不和 char 在一起. 检索速度也满,因为每次都要对比索引长度.**      _varchar类型最大长度是 0&lt;= varchar &lt;= 65535之间\(ASCII\), 如果是UTF8 则是  822000 左右._
    * _mysql&gt; CREATE TABLE_  testVarchar\( vch  varchar\(6\) not null default ''\) ENGINE myisam charset utf8;
  * **text      一般是中小型文本,文章和新闻当, 没有任何默认值和参数, 几万个字.  搜索慢**
    * _mysql&gt; CREATE TABLE testText\( tex  text \)ENGINE myisam charset utf8;_
  * **blob       二进制类型, 用来存储图像, 音频等二进制信息. blob 能防止因为字符集的问题,导致信息丢失. 而且尾部空格不会被删除. 写入什么就存什么,不会过滤.**
    * _mysql&gt; ALTER TABLE  testText ADD img blob;_

### **日期时间类型**

* **date** __     日期, 范围是1000-01-01  到 9999-12-31, 占3字节
  * _mysql&gt; ALTER TABLE test3 ADD date not null default '1900-01-01';_
* **time**      时间类型,   范围 -838:59:59 到 838:59:59 ,   占3字节
  * _mysql&gt;  ALTER TABLE test3 ADD time not null default '00:00:00';_
* **datetime**     时间和日期, 格式 YYYY-MM-DD HH:MM:SS    ,占8字节
  * _mysql&gt;  CREATE TABLE test4  \( mydt datetime not null default  '1000-01-01 00:00:00' \)ENGINE myisam charset utf8;_
* **year**     年份类型,   YYYY    , 范围 1901到 2155  , 基数是1901,还可以存 0000 ,占1字节,最多255种变化. 如果设定超出界限,那么会自动转换为 0000 .
  * _mysql&gt; ALTER TABLE test3 \(my  year not null default '1901' \) ENGINE myisam charset utf8;_
* _**timestamp**_     时间戳, 通过特定的参数 CURRENT\_TIMESTAMP 来自动获取目前时间和日期,然后自动写入数据内. 设为默认参数的话 就可以不用管它了,非常方便.
  * _mysql&gt; CREATE TABLE test5\( ts timestamp default CURRENT\_TIMESTAMP, id int \)ENGINE myisam charset utf8;_

\_\_

















