---
title:sql导出导入文件
data:2020-11-02
---

# 导出文件的常用函数
## load_file(file_name)

```
1. 读取文件并返回文件内容作为一个字符串
2. 读取文件条件：
   （1）必须有权读取且文件必须完全可读
   （2）欲读取文件必须在服务器上
   （3）必须指定文件完整路径
   （4）欲读取文件必须小于max_allowed_packet(最大允许传输包）
3. 判断读取文件是否有权限
    and (select count(*) from mysql.user>/0*)
   （1）若返回正确，则文件有权可读
   （2）若返回错误，则需要管理员给数据库账户降权
    注意：如果NTFS设置得当，是没有权限读取文件的，当遇到administrators才能访问的文件，使用者就别想load_file出来
4. load_file()里的文件路径可以是ascii码或十六进制，也可以是路径形式（路径里的/用\\代替）

```

## replace()

```
1. 替换字符
2. 格式：replace(原字段，"原字段旧内容","原字段新内容")
```

## hex()

```
1. 将文件导出来，将load_file()读取的二进制文件转换为十六进制
```


# 实际注入中的难点

```
1. 绝对物理路径
   构造有效地畸形语句（报错爆出绝对路径）
   在许多php程序中，只要提交一个疑问进去，如果dispaly_errors=on的话，就会爆出web程序绝对的路径
```


# 导入数据库的常用函数
## load data infile()

```
1. 在一个文本文件中读取行，并装入一个表中，文件名称必须是文字字符串
2. 如：load data infile'/tml/t0.txt'ignore into t0 character set gbk fields teminated by lines terminated by'\n'
   character set gbk是字符集设置为gbk,fields teminated by 为每一项数据之间的分隔符，lines terminated by'\n'是行   的结尾符
3. 判断方式：错误代码是2的时候，文件不存在，错误代码为13的时候为没有权限，可以考虑/tmp等文件夹
```


# 导入到文件函数
## select....into outfile'file_name'

```
1. 可以把选择的行写入一个文件中，文件在服务器主机中创建，但必须拥有file权限，才能用这一语法，但file_name不能是一   个已经存在的文件
2. 两种利用方式：
   （1）直接将select内容导入到文件中
    如：select version() into outfile"c:\\phpnow\\htdocs\\test.php",version()可以换成一句话代码
   （2）修改文件结尾
    如： select version() into outfile"c:\\phpnow\\htdocs\\test.php"lines terminated by 0x16进制文件
3. secure_file_pri参数会限制load_file(),select...into outfile"",load data infile等函数，有三种情况：
   （1）secure_file-pri=null时表示限制mysql,不准导入导出文件（mysql中的默认值）
   （2）secure_file_pri=目录时限制mysql导出导入文件只发生在这个目录下
   （3）secure_file_pri=没有具体值时，不限制mysql导出导入文件
4. 如果secure_file_pri=null(默认值），则只要改成secure_file_pri="/",然后重启mysql就可以了
   
```