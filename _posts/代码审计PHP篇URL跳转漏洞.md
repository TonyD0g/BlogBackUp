---
title: "[代码审计]PHP篇:URL跳转漏洞"
desc: TonyD0g
date: 2022-01-26 22:26:56
tags: 代码审计
toc: true
---
<font size=4 >

<!-- more -->

## 1.URL任意跳转:

未做任何限制，传入任何网址即可进行跳转:
漏洞示例代码:
```
<?php  
  $redirect_url = $_GET['url'];
  header("Location: " . $redirect_url);
  exit;
?>
```

**Payload：?url=http://www.baidu.com，即可跳转到百度首页**

## 2.编码解码:

**比如base64解码:**
```
<?php  
  $url = base64_decode($_GET['url']);
  header("Location: " . $url);
?>
```

**Paylod：?url=aHR0cDovL3d3dy5iYWlkdS5jb20=**

## 3.常见的绕过姿势:

**利用默认协议:**
```
?url=\\www.baidu.com
?url=\/www.baidu.com
?url=\\\\www.baidu.com     等价于：?url=http://www.baidu.com
```

**前缀式:**
```
利用问号?： ?url=http://www.evil.com?www.aaa.com
利用井号#： http://www.aaa.com?returnUrl=http://www.evil.com#www.aaa.com
```

**其他形式:**
```
?url=http://www.baidu.com\aaa.com
?url=http://www.baidu.com\\aaa.com
```

**后缀式:**
```
利用@符号：?url=http://www.aaa.com@www.evil.com
其他形式： http://www.aaa.com.evil.com
```

**其他思路:**
```
使用IP地址、IPv6地址、更换ftp、gopher协议
```

## 4.白名单限制绕过:

**白名单限制:**
比如:
?redirect_uri=http://www.baidu.com
尝试进行跳转到其他网站时发现做了白名单限制，非QQ域名禁止跳转，会报错说跳转链接非法

**利用问号绕过限制:**
```
?redirect_uri=http://www.baidu.com
?redirect_uri=http://www.qq.com?http://www.baidu.com
?redirect_uri=http://www.baidu.com?&http://www.qq.com
?redirect_uri=http://www.baidu.com/test.html?&http://www.qq.com
?redirect_uri=http://www.baidu.com\test.html?&http://www.qq.com
```
**在代码中判断是否为目标域名，但开发小哥哥们喜欢用字符串包含来判断**
```
//下面的语句都返回"http://www.aaa.com.evil.com"

http://www.aaa.com?returnUrl=http://www.aaa.com.evil.com
http://www.aaa.com?returnUrl=http://www.evil.com/www.aaa.com
http://www.aaa.com?returnUrl=http://www.xxxaaa.com
```
**利用反斜线:**
```
http://www.aaa.com?returnUrl=http://www.evil.comwww.aaa.com
http://www.aaa.com?returnUrl=http://www.evil.com\www.aaa.com
多次跳转,即aaa公司信任ccc公司，ccc公司同样存在漏洞或者提供跳转服务:
http://www.aaa.com?returnUrl=http://www.ccc.com?jumpto=http://www.evil.com
实际挖掘过程中还可以将上述方法混合使用，甚至使用URL编码、ip地址替代域名等手段。
```

## 学习来源:
```
公众号Bypass 《PHP代码审计入门笔记》
```
</font>