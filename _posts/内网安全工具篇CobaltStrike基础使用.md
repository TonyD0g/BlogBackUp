---
title: "[内网安全]工具篇:CobaltStrike基础使用"
desc: TonyD0g
date: 2022-03-04 19:54:34
tags: 内网安全
---
<font size=4 >

###### [版本0x2] 
###### 2022-03-04

## 目录:
1.Cobalt Strike简介及安装
2.Cobalt Strike功能简介
3.Cobalt Strike简单使用


# Cobalt Strike简介及安装

### 一、什么是Cobalt Strike

Cobalt  Strike基于Java开发，业界称为CS神器，是一款功能强大的商业渗透测试工具，它的交互后渗透（post-exploit）功能涵盖了ATT&CK战术的所有领域，且可以全部在一个集成系统中使用。除此之外，Cobalt Strike还可以调用像Metasploit和Mimikatz之类的其他神器，极大地丰富了自身能力。

CobaltStrike集成了端口转发、端口扫描、socket代理、提权、钓鱼、远控木马等功能。

Cobalt Strike是C/S架构的。分为服务端和客户端。可以将服务端部署在云服务器上，也可以部署在本地局域网中。如果仅是个人使用，可以将服务端和客户端都部署在虚拟机KALI中。



### 二、Cobalt Strike安装

#### 1、环境准备：

```
KALI虚拟机（版本没特定要求）
JDK8或者JDK11（kali自带，可以不用安装。如果是windows系统需要自行安装。）
Cobalt Strike（在本章节目录中，压缩包名为cobaltstrike4.0-cracked.zip）
```

#### 2、安装Cobalt Strike

将压缩包复制粘贴到KALI中。

使用`unzip`命令解压文件。

