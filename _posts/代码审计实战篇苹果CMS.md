---
title: "[代码审计]实战篇:苹果CMS 8.x版本"
desc: TonyD0g
date: 2022-02-23 11:30:26
tags: 代码审计
toc: true
---
<font size=4 >

<!-- more -->

[代码审计入门必看](https://xz.aliyun.com/t/3767#toc-0)

## 持续更新中ing

## 源代码:
https://github.com/yaofeifly/Maccms8.x


## 基本分析:
![avatar](https://s4.ax1x.com/2022/02/22/bSrAWF.png)
![avatar](https://s4.ax1x.com/2022/02/22/bSrEz4.png)



## 任意文件读取漏洞:
输入127.0.0.1/index.php?m=label-TonyD0g/../../../../test.php触发.

分析流程:
由上图得知,传入?m=label-TonyD0g/../../../../test.php则进入label.php
'TonyD0g'为method

![avatar](https://s4.ax1x.com/2022/02/22/bSrkJU.png)

## 参考链接:
https://www.cnblogs.com/jinqi520/p/11008915.html
https://www.cnblogs.com/ichunqiu/p/9548754.html
</font>