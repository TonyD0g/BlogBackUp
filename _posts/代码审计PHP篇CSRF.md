---
title: "[代码审计]PHP篇:CSRF"
desc: TonyD0g
date: 2022-01-22 11:11:36
tags: 代码审计
---
<font size=4 >

###### [版本0x2] 
###### 2022-01-22

## CSRF跨站请求伪造
1.漏洞案例
2.漏洞防范
3.绕过技巧

## 1.漏洞案例: (暂未复现,先不写)
CMS官网：http://www.doccms.com
程序源码：DocCms2016
在\doccms\admini\controllers\system\back.php中,export函数直接对提交上来的参数tables/sizelimit进行处理，
导出sql备份文件，未对访问来源进行有效验证，导致数据库备份模块存在CSRF漏洞
```
function export()
{
  global $db,$request,$sizelimit,$startrow;
  $tables=$request['tables'];
  $sizelimit=$request['sizelimit'];
  if($request['dosubmit'])
  {
      $fileid = isset($request['fileid']) ? $request['fileid'] : 1;
      if($fileid==1 && $tables)
      {
          if(!isset($tables) || !is_array($tables))
              echo "<script>alert('请选择要备份的数据表!');window.history.go(0);</script>";
          $random = mt_rand(100000, 999999);
          cache_write('bakup_tables.php', $tables);
      }
      else
      {
          if(!$tables = cache_read('bakup_tables.php'))
              echo "<script>alert('请选择要备份的数据表!');window.history.go(-1);</script>";
      }
      $sqldump = '';
      $tableid = isset($request['tableid']) ? $request['tableid'] - 1 : 0;
      $startfrom = isset($request['startfrom']) ? intval($request['startfrom']) : 0;
      $tablenumber = count($tables);
      for($i = $tableid; $i < $tablenumber && strlen($sqldump) < $sizelimit * 1024; $i++)
      {
          $sqldump .= sql_dumptable($tables[$i], $startfrom, strlen($sqldump));
          $startfrom = 0;
      }
```


## 2.漏洞防范:
CSRF攻击是攻击者利用用户的身份操作用户帐户的一种攻击方式，通常可以采用如下措施来进行防御:
```
1、增加Token/Referer验证
2、增加验证码
3、用户二次验证
4、HTTP 头中自定义属性并验证
```

## 3.绕过技巧:
CSRF可以使用验证Referer/Token的方式进行防御，但是有防护就有可能被绕过，网站服务端的验证方法仍然可能存
在漏洞，需要进行不断的尝试.
(有条件限制 不一定所有的Refere/Token验证就可以绕过)

- **Referer绕过姿势**
#### 1.空Referer绕过
跨协议间提交请求。常见的协议：ftp://,   http://,    https://,   file://,    javascript:,    data:，最简单的情况就是我们在本地打开
一个HTML页面，这个时候浏览器地址栏是    file:// 开头的，如果这个HTML页面向任何http站点提交请求的话，这些请
求的Referer都是空的。那么我们接下来可以利用 data:   协议来构造一个自动提交的CSRF攻击。当然这个协议是IE不支
持的，我们可以换用javascript:
假如http://a.b.com/d 这个接口存在空Referer绕过的CSRF，那么我们的POC可以是这样的:

```
<html>
  <body>
      <iframe
src="data:text/html;base64,PGZvcm0gbWV0aG9kPXBvc3QgYWN0aW9uPWh0dHA6Ly9hLmIuY29tL2Q+PGlucHV0
IHR5cGU9dGV4dCBuYW1lPSdpZCcgdmFsdWU9JzEyMycvPjwvZm9ybT48c2NyaXB0PmRvY3VtZW50LmZvcm1zWzBdLnN
1Ym1pdCgpOzwvc2NyaXB0Pg==">
  </doby>
</html>
```

上面iframe的src的代码其实是:
```
<form method=post action=http://a.b.com/d><input type=text name='id' value='123'/></form>
<script>document.forms[0].submit();</script>
```

#### 2.判断Referer是某域情况下绕过
比如你找的CSRF是**xxx.com** 验证的referer是验证的   *.xx.com 可以找个二级域名 之后  **img."CSRF地址"** 之后在把文章地址发出去 就可以伪造
```
Referfer：https://www.evil.com
修改为：
Referfer：https://img.evil.com
```

#### 3.判断Referer是否存在某关键词
referer判断存在不存在google.com这个关键词
**在网站新建一个google.com目录 把CSRF存放在google.com目录,即可绕过**
```
Referer：https://www.google.com/xxx.jsp
修改为：
Referer：https://www.evil.com/www.google.com/xxx.jsp
Referer：https://www.evil.com/www.google.com/xxx.jsp.php
```

#### 4.判断referer是否有某域名
仅判断了Referer开头是否以google.com以及google子域名，**不验证根域名**为233.com 那么我这里可以构造子域名
x.google.com.xxx.com作为蠕虫传播的载体服务器，即可绕过

```
Referer: http://member.xxx.com
修改为：
Referer: http://member.xxx.com.evil.com
```

- **Token绕过姿势:**

#### 1.Token无效验证
服务端没有校验token，直接将URL中将参数token去掉。另外，部分模块有Token校验，有些模块却没有
token，找那些没有校验的漏网之鱼。

#### 2.利用xss漏洞来绕过CSRF防御(这个我不会)
存在xss的情况下，使用ajax来跨域获取DOM节点中的Token字段,来进行构造。

#### 3.Token是固定不变的
Token规则过于简单，比如根据某个用户id做了单向hash获得的，可以直接去构造。

#### 4.Token泄露
Token的表数据泄露，或者算法泄露，程序逻辑不严谨导致的安全隐患。

## 学习来源:
```
公众号Bypass 《PHP代码审计入门笔记》
```
</font>