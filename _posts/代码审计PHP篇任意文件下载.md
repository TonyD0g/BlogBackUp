---
title: "[代码审计]PHP篇:任意文件下载"
desc: TonyD0g
date: 2022-01-28 19:23:15
tags: 代码审计
---
<font size=4 >

###### [版本0x1] 
###### 2022-01-28

## 任意文件下载:
0.前言
1.客户端下载
2.服务端下载
3.任意文件读取

## 0.前言:
在文件下载操作中，文件名及路径由客户端传入的参数控制，并且未进行有效的过滤，导致用户可恶意下载任意文件

## 1.客户端下载:
常见于系统中存在文件(附件/文档等资源)下载的地方.
漏洞示例代码:
```
<?php
$filename = $_GET['filename'];
echo file_get_contents($filename);

header('Content-Type: imgage/jpeg');
header('Content-Disposition: attachment; filename='.$filename);
header('Content-Lengh: '.filesize($filename));
?>
```
文件名用户可控，导致存在任意文件下载漏洞，攻击者提交url
**?filename=test.php**
## 2.服务端下载:
暂时不懂

## 3.任意文件下载:
漏洞示例代码:
```
<?php
  $filename = $_GET['filename'];
  readfile($filename);
?>
```
可以看到参数并未进行任何过滤或处理，直接导入readfile函数中执行，导致程序在实现上存在任意文件读取漏洞

#### Windows:
```
C:\boot.ini //查看系统版本
C:WindowsSystem32inetsrvMetaBase.xml //IIS配置文件
C:Windowsrepairsam //存储系统初次安装的密码
C:Program Filesmysqlmy.ini //Mysql配置
C:Program Filesmysqldatamysqluser.MYD //Mysql root
C:Windowsphp.ini //php配置信息
C:Windowsmy.ini //Mysql配置信息C:Windowswin.ini //Windows系统的一个基本系统配置文件
```
#### Linux:
```
/root/.ssh/authorized_keys
/root/.ssh/id_rsa
/root/.ssh/id_ras.keystore
/root/.ssh/known_hosts //记录每个访问计算机用户的公钥
/etc/passwd
/etc/shadow
/etc/my.cnf //mysql配置文件
/etc/httpd/conf/httpd.conf //apache配置文件
/root/.bash_history //用户历史命令记录文件
/root/.mysql_history //mysql历史命令记录文件
/proc/mounts //记录系统挂载设备
/porc/config.gz //内核配置文件
/var/lib/mlocate/mlocate.db //全文件路径
/porc/self/cmdline //当前进程的cmdline参数
```
## 学习来源:
```
公众号Bypass 《PHP代码审计入门笔记》
```
</font>