---
title: "[代码审计]PHP篇:XSS漏洞"
desc: TonyD0g
date: 2022-01-21 23:02:19
tags: 代码审计
toc: true
---
<font size=4 >

<!-- more -->

## 1.基本xss:
漏洞代码示例:
```
<?php
  echo $_REQUEST[ 'id' ];
?>
```
测试语句: ?id=\<script>alert(/xss/)\</script>

## 2.编码解码:
**编码解码输出时，可能导致XSS编码绕过的情况**
漏洞代码示例:
```
<?php
$a=urldecode($_GET['id']); //接收参数并进行url解码
$b=htmlspecialchars($a);   //HTML ENCODE处理,到这里都是没有问题的
echo urldecode($b);       //最后，url解码输出
?>
```
**测试语句： id=%25253Cscript%25253Ealert(/xss/)%25253C/script%25253E**
这边代码逻辑中，问题根源在于**最后一句的url解码输出，导致存在三重url编码绕过**的情况。
根据实际情况，给出安全建议：HTML ENCODE处理后直接输出变量。

## 3.HTML不规范:
HTML代码编写不规范，可能导致的问题，我们来看一个案例：
漏洞代码示例:
```
<?php
  $name = htmlspecialchars($_GET['name']);
?>
<input type='text' class='search' value='<?=$name?>'>
```
获取参数，在一个input元素的属性里输出这个变量，我们注意到这里使用的是单引号闭合，
而**htmlspecialchars函数默认只是转化双引号("), 不对单引号(')做转义,因此可以用单引号闭合**
**测试语句：?name=222' onclick='alert(/xxs/)**
安全建议：将HTML标签的属性值用双引号引起来

## 4.绕过黑名单过滤:
通过在全局引入过滤函数，提供黑名单过滤，
漏洞代码示例:
```
<?php
  $name = htmlspecialchars($_GET['name']);
  $pregs = "/<script>|
<\/script>|onclick|oncontextmenu|ondblclick|onmousedown|onmouseenter|onmouseleave|onmousemo
ve|onmouseover|onmouseout|onmouseup|onkeydown|onkeypress|onkeyup/i";
  $check = preg_match($pregs, $name);
  if ($check) {
          echo 'not found';
          exit;
  }
?>
<input type='text' class='search' value='<?=$name?>'>
```
从html编写不规范，我们可以使用单引号闭合，然后去进一步构造触发事件，可是常见的XSS事件大多都被过滤了，
怎么快速地去找到可以拿来利用的XSS触发事件呢? 答：XSS FUZZ。
前提是要收集积累一些触发事件，利用自己编写python脚本进行fuzz,或者使用前辈写的一些工具

例如:
**XSStrike:**
```
https://github.com/s0md3v/XSStrike
```
## 其他涉及资源详见:[WAF绕过]XSS篇

## 5.漏洞防护:
1.**PHP提供了两个函数htmlentities()和htmlspecialchars() ，把一些预定义的字符转换为 HTML 实体。**
防御代码示例:
```
<?php    
  echo htmlspecialchars($_REQUEST[ 'id' ]);
?>
```
2.**其它的通用的补充性防御手段**
```
1.在输出html时，加上Content Security Policy的Http Header
（作用：可以防止页面被XSS攻击时，嵌入第三方的脚本文件等）
（缺陷：IE或低版本的浏览器可能不支持）

2.在设置Cookie时，加上HttpOnly参数
（作用：可以防止页面被XSS攻击时，Cookie信息被盗取，可兼容至IE6）
（缺陷：网站本身的JS代码也无法操作Cookie，而且作用有限，只能保证Cookie的安全）

3.在开发API时，检验请求的Referer参数
（作用：可以在一定程度上防止CSRF攻击）
（缺陷：IE或低版本的浏览器中，Referer参数可以被伪造）
```

## 学习来源:
```
公众号Bypass 《PHP代码审计入门笔记》
```
</font>