# 对iv进行修复

```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
import base64
import urllib
cipher=' '  #提交后所得的无法反序列化密文
iv=' '  #一开始提交的iv
cipher=urllib.unquote(cipher)
cipher=base64.b64decode(urllib.unquote(iv))
newIv=' '
right='a:2:{s:8:"userna'  #被损坏前的正确明文

for i in range(16):
	newIv+=chr(ord(right[i])^ord(iv[i])^ord(cipher[i]))  #这一步相当于把原来iv不匹配的部分修改过来
print urllib.quote(base64.b64encode(newIv))

```