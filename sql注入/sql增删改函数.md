---
title：sql增删改函数
data：2020-11-17
---

# 在数据库中增加一段数据


## insert语句

1. 在标准的sql语句中，一次插入一条记录的insert语句的一种形式
    insert into tablename(列名...) values(列值...）;
    这种方法将列表和列值分开了，使用时列表和列值的数必须一致，如下面向user表中插入一段数据
    insert into user(id,name,age) value(123,'绕',19);
2. 在mysql中还有另一种形式
    insert into user set column_name1=value1,column_name2=value2,....;
    这种方法允许将列表和列值成对的出现和使用，如：
    insert into user set id=123,name='绕',age=18;
3. 前面两种方法的不同点:  
   (1)第一种方法如果value中什么都不写，则表示mysql将使用表中的每一列的默认值插入新纪录，如：
                                                      insert into user() value()
       如果表名后面什么都不写，即表示向表中所有字段赋值，此时不仅在values中的值与列数一
       致，且顺序不能颠倒。如：
                                                        列数为3列
                                                         insert into user value(123,'绕');错
                                                         insert into user value(123,'绕',19);对
   (2)第二种方法使用了set方式，必须至少为一列赋值。如果一个字段使用了省缺值（如默认或自增值）
       ，这两种方法都可以省略这些字段。如：
                                                          id字段使用了自增值
                                                          insert into user(name,age) value(ni,19);
                                                          insert into user set name=ni,age=19;

# 删除函数

## 删数据

1. delete from 表名；
2. 有条件的删除数据：delete from 表名 where id=1;

## 删除结构

1. 删数据库：drop database 数据库名
2. 删除表：drop table 表名
3. 删除表中的列：alter table 表名 drop column 列名
   如：delete from user where id=16

# 修改函数

1. 修改所有：updata 表名 set 列名='新的值，非数字加单引号'
2. 带条件的修改：updata 表名 set 列名='新的值，非数字加单引号'
    如：updata user set username='tt' where id=15

# 函数


## addslashes()

1. addslashes()函数返回在预定义字符之前添加反斜杠的字符串
    预定字符:
                  单引号(')
                  双引号(")
                  反斜杠(\)
                  NULL
2. 该函数用于为存储在数据中的字符串以及数据库查询语句准备字符串
3. 默认地，php对所有get和post和cookie数据进行自动查询addslashes()。所以你不要对已转义的字符串再用addslashes()，这会造成双层转义。可以用get_magic_quotes_gpc()来检测
4. 语法：addslashes(string)  string为要转义的字符串

## stripslashes()
函数删除由addslashes()函数添加的反斜杠

## mysql_real_escape_string()

1. 函数转义sql语句中使用字符串中的特殊字符
            特殊字符：
                          \x00
                          \n
                          \r
                          \
                          '
                          "
                          \x1a
2. 语法：mysql_real_escape_string(string,connection)
             string为转义字符串
             connection 可选。规定mysql连接，未规定，则用上一个连接

## ctype_digit()

检查字符串里的字符是不是数字，是返回ture，不是返回false

## get_magic_quotes_gpc()

1. magic_quotes_gpc=on时，函数magic_quotes_gpc()返回1
   magic-quotes_gpc=off时，函数magic_quotes_gpc()返回0
2. get_magic-quotes-gpc就是得到环境变量magic_quotes_gpc的值

## intval()
用于获取变量的整数值

## mysql_fetch_array
从结果中取出一行作为关联数组
