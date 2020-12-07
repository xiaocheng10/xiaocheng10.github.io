---
title: _wakeup的反序列化
date:2020-10-15
---

# _wakeup的常用方式
_wakeup经常用在反序列化中，如重新建立数据库连接或执行初始化操作

# _wakeup反序列化的方式
1. 对某个字符进行反序列化
2. 对一个类进行实例化，然后进行反序列化

# 针对类的反序列化形式的解决方案

[_wakeup对类进行反序列化的例子]（https://zhidao.baidu.com/question/495110936393795804.html）
1. 利用php的new对类进行实例化（new是申请操作空间的操作符，一般用于类。如:class a{public i=1}，
   输入$c=new a()     echo(serialize($a)）；相当于定义一个基于a类的对象，$c->就是1
2. 利用_wakeup()函数漏洞原理：当序列化字符串表示对象属性个数的值大于真实个数的属性时就会跳过
   _wakeup的执行（序列   化返回的字符串格式：
```
      O:<length>:"<class name>":<n>:{<field name 1><field value 1>...<field name n><field>}
```
         O:   表示序列化的事对象；< length>:表示序列化的类名称长度；< class name>：表示序列化的类的名；
        < n >:表示被序列化的   对象的属性个数；< field name 1>：属性名；< field value 1>：属性值）
3. 然后将序列化返回的字符串（有时需进行相应的加密）用get提交