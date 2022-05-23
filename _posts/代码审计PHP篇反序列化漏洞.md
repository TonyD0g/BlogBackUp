---
title: "[代码审计]PHP篇:反序列化漏洞"
desc: TonyD0g
date: 2022-01-29 20:26:57
tags: 代码审计
toc: true
---
<font size=4 >

<!-- more -->

## 0.前言
**何为反序列化，那序列化呢:**
序列化和反序列化是相对的，你可以就将其理解为数据的的编码和解码过程。一种语言系统下的数据结构只有在当前这个系统下才能够识别运行；当数据需要跨语言跨系统传输时，必须将其转成一种中间结构，这个中间结构能被双方识别、还原，这个过程就是序列化和反序列化。 

简单的说，序列化就是把一个对象变成可以传输的字符串，可以以特定的格式在进程之间跨平台安全的进行通信

## 1.简单案例:
serialize() 序列化：使用函数serialize()可将实例序列化为字符串 unserialize() 反序列化：使用函数unserialize()可将序列化的字符串还原.

示例代码:
```
<?php
class Example {
  var $var = '';
  function __destruct() {
      eval($this->var);
  }
}
unserialize($_GET['code']);
?>
```

漏洞利用:
构造漏洞利用的代码，保存为test.php，
```
<?php
class Example {
  var $var = 'phpinfo();';
  function __destruct() {
      eval($this->var);
  }
}
$a=new Example();
echo serialize($a);
?>
```
运行代码,输出结果:
O:7:"Example":1:{s:3:"var";s:10:"phpinfo();";}

**提交?code=O:7:"Example":1:{s:3:"var";s:10:"phpinfo();";} 即可执行phpinfo()**
成功Hacking

## 2.PHP SESSION反序列化:
主要原因是： 
ini_set(‘session.serialize_handler’, ‘**php_serialize**’);
ini_set(‘session.serialize_handler’, ‘**php**’);
**两者处理session的方式不同**

示例代码:
```
<?php

//ini_set('session.serialize_handler','php');
ini_set('session.serialize_handler','php_serialize');

session_start();
$_SESSION["test"]=$_GET["a"];
?>//提交?a=1111
```

输出结果:
```
php_serialize： a:1:{s:4:"test";s:4:"1111";}
php:            test|s:4:"1111";

```
## 3.CTF比赛中的形式:
```
文档：6.反序列化.note
链接：http://note.youdao.com/noteshare?id=87a37fce0a327efbc6472ddfe6acd7de&sub=ABC648E3631D4FB29EFA99E4E995ACA6
```

## 学习来源:
```
公众号Bypass 《PHP代码审计入门笔记》
```
</font>