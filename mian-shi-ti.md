---
description: '1'
---

# 面试题

### 查询模型面试题

1.有个 mian 表, 其中有列为 num 是int类型,  里面存放了若干个值\(1 ~ 50之间\) ,  要求 : 将  20&lt;=num&lt;30  之间的值修改为20 ,  和 30 &lt;= num &lt;40  之间的值修改为 30 .   使用一条指令.

```sql
解:
mysql> UPDATE mian SET num=floor(num/10)*10     
        WHERE num>=20 AND num<=39;
# 主要思路是将num当初变量来看, int会发生截断,小数丢失,然后再乘10, 会得到整数.
# 故意挑选的两个值,  如果是其他的值, 那么可以使用  a AND a  OR b AND b;
# floor()是一个函数, 他会把括号中的值进行取整数,就是扔掉小数点. 
```

