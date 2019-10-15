---
layout:     post
title:      Mac下升级ruby到最新版
summary:
categories: Terminal
technique: true
---

## | 查看ruby版本

命令：ruby --version

## | 升级ruby

命令： brew install ruby

此时，会提示如下，先升级Homebrew，然后是个漫长的等待过程，你可以先喝口水等待一会儿了。
    安装完成之后，执行查看版本命令，发现还是2.0.0，版本没有变，通过百度发现还应该设置环境变量

## | 设置环境变量

打开文件：vi .bashrc
编辑：i
输入下面内容：export PATH=/usr/local/Cellar/ruby/2.5.1/bin:$PATH
保存退出：:wq
执行导入命令：source .bashrc

再次查看版本，大功告成。·