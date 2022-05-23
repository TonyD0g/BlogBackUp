---
title: "[代码审计]PHP篇:文件上传漏洞"
desc: TonyD0g
date: 2022-01-22 11:15:05
tags: 代码审计
toc: true
---
<font size=4 >

<!-- more -->

## 1.普通上传:
未进行文件类型和格式做合法性校验，任意文件上传
漏洞代码示例：
新建一个提供上传文件的 upload.html
```
<html>
<body>
<form action="upload_file.php" method="post" enctype="multipart/form-data">
<label for="file">Filename:</label>
<input type="file" name="file" id="file" />
<input type="submit" name="submit" value="Submit" />
</form>
</body>
</html>
```
创建上传脚本 upload_file.php
```
<?php
if ($_FILES["file"]["error"] > 0)
{
echo "Error: " . $_FILES["file"]["error"] . "<br />";
}
else
{
echo "Upload: " . $_FILES["file"]["name"] . "<br />";
echo "Type: " . $_FILES["file"]["type"] . "<br />";
echo "Size: " . ($_FILES["file"]["size"] / 1024) . " Kb<br />";
echo "Temp file: " . $_FILES["file"]["tmp_name"] . "<br />";
move_uploaded_file($_FILES["file"]["tmp_name"],"upload/" . $_FILES["file"]["name"]);
echo "Stored in: " . "upload/" . $_FILES["file"]["name"];
}
?>
```

## 2.upload-labs
```
upload-labs通关记录：
https://blog.csdn.net/weixin_44677409/article/details/92799366
```

## 3.漏洞防护:
基于安全方面的考虑，应增加用户上传文件的限制，比如检查文件类型、限制文件大小，限定文件路径，文件名随机名、白名单限制文件上传类型等
## 学习来源:
```
公众号Bypass 《PHP代码审计入门笔记》
```
</font>