![avatar](https://s4.ax1x.com/2022/03/04/bNBPjs.png)


### 三、启动服务器和客户端

首先在KALI终端中输入`ifconfig`查看本机IP地址，用于服务器的IP地址，以便客户端的链接。

![avatar](https://s4.ax1x.com/2022/03/04/bNB93Q.png)

#### 1、启动服务端

输入以下命令，启动**服务端**（记得cd进入cobaltstrike文件目录中）：

```
sudo ./teamserver 192.168.12.129 password(输入一个密码)
```

#### 2、启动客户端

(前提还是要进入cobaltstrike文件目录中)
输入以下命令，启动**客户端**：

```
java -XX:ParallelGCThreads=4 -XX:+AggressiveHeap -XX:+UseParallelGC -Xms512M -Xmx1024M -jar cobaltstrike.jar
```

输入命令后，会出现一个对话框，需填写`Host`和`Password`，即服务端IP地址，和设置的密码，最后点击`Connect`：

![avatar](https://s4.ax1x.com/2022/03/04/bNBCcj.png)

# Cobalt Strike功能简介

#### 1、Coblat Strike菜单
![avatar](https://s4.ax1x.com/2022/03/04/bNrAYT.png)

```
New Connection 	新的连接(支持连接多个服务器端) 
Preferences 	偏好设置(设置Cobal Strike界面、控制台、以及输出报告样式、TeamServer连接记录等)
Visualization 	窗口视图模式(展示输出结果的形式)
VPN Interfaces 	VPN接入
Listenrs 	    监听器(创建Listener)
Script Manager 	脚本管理
Close 	        关闭客户端 
```

#### 2、View菜单

![avatar](https://s4.ax1x.com/2022/03/04/bNrZpF.png)

```
Applications 	应用(显示受害者机器的应用信息)
Credentials 	凭证(通过hashdump或Mimikatz抓取过的密码都会储存在这里)
Downloads 	    下载文件
Event Log 	    事件日志(主机上线记录以及团队协作聊天记录)
Keystrokes 	    键盘记录
Proxy Pivots 	代理模块
Screenshots 	受害者机器屏幕截图
Script Console 	脚本控制台(可以加载各种脚本)
Targets 	    显示目标主机
Web Log 	    Web日志 
```

#### 3、Attacks模块下Packages功能
![avatar](https://s4.ax1x.com/2022/03/04/bNrEfU.png)

```
HTML Application 		生成恶意的HTA木马文件
MS Office Macro 		生成office宏病毒文件
Payload Generator 		生成各种语言版本的payload
Windows Executable 		生成Windows可执行payload
Windows Executable(S) 	把包含payload,Stageless生成可执行文件(包含多数功能) 
```

#### 4、  Attacks模块下Web Drive-by功能

![avatar](https://s4.ax1x.com/2022/03/04/bNrkkV.png)

```
Manage 					对开启的web服务进行管理
Clone Site 				克隆网站(可记录受害者提交的数据)
Host File 				提供Web以供下载某文件
Scripted Web Delivery 	提供Web服务，便于下载和执行PowerShell Payload
Signed Applet Attack 	启动一个Web服务以提供自签名Java Applet的运行环境
Smart Applet Attack 	自动检测Java版本并利用已知的exploits绕过security
System Profiler 		用来获取一些系统信息，比如系统版本，Flash版本，浏览器版本等 
```

#### 5、Reporting模块
![avatar](https://s4.ax1x.com/2022/03/04/bNriT0.png)

```
Activity report 						活动报告
Hosts report 							主机报告
Indicators of Compromise 				威胁报告
Sessions report 						会话报告
Social engineering report 				社会工程学报告
Tactics, Techniques, and Procedures 	策略、技巧和程序
Reset Data 								重置数据
Export Data 							导出数据 
```

#### 6、Help模块
![avatar](https://s4.ax1x.com/2022/03/04/bNrel4.png)

```
Homepage 			官方网站
Support 			技术支持，文档介绍
Arsenal 			武器库工具集（需正版授权码）
System information 	系统及版本信息
About 				关于 
```

# Cobalt Strike简单使用

### 一、配置监听器

1、选择`Cobalt Strike下Listener下的Add`

2、Payload选择Beacon HTTP，Host填入团队服务器IP

![avatar](https://s4.ax1x.com/2022/03/04/bNsWxe.png)

3、点击Sava即可

![avatar](https://s4.ax1x.com/2022/03/04/bNshKH.png)

### 二、创建远程Payload

1、选择 Attacks 下的Packages下的Windows Executable。

2、Listener选择刚刚创建的listener1。

![avatar](https://s4.ax1x.com/2022/03/04/bNsyUx.png)


3、点击Generate，生成文件artifact1.exe选择文件保存位置。
![avatar](https://s4.ax1x.com/2022/03/04/bNsDbR.png)

![avatar](https://s4.ax1x.com/2022/03/04/bNsBr9.png)
4、将保存的exe放入靶机中，可以自己新建一个windows虚拟机（基础使用没有免杀，记得关杀毒），放入之后，双击执行。

![avatar](https://s4.ax1x.com/2022/03/04/bNs656.png)

### 三、右键菜单功能介绍

如果受害者机器上线，可以使用右键下的功能，进一步实验测试。

#### 0、Interact功能简介：

Interact功能主要是使用Beacon，来执行各种命令。在Cobalt  Strike中，默认心跳为60s，执行命令的响应很慢。可以对其进行更改。在beacon中，如果想对目标进行命令管理，需要在前面加上shell，如shell whoami、shell ipconfig等。



#### 1、Access下功能简介：

![avatar](https://s4.ax1x.com/2022/03/04/bNssV1.png)

```
Dump Hashes 	获取hash
Elevate 		提权
Golden Ticket 	生成黄金票据注入当前会话
Make Token 		新建登录用户
One-liner 		创建反向shell
Run Mimikatz 	运行 Mimikatz
Spawn As 		用其他用户生成Cobalt Strike侦听器 
```

#### 2、Explore功能简介：
![avatar](https://s4.ax1x.com/2022/03/04/bNsgPK.png)
```
Browser Pivot 	劫持目标浏览器进程
Desktop(VNC) 	桌面交互
File Browser 	文件浏览器
Net View 		命令Net View
Port Scan 		端口扫描
Process List 	进程列表
Screenshot 		屏幕截图 
```

#### 3、Pivoting功能简介:
![avatar](https://s4.ax1x.com/2022/03/04/bNs28O.png)
```
SOCKS Server 	socks代理服务
Listener 		反向端口转发
Deploy VPN 		部署VPN 
```

#### 4、Spawn功能简介：

```
Spawn 	外部监听器(如指派给MSF，获取meterpreter权限)
```

#### 5、Session功能简介：

![avatar](https://s4.ax1x.com/2022/03/04/bNsR2D.png)

```
Note 	备注
Color 	颜色标记
Remove 	删除
Sleep 	指定被控端休眠时间，默认60秒一次回传，让被控端每10秒来下载一次任务。实际中频率不宜过快,容易被发现。
Exit 	退出 
```


## 学习来源:
```
网络(忘了哪了...)

Cobalt Strike 4.0 手册:
https://blog.ateam.qianxin.com/CobaltStrike4.0%E7%94%A8%E6%88%B7%E6%89%8B%E5%86%8C_%E4%B8%AD%E6%96%87%E7%BF%BB%E8%AF%91.pdf
```
</font>
