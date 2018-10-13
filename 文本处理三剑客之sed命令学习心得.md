# sed命令学习心得

***
## 先初步介绍下sed

sed是一种流编辑器，它一次处理一行内容。处理时，把当前处理的行存储在临时缓冲区中，称为“模式空间”（pattern space）,接着用sed命令处理缓冲区中的内容，处理完成后，把缓冲区的内容送往屏幕。然后读入下行，执行下一个循环。如果没有使诸如‘D’的特殊命令，那会在两个循环之间清空模式空间，但不会清空保留空间。这样不断重复，直到文件末尾。文件内容并没有改变，除非你使用重定向存储输出。  

## 基本用法     

        sed [option]... 'script' inputfile...      
***

#### [option]：    

        -n               不输出模式空间内容到屏幕，即不自动打印    
        
        -e               多点编辑    
        
        -f  /PATH/SCRIPT_FILE             从指定文件中读取编辑脚本    
        
        -r               支持使用扩展正则表达式    
        
        -i.bak           备份文件并原处编辑      

#### script:    

            '地址+命令     
        
#### 地址定界： 

         (1) 不给地址：对全文进行处理   
         
         (2) 单地址：   
         
                  #: 指定的行，$：最后一行   
                  
                 /pattern/ ：被此处模式所能够匹配到的每一行   
                 
         (3) 地址范围：   
         
                  #,#   
                  
                  #,+#   
                  
                 /pat1/,/pat2/   
                 
                  #,/pat1/   
                  
         (4) ~：步进   
         
                  1~2 奇数行   
                  
                  2~2 偶数行     
                  
#### 编辑命令：   

        d              删除模式空间匹配的行，并立即启用下一轮循环   
        
        p             打印当前模式空间内容，追加到默认输出之后   
        
        a  \text      在指定行后面追加文本，支持使用\n实现多行追加   
          
        i  \text     在行前面插入文本   
        
        c  \text      替换行为单行或多行文本   
        
        w /path/ file  保存模式匹配的行至指定文件   
        
        r /path/ file  读取指定文件的文本至模式空间中匹配到的行后   
        
        =              为模式空间中的行打印行号   
        
        !             模式空间中匹配行取反处理    
        
       s/AA/aa/      查找替换 查找AA替换成aa(支持使用其它分隔符，s@@@，s###)   
       
        替换标记：   
        
         g       行内全局替换   
         
         p       显示替换成功的行   
         
         w /PATH/FILE      将替换成功的行保存至文件中  
         

## 基础练习

1.删除centos7系统/etc/grub2.cfg文件中所有以空白开头的行行首的空白字符 

`[root@centos7 ~]#sed -i -r 's/^[[:space:]]*([[:alnum:]].*)/\1/' /etc/grub2.cfg `  

2.删除/etc/fstab文件中所有以#开头，后面至少跟一个空白字符的行的行首的#和空白字符   

`[root@centos7 ~]#sed -i -r 's/^#[[:space:]]+(.*)/\1/' /etc/fstab `  

3.在centos6系统/root/install.log每一行行首增加#号   

`[root@centos6 ~]#sed -i -r 's/(.*)/#\1/' /root/install.log `  

4.在/etc/fstab文件中不以#开头的行的行首增加#号   

`[root@centos7 ~]#sed -i -r 's/^[^#](.*)/#\1/' /etc/fstab`  

5.处理/etc/fstab路径,使用sed命令取出其目录名和基名   

`[root@centos7 ~]#echo /etc/fstab |sed -nr 's@(^.*/)([^/].*/?)@\1@p'`  

`[root@centos7 ~]#echo /etc/fstab |sed -nr 's@(^.*/)([^/].*/?)@\2@p'`    

6.利用sed 取出ifconfig命令中本机的IPv4地址   

`[root@centos7 ~]#ifconfig | sed -nr 's/.*inet (addr:)?(.*)(netmask|Bcast).*/\2/p' | head -n 1 `  

`[root@centos7 ~]#ifconfig | sed -r '2!d;s/.*inet (addr:)?//;s/ .*//'`  

7.统计centos安装光盘中Package目录下的所有rpm文件的以.分隔倒数第二个字段的重复次数   

`[root@centos7 ~]#ls /run/media/root/CentOS\ 7\ x86_64/Packages/ | sed -r 's/.*\.(.*).rpm$/\1/' | sort | uniq -c `  

`[root@centos7 ~]#ls *.rpm | rev | cut -d. -f2 | rev |sort | uniq -c` 

8.统计/etc/init.d/functions文件中每个单词的出现次数，并排序（用grep和sed两种方法分别实现）   

`[root@centos7 ~]#cat /etc/init.d/functions | grep -Eo [[:alpha:]]+ | sort | uniq -c`  

`[root@centos7 ~]#sed -r 's/[^[:alpha:]]+/\n/g' /etc/init.d/functions | sort | uniq -c`  

9.将文本文件的n和n+1行合并为一行，n为奇数行   

`[root@centos7 ~]#sed -n 'N;s/\n//p' f1`

## 深层次的理解

+ 新建一个test文本
````bash
<table>
[root@centos7 test]#cat test.txt 
systemctl_redirect
redhalt5
strstr8

return
````
+ 运行sed options为-n 且查找替换后有p选项 
````bash
[root@centos7 test]#sed -nr 's/[^[:alpha:]]+/\n/gp' test.txt
systemctl
redirect
redhalt

strstr
````
+ 运行sed options无-n 且查找替换后没有p选项
````bash
[root@centos7 test]#sed -r 's/[^[:alpha:]]+/\n/g' test.txt  
systemctl
redirect
redhalt

strstr


return
````
+ 以上可以加深sed的模式空间与打印屏幕之间的关系理解
***
