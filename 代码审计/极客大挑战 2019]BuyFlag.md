#极客大挑战 2019]BuyFlag

查看页面源代码。可以看见一段代码，对代码审计

```
if (isset($_POST['password'])) {
	$password = $_POST['password'];
	if (is_numeric($password)) {
		echo "password can't be number</br>";
	}
	else  if ($password == 404) {
		echo "Password Right!</br>";
	}
}

```
通过代码，我们可以知道我们要绕过is_numeric()函数，我们可以用两种方法：
第一种，我们可以利用php弱类型，构造password=404asd来绕过
第二种，我们可以利用is_numeric()漏洞来绕过，我们可以构造password=404%00来绕过

同时我们观察页面，发现需要money，所以我们可以构造post提交的数据
password=404%00&money=100000000

然后我们再进行抓包
![buyctf1](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/buyctf1.jpg)


然后我们将user=0改为user=1，由于要绕过第一个student，直接input password
![buyctf2](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/buyctf2.jpg)


发现显示money字段太长，我们猜测这里有个比较，可能是strcmp函数进行比较，此时我们利用strcmp函数的漏洞，构造一个数组
password=404%00&money[]=100000000

然后我们就能得到flag
![buyctf3](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/buyctf3.jpg)
