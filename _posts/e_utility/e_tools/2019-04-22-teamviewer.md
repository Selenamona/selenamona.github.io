---
layout:     post
title:      Teamviewer for MAC 检测到商业用途的解决方法
summary:
categories: Tools
technique: true
---

网上查找后发现一个不付费的解决方法。
但要注意：Teamviewer 14及以上的版本此办法无法解决，必须把版本降到13，亲测！！

1、假如你的Teamviewer版本是13，恭喜你，可以直接进行第2步。如果是14或者13的版本尝试第2步后仍未解决，请卸载并重装我提供的版本。

[TeamViewer for mac V13.0.6447](https://pan.baidu.com/disk/home#/all?path=%2Fteamviewer&vmode=list)

2、修改Teamviewer页面上显示的ID，脚本来自GitHub-zhovner。可以先记录下修改前的ID。 

2.1 [下载脚本](https://pan.baidu.com/disk/home#/all?path=%2Fteamviewer&vmode=list) 

2.2 退出Teamviewer 

2.3 在终端执行以下命令：sudo python TeamViewer-id-changer.py

    注意使用的是MAC自带的python2，假如你修改了默认的python环境，可以使用sudo /usr/bin/python TeamViewer-id-changer.py或sudo /usr/bin/python2 TeamViewer-id-changer.py然后输入root密码，回车继续执行。 

2.4 如图，提示“ID changed sucessfully.”后，千万不要打开Teamviewer，要先重启电脑！！！

重启电脑后打开Teamviewer，会发现ID修改了，这时候再尝试远程另一台电脑，发现不会再提示商业用途了，成功！！

[原文连接](https://www.jianshu.com/p/c1a77c351283)