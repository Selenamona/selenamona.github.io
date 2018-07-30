---
layout:     post
title:      tips / 常用快捷键 
summary:  
categories: Guides
technique: true
---

### 常用快捷键

任务管理器：   ctrl + alt + delete      
打开cmd： win + R      

### IDEA

psvm：public static void main      
fori：for循环      
sout：System.out.println()      

shift + F6 ：重命名      
shift两次：搜索      

ctrl + J ： 查询简写      
ctrl + ←或者→  ： 光标跳转单词最前/最后      
ctrl + shift + ←或者→ ： 选中单词      
Ctrl + Shift + U ：大小写转化      
Ctrl + Shift + R ：查找接口      
Ctrl + ”+/-”：当前方法展开、折叠      
ctrl + R ： 替换      
ctrl + F ： 搜索      
Ctrl + Alt + T ：用if、while、try catch来围绕选中的代码行      
ctrl + alt + 空格 ：            

Alt + Up/Down ：在方法间快速移动定位      
Alt + F3 ：逐个往下查找相同文本，并高亮显示      
Alt + Enter：引入对应类      
Alt + Insert：新建类      

### ECLIPSE

ctrl+shift+x   转为大写        
ctrl+shift+y   转为小写       

### xshell-常用命令

ls       
sh creditfrontol-exec.sh      
cd ../logs/creditfrontol      
tail -f creditfrontol.log       //查看日志      
cat creditfrontol.log | grep '关键字' -C20     //检索        

docker exec -it integratedqueryApplicationImpl /bin/bash	      

### 关于日期 

获取某年某月多少天：new Date(new Date(2017,12,1).getTime()-864e5).getDate();              
转换日期格式：new Date().toLocaleDateString().replace(/\//g,"-");                
日期字符串排序：new Date(a).getTime();                  


### 关于数组对象

触发数组对象，视图更新：             
this.$set('数组变量'，index，newVal)；                 
this.$set('eMsg['+index+']',false);           
this.arr.splice(n,1,newVal); 
Obiect.assign({},obj,{a:1})；   
数组去重：1、obj添加attr；2、[...new Set([1,2,3,2,1,"1"])]             
取数组中最大值：Math.max.apply(Math,arr)             
取数组中最小值：Math.min.apply(Math,arr)            


### 其他

**启动Mysql ：**
net start mysql              

**创建maven项目：**          
mvn archetype:generate -DgroupId=imooc-arthhur -DartifactId=spring-mvc-study            -DarchetypeArtifatId=maven-archetype-webapp           

**获取event及参数：**          
var ev = window.event || arguments[0].target;           

**组件切换**：先加载新组件，等新组件渲染好还没挂载前，销毁旧组件，再挂载新组件；           
切换执行顺序：：新组件------beforeCreate---------created--------beforeMount           
  	旧组件------beforeDestory--------destory           
  	新组件------mounted           

**全局安装的包**    npm list --depth=0 -global           

**判断对象是否为空：**            
Object.keys(obj).length           

**动态添加样式：**           
:class = "{active:isActive}"           
:class="[isActive?ActiveClass:'']"           