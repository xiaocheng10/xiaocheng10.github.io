#suctf2019easysql

## 待定系数法
可以猜测sql语句：select (a,$query,b) from table(c)

如果直接查询1，返回1，
则如果a是存在的，则不可能单独输出这一列的数据出来，否则不可能只输出一个值出来
同理，如果b是存在的，则不可能单独输出这一列的数据出来。
如果a和b都是不存在的，则查询数字时必然会返回数字本身
所以可以通过判断返回的数据判断a,b是否是存在的
c是否存在，主要看数据表的数量和会调用哪个数据表中的数据信息


## 实例
首先我们可以尝试各种符号的闭合，当尝试到双引号时，发现会出现nonono的字样，可以判断对双引号有过滤，然后
尝试其它的符号时发现没有返回，可判断当输入出现错误时，页面不会返回错误的信息
![suctf2019easysql1](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/suctf2019easysql1.jpg)



![suctf2019easysql2](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/suctf2019easysql2.jpg)


然后我们可以尝试1#或1--+，发现返回正确的数据，而#和--+是注释符的作用，因此可以判断是整型注入

![suctf2019easysql3](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/suctf2019easysql3.jpg)


然后我们构造联合注入，发现union被过滤掉了，不过使用堆叠注入的时候，发现没有问题
因此我们可以构造payload:
显示数据库
```
1;show databases;
```
![suctf2019easysql4](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/suctf2019easysql4.jpg)
显示数据表
```
1;show tables;
```
![suctf2019easysql5](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/suctf2019easysql5.jpg)
当时使用payload：1;show columns from flag;  时，发现flag被过滤掉

此时我们可以知道只有一张数据表，它是flag，因此我们可以猜测sql的语句中一定有：select和from flag
因此我们可以使用待定系数法，构造几个未知的变量，然后根据sql语句的格式猜测本题sql语句的格式：
```
select (a,$query,b) from flag(c)
```

如果查询1，发现返回1
如果a存在的话，不可能单独输出一列的数据信息，否则不会输出一个值的信息出来
同理，如果b存在的话，也不可能单独输出一列的数据信息出来
又因为如果a,b都不存在的话，则查询什么数字，则返回什么数字
因此可判断要么a,b都存在，要么a和b存在其一

因此可以构造出payload
```
1；  此时sql语句为：select (a) 1; (b) from flag
```
返回1的值，而没有报错或无返回

构造payload
```
2;  此时sql语句为：select (a) 2; (b) from flag
```
返回2的数值，而没有报错或无返回

由此可进一步判断，(a)很可能是不存在的

由于无论输入的是什么数值进去，返回的都是1，由经验可判断能是$query || 1，在sql语句中
||符号是逻辑或的意思，如果
```
NULL || NULL =NULL

NULL || 非零数值=1，否则返回NULL

两个操作数都为非NULL值，则只要有一个操作数为非零值，则返回1，否则返回0

```

因为输入的数字都是返回1的，且由以上||的定义可知，$query || （不是NULL值，也不是非零数字），由此可猜测
sql语句格式为：
```
select $query || （不是NULL值，也不是非零数字）from flag
```

因此有两种方法：
第一种
只需输入
```
*,1  此时sql语句：select  * , 1 ||（不是NULL值，也不是非零数字）from flag
```
可输出两列的信息数据

第二种
sql_mode是一组mysql支持的基本语法及检验规则
查看命令为：select @@sql-mode;

可以构造payload:
```
1;set sql_mode=PIPES_S_CONCAT;select 1;

```

sql_mode的具体知识看；
https://www.cnblogs.com/piperck/p/9835695.html