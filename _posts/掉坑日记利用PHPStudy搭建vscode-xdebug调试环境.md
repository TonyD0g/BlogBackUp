---
title: "[掉坑日记]利用PHPStudy搭建vscode + xdebug调试环境"
desc: TonyD0g
date: 2022-02-22 11:31:34
tags: 掉坑日记
toc: true
---
<font size=4 >


<!-- more -->

## 前言:
坑爹日记:
新开的坑,记录自己走过的坑,避免以后忘记导致查资料会很痛苦.

"利用PHPStudy搭建vscode + xdebug调试环境" 的作用:
方便代码审计

牢骚:
代码审计真的太难了,审一个cms审了很久也挖不出漏洞,挫败感太强了,所以找了以前被挖穿的cms来复现看看,学下前辈们的思路和方法.最近更新速度下降了,是因为我没东西产出了,未来更新速度可能依旧会很慢(毕竟没东西产出,该产出的都一股脑输出了).因为学校突如其来的延期开学,我想着不能浪费差不多一个月的时间,所以就买了书学习,谁知道这快递速度真的感人,导致这几天我一直在等快递,心累┓(;´_｀)┏.

## 小技巧:
```
修改debug中的变量大小:
https://www.baidu.com/link?url=DJqbgSK3g4NZviIBkMEvYuZvIZC0zoJ8Cy_2TJ1XjJZGWj5fB3mqC7p6ytxSarvxZfsABwVdpoKdoCOq&wd=&eqid=c01377e70001d7de00000006624d51fb
```

## 正文
利用PHPStudy搭建xdebug调试环境 + vscode
```
https://www.cnblogs.com/Risk2S/p/12369002.html 
https://www.cnblogs.com/phonecom/p/10340038.html
https://www.baidu.com/link?url=8d4VCGsR9zKH69FzQDtb8K0LJBjXvzVZFyZqlxKpFiOlTlOj2wcTU4W0_cJP7V2rlTpZaBHjVTDJZDIXfPdEjdpV7jyAW-DPux0ycLZHFFO&wd=&eqid=dc8e04b000001e540000000662144793
```

## PS: php.ini 文件的最后加上下面这些代码
```
[XDebug]
    zend_extension=D:\phpstudy_pro\Extensions\php\php5.4.45nts\ext\php_xdebug.dll     ;用自己的路径,(全路径)
    xdebug.remote_enable = 1   ;开启远程调试功能
    xdebug.remote_autostart = 1    ;这个配置是比较重要的一个配置
    xdebug.remote_handler = "dbgp"
    xdebug.remote_port = "9001"   ;端口号
    xdebug.remote_host = "127.0.0.1" ;远程调试的ip地址，即你自己的本机ip
```

## 调试步骤:
1.vscode打开项目源代码,下断点
2.按F5,开始调试
3.刷新浏览器,开始代码审计

</font>