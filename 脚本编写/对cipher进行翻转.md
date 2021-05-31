# CBC中对cipher进行翻转

```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
import base64
import requests
import urllib


iv_raw='NdSNDmRE9krO3GFhtgtpCQ%3D%3D'
print("原来的iv_raw："+iv_raw)

cipher_raw='ZtbLOd8aevMJOTnaG6irEdkhmf3qfqBigqioL1vsDugWkwYJ%2FHUZrkF56V6zza4ZdzNex8EOb1u%2Bc%2FPPakb0VA%3D%3D'

print("原来的cipher："+cipher_raw)

cipher=base64.b64decode(urllib.unquote(cipher_raw)) #对cipher_raw用urllib.unqote进行url解码，然后用base64.b54decode()对url解码出来的东西进行base64字节流解码

#a:2:{s:8:"username";s:5:"zdmin";s:8:"password";s:5:"12345"}
#s:2:{s:8:"userna
#me";s:5:"zdmin";
#s:8:"password";s
#:3:"12345";}

xor_cipher=cipher[0:9]+chr(ord(cipher[9])^ord('z')^ord('a'))+cipher[10:] #取出cipher（即原序列化）中我们要更改的字符，然后用异或的方式进行更改

print("翻转后的cipher：")
print(urllib.quote(base64.b64encode(xor_cipher)))
```