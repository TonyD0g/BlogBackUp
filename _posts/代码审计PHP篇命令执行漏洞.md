---
title: "[代码审计]PHP篇:命令执行漏洞"
desc: TonyD0g
date: 2022-01-28 10:49:00
tags: 代码审计
---
<font size=4 >

###### [版本0x2] 
###### 2022-01-28

## 命令执行漏洞:
1.简单案例
2.常见命令执行函数
3.漏洞利用及绕过姿势
4.防护
5.防护实例代码

## 1.简单案例:
```
<?php
  $target=$_REQUEST['ip'];
  $cmd = shell_exec('ping '.$target);
  echo "<pre>{$cmd}</pre>";
?>
```

**Payload: http://127.0.0.1/cmd.php?ip=|net user**

提交以后，命令变成了 shell_exec('ping |net user')

## 2.常见命令执行函数:
exec()、system()、popen()、passthru()、proc_open()、pcntl_exec()、shell_exec() 、反引号`(实际上是使用shell_exec()函数) 。

system() 输出并返回最后一行shell结果。 exec() 不输出结果，返回最后一行shell结果，所有结果可以保存到一个返
回的数组里面。 passthru() 只调用命令，把命令的运行结果原样地直接输出到标准输出设备上。

popen()、proc_open() 不会直接返回执行结果，而是返回一个文件指针

## 3.漏洞利用及绕过姿势:
详见[[漏洞总结]命令执行漏洞绕过总结](https://tonyd0g.gitee.io/2022/01/05/%E6%BC%8F%E6%B4%9E%E6%80%BB%E7%BB%93%E5%91%BD%E4%BB%A4%E6%89%A7%E8%A1%8C%E6%BC%8F%E6%B4%9E%E7%BB%95%E8%BF%87%E6%80%BB%E7%BB%93/)

## 4.防护:
PHP内置的两个函数可以有效防止命令执行：
- **escapeshellarg()** :
escapeshellarg() 函数将给字符串增加一个单引号并且能引用或者转码任何已经存在的单引号，这样以确保能够直接
将一个字符串传入 shell 函数，并且还是确保安全的。对于用户输入的部分参数就应该使用这个函数。
资料参考：
http://cn.php.net/manual/zh/function.escapeshellarg.php
- **escapeshellcmd()** :
escapeshellcmd() 函数对字符串中可能会欺骗 shell 命令执行任意命令的字符进行转义。 此函数保证用户输入的数
据在传送到 exec() 或 system() 函数，或者 执行操作符 之前进行转义。
资料参考：
http://cn.php.net/manual/zh/function.escapeshellcmd.php
当然，修复方法还有很多方式，修复方式一般有两种思维：
1、黑名单：过滤特殊字符或替换字符 2、白名单：只允许特殊输入的类型/长度

## 5.防护实例代码:
实例1:
```
<?php
  $target=$_REQUEST['ip'];
  //将$target拆成数组
  $octet = explode( ".", $target );
  //判断各位置是否是数字,即是否是正确的ip
  if( ( is_numeric( $octet[0] ) ) && ( is_numeric( $octet[1] ) ) && ( is_numeric($octet[2] ) ) && ( is_numeric( $octet[3] ) ) && ( sizeof( $octet ) == 4 ) ) 
  {
      $target = $octet[0] . '.' . $octet[1] . '.' . $octet[2] . '.' . $octet[3];
      $cmd = shell_exec('ping '.$target);
      echo "<pre>{$cmd}</pre>";
  }
  else {
      echo '<pre>ERROR: You have entered an invalid IP.</pre>';
  }
?>

explode函数:
https://www.runoob.com/try/runcode.php?filename=demo_func_string_explode&type=php
```

实例2:
```
<?php
  $target=$_REQUEST['ip'];
  $cmd = shell_exec('ping '. escapeshellcmd($target));
  echo "<pre>{$cmd}</pre>";
?>
```

## 学习来源:
```
公众号Bypass 《PHP代码审计入门笔记》
```
</font>