---
title: "[掉坑日记]VPS搭建Viper平台"
desc: TonyD0g
date: 2022-03-12 19:03:32
tags: 掉坑日记
toc: true
---
<font size=4 >

<!-- more -->

## 前言
Viper：内网渗透利器
未来也会搭建CS
搭建这个搞了两小时，真的麻了,记录一下防止以后再坑我一次.

## 必看:
[Xshell连接服务器](https://zhuanlan.zhihu.com/p/76391447)
[Viper安装指南](https://www.yuque.com/vipersec/help/olg1ua)
先去服务器厂商那关闭服务器,

然后创建密钥,
![avatar](https://s1.ax1x.com/2022/03/12/b7xKEt.png)

![avatar](https://s1.ax1x.com/2022/03/12/b7xMUP.png)

并绑定密钥,

注意创建密钥后会**只会提供一次密钥文件下载！，所以一定要保存好该密钥文件,因为用xshell连接服务器会需要它，而且经常需要！！！**
![avatar](https://s1.ax1x.com/2022/03/12/b7xQ4f.png)

**如果你使用VPS部署Viper,请确认VPS的防火墙开放了60000端口及后续监听需要的端口**

## 请注意，
如果你装了什么小皮面板，或者其他什么面板，一定要去同时去 面板和服务器厂商那开放指定端口，不然会出现各种错误，我就是栽在这了。
![avatar](https://s1.ax1x.com/2022/03/12/b7x1C8.png)

![avatar](https://s1.ax1x.com/2022/03/12/b7zCrj.png)
</font>