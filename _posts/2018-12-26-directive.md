---
layout:     post
title:      xshell 命令
summary: 
categories: Js
technique: true
---



## | nignx

pwd 查看目录路径 

修改代理配置：
cd /usr/local/nginx/conf
vim nginx.conf

i 编辑

退出：
esc 
:wq 保存并退出
:q! 未保存退出

重启：
cd /usr/local/nginx/sbin  
./nginx -s reload   


------------

发版：/data/web-static/aries

rm -f index.html
rm -rf static
rz 上传
unzip 解压
 
---------------

查看日志：
sh creditfrontol-exec.sh      
cd ../logs/creditfrontol      
tail -f creditfrontol.log       //查看日志      
cat creditfrontol.log | grep '关键字' -C20     //检索        

docker exec -it integratedqueryApplicationImpl /bin/bash	

