---
title:SSTI服务器模板注入
data:2020-12-07
---

# SSTI漏洞形成原因
1. SSTI漏洞的形成的原因是程序员的代码不规范或对用户的输入过于信任，导致的漏洞，与渲染模板本身没有关系
2. 渲染模板是对数据（变量）到实际的视觉表现（HTML代码）的实现手段
3. 渲染模板主要为：python的一些框架 jinja2 mako tomado django
                             java框架jade velocity
                             PHP框架smarty twig

# 服务端模板注入的原理
   通过输入转换成特定的HTML文件，然后返回页面，用Twip php 模板引擎做示例：
   代码：$output=$twig->render($_GET['custom_email'],array["first_name"=>$user.first_name) );
   这里由于代码的不规范存在着较为严重的ssti漏洞，如：
   输入：url:xx.xx.xx.xx/?custom_email={{7*7}}
             返回49
            url:xx.xx.xx.xx/?custom_email={{self}}
             返回f<templatereference none""></templatereference>
   这里对{{}}里的代码进行了执行，而没有对输入的数据进行了处理和过滤就进行了执行，如果此时用户输入恶意的代码也会进行执行。

# 漏洞挖掘

各类的模板引擎都有自己的扫描规则，致使不同的引擎会有不同的解析，所以我们在挖掘之前需要对web框架的漏洞进行检查，不然
    会出现{{}}没有用的错误判断

下图为ssti的一些模板渲染引擎及利用

![ssti1](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/ssti1.jpg)

此图为url的可控，即url输入什么就输出什么