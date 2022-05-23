---
title: "[内网安全]靶场篇:Vulnstack-ReadTeam系列1"
desc: TonyD0g
date: 2022-04-14 18:08:29
tags: 内网安全
toc: true
---
<font size = 4>
<!-- more -->

# 通关教程


(这里psexec不知道为什么我不行，所以换msf上了,结果msf搞得我头大，proxychains死活不行。直接上头了，让kali镜像回溯为初始状态,然后psexec就能用了,迷惑...)

[所以这里我看的这个通关](https://blog.csdn.net/qq_46527080/article/details/112648202)

[这个是上面的详细版](https://blog.csdn.net/weixin_45605352/article/details/119740990?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522164033306816780261969442%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=164033306816780261969442&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-1-119740990.first_rank_v2_pc_rank_v29&utm_term=%E7%BA%A2%E6%97%A5%E5%AE%89%E5%85%A8vulnstack-ATT&CK%E5%AE%9E%E6%88%98%E7%B3%BB%E5%88%97%20%E7%BA%A2%E9%98%9F%E5%AE%9E%E6%88%98%EF%BC%88%E4%B8%80%EF%BC%89&spm=1018.2226.3001.4187)

也可以学习下别的师傅的思路：
[链接1](https://xz.aliyun.com/t/10076#toc-5)

[链接2](https://www.baidu.com/link?url=TwVhsVlUPEBKKs78IVPB46vMwZbdk7SGXvwoGllzdSaFSAaX9doWX8-HnuKCzgTZqbZHKHQ6TVtv57VnytEqoK&wd=&eqid=cde645d5000025d70000000662578f72)

# 坑点记录

(坑点0)
[MSF进程迁移](https://www.cnblogs.com/g0udan/p/12411937.html)

(坑点1)
msf生成的shell.exe需要通过蚁剑上传到web服务器上,msf开启监听反弹shell
再通过蚁剑终端start shell.exe

(坑点2)
[proxychains的使用](https://www.baidu.com/link?url=MG-VGqpe91mLDxUp36Hm-j59ad16vfB6acPzId8xVPfY6SY_FghlxKO_EnryUh5eDsrUGO2smIYWhbLOpBIlayOAH6ATO6W7pzfM-JYWVfa&wd=&eqid=ca8584de000378ae0000000662579c45)

(坑点3)
CS psexec使用不了说明你CS卡死了，出现其他莫名其妙的情况,比如remove不了也是CS卡死了,重启CS 或 直接暴力回溯镜像 解决问题

(坑点4)
auxiliary/server/socks4a 在MSF6用不了
只能用 auxiliary/server/socks_proxy
