# 视图view

在查询中, 我们经常把查询结果当成临时表来看.

## view是什么?  

**View可以看成是一张虚拟的表**, 是表通过某种运算得到的一个投影.

### 如何创建视图

#### 创建视图的时候. 不需要指定视图的列名于类型 ,它是个影子,继承了上面的字段, 只是一种关系.

#### 既然视图只是表的某种查询的投影, 所以主要步骤在于查询表上.

#### 查询的结果命名为视图就可以了.

### 创建视图的语法

```sql
mysql>  CREATE VIEW  视图名  AS SELECT 语句;

#例子:
mysql> CREATE VIEW stats
        AS 
        SELECT cat_id,AVG(shop_price) AS pj
        FROM goods
        GROUP BY cat_id;
#上面语句执行完成后,会创建一个 stats 名字的表, 可以对这张表使用任何的SQL语句
mysql> SELECT cat_id,pj  FROM stats;
```

_**语句会创建一张表,  并且存放 SELECT 语句的查询结果, \( 很像输出重定向\)**_

### 视图的用途

* _**可以简化我们的查询**_
  * 比如.复杂的统计时,先用视图生成一个中间结果, 再查询视图.
* _**更精细的权限控制**_
  *  比如某张表,用户表为例, 需要给对开放用户表权限,但是,又不能开放密码字段.
  * 所以去除密码字段后, 根据用户表 生成出来的视图权限给对方.
* _**数据多, 分表时可以用到.**_
  * 比如小说站, article 表, 1000多万篇
  * 分成 article1, article2, article3 .... article5  这五张表,每张表放 200万条数据.
  * 查询小说时,不知道在那一张表, 可以使用下面语句
  * mysql&gt; CREATE VIEW article AS SELECT title FROM article1 UNION SELECT title FROM article2 .... UNION SELECT TITLE article5;

## 视图是表的一个影子

### 表与视图, 数据变化时 相互影响问题

#### 表的数据变化,要影响到视图的变化. \(自动\)

### 视图的数据和表的数据 一一对应, 就像函数映射

这个一一对应指的是: 根据SELECT 的关系, 从表中取出的行, 能计算出视图中确定的一行,  反之, 视图中任意抽出一行,能够反推出表中的确定的一行. 

#### ORDER BY 和 LIMIT 得到的结果,与表不是一一对应的, 所以 CREATE VIEW 语句中的 SELECT 后面出现了 ORDER BY 和 LIMIT 等能够影响表结构的语句和函数时, 那么就无法反推出表和视图的对应关系,也就说明,无法更新和修改视图.

#### 通过表 能够推出视图对应的数据

#### 通过视图 能够推出表对应的数据

## 对视图进行增删改查的必要条件

#### 也就是说, 表和视图的 数据类型 以及结构 必须 一模一样, 行数可以不相同. 这样就可以对视图进行增删改查, 然后也会映射到 表中去, 就相当于一条语句同时更改了 表和 视图.

#### 如果视图和表的数据类型 和 数据结构 有一丁点不同, 就无法进行增删改 这三个操作, 因为无法通过视图 来反向找到表中对应的列.



### 对于一些简单的视图, 他在发挥作用的过程中, 并没有建立临时表, 而只是把条件都存起来, 下次来查询, 把条件一一合并,直接去查表

#### 思考: 相对于建临时表和视图,  哪个更快?

#### 建表: 查询 -&gt;  形成临时表  -&gt;  查询临时表

#### 叠加: 合并条件 -&gt;  查询表

#### 到底要不要建立临时表, 还是合并语句 .

algorithm = merge  合并查询语句

algorithm = temptable   临时表

algorithm = undefined  未定义,   由系统判断

```sql
mysql>  CREATE algorithm=undefined  VIEW  v3
         AS
         SELECT goods_id,car_id,goods_name,shop_price
         FROM goods
         ORDER BY cat_id ASC,SHOP_PRICE desc;
# 让系统自动判别. 
```

