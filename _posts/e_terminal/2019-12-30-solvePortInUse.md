---
layout:     post
title:      解决端口被占用
summary:
categories: Terminal
technique: true
--- 


## | 解决端口被占用

1. Win+R  打开运行 ，输入cmd 打开命令行窗口

2. 假设要查询端口8080被占用情况，在命令行下输入：`netstat  -aon|findstr  "8080" `

    ![](https://raw.githubusercontent.com/Selenamona/Selenamona.github.io/master/assets/images/easytip-port1.jpg)

3. 得到进程号 2996，继续输入命令： `tasklist|findstr  "2996"`

    ![](https://raw.githubusercontent.com/Selenamona/Selenamona.github.io/master/assets/images/easytip-port2.jpg)

4. 得到进程映像名称  TNSLSNR.EXE

5. Ctrl + Alt + delete 打开任务管理器，找到 名为 TNSLSNR.EXE的进程，右键，结束该进程

6. 这样端口被占用的问题就解决